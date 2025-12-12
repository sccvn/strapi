# Content Manager - Developer Guide

Comprehensive guide for developers integrating with and extending Strapi Content Manager.

**Version**: 5.31.3  
**Audience**: Backend Developers, Plugin Developers, System Architects

---

## Table of Contents

1. [Architecture Overview](#architecture-overview)
2. [Service API](#service-api)
3. [Controller Customization](#controller-customization)
4. [Permission System](#permission-system)
5. [Extending Content Manager](#extending-content-manager)
6. [Hooks and Lifecycles](#hooks-and-lifecycles)
7. [Testing](#testing)
8. [Performance Optimization](#performance-optimization)
9. [Migration Guide](#migration-guide)
10. [Code Examples](#code-examples)

---

## Architecture Overview

### Plugin Structure

```
@strapi/plugin-content-manager
│
├── server/                      # Backend logic
│   ├── controllers/            # HTTP request handlers
│   ├── services/               # Business logic
│   ├── routes/                 # API endpoints
│   ├── policies/               # Access control
│   ├── middlewares/            # Request processing
│   └── validation/             # Input validation
│
├── admin/                       # Frontend React app
│   ├── src/
│   │   ├── components/        # UI components
│   │   ├── pages/             # Route pages
│   │   ├── hooks/             # React hooks
│   │   └── utils/             # Utilities
│
└── shared/                      # Shared code
```

### Core Services

| Service | Purpose |
|---------|---------|
| **document-manager** | CRUD operations for documents |
| **permission-checker** | Permission validation |
| **populate-builder** | Query population building |
| **document-metadata** | Metadata generation |
| **data-mapper** | Data transformation |
| **uid** | UID generation |

---

## Service API

### Accessing Services

```typescript
// In plugin code
const documentManager = strapi
  .plugin('content-manager')
  .service('document-manager');

// In bootstrap/lifecycle
module.exports = ({ strapi }) => {
  const service = strapi
    .plugin('content-manager')
    .service('document-manager');
};
```

---

### Document Manager Service

Complete API for document operations.

#### Find One

```typescript
/**
 * Find a single document by ID
 */
const document = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .findOne(
    id: string,                  // Document ID or documentId
    uid: string,                 // Content type UID
    opts?: {
      populate?: object | string,  // Relations to populate
      locale?: string,             // Locale code
      status?: 'draft' | 'published' // Document status
    }
  );
```

**Example**:

```typescript
const article = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .findOne('xyz123', 'api::article.article', {
    populate: { author: true, categories: true },
    locale: 'en',
    status: 'draft'
  });

console.log(article.title); // "My Article"
```

---

#### Find Many

```typescript
/**
 * Find multiple documents
 */
const documents = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .findMany(
    opts: {
      filters?: object,           // Query filters
      sort?: string | string[],   // Sort criteria
      populate?: object | string, // Relations to populate
      locale?: string,            // Locale code
      status?: 'draft' | 'published'
    },
    uid: string                   // Content type UID
  );
```

**Example**:

```typescript
const articles = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .findMany(
    {
      filters: {
        title: { $contains: 'Strapi' }
      },
      sort: 'publishedAt:desc',
      populate: { author: true },
      locale: 'en',
      status: 'published'
    },
    'api::article.article'
  );

console.log(articles.length); // Number of matching articles
```

---

#### Find Page (Paginated)

```typescript
/**
 * Find documents with pagination
 */
const { results, pagination } = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .findPage(
    opts: {
      page?: number,              // Page number (default: 1)
      pageSize?: number,          // Items per page (default: 10)
      filters?: object,
      sort?: string | string[],
      populate?: object | string,
      locale?: string,
      status?: 'draft' | 'published'
    },
    uid: string
  );
```

**Example**:

```typescript
const { results, pagination } = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .findPage(
    {
      page: 2,
      pageSize: 20,
      sort: 'createdAt:desc',
      filters: { publishedAt: { $notNull: true } }
    },
    'api::article.article'
  );

console.log(pagination);
// {
//   page: 2,
//   pageSize: 20,
//   pageCount: 5,
//   total: 95
// }
```

---

#### Create Document

```typescript
/**
 * Create a new document (always as draft)
 */
const document = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .create(
    uid: string,
    opts: {
      data: object,               // Document data
      locale?: string,            // Locale code
      populate?: object | string  // Relations to populate in response
    }
  );
```

**Example**:

```typescript
const newArticle = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .create('api::article.article', {
    data: {
      title: 'New Article',
      content: 'Article content...',
      author: 1,
      categories: [1, 2, 3]
    },
    locale: 'en',
    populate: { author: true }
  });

console.log(newArticle.documentId); // "abc123"
console.log(newArticle.publishedAt); // null (draft)
```

---

#### Update Document

```typescript
/**
 * Update an existing document (draft version)
 */
const document = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .update(
    id: string,                   // Document ID or documentId
    uid: string,
    opts: {
      data: object,               // Updated fields
      locale?: string,
      populate?: object | string
    }
  );
```

**Example**:

```typescript
const updated = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .update('abc123', 'api::article.article', {
    data: {
      title: 'Updated Title',
      content: 'Updated content...'
    },
    locale: 'en'
  });

console.log(updated.title); // "Updated Title"
```

---

#### Delete Document

```typescript
/**
 * Delete a document (all locales and versions)
 */
const result = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .delete(
    id: string,
    uid: string,
    opts?: {
      locale?: string  // Delete specific locale only
    }
  );
```

**Example**:

```typescript
// Delete all locales
await strapi
  .plugin('content-manager')
  .service('document-manager')
  .delete('abc123', 'api::article.article');

// Delete specific locale only
await strapi
  .plugin('content-manager')
  .service('document-manager')
  .delete('abc123', 'api::article.article', { locale: 'fr' });
```

---

#### Publish Document

```typescript
/**
 * Publish a draft document
 */
const published = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .publish(
    id: string,
    uid: string,
    opts?: {
      locale?: string,
      populate?: object | string
    }
  );
```

**Example**:

```typescript
const published = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .publish('abc123', 'api::article.article', {
    locale: 'en'
  });

console.log(published.publishedAt); // "2025-12-12T10:00:00.000Z"
```

---

#### Unpublish Document

```typescript
/**
 * Unpublish a published document
 */
const unpublished = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .unpublish(
    id: string,
    uid: string,
    opts?: {
      locale?: string
    }
  );
```

**Example**:

```typescript
const unpublished = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .unpublish('abc123', 'api::article.article');

console.log(unpublished.publishedAt); // null
```

---

#### Clone Document

```typescript
/**
 * Clone/duplicate a document
 */
const clone = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .clone(
    id: string,
    body: object,                 // Override data
    uid: string
  );
```

**Example**:

```typescript
const cloned = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .clone(
    'abc123',
    {
      title: 'Cloned Article',
      locale: 'fr'
    },
    'api::article.article'
  );

console.log(cloned.documentId); // New ID: "def456"
console.log(cloned.title); // "Cloned Article"
```

---

#### Check Document Exists

```typescript
/**
 * Check if a document exists
 */
const exists = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .exists(
    uid: string,
    id?: string  // Optional for single types
  );
```

**Example**:

```typescript
// Collection type
const exists = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .exists('api::article.article', 'abc123');

console.log(exists); // true or false

// Single type (no ID needed)
const homepageExists = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .exists('api::homepage.homepage');
```

---

### Permission Checker Service

Validate user permissions for content operations.

#### Create Permission Checker

```typescript
const permissionChecker = strapi
  .plugin('content-manager')
  .service('permission-checker')
  .create({
    userAbility: ctx.state.userAbility,  // From context
    model: 'api::article.article'        // Content type UID
  });
```

---

#### Check Permissions

```typescript
// Check if user can create
if (permissionChecker.cannot.create()) {
  throw new ForbiddenError();
}

// Check if user can update specific document
const document = await findDocument();
if (permissionChecker.cannot.update(document)) {
  throw new ForbiddenError();
}

// Check if user can delete
if (permissionChecker.cannot.delete(document)) {
  throw new ForbiddenError();
}

// Check if user can read
if (permissionChecker.cannot.read()) {
  throw new ForbiddenError();
}
```

---

#### Sanitize Input

```typescript
// Sanitize create input (removes fields user can't set)
const sanitizedData = await permissionChecker.sanitizeCreateInput(data);

// Sanitize update input (for specific document)
const sanitizedData = await permissionChecker.sanitizeUpdateInput(document)(data);

// Sanitize query (for read operations)
const sanitizedQuery = await permissionChecker.sanitizedQuery.read(query);
```

**Example**:

```typescript
async createArticle(ctx) {
  const { data } = ctx.request.body;
  const { userAbility } = ctx.state;
  
  const permissionChecker = strapi
    .plugin('content-manager')
    .service('permission-checker')
    .create({ userAbility, model: 'api::article.article' });
  
  // Check permission
  if (permissionChecker.cannot.create()) {
    return ctx.forbidden();
  }
  
  // Sanitize input (remove unauthorized fields)
  const sanitizedData = await permissionChecker.sanitizeCreateInput(data);
  
  // Create document
  const article = await strapi
    .plugin('content-manager')
    .service('document-manager')
    .create('api::article.article', { data: sanitizedData });
  
  return article;
}
```

---

### Populate Builder Service

Build complex populate queries for relations.

```typescript
const populate = await strapi
  .plugin('content-manager')
  .service('populate-builder')('api::article.article')
  .populateFromQuery(query)      // Parse query params
  .populateDeep(2)                // Deep populate (2 levels)
  .countRelations({               // Count related items
    toOne: false,                 // Don't count one-to-one
    toMany: true                  // Count one-to-many
  })
  .build();                       // Build final object

// Use in query
const article = await strapi.documents('api::article.article').findOne({
  documentId: 'abc123',
  populate
});
```

**Example**:

```typescript
// Manual populate
const populate = {
  author: {
    populate: {
      avatar: true
    }
  },
  categories: true,
  comments: {
    populate: {
      user: true
    },
    count: true
  }
};

// Using populate builder
const populate = await strapi
  .plugin('content-manager')
  .service('populate-builder')('api::article.article')
  .populateDeep(2)
  .countRelations({ toMany: true })
  .build();
```

---

## Controller Customization

### Extending Controllers

Override default controller methods:

```typescript
// src/extensions/content-manager/strapi-server.js

module.exports = (plugin) => {
  // Extend collection-types controller
  plugin.controllers['collection-types'].find = async (ctx) => {
    // Custom logic before
    console.log('Custom find logic');
    
    // Call original method
    await strapi.plugin('content-manager')
      .controller('collection-types')
      .find(ctx);
    
    // Custom logic after
    console.log('Results:', ctx.body.results.length);
  };
  
  return plugin;
};
```

---

### Custom Endpoints

Add new endpoints to Content Manager:

```typescript
module.exports = (plugin) => {
  // Add custom route
  plugin.routes['admin'].routes.push({
    method: 'GET',
    path: '/collection-types/:model/custom-action',
    handler: 'collection-types.customAction',
    config: {
      policies: ['admin::isAuthenticatedAdmin']
    }
  });
  
  // Add custom controller method
  plugin.controllers['collection-types'].customAction = async (ctx) => {
    const { model } = ctx.params;
    
    // Custom logic
    const data = await customLogic(model);
    
    ctx.body = { data };
  };
  
  return plugin;
};
```

---

## Permission System

### Understanding Permissions

Permissions are hierarchical:

```
Role → Permissions → Content Type → Actions → Fields → Conditions
```

**Levels**:
1. **Role**: User role (Admin, Editor, Author, etc.)
2. **Content Type**: Specific content type
3. **Action**: Create, Read, Update, Delete, Publish
4. **Fields**: Specific fields within content type
5. **Conditions**: Dynamic conditions based on content

---

### Checking Permissions in Code

```typescript
async customAction(ctx) {
  const { userAbility, user } = ctx.state;
  
  // Create permission checker
  const checker = strapi
    .plugin('content-manager')
    .service('permission-checker')
    .create({
      userAbility,
      model: 'api::article.article'
    });
  
  // Check action permission
  if (checker.cannot.create()) {
    return ctx.forbidden('You cannot create articles');
  }
  
  // Check field-level permission
  const sanitized = await checker.sanitizeCreateInput({
    title: 'Article',
    sensitiveField: 'value'  // Removed if no permission
  });
  
  // Check conditional permission (on existing document)
  const article = await getArticle();
  if (checker.cannot.update(article)) {
    return ctx.forbidden('You cannot update this specific article');
  }
}
```

---

### Custom Permission Conditions

Add custom conditions in permission configuration:

```json
{
  "conditions": {
    "author": {
      "id": "{{user.id}}"
    },
    "status": {
      "$eq": "draft"
    }
  }
}
```

**Meaning**: User can only update articles where they are the author and status is draft.

---

## Extending Content Manager

### Custom Middlewares

Add middleware to Content Manager routes:

```typescript
module.exports = (plugin) => {
  // Add middleware to all routes
  plugin.routes['admin'].routes = plugin.routes['admin'].routes.map(route => ({
    ...route,
    config: {
      ...route.config,
      middlewares: [
        ...(route.config.middlewares || []),
        'plugin::content-manager.custom-middleware'
      ]
    }
  }));
  
  return plugin;
};

// Define middleware
strapi.plugin('content-manager').middleware('custom-middleware', (ctx, next) => {
  console.log('Before request');
  await next();
  console.log('After request');
});
```

---

### Custom Policies

Add custom policies for access control:

```typescript
module.exports = (plugin) => {
  // Add custom policy
  plugin.policies['custom-policy'] = async (ctx, config, { strapi }) => {
    const { user } = ctx.state;
    
    // Custom authorization logic
    if (!user.premium) {
      return ctx.forbidden('Premium users only');
    }
    
    return true;  // Allow
  };
  
  // Apply to route
  plugin.routes['admin'].routes.push({
    method: 'POST',
    path: '/premium-action',
    handler: 'collection-types.premiumAction',
    config: {
      policies: ['plugin::content-manager.custom-policy']
    }
  });
  
  return plugin;
};
```

---

### Custom Validation

Add custom validation to Content Manager:

```typescript
const { yup } = require('@strapi/utils');

module.exports = (plugin) => {
  // Override validation schema
  const originalValidation = plugin.controllers['collection-types'].validateBulkActionInput;
  
  plugin.controllers['collection-types'].validateBulkActionInput = async (data) => {
    // Custom validation
    const schema = yup.object({
      documentIds: yup.array().of(yup.string()).min(1).max(100).required()
    });
    
    await schema.validate(data, { abortEarly: false });
    
    // Call original if needed
    return originalValidation(data);
  };
  
  return plugin;
};
```

---

## Hooks and Lifecycles

### Document Lifecycle Hooks

Hook into document operations via Strapi's lifecycle system:

```typescript
// src/index.js or bootstrap

module.exports = ({ strapi }) => ({
  async bootstrap() {
    // Listen to document events
    strapi.db.lifecycles.subscribe({
      models: ['api::article.article'],
      
      async beforeCreate(event) {
        const { data } = event.params;
        console.log('Creating article:', data.title);
        
        // Modify data before creation
        data.slug = slugify(data.title);
      },
      
      async afterCreate(event) {
        const { result } = event;
        console.log('Created article:', result.id);
        
        // Trigger external actions
        await notifyExternalService(result);
      },
      
      async beforeUpdate(event) {
        const { data, where } = event.params;
        console.log('Updating article:', where.id);
      },
      
      async afterUpdate(event) {
        const { result } = event;
        console.log('Updated article:', result.id);
      },
      
      async beforeDelete(event) {
        const { where } = event.params;
        console.log('Deleting article:', where.id);
      },
      
      async afterDelete(event) {
        const { result } = event;
        console.log('Deleted article:', result.id);
      }
    });
  }
});
```

---

### Event Hub Integration

Use Strapi's Event Hub for cross-cutting concerns:

```typescript
module.exports = ({ strapi }) => ({
  async bootstrap() {
    // Subscribe to events
    strapi.eventHub.on('entry.create', async ({ entry, model }) => {
      console.log(`Entry created in ${model}:`, entry.id);
      
      // Custom logic
      await sendNotification(entry);
    });
    
    strapi.eventHub.on('entry.update', async ({ entry, model }) => {
      console.log(`Entry updated in ${model}:`, entry.id);
    });
    
    strapi.eventHub.on('entry.delete', async ({ entry, model }) => {
      console.log(`Entry deleted from ${model}:`, entry.id);
    });
    
    strapi.eventHub.on('entry.publish', async ({ entry, model }) => {
      console.log(`Entry published in ${model}:`, entry.id);
      
      // Invalidate cache
      await invalidateCache(entry);
    });
  }
});
```

---

## Testing

### Unit Testing Services

```typescript
// tests/content-manager/document-manager.test.js

const { setupStrapi, cleanupStrapi } = require('../../helpers/strapi');

describe('Document Manager Service', () => {
  let strapi;
  
  beforeAll(async () => {
    strapi = await setupStrapi();
  });
  
  afterAll(async () => {
    await cleanupStrapi(strapi);
  });
  
  describe('findOne', () => {
    it('should find a document by ID', async () => {
      const service = strapi
        .plugin('content-manager')
        .service('document-manager');
      
      const document = await service.findOne(
        'test-id',
        'api::article.article'
      );
      
      expect(document).toBeDefined();
      expect(document.id).toBe('test-id');
    });
  });
  
  describe('create', () => {
    it('should create a new document', async () => {
      const service = strapi
        .plugin('content-manager')
        .service('document-manager');
      
      const created = await service.create('api::article.article', {
        data: {
          title: 'Test Article',
          content: 'Test content'
        }
      });
      
      expect(created).toBeDefined();
      expect(created.title).toBe('Test Article');
      expect(created.publishedAt).toBeNull(); // Draft
    });
  });
});
```

---

### Integration Testing Controllers

```typescript
// tests/content-manager/controllers.test.js

const request = require('supertest');

describe('Collection Types Controller', () => {
  let strapi;
  let token;
  
  beforeAll(async () => {
    strapi = await setupStrapi();
    token = await getAdminToken(strapi);
  });
  
  afterAll(async () => {
    await cleanupStrapi(strapi);
  });
  
  describe('GET /content-manager/collection-types/:model', () => {
    it('should return paginated documents', async () => {
      const response = await request(strapi.server.httpServer)
        .get('/content-manager/collection-types/api::article.article')
        .set('Authorization', `Bearer ${token}`)
        .expect(200);
      
      expect(response.body).toHaveProperty('results');
      expect(response.body).toHaveProperty('pagination');
      expect(Array.isArray(response.body.results)).toBe(true);
    });
    
    it('should respect pagination parameters', async () => {
      const response = await request(strapi.server.httpServer)
        .get('/content-manager/collection-types/api::article.article')
        .query({ page: 1, pageSize: 5 })
        .set('Authorization', `Bearer ${token}`)
        .expect(200);
      
      expect(response.body.results.length).toBeLessThanOrEqual(5);
      expect(response.body.pagination.pageSize).toBe(5);
    });
  });
  
  describe('POST /content-manager/collection-types/:model', () => {
    it('should create a new document', async () => {
      const response = await request(strapi.server.httpServer)
        .post('/content-manager/collection-types/api::article.article')
        .set('Authorization', `Bearer ${token}`)
        .send({
          title: 'New Article',
          content: 'Article content'
        })
        .expect(201);
      
      expect(response.body).toHaveProperty('id');
      expect(response.body.title).toBe('New Article');
    });
    
    it('should validate required fields', async () => {
      await request(strapi.server.httpServer)
        .post('/content-manager/collection-types/api::article.article')
        .set('Authorization', `Bearer ${token}`)
        .send({
          content: 'Missing title'
        })
        .expect(400);
    });
  });
});
```

---

## Performance Optimization

### Query Optimization

```typescript
// ❌ BAD: Deep populate can be expensive
const articles = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .findMany(
    {
      populate: {
        author: {
          populate: {
            avatar: {
              populate: {
                formats: true
              }
            },
            articles: {
              populate: {
                categories: true
              }
            }
          }
        }
      }
    },
    'api::article.article'
  );

// ✅ GOOD: Selective populate
const articles = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .findMany(
    {
      populate: {
        author: {
          fields: ['id', 'name'],
          populate: {
            avatar: {
              fields: ['url']
            }
          }
        }
      }
    },
    'api::article.article'
  );
```

---

### Pagination Best Practices

```typescript
// ✅ Always use pagination for large datasets
const { results, pagination } = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .findPage(
    {
      page: 1,
      pageSize: 20,  // Reasonable page size
      filters: { /* ... */ }
    },
    'api::article.article'
  );

// ❌ Avoid fetching all records
const all = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .findMany({}, 'api::article.article');  // Could return thousands
```

---

### Caching Strategy

```typescript
const NodeCache = require('node-cache');
const cache = new NodeCache({ stdTTL: 600 }); // 10 minutes

module.exports = (plugin) => {
  const originalFind = plugin.controllers['collection-types'].find;
  
  plugin.controllers['collection-types'].find = async (ctx) => {
    const cacheKey = `find:${ctx.params.model}:${JSON.stringify(ctx.query)}`;
    
    // Check cache
    const cached = cache.get(cacheKey);
    if (cached) {
      ctx.body = cached;
      return;
    }
    
    // Call original
    await originalFind(ctx);
    
    // Cache result
    cache.set(cacheKey, ctx.body);
  };
  
  return plugin;
};
```

---

### Database Indexing

Ensure proper indexes for frequently queried fields:

```typescript
// src/api/article/content-types/article/schema.json
{
  "attributes": {
    "title": {
      "type": "string",
      "required": true
    },
    "slug": {
      "type": "string",
      "unique": true  // Creates index
    },
    "publishedAt": {
      "type": "datetime"
      // Automatically indexed by Strapi
    }
  }
}
```

**Manually add indexes** if needed:

```typescript
// Database migration
module.exports = {
  async up(knex) {
    await knex.schema.table('articles', (table) => {
      table.index('created_at');
      table.index(['author_id', 'published_at']);
    });
  },
  
  async down(knex) {
    await knex.schema.table('articles', (table) => {
      table.dropIndex('created_at');
      table.dropIndex(['author_id', 'published_at']);
    });
  }
};
```

---

## Migration Guide

### From Strapi v4 to v5

**Major Changes**:

1. **Entity Service → Document Service**
2. **Draft/Publish Workflow Changes**
3. **Locale Handling Changes**

---

#### Entity Service → Document Service

```typescript
// ❌ v4
const articles = await strapi.entityService.findMany('api::article.article', {
  filters: { title: { $contains: 'test' } }
});

// ✅ v5
const articles = await strapi.documents('api::article.article').findMany({
  filters: { title: { $contains: 'test' } }
});
```

---

#### Draft/Publish Changes

```typescript
// ❌ v4: publishedAt automatically set on create
const article = await strapi.entityService.create('api::article.article', {
  data: { title: 'Article' }
});
console.log(article.publishedAt); // Set automatically

// ✅ v5: Explicit publish required
const article = await strapi.documents('api::article.article').create({
  data: { title: 'Article' }
});
console.log(article.publishedAt); // null (draft)

const published = await strapi.documents('api::article.article').publish({
  documentId: article.documentId
});
console.log(published.publishedAt); // Now set
```

---

#### Locale Handling

```typescript
// ❌ v4: Locale as filter
const article = await strapi.entityService.findOne('api::article.article', id, {
  locale: 'fr'
});

// ✅ v5: Locale as parameter
const article = await strapi.documents('api::article.article').findOne({
  documentId: id,
  locale: 'fr'
});
```

---

## Code Examples

### Complete CRUD Implementation

```typescript
// src/api/article/controllers/article.js

module.exports = {
  /**
   * List articles with permissions and pagination
   */
  async find(ctx) {
    const { userAbility } = ctx.state;
    const { query } = ctx.request;
    
    const documentManager = strapi
      .plugin('content-manager')
      .service('document-manager');
    
    const permissionChecker = strapi
      .plugin('content-manager')
      .service('permission-checker')
      .create({ userAbility, model: 'api::article.article' });
    
    // Check permission
    if (permissionChecker.cannot.read()) {
      return ctx.forbidden();
    }
    
    // Sanitize query
    const sanitizedQuery = await permissionChecker.sanitizedQuery.read(query);
    
    // Build populate
    const populate = await strapi
      .plugin('content-manager')
      .service('populate-builder')('api::article.article')
      .populateFromQuery(sanitizedQuery)
      .populateDeep(1)
      .countRelations({ toMany: true })
      .build();
    
    // Fetch data
    const { results, pagination } = await documentManager.findPage(
      {
        ...sanitizedQuery,
        populate,
        status: 'published'
      },
      'api::article.article'
    );
    
    // Return
    ctx.body = { results, pagination };
  },
  
  /**
   * Get single article
   */
  async findOne(ctx) {
    const { id } = ctx.params;
    const { userAbility } = ctx.state;
    
    const documentManager = strapi
      .plugin('content-manager')
      .service('document-manager');
    
    const permissionChecker = strapi
      .plugin('content-manager')
      .service('permission-checker')
      .create({ userAbility, model: 'api::article.article' });
    
    if (permissionChecker.cannot.read()) {
      return ctx.forbidden();
    }
    
    const article = await documentManager.findOne(
      id,
      'api::article.article',
      { status: 'published' }
    );
    
    if (!article) {
      return ctx.notFound();
    }
    
    ctx.body = article;
  },
  
  /**
   * Create article
   */
  async create(ctx) {
    const { data } = ctx.request.body;
    const { userAbility, user } = ctx.state;
    
    const documentManager = strapi
      .plugin('content-manager')
      .service('document-manager');
    
    const permissionChecker = strapi
      .plugin('content-manager')
      .service('permission-checker')
      .create({ userAbility, model: 'api::article.article' });
    
    if (permissionChecker.cannot.create()) {
      return ctx.forbidden();
    }
    
    // Sanitize and set creator
    const sanitized = await permissionChecker.sanitizeCreateInput(data);
    const withCreator = {
      ...sanitized,
      createdBy: user.id,
      updatedBy: user.id
    };
    
    const article = await documentManager.create('api::article.article', {
      data: withCreator
    });
    
    ctx.body = article;
    ctx.status = 201;
  },
  
  /**
   * Update article
   */
  async update(ctx) {
    const { id } = ctx.params;
    const { data } = ctx.request.body;
    const { userAbility, user } = ctx.state;
    
    const documentManager = strapi
      .plugin('content-manager')
      .service('document-manager');
    
    const permissionChecker = strapi
      .plugin('content-manager')
      .service('permission-checker')
      .create({ userAbility, model: 'api::article.article' });
    
    if (permissionChecker.cannot.update()) {
      return ctx.forbidden();
    }
    
    // Load document
    const existing = await documentManager.findOne(id, 'api::article.article');
    
    if (!existing) {
      return ctx.notFound();
    }
    
    // Check conditional permission
    if (permissionChecker.cannot.update(existing)) {
      return ctx.forbidden();
    }
    
    // Sanitize
    const sanitized = await permissionChecker.sanitizeUpdateInput(existing)(data);
    const withUpdater = {
      ...sanitized,
      updatedBy: user.id
    };
    
    const updated = await documentManager.update(id, 'api::article.article', {
      data: withUpdater
    });
    
    ctx.body = updated;
  },
  
  /**
   * Delete article
   */
  async delete(ctx) {
    const { id } = ctx.params;
    const { userAbility } = ctx.state;
    
    const documentManager = strapi
      .plugin('content-manager')
      .service('document-manager');
    
    const permissionChecker = strapi
      .plugin('content-manager')
      .service('permission-checker')
      .create({ userAbility, model: 'api::article.article' });
    
    if (permissionChecker.cannot.delete()) {
      return ctx.forbidden();
    }
    
    // Load document
    const existing = await documentManager.findOne(id, 'api::article.article');
    
    if (!existing) {
      return ctx.notFound();
    }
    
    // Check conditional permission
    if (permissionChecker.cannot.delete(existing)) {
      return ctx.forbidden();
    }
    
    await documentManager.delete(id, 'api::article.article');
    
    ctx.body = { id };
  },
  
  /**
   * Publish article
   */
  async publish(ctx) {
    const { id } = ctx.params;
    const { userAbility } = ctx.state;
    
    const documentManager = strapi
      .plugin('content-manager')
      .service('document-manager');
    
    const permissionChecker = strapi
      .plugin('content-manager')
      .service('permission-checker')
      .create({ userAbility, model: 'api::article.article' });
    
    // Check publish permission
    if (permissionChecker.cannot.publish) {
      return ctx.forbidden();
    }
    
    const published = await documentManager.publish(id, 'api::article.article');
    
    ctx.body = published;
  }
};
```

---

**Last Updated**: December 12, 2025  
**Version**: 5.31.3  
**For**: Strapi Developers
