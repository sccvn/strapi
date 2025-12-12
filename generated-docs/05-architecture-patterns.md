# Architecture Patterns - Strapi Core

## Overview

This document extracts and documents the key architectural patterns used in Strapi v5.31.3's core framework (`packages/core/strapi`). These patterns form the foundational structure for the entire CMS platform.

## Table of Contents

1. [Dependency Injection Container](#1-dependency-injection-container)
2. [Plugin Architecture](#2-plugin-architecture)
3. [Event-Driven Architecture](#3-event-driven-architecture)
4. [Middleware Chain (Koa.js)](#4-middleware-chain-koajs)
5. [Service Layer](#5-service-layer)
6. [Registry Pattern](#6-registry-pattern)
7. [Provider Pattern](#7-provider-pattern)
8. [Lifecycle Hooks](#8-lifecycle-hooks)
9. [Configuration Management](#9-configuration-management)
10. [Request Context](#10-request-context)

---

## 1. Dependency Injection Container

### Pattern Description

Strapi uses a **Dependency Injection (DI) Container** pattern to manage service instances and their dependencies. This creates a centralized, testable service registry.

### Implementation

**File**: `packages/core/core/src/container.ts`

```typescript
export class Container {
  private container: Map<string, any> = new Map();

  // Register a service/dependency
  add(name: string, service: any): this {
    this.container.set(name, service);
    return this;
  }

  // Retrieve a service/dependency
  get(name: string): any {
    if (!this.container.has(name)) {
      throw new Error(`Service "${name}" not found in container`);
    }
    return this.container.get(name);
  }

  // Check if service exists
  has(name: string): boolean {
    return this.container.has(name);
  }

  // Remove a service
  remove(name: string): boolean {
    return this.container.delete(name);
  }
}
```

### Usage in Strapi Class

**File**: `packages/core/core/src/Strapi.ts`

```typescript
class Strapi extends Container implements Core.Strapi {
  constructor(opts: StrapiOptions) {
    super(); // Initialize Container
    
    this.internal_config = loadConfiguration(opts);
    this.registerInternalServices();
    
    for (const provider of providers) {
      provider.init?.(this);
    }
  }

  // Convenient accessors using DI container
  get db(): Database {
    return this.get('db');
  }

  get eventHub(): Modules.EventHub.EventHub {
    return this.get('eventHub');
  }

  get documents(): Modules.Documents.Service {
    return this.get('documents');
  }

  get log(): Logger {
    return this.get('logger');
  }
}
```

### Service Registration

```typescript
// In registerInternalServices()
this.add('logger', createLogger());
this.add('eventHub', createEventHub());
this.add('fs', createStrapiFs());
this.add('requestContext', requestContext());
this.add('entityValidator', entityValidator());
this.add('documents', createDocumentService(this));
this.add('features', createFeaturesService(this));
```

### Benefits

- **Testability**: Easy to mock services for unit testing
- **Loose Coupling**: Services don't directly depend on each other
- **Centralized Management**: Single source of truth for all services
- **Lazy Loading**: Services can be registered dynamically
- **Clear Dependencies**: Explicit service dependencies

---

## 2. Plugin Architecture

### Pattern Description

Strapi implements a **Plugin Architecture** enabling extensibility through modular, self-contained plugins with standardized registration and lifecycle.

### Plugin Discovery & Loading

**File**: `packages/core/strapi/src/node/core/plugins.ts`

```typescript
interface PluginMeta {
  name: string;
  importName: string;    // camelCased version
  modulePath: string;    // Import path
  type: 'local' | 'module';
}

const getEnabledPlugins = async ({ cwd, logger, strapi }) => {
  const plugins: Record<string, PluginMeta> = {};

  // 1. Load plugins from package.json dependencies
  const deps = strapi.config.get('info.dependencies', {});
  
  for (const dep of Object.keys(deps)) {
    const pkg = await getModule(dep, cwd);
    
    // Check if it's a Strapi plugin
    if (pkg && validatePackageIsPlugin(pkg)) {
      const name = pkg.strapi.name || pkg.name;
      
      plugins[name] = {
        name,
        importName: camelCase(name),
        type: 'module',
        modulePath: dep  // e.g., '@strapi/plugin-i18n'
      };
    }
  }

  // 2. Load local plugins from config/plugins.js
  const userPluginsFile = await loadUserPluginsFile(strapi.dirs.app.config);
  
  for (const [userPluginName, userPluginConfig] of Object.entries(userPluginsFile)) {
    if (userPluginConfig.enabled && userPluginConfig.resolve) {
      plugins[userPluginName] = {
        name: userPluginName,
        importName: camelCase(userPluginName),
        type: 'local',
        modulePath: userPluginConfig.resolve,
        path: sysPath
      };
    }
  }

  return plugins;
};
```

### Plugin Registration

**File**: `packages/core/strapi/src/admin.ts`

```typescript
const render = (mountNode: HTMLElement | null, { plugins, ...restArgs }: RenderAdminArgs) => {
  return renderAdmin(mountNode, {
    ...restArgs,
    plugins: {
      // Core plugins (always loaded)
      'content-manager': contentManager,
      'content-type-builder': contentTypeBuilder,
      'email': email,
      'upload': upload,
      'contentReleases': contentReleases,
      'i18n': i18n,
      'reviewWorkflows': reviewWorkflows,
      
      // User-installed plugins
      ...plugins,
    },
  });
};
```

### Plugin Structure

```javascript
// src/plugins/my-plugin/strapi-server.js
module.exports = {
  register({ strapi }) {
    // Register services, controllers, routes
    strapi.container.add('my-service', myService);
  },
  
  async bootstrap({ strapi }) {
    // Initialize plugin when Strapi starts
    await initializePlugin(strapi);
  },
  
  destroy({ strapi }) {
    // Cleanup when Strapi stops
    cleanupResources();
  },
  
  config: {
    default: {
      // Default configuration
    },
    validator: (config) => {
      // Validate configuration
    }
  },
  
  contentTypes: {
    // Plugin content types
  },
  
  routes: [
    // Plugin routes
  ],
  
  controllers: {
    // Plugin controllers
  },
  
  services: {
    // Plugin services
  },
  
  policies: {
    // Plugin policies
  },
  
  middlewares: {
    // Plugin middlewares
  }
};
```

### Benefits

- **Extensibility**: Add features without modifying core
- **Modularity**: Self-contained functionality
- **Reusability**: Share plugins across projects
- **Isolation**: Plugins don't interfere with each other
- **Standardization**: Common structure and lifecycle

---

## 3. Event-Driven Architecture

### Pattern Description

Strapi uses an **Event-Driven Architecture** with a central EventHub, enabling loose coupling through publish-subscribe messaging.

### EventHub Implementation

**File**: `packages/core/core/src/services/event-hub.ts`

```typescript
export interface EventHub {
  emit(eventName: string, ...args: unknown[]): Promise<void>;
  on(eventName: string, listener: Listener): () => void;
  once(eventName: string, listener: Listener): () => void;
  off(eventName: string, listener: Listener): void;
  subscribe(subscriber: Subscriber): () => void;
  unsubscribe(subscriber: Subscriber): void;
  removeAllListeners(): EventHub;
  destroy(): EventHub;
}

export default function createEventHub(): EventHub {
  const listeners = new Map<string, Listener[]>();
  
  // Default subscriber to handle on() listeners
  const defaultSubscriber = async (eventName: string, ...args: unknown[]) => {
    if (listeners.has(eventName)) {
      for (const listener of listeners.get(eventName)) {
        await listener(...args);
      }
    }
  };

  // Store of subscribers (global observers)
  const subscribers = [defaultSubscriber];

  return {
    // Emit event to all subscribers and listeners
    async emit(eventName, ...args) {
      for (const subscriber of subscribers) {
        await subscriber(eventName, ...args);
      }
    },

    // Add specific event listener
    on(eventName, listener) {
      if (!listeners.has(eventName)) {
        listeners.set(eventName, [listener]);
      } else {
        listeners.get(eventName).push(listener);
      }
      
      // Return unsubscribe function
      return () => this.off(eventName, listener);
    },

    // Add one-time listener
    once(eventName, listener) {
      return this.on(eventName, async (...args) => {
        this.off(eventName, listener);
        return listener(...args);
      });
    },

    // Remove listener
    off(eventName, listener) {
      const list = listeners.get(eventName);
      if (list) {
        const index = list.indexOf(listener);
        if (index >= 0) {
          list.splice(index, 1);
        }
      }
    },

    // Add global subscriber (observes all events)
    subscribe(subscriber) {
      subscribers.push(subscriber);
      
      return () => this.unsubscribe(subscriber);
    },

    // Remove subscriber
    unsubscribe(subscriber) {
      const index = subscribers.indexOf(subscriber);
      if (index >= 0) {
        subscribers.splice(index, 1);
      }
    },

    // Cleanup
    removeAllListeners() {
      listeners.clear();
      return this;
    },

    destroy() {
      this.removeAllListeners();
      subscribers.length = 0;
      return this;
    }
  };
}
```

### Document Service Events

**File**: `packages/core/core/src/services/document-service/events.ts`

```typescript
// Common events emitted by Strapi

// Content lifecycle
'entry.create'           // When content is created
'entry.update'           // When content is updated
'entry.delete'           // When content is deleted
'entry.publish'          // When content is published
'entry.unpublish'        // When content is unpublished

// Media
'media.create'           // File uploaded
'media.update'           // File metadata updated
'media.delete'           // File deleted

// Admin
'admin.auth.success'     // Admin login successful
'admin.auth.error'       // Admin login failed
'admin.logout'           // Admin logout

// Lifecycle hooks (more specific)
'beforeCreate'           // Before entity creation
'afterCreate'            // After entity creation
'beforeUpdate'           // Before entity update
'afterUpdate'            // After entity update
'beforeDelete'           // Before entity deletion
'afterDelete'            // After entity deletion
```

### Usage Example

```typescript
// Listening to events
strapi.eventHub.on('entry.create', async ({ uid, documentId, entry }) => {
  console.log(`New ${uid} created: ${documentId}`);
  
  // Trigger additional actions
  await sendNotification(entry);
  await updateSearchIndex(entry);
});

// Emitting events (from Document Service)
async create(uid, params) {
  const entry = await this.repository.create(uid, params);
  
  // Emit event
  await strapi.eventHub.emit('entry.create', {
    uid,
    documentId: entry.documentId,
    entry
  });
  
  return entry;
}

// Global subscriber (observes all events)
strapi.eventHub.subscribe(async (eventName, ...args) => {
  // Log all events
  console.log(`Event: ${eventName}`, args);
  
  // Audit logging
  await auditLog.record(eventName, args);
});
```

### Benefits

- **Decoupling**: Publishers don't know about subscribers
- **Extensibility**: Add new listeners without modifying emitters
- **Observability**: Monitor all system events
- **Asynchronous**: Non-blocking event handling
- **Plugin Integration**: Plugins can listen to core events

---

## 4. Middleware Chain (Koa.js)

### Pattern Description

Strapi uses **Koa.js middleware chain** pattern for request processing through a series of composable, reusable functions.

### Middleware Resolution

**File**: `packages/core/core/src/services/server/middleware.ts`

```typescript
const resolveMiddlewares = (
  config: Array<Core.MiddlewareName | Core.MiddlewareConfig | Core.MiddlewareHandler>,
  strapi: Core.Strapi
) => {
  const middlewares: {
    name: string | null;
    handler: Core.MiddlewareHandler;
  }[] = [];

  for (const item of config) {
    // 1. Inline function middleware
    if (typeof item === 'function') {
      middlewares.push({
        name: null,
        handler: item,
      });
      continue;
    }

    // 2. Named middleware (string)
    if (typeof item === 'string') {
      const middlewareFactory = strapi.middleware(item);
      
      if (!middlewareFactory) {
        throw new Error(`Middleware ${item} not found.`);
      }

      middlewares.push({
        name: item,
        handler: instantiateMiddleware(middlewareFactory, item, {}, strapi),
      });
      continue;
    }

    // 3. Configured middleware (object)
    if (typeof item === 'object' && item !== null) {
      const { name, resolve, config = {} } = item;

      if (name) {
        const middlewareFactory = strapi.middleware(name);
        middlewares.push({
          name,
          handler: instantiateMiddleware(middlewareFactory, name, config, strapi),
        });
        continue;
      }

      if (resolve) {
        const resolvedMiddlewareFactory = resolveCustomMiddleware(resolve, strapi);
        middlewares.push({
          name: resolve,
          handler: instantiateMiddleware(resolvedMiddlewareFactory, resolve, config, strapi),
        });
        continue;
      }

      throw new Error('Invalid middleware configuration. Missing name or resolve properties.');
    }
  }

  return middlewares;
};
```

### Route Middleware

```typescript
const resolveRouteMiddlewares = (route: Core.Route, strapi: Core.Strapi) => {
  const middlewaresConfig = route?.config?.middlewares ?? [];

  if (!isArray(middlewaresConfig)) {
    throw new Error('Route middlewares config must be an array');
  }

  const middlewares = resolveMiddlewares(middlewaresConfig, strapi);

  return middlewares.map(({ handler }) => handler);
};
```

### Core Middlewares

**Directory**: `packages/core/core/src/middlewares/`

```typescript
// 1. Body Parser (packages/core/core/src/middlewares/body.ts)
export default {
  name: 'strapi::body',
  factory() {
    return koaBody({
      multipart: true,
      formidable: {
        maxFileSize: 200 * 1024 * 1024 // 200MB
      }
    });
  }
};

// 2. CORS (packages/core/core/src/middlewares/cors.ts)
export default {
  name: 'strapi::cors',
  factory(config) {
    return cors({
      origin: config.origin || '*',
      credentials: config.credentials || false
    });
  }
};

// 3. Error Handling (packages/core/core/src/middlewares/errors.ts)
export default {
  name: 'strapi::errors',
  factory({ strapi }) {
    return async (ctx, next) => {
      try {
        await next();
      } catch (error) {
        // Format error response
        ctx.status = error.status || 500;
        ctx.body = {
          error: {
            message: error.message,
            name: error.name
          }
        };
        
        // Log error
        strapi.log.error(error);
      }
    };
  }
};

// 4. Compression (packages/core/core/src/middlewares/compression.ts)
export default {
  name: 'strapi::compression',
  factory(config) {
    return compress({
      threshold: config.threshold || 1024
    });
  }
};

// 5. Response Time (packages/core/core/src/middlewares/response-time.ts)
export default {
  name: 'strapi::response-time',
  factory() {
    return async (ctx, next) => {
      const start = Date.now();
      await next();
      const delta = Date.now() - start;
      ctx.set('X-Response-Time', `${delta}ms`);
    };
  }
};

// 6. Logger (packages/core/core/src/middlewares/logger.ts)
export default {
  name: 'strapi::logger',
  factory({ strapi }) {
    return async (ctx, next) => {
      const start = Date.now();
      await next();
      const delta = Date.now() - start;
      
      strapi.log.http(`${ctx.method} ${ctx.url} ${ctx.status} - ${delta}ms`);
    };
  }
};

// 7. Security (packages/core/core/src/middlewares/security.ts)
export default {
  name: 'strapi::security',
  factory(config) {
    return helmet({
      contentSecurityPolicy: config.contentSecurityPolicy,
      hsts: config.hsts,
      frameguard: config.frameguard
    });
  }
};

// 8. Session (packages/core/core/src/middlewares/session.ts)
export default {
  name: 'strapi::session',
  factory({ strapi }) {
    return session({
      key: 'strapi.sid',
      store: strapi.sessionManager.getStore()
    });
  }
};
```

### Middleware Execution Order

```typescript
// Global middlewares (applied to all routes)
app.use(errorHandler);          // 1. Catch errors
app.use(responseTime);           // 2. Track response time
app.use(logger);                 // 3. Log requests
app.use(cors);                   // 4. Handle CORS
app.use(security);               // 5. Security headers
app.use(compression);            // 6. Compress responses
app.use(session);                // 7. Session management
app.use(body);                   // 8. Parse body

// Route-specific middlewares
router.get(
  '/api/articles',
  isAuthenticated,               // 9. Check authentication
  hasPermission('read'),         // 10. Check authorization
  rateLimit({ max: 100 }),       // 11. Rate limiting
  controller.find                // 12. Controller handler
);
```

### Custom Middleware Example

```typescript
// config/middlewares.js
module.exports = [
  'strapi::errors',
  'strapi::security',
  'strapi::cors',
  'strapi::poweredBy',
  'strapi::logger',
  'strapi::query',
  'strapi::body',
  'strapi::session',
  'strapi::favicon',
  'strapi::public',
  
  // Custom middleware (inline)
  {
    name: 'custom::analytics',
    config: { trackingId: 'UA-XXXXX' },
    async resolve(config, { strapi }) {
      return async (ctx, next) => {
        // Track request
        await analytics.track(ctx.url, config.trackingId);
        await next();
      };
    }
  }
];
```

### Benefits

- **Composability**: Chain multiple functions
- **Reusability**: Share middleware across routes
- **Separation of Concerns**: Each middleware has one responsibility
- **Order Control**: Explicit execution order
- **Error Handling**: Centralized error middleware

---

## 5. Service Layer

### Pattern Description

Strapi implements a **Service Layer** pattern to encapsulate business logic separate from controllers and routes.

### Service Structure

```typescript
// packages/core/core/src/services/document-service/index.ts

export function createDocumentService(strapi: Core.Strapi): DocumentService {
  return {
    // CRUD operations
    async findOne(uid, documentId, params = {}) {
      // Business logic
      const document = await repository.findOne(uid, documentId, params);
      
      // Transform data
      const transformedDocument = await transform.toOutput(document, uid);
      
      // Emit event
      await strapi.eventHub.emit('entry.findOne', { uid, document });
      
      return transformedDocument;
    },

    async findMany(uid, params = {}) {
      const documents = await repository.findMany(uid, params);
      const transformedDocuments = await transform.toOutputList(documents, uid);
      
      await strapi.eventHub.emit('entry.findMany', { uid, count: documents.length });
      
      return transformedDocuments;
    },

    async create(uid, params = {}) {
      // Validate
      await this.validate(uid, params.data);
      
      // Permission check
      await permissionChecker.can(params.userId, 'create', uid);
      
      // Create document
      const document = await repository.create(uid, params);
      
      // Lifecycle hooks
      await lifecycles.run('beforeCreate', { uid, data: document });
      
      // Transform
      const output = await transform.toOutput(document, uid);
      
      // Lifecycle hooks
      await lifecycles.run('afterCreate', { uid, result: output });
      
      // Emit event
      await strapi.eventHub.emit('entry.create', { uid, document: output });
      
      return output;
    },

    async update(uid, documentId, params = {}) {
      await this.validate(uid, params.data);
      await permissionChecker.can(params.userId, 'update', uid);
      
      await lifecycles.run('beforeUpdate', { uid, documentId, data: params.data });
      
      const document = await repository.update(uid, documentId, params);
      
      const output = await transform.toOutput(document, uid);
      
      await lifecycles.run('afterUpdate', { uid, result: output });
      await strapi.eventHub.emit('entry.update', { uid, document: output });
      
      return output;
    },

    async delete(uid, documentId, params = {}) {
      await permissionChecker.can(params.userId, 'delete', uid);
      
      await lifecycles.run('beforeDelete', { uid, documentId });
      
      const document = await repository.delete(uid, documentId, params);
      
      await lifecycles.run('afterDelete', { uid, result: document });
      await strapi.eventHub.emit('entry.delete', { uid, documentId });
      
      return document;
    },

    async publish(uid, documentId, params = {}) {
      const document = await repository.publish(uid, documentId, params);
      
      await strapi.eventHub.emit('entry.publish', { uid, documentId });
      
      return document;
    },

    async unpublish(uid, documentId, params = {}) {
      const document = await repository.unpublish(uid, documentId, params);
      
      await strapi.eventHub.emit('entry.unpublish', { uid, documentId });
      
      return document;
    },

    // Utility methods
    async validate(uid, data) {
      return strapi.entityValidator.validateEntityCreation(
        strapi.getModel(uid),
        data
      );
    },

    // Populate helpers
    async populateRelations(uid, document, populate) {
      return populateBuilder.build(uid, document, populate);
    }
  };
}
```

### Service Registration

```typescript
// packages/core/core/src/Strapi.ts

class Strapi extends Container {
  registerInternalServices() {
    // Core services
    this.add('logger', createLogger());
    this.add('eventHub', createEventHub());
    this.add('fs', createStrapiFs());
    
    // Business logic services
    this.add('documents', createDocumentService(this));
    this.add('entityValidator', entityValidator());
    this.add('auth', createAuth(this));
    this.add('customFields', createCustomFields(this));
    this.add('contentAPI', createContentAPI(this));
    
    // Infrastructure services
    this.add('cron', createCron());
    this.add('sessionManager', createSessionManager());
    this.add('requestContext', requestContext());
  }
}
```

### Controller Using Service

```typescript
// packages/core/content-manager/server/src/controllers/collection-types.ts

export default {
  async find(ctx) {
    const { userAbility, model } = ctx.state;
    const { query } = ctx.request;

    // Use service layer (NOT direct database access)
    const documents = await strapi.documents(model.uid).findMany({
      ...query,
      populate: await populateBuilder.populateFromQuery(query, model),
    });

    // Permission check (using another service)
    const permittedDocuments = await permissionChecker.sanitize(
      userAbility,
      documents,
      model.uid
    );

    ctx.body = {
      data: permittedDocuments,
    };
  },

  async create(ctx) {
    const { userAbility, model } = ctx.state;
    const { body } = ctx.request;

    // Use service layer
    const document = await strapi.documents(model.uid).create({
      data: body,
      populate: await populateBuilder.populateFromQuery(ctx.query, model),
    });

    // Permission check
    const permittedDocument = await permissionChecker.sanitize(
      userAbility,
      document,
      model.uid
    );

    ctx.body = {
      data: permittedDocument,
    };
    ctx.status = 201;
  }
};
```

### Benefits

- **Separation of Concerns**: Business logic separate from HTTP handling
- **Reusability**: Services used by multiple controllers
- **Testability**: Easy to unit test services independently
- **Consistency**: Centralized business rules
- **Maintainability**: Changes in one place

---

## 6. Registry Pattern

### Pattern Description

Strapi uses **Registry Pattern** to manage collections of similar entities (content types, controllers, services, middlewares, policies, plugins).

### Registry Implementation

**File**: `packages/core/core/src/registries/index.ts`

```typescript
// Base registry structure
export interface Registry<T> {
  get(key: string): T | undefined;
  getAll(): Record<string, T>;
  set(key: string, value: T): Registry<T>;
  has(key: string): boolean;
  delete(key: string): boolean;
  clear(): void;
  keys(): string[];
  values(): T[];
}

// Generic registry factory
function createRegistry<T>(): Registry<T> {
  const store = new Map<string, T>();

  return {
    get(key) {
      return store.get(key);
    },
    
    getAll() {
      return Object.fromEntries(store);
    },
    
    set(key, value) {
      store.set(key, value);
      return this;
    },
    
    has(key) {
      return store.has(key);
    },
    
    delete(key) {
      return store.delete(key);
    },
    
    clear() {
      store.clear();
    },
    
    keys() {
      return Array.from(store.keys());
    },
    
    values() {
      return Array.from(store.values());
    }
  };
}
```

### Content Types Registry

**File**: `packages/core/core/src/registries/content-types.ts`

```typescript
const createContentTypesRegistry = (strapi: Core.Strapi) => {
  const contentTypes = new Map<UID.ContentType, Schema.ContentType>();

  return {
    /**
     * Returns this list of registered contentTypes uids
     */
    keys() {
      return Array.from(contentTypes.keys());
    },

    /**
     * Returns the instance of a contentType. Instantiate the contentType if not already done
     */
    get(uid: UID.ContentType) {
      return contentTypes.get(uid);
    },

    /**
     * Returns a map with all the contentTypes in a namespace
     */
    getAll(namespace?: string): Record<UID.ContentType, Schema.ContentType> {
      const filteredContentTypes = Array.from(contentTypes.entries()).filter(
        ([uid]) => !namespace || uid.startsWith(`${namespace}.`)
      );

      return Object.fromEntries(filteredContentTypes);
    },

    /**
     * Registers a contentType
     */
    set(uid: UID.ContentType, contentType: Schema.ContentType) {
      contentTypes.set(uid, contentType);
      return this;
    },

    /**
     * Registers a map of contentTypes for a specific namespace
     */
    add(namespace: string, newContentTypes: Record<string, Schema.ContentType>) {
      for (const rawName in newContentTypes) {
        const uid = `${namespace}.${rawName}` as UID.ContentType;
        this.set(uid, newContentTypes[rawName]);
      }
    },

    /**
     * Wraps a contentType to extend it
     */
    extend(uid: UID.ContentType, extendFn: (contentType: Schema.ContentType) => Schema.ContentType) {
      const currentContentType = this.get(uid);

      if (!currentContentType) {
        throw new Error(`Content-Type ${uid} doesn't exist`);
      }

      const newContentType = extendFn(currentContentType);
      this.set(uid, newContentType);

      return this;
    }
  };
};
```

### Multiple Registries in Strapi

```typescript
// packages/core/core/src/Strapi.ts

class Strapi extends Container {
  // Content Types
  contentTypes: ReturnType<typeof createContentTypesRegistry>;
  
  // Components
  components: ReturnType<typeof createComponentsRegistry>;
  
  // Controllers
  controllers: ReturnType<typeof createControllersRegistry>;
  
  // Services
  services: ReturnType<typeof createServicesRegistry>;
  
  // Middlewares
  middlewares: ReturnType<typeof createMiddlewaresRegistry>;
  
  // Policies
  policies: ReturnType<typeof createPoliciesRegistry>;
  
  // Plugins
  plugins: ReturnType<typeof createPluginsRegistry>;
  
  // Models
  models: ReturnType<typeof createModelsRegistry>;
  
  // Hooks
  hooks: ReturnType<typeof createHooksRegistry>;

  constructor(opts: StrapiOptions) {
    super();
    
    // Initialize all registries
    this.contentTypes = createContentTypesRegistry(this);
    this.components = createComponentsRegistry(this);
    this.controllers = createControllersRegistry(this);
    this.services = createServicesRegistry(this);
    this.middlewares = createMiddlewaresRegistry(this);
    this.policies = createPoliciesRegistry(this);
    this.plugins = createPluginsRegistry(this);
    this.models = createModelsRegistry(this);
    this.hooks = createHooksRegistry(this);
  }
}
```

### Usage Examples

```typescript
// Register a content type
strapi.contentTypes.set('api::article.article', {
  kind: 'collectionType',
  modelName: 'article',
  attributes: {
    title: { type: 'string' },
    body: { type: 'text' }
  }
});

// Get a content type
const articleSchema = strapi.contentTypes.get('api::article.article');

// Get all content types in a namespace
const apiContentTypes = strapi.contentTypes.getAll('api');

// Register a service
strapi.services.set('api::article.article', articleService);

// Get a service
const service = strapi.service('api::article.article');

// Register a controller
strapi.controllers.set('api::article.article', articleController);

// Register middleware
strapi.middlewares.set('global::analytics', analyticsMiddleware);

// Get middleware
const middleware = strapi.middleware('global::analytics');

// Register policy
strapi.policies.set('plugin::users-permissions.isAuthenticated', isAuthenticatedPolicy);

// Get policy
const policy = strapi.policy('plugin::users-permissions.isAuthenticated');
```

### Benefits

- **Organization**: Centralized storage of similar entities
- **Namespacing**: Avoid naming conflicts (api::, plugin::, admin::)
- **Discovery**: Easily list all registered entities
- **Extension**: Extend/modify registered entities
- **Lazy Loading**: Load entities on demand

---

## 7. Provider Pattern

### Pattern Description

Strapi uses **Provider Pattern** for pluggable implementations of services with common interfaces (email, upload, database).

### Provider Structure

```typescript
// Base provider interface
interface Provider<TConfig = any, TService = any> {
  init(strapi: Core.Strapi): void | Promise<void>;
  bootstrap?(strapi: Core.Strapi): void | Promise<void>;
  register?(strapi: Core.Strapi): void | Promise<void>;
  destroy?(): void | Promise<void>;
}
```

### Email Provider Example

```typescript
// packages/providers/email-sendgrid/src/index.ts

module.exports = {
  provider: 'sendgrid',
  name: 'SendGrid',
  
  init(providerOptions, settings) {
    const sendgrid = require('@sendgrid/mail');
    sendgrid.setApiKey(providerOptions.apiKey);

    return {
      async send(options) {
        const { from, to, cc, bcc, subject, text, html } = options;

        const msg = {
          from: from || settings.defaultFrom,
          to,
          cc,
          bcc,
          subject,
          text,
          html,
        };

        await sendgrid.send(msg);
      },
    };
  },
};
```

### Upload Provider Example

```typescript
// packages/providers/upload-aws-s3/src/index.ts

const AWS = require('aws-sdk');

module.exports = {
  provider: 'aws-s3',
  name: 'AWS S3',
  
  init(config) {
    const S3 = new AWS.S3({
      accessKeyId: config.accessKeyId,
      secretAccessKey: config.secretAccessKey,
      region: config.region,
    });

    return {
      async upload(file) {
        const params = {
          Bucket: config.params.Bucket,
          Key: `${file.hash}${file.ext}`,
          Body: file.stream || Buffer.from(file.buffer, 'binary'),
          ACL: 'public-read',
          ContentType: file.mime,
        };

        const result = await S3.upload(params).promise();

        file.url = result.Location;
      },

      async delete(file) {
        const params = {
          Bucket: config.params.Bucket,
          Key: `${file.hash}${file.ext}`,
        };

        await S3.deleteObject(params).promise();
      },

      async checkFileSize(file, { sizeLimit }) {
        if (file.size > sizeLimit) {
          throw new Error(`File size exceeds limit of ${sizeLimit} bytes`);
        }
      },
    };
  },
};
```

### Provider Configuration

```javascript
// config/plugins.js

module.exports = ({ env }) => ({
  // Email provider
  email: {
    config: {
      provider: 'sendgrid',
      providerOptions: {
        apiKey: env('SENDGRID_API_KEY'),
      },
      settings: {
        defaultFrom: 'noreply@mysite.com',
        defaultReplyTo: 'support@mysite.com',
      },
    },
  },
  
  // Upload provider
  upload: {
    config: {
      provider: 'aws-s3',
      providerOptions: {
        accessKeyId: env('AWS_ACCESS_KEY_ID'),
        secretAccessKey: env('AWS_ACCESS_SECRET'),
        region: env('AWS_REGION'),
        params: {
          Bucket: env('AWS_BUCKET'),
        },
      },
    },
  },
});
```

### Core Providers

**File**: `packages/core/core/src/providers/index.ts`

```typescript
export const providers = [
  // Telemetry provider
  {
    init(strapi) {
      strapi.add('telemetry', createTelemetryService(strapi));
    }
  },

  // Cron provider
  {
    init(strapi) {
      strapi.add('cron', createCronService(strapi));
    },
    
    async bootstrap(strapi) {
      await strapi.cron.start();
    },
    
    async destroy(strapi) {
      await strapi.cron.stop();
    }
  },

  // Core Store provider
  {
    init(strapi) {
      strapi.add('coreStore', createCoreStore(strapi));
    }
  },

  // Webhooks provider
  {
    init(strapi) {
      strapi.add('webhookStore', createWebhookStore(strapi));
      strapi.add('webhookRunner', createWebhookRunner(strapi));
    },
    
    async bootstrap(strapi) {
      await strapi.webhookStore.loadWebhooks();
    }
  },

  // Admin provider
  {
    async init(strapi) {
      strapi.add('admin', await createAdminService(strapi));
    },
    
    async bootstrap(strapi) {
      await strapi.admin.bootstrap();
    }
  },

  // Registries provider
  {
    init(strapi) {
      strapi.contentTypes = createContentTypesRegistry(strapi);
      strapi.components = createComponentsRegistry(strapi);
      strapi.services = createServicesRegistry(strapi);
      strapi.controllers = createControllersRegistry(strapi);
      strapi.middlewares = createMiddlewaresRegistry(strapi);
      strapi.policies = createPoliciesRegistry(strapi);
      strapi.plugins = createPluginsRegistry(strapi);
    }
  },
];
```

### Benefits

- **Swappable Implementations**: Change providers without code changes
- **Configuration-Based**: Select provider via configuration
- **Extensibility**: Add new providers without modifying core
- **Testability**: Mock providers for testing
- **Consistency**: Common interface for similar services

---

## 8. Lifecycle Hooks

### Pattern Description

Strapi provides **Lifecycle Hooks** to execute custom logic before/after CRUD operations on content.

### Available Hooks

```typescript
// Document Service Lifecycle Hooks

// Create hooks
'beforeCreate'    // Before document creation
'afterCreate'     // After document creation

// Update hooks
'beforeUpdate'    // Before document update
'afterUpdate'     // After document update

// Delete hooks
'beforeDelete'    // Before document deletion
'afterDelete'     // After document deletion

// Find hooks
'beforeFindOne'   // Before finding one document
'afterFindOne'    // After finding one document
'beforeFindMany'  // Before finding multiple documents
'afterFindMany'   // After finding multiple documents

// Publish hooks
'beforePublish'   // Before publishing
'afterPublish'    // After publishing

// Unpublish hooks
'beforeUnpublish' // Before unpublishing
'afterUnpublish'  // After unpublishing

// Count hooks
'beforeCount'     // Before count query
'afterCount'      // After count query
```

### Hook Registration

```javascript
// src/api/article/content-types/article/lifecycles.js

module.exports = {
  async beforeCreate(event) {
    const { data } = event.params;
    
    // Validate data
    if (!data.slug) {
      data.slug = slugify(data.title);
    }
    
    // Set defaults
    data.viewCount = 0;
    data.status = 'draft';
  },

  async afterCreate(event) {
    const { result } = event;
    
    // Trigger external actions
    await sendNotification({
      type: 'article.created',
      article: result
    });
    
    // Update search index
    await searchIndex.add(result);
    
    // Clear cache
    await cache.invalidate('articles');
  },

  async beforeUpdate(event) {
    const { data, where } = event.params;
    
    // Audit logging
    const oldDocument = await strapi.documents('api::article.article')
      .findOne({ documentId: where.documentId });
    
    await auditLog.record('article.update', {
      before: oldDocument,
      after: data
    });
  },

  async afterUpdate(event) {
    const { result } = event;
    
    // Update related entities
    if (result.categoryId) {
      await strapi.documents('api::category.category')
        .update({ documentId: result.categoryId }, {
          data: { articleCount: await getArticleCount(result.categoryId) }
        });
    }
    
    // Update search index
    await searchIndex.update(result);
  },

  async beforeDelete(event) {
    const { where } = event.params;
    
    // Prevent deletion if referenced
    const relatedCount = await strapi.db.query('api::comment.comment')
      .count({ where: { article: where.documentId } });
    
    if (relatedCount > 0) {
      throw new Error('Cannot delete article with existing comments');
    }
  },

  async afterDelete(event) {
    const { result } = event;
    
    // Cleanup related data
    await strapi.db.query('api::tag.tag').deleteMany({
      where: { articles: { $in: [result.documentId] } }
    });
    
    // Remove from search index
    await searchIndex.remove(result.documentId);
    
    // Clear cache
    await cache.invalidate('articles');
  },

  async beforePublish(event) {
    const { data } = event.params;
    
    // Validate before publish
    if (!data.featuredImage) {
      throw new Error('Featured image required before publishing');
    }
  },

  async afterPublish(event) {
    const { result } = event;
    
    // Send to CDN
    await cdn.purge(`/articles/${result.slug}`);
    
    // Notify subscribers
    await newsletter.send({
      subject: `New article: ${result.title}`,
      content: result.excerpt
    });
  }
};
```

### Hook Implementation (Internal)

**File**: `packages/core/core/src/services/document-service/index.ts`

```typescript
export function createDocumentService(strapi: Core.Strapi) {
  return {
    async create(uid, params) {
      // 1. Run beforeCreate hooks
      const eventData = { params: { data: params.data } };
      await lifecycles.run('beforeCreate', uid, eventData);
      
      // 2. Perform creation
      const document = await repository.create(uid, params);
      
      // 3. Run afterCreate hooks
      await lifecycles.run('afterCreate', uid, {
        params: { data: params.data },
        result: document
      });
      
      // 4. Emit event
      await strapi.eventHub.emit('entry.create', { uid, document });
      
      return document;
    },

    async update(uid, documentId, params) {
      const eventData = {
        params: {
          where: { documentId },
          data: params.data
        }
      };
      
      await lifecycles.run('beforeUpdate', uid, eventData);
      
      const document = await repository.update(uid, documentId, params);
      
      await lifecycles.run('afterUpdate', uid, {
        ...eventData,
        result: document
      });
      
      await strapi.eventHub.emit('entry.update', { uid, document });
      
      return document;
    },

    async delete(uid, documentId, params) {
      const eventData = {
        params: { where: { documentId } }
      };
      
      await lifecycles.run('beforeDelete', uid, eventData);
      
      const document = await repository.delete(uid, documentId, params);
      
      await lifecycles.run('afterDelete', uid, {
        ...eventData,
        result: document
      });
      
      await strapi.eventHub.emit('entry.delete', { uid, documentId });
      
      return document;
    }
  };
}
```

### Benefits

- **Extensibility**: Add custom logic without modifying core
- **Cross-Cutting Concerns**: Validation, audit logging, caching
- **Event Handling**: Trigger actions on content changes
- **Data Integrity**: Enforce business rules
- **Integration**: Connect to external systems

---

## 9. Configuration Management

### Pattern Description

Strapi uses a **Configuration Management** pattern with environment-based overrides and dynamic loading.

### Configuration Structure

```
config/
├── admin.js              # Admin panel configuration
├── api.js                # API configuration
├── database.js           # Database configuration
├── middlewares.js        # Middleware stack
├── plugins.js            # Plugin configuration
├── server.js             # Server configuration
├── env/
│   ├── production/
│   │   ├── database.js
│   │   ├── server.js
│   │   └── plugins.js
│   ├── staging/
│   │   └── database.js
│   └── development/
│       └── database.js
```

### Configuration Loader

**File**: `packages/core/core/src/configuration/config-loader.ts`

```typescript
const loadConfigFile = async (configDir: string, filename: string) => {
  const possibleExtensions = ['.js', '.mjs', '.ts'];
  
  for (const ext of possibleExtensions) {
    const filePath = path.join(configDir, `${filename}${ext}`);
    
    if (await pathExists(filePath)) {
      const module = await import(filePath);
      
      // Support default export or module.exports
      const config = module.default || module;
      
      // If function, call with { env } object
      if (typeof config === 'function') {
        return config({ env: process.env });
      }
      
      return config;
    }
  }
  
  return null;
};

const loadConfiguration = async (appDir: string) => {
  const configDir = path.join(appDir, 'config');
  const env = process.env.NODE_ENV || 'development';
  const envConfigDir = path.join(configDir, 'env', env);
  
  // Load base configs
  const database = await loadConfigFile(configDir, 'database');
  const server = await loadConfigFile(configDir, 'server');
  const admin = await loadConfigFile(configDir, 'admin');
  const api = await loadConfigFile(configDir, 'api');
  const middlewares = await loadConfigFile(configDir, 'middlewares');
  const plugins = await loadConfigFile(configDir, 'plugins');
  
  // Load environment-specific overrides
  const envDatabase = await loadConfigFile(envConfigDir, 'database');
  const envServer = await loadConfigFile(envConfigDir, 'server');
  const envAdmin = await loadConfigFile(envConfigDir, 'admin');
  const envPlugins = await loadConfigFile(envConfigDir, 'plugins');
  
  // Merge configs (environment-specific overrides base)
  return {
    database: _.merge({}, database, envDatabase),
    server: _.merge({}, server, envServer),
    admin: _.merge({}, admin, envAdmin),
    api,
    middlewares,
    plugins: _.merge({}, plugins, envPlugins),
  };
};
```

### Configuration Examples

```javascript
// config/database.js
module.exports = ({ env }) => ({
  connection: {
    client: 'postgres',
    connection: {
      host: env('DATABASE_HOST', 'localhost'),
      port: env.int('DATABASE_PORT', 5432),
      database: env('DATABASE_NAME', 'strapi'),
      user: env('DATABASE_USERNAME', 'strapi'),
      password: env('DATABASE_PASSWORD', ''),
      ssl: env.bool('DATABASE_SSL', false),
    },
    pool: {
      min: env.int('DATABASE_POOL_MIN', 2),
      max: env.int('DATABASE_POOL_MAX', 10),
    },
  },
});

// config/server.js
module.exports = ({ env }) => ({
  host: env('HOST', '0.0.0.0'),
  port: env.int('PORT', 1337),
  app: {
    keys: env.array('APP_KEYS'),
  },
  url: env('PUBLIC_URL', 'http://localhost:1337'),
  proxy: env.bool('IS_PROXIED', false),
  cron: {
    enabled: env.bool('CRON_ENABLED', true),
  },
});

// config/admin.js
module.exports = ({ env }) => ({
  auth: {
    secret: env('ADMIN_JWT_SECRET'),
  },
  apiToken: {
    salt: env('API_TOKEN_SALT'),
  },
  transfer: {
    token: {
      salt: env('TRANSFER_TOKEN_SALT'),
    },
  },
});

// config/plugins.js
module.exports = ({ env }) => ({
  upload: {
    config: {
      provider: 'aws-s3',
      providerOptions: {
        accessKeyId: env('AWS_ACCESS_KEY_ID'),
        secretAccessKey: env('AWS_ACCESS_SECRET'),
        region: env('AWS_REGION'),
        params: {
          Bucket: env('AWS_BUCKET'),
        },
      },
    },
  },
  
  email: {
    config: {
      provider: 'sendgrid',
      providerOptions: {
        apiKey: env('SENDGRID_API_KEY'),
      },
      settings: {
        defaultFrom: 'noreply@mysite.com',
      },
    },
  },
});

// config/middlewares.js
module.exports = [
  'strapi::errors',
  {
    name: 'strapi::security',
    config: {
      contentSecurityPolicy: {
        useDefaults: true,
        directives: {
          'connect-src': ["'self'", 'https:'],
          'img-src': ["'self'", 'data:', 'blob:', 'cdn.example.com'],
          upgradeInsecureRequests: null,
        },
      },
    },
  },
  {
    name: 'strapi::cors',
    config: {
      origin: ['http://localhost:3000', 'https://example.com'],
      credentials: true,
    },
  },
  'strapi::poweredBy',
  'strapi::logger',
  'strapi::query',
  'strapi::body',
  'strapi::session',
  'strapi::favicon',
  'strapi::public',
];
```

### Environment Variable Helper

```typescript
// Utility: env helper with type conversion

interface EnvHelper {
  (key: string, defaultValue?: string): string;
  int(key: string, defaultValue?: number): number;
  float(key: string, defaultValue?: number): number;
  bool(key: string, defaultValue?: boolean): boolean;
  json(key: string, defaultValue?: any): any;
  array(key: string, defaultValue?: any[]): any[];
}

const env: EnvHelper = (key, defaultValue) => {
  return process.env[key] || defaultValue || '';
};

env.int = (key, defaultValue = 0) => {
  return parseInt(process.env[key] || String(defaultValue), 10);
};

env.float = (key, defaultValue = 0) => {
  return parseFloat(process.env[key] || String(defaultValue));
};

env.bool = (key, defaultValue = false) => {
  const value = process.env[key];
  if (!value) return defaultValue;
  return value === 'true' || value === '1';
};

env.json = (key, defaultValue = {}) => {
  const value = process.env[key];
  if (!value) return defaultValue;
  return JSON.parse(value);
};

env.array = (key, defaultValue = []) => {
  const value = process.env[key];
  if (!value) return defaultValue;
  return value.split(',').map(v => v.trim());
};
```

### Benefits

- **Environment Separation**: Different configs per environment
- **Security**: Secrets in environment variables, not code
- **Flexibility**: Override base config per environment
- **Type Safety**: Type conversion helpers
- **Dynamic Loading**: Function-based configs with access to env

---

## 10. Request Context

### Pattern Description

Strapi uses **Request Context** pattern (AsyncLocalStorage) to maintain request-scoped data throughout the request lifecycle.

### Implementation

**File**: `packages/core/core/src/services/request-context.ts`

```typescript
import { AsyncLocalStorage } from 'async_hooks';

interface RequestContext {
  state: {
    user?: any;
    auth?: any;
    route?: any;
    [key: string]: any;
  };
}

const storage = new AsyncLocalStorage<RequestContext>();

export default function createRequestContext() {
  return {
    // Run function within request context
    async run(ctx: RequestContext, fn: () => Promise<any>) {
      return storage.run(ctx, fn);
    },

    // Get current request context
    get(): RequestContext | undefined {
      return storage.getStore();
    },

    // Set value in current context
    set(key: string, value: any) {
      const store = storage.getStore();
      if (store) {
        store.state[key] = value;
      }
    },

    // Get value from current context
    getValue(key: string): any {
      const store = storage.getStore();
      return store?.state[key];
    }
  };
}
```

### Middleware Integration

```typescript
// Set up request context in middleware

app.use(async (ctx, next) => {
  await strapi.requestContext.run(
    {
      state: {
        user: ctx.state.user,
        auth: ctx.state.auth,
        route: ctx.state.route,
      }
    },
    async () => {
      await next();
    }
  );
});
```

### Usage in Services

```typescript
// Access request context deep in service layer (no need to pass ctx)

export function createAuditService() {
  return {
    async logAction(action: string, data: any) {
      // Get current user from request context
      const context = strapi.requestContext.get();
      const userId = context?.state.user?.id;

      await strapi.db.query('audit-log').create({
        data: {
          action,
          data,
          userId,
          timestamp: new Date()
        }
      });
    }
  };
}

// No need to pass user info explicitly
await auditService.logAction('article.create', { articleId: 123 });
```

### Benefits

- **Implicit Context**: No need to pass context through call stack
- **Clean APIs**: Services don't need ctx parameter
- **Request Isolation**: Each request has isolated context
- **Thread-Safe**: Async-safe storage mechanism
- **Testability**: Easy to mock context

---

## Architecture Patterns Summary

| Pattern | Purpose | Key Benefit | Files |
|---------|---------|-------------|-------|
| **Dependency Injection** | Service management | Testability, loose coupling | `container.ts`, `Strapi.ts` |
| **Plugin Architecture** | Extensibility | Modular extensions | `plugins.ts`, `admin.ts` |
| **Event-Driven** | Decoupling | Reactive system | `event-hub.ts` |
| **Middleware Chain** | Request processing | Composable handlers | `middleware.ts` |
| **Service Layer** | Business logic | Separation of concerns | `document-service/` |
| **Registry** | Entity management | Centralized storage | `registries/` |
| **Provider** | Swappable implementations | Configuration-based selection | `providers/` |
| **Lifecycle Hooks** | Extension points | Custom logic injection | `lifecycles.ts` |
| **Configuration** | Settings management | Environment-based config | `configuration/` |
| **Request Context** | State propagation | Clean service APIs | `request-context.ts` |

---

## Generated Information

**Date**: December 12, 2025  
**Strapi Version**: 5.31.3  
**Analysis Method**: Code inspection and pattern extraction  
**Source**: `packages/core/strapi`, `packages/core/core`  
**Validation**: Cross-referenced with actual implementation files
