# Architecture and Design Patterns

## Core Architecture

### Strapi Core Class
The main `Strapi` class (in `packages/core/core/src/Strapi.ts`) extends a `Container` class and implements the `Core.Strapi` interface. It serves as the central orchestrator for the entire system.

### Design Patterns

#### 1. **Dependency Injection Container Pattern**
- The `Container` class (`packages/core/core/src/container.ts`) provides a DI container
- Services, plugins, and modules are registered and retrieved via `strapi.get('serviceName')`
- Supports lazy loading and singleton pattern

#### 2. **Plugin Architecture**
- Plugins are first-class citizens in Strapi
- Loaded via `packages/core/core/src/loaders/plugins`
- Each plugin can provide:
  - Server-side logic (controllers, services, routes, policies, middlewares)
  - Admin panel extensions (React components, reducers, routes)
  - Content-type extensions
  - Custom fields

#### 3. **Loader Pattern**
Located in `packages/core/core/src/loaders/`, loaders are responsible for:
- `loadAPIs` - Load user-defined APIs
- `loadMiddlewares` - Load middleware stack
- `loadPolicies` - Load authorization policies
- `loadPlugins` - Load and initialize plugins
- `loadComponents` - Load reusable components
- `loadSanitizers` - Load data sanitizers
- `loadValidators` - Load data validators

All loaders run in parallel during application initialization.

#### 4. **Service Layer Pattern**
Core services (in `packages/core/core/src/services/`):
- **Entity Service** (deprecated, being replaced by Document Service)
- **Document Service** - New unified content API
- **Event Hub** - Event-driven architecture for lifecycle hooks
- **Request Context** - Async local storage for request state
- **Entity Validator** - Schema validation
- **Auth Service** - Authentication and authorization
- **Custom Fields** - Extensible field type system
- **Content API** - REST/GraphQL API layer
- **Webhook Runner** - Webhook execution
- **Cron Service** - Scheduled tasks
- **Worker Queue** - Background job processing

#### 5. **Middleware Stack Pattern**
Koa.js middleware architecture:
- Middlewares are composable functions
- Loaded and registered in specific order
- Support for route-specific and global middlewares
- Custom middleware support

#### 6. **Factory Pattern**
`packages/core/core/src/factories.ts` provides factory functions for creating:
- Controllers
- Services
- Policies
- Middlewares
- Content types

#### 7. **Provider Pattern**
`packages/core/core/src/providers/` - Providers initialize specific subsystems
- Each provider has an `init()` method called during Strapi construction
- Examples: Database provider, Server provider

#### 8. **Event-Driven Architecture**
- **Event Hub** (`packages/core/core/src/services/event-hub.ts`)
- Inspired by lifecycle hooks, not Node.js EventEmitter
- Prevents memory leaks by using single subscriber per feature
- Events for database operations, server lifecycle, content changes

#### 9. **Registry Pattern**
`packages/core/core/src/registries/` - Centralized registries for:
- Content types
- Controllers
- Services
- Middlewares
- Policies
- Routes

#### 10. **Monorepo with Workspace Pattern**
- Yarn workspaces for dependency sharing
- Nx for task orchestration and caching
- Independent versioning via Lerna
- Shared build configuration via `rollup.utils.mjs`

## Key Architectural Concepts

### Content Types
- **Collection Types**: Multiple entries (e.g., articles, products)
- **Single Types**: Single entry (e.g., homepage, settings)
- **Components**: Reusable fields group
- **Dynamic Zones**: Flexible component composition

### Database Abstraction
- Custom ORM in `@strapi/database`
- Query builder pattern
- Transaction support
- Migration system
- Relation management

### Admin Panel Architecture
- React-based SPA
- Module federation for plugins
- Redux/RTK for state management
- Design system components
- Role-based access control (RBAC)

### API Generation
- Automatic REST API from content types
- GraphQL support via plugin
- Document API (new) vs Entity Service API (deprecated)

### Enterprise Features
- **Review Workflows**: Content approval process
- **Content Releases**: Scheduled publishing
- **i18n**: Internationalization support
- **Audit logs**: Track changes (EE)
- **SSO**: Single sign-on (EE)

## Build System
- **Rollup** for bundling
- **SWC** for fast TypeScript compilation (replaces Babel)
- **Nx** for incremental builds and caching
- Separate build targets: `build:code` and `build:types`
- Preserves module structure in output
