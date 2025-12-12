# Strapi Content Manager - Technical Documentation

## Overview

The Content Manager is a core plugin in Strapi that provides a powerful interface for managing content entries across all content types. It handles CRUD operations, permissions, localization, draft/publish workflows, and relationships.

**Version**: 5.31.3  
**Package**: `@strapi/plugin-content-manager`  
**Location**: `packages/core/content-manager`

## Table of Contents

1. [Architecture Overview](#architecture-overview)
2. [Core Features](#core-features)
3. [API Reference](#api-reference)
4. [User Guide](#user-guide)
5. [Developer Guide](#developer-guide)
6. [Performance & Security](#performance--security)

---

## Architecture Overview

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Content Manager Plugin                    │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │ Controllers  │  │  Services    │  │   Routes     │      │
│  ├──────────────┤  ├──────────────┤  ├──────────────┤      │
│  │ Collection   │  │ Document Mgr │  │ Admin API    │      │
│  │ Single Type  │  │ Permission   │  │ Content API  │      │
│  │ Components   │  │ Populate     │  │ History API  │      │
│  │ Relations    │  │ Metadata     │  │ Preview API  │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│         │                 │                  │               │
│         └─────────────────┼──────────────────┘               │
│                           │                                  │
└───────────────────────────┼──────────────────────────────────┘
                            │
                            ▼
                 ┌──────────────────────┐
                 │  Strapi Core APIs    │
                 ├──────────────────────┤
                 │ - Document Service   │
                 │ - Query Engine       │
                 │ - Permission System  │
                 │ - Event Hub          │
                 └──────────────────────┘
```

### Component Structure

```
content-manager/
├── admin/                    # Frontend React application
│   ├── src/
│   │   ├── components/      # UI components
│   │   ├── pages/           # Page components
│   │   ├── hooks/           # Custom React hooks
│   │   └── utils/           # Utility functions
│
├── server/                   # Backend Node.js application
│   ├── src/
│   │   ├── controllers/     # Request handlers
│   │   │   ├── collection-types.ts
│   │   │   ├── single-types.ts
│   │   │   ├── components.ts
│   │   │   └── relations.ts
│   │   │
│   │   ├── services/        # Business logic
│   │   │   ├── document-manager.ts
│   │   │   ├── permission-checker.ts
│   │   │   ├── populate-builder.ts
│   │   │   └── document-metadata.ts
│   │   │
│   │   ├── routes/          # Route definitions
│   │   ├── policies/        # Access control policies
│   │   ├── middlewares/     # Request middlewares
│   │   └── validation/      # Input validation
│
└── shared/                   # Shared utilities
```

---

## Core Features

### 1. Document Management

**CRUD Operations** for all content types with support for:
- ✅ Create, Read, Update, Delete documents
- ✅ Draft and Published states
- ✅ Localization (i18n)
- ✅ Version history
- ✅ Bulk operations
- ✅ Cloning/Duplication

### 2. Permission System

**Role-Based Access Control (RBAC)**:
- Field-level permissions
- Conditional permissions based on content
- Integration with Strapi's admin permissions
- User-specific content ownership

### 3. Localization Support

**Multi-language content**:
- Create content in multiple locales
- Locale-specific drafts and published versions
- Fallback locale support
- Locale switching

### 4. Draft/Publish Workflow

**Content lifecycle management**:
- Draft state for work-in-progress content
- Publish/unpublish operations
- Scheduled publishing (via external plugins)
- Auto-save drafts

### 5. Relations Management

**Handle complex relationships**:
- One-to-One, One-to-Many, Many-to-Many
- Bi-directional relations
- Polymorphic relations
- Relation counting and pagination

### 6. Component System

**Reusable content blocks**:
- Dynamic zones
- Repeatable components
- Nested components
- Component validation

---

## Key Services

### Document Manager Service

**Purpose**: Core service for document operations

**Key Methods**:
```typescript
documentManager.findOne(id, uid, opts)      // Find single document
documentManager.findMany(opts, uid)         // Find multiple documents
documentManager.findPage(opts, uid)         // Paginated results
documentManager.create(uid, opts)           // Create new document
documentManager.update(id, uid, opts)       // Update document
documentManager.delete(id, uid, opts)       // Delete document
documentManager.clone(id, body, uid)        // Clone/duplicate document
documentManager.publish(id, uid, opts)      // Publish document
documentManager.unpublish(id, uid, opts)    // Unpublish document
```

**Features**:
- Automatic population of relations
- Draft/publish state management
- Locale handling
- Permission integration
- Transaction support

### Permission Checker Service

**Purpose**: Validate user permissions for content operations

**Key Methods**:
```typescript
permissionChecker.cannot.create()           // Check create permission
permissionChecker.cannot.update(doc)        // Check update permission
permissionChecker.cannot.delete(doc)        // Check delete permission
permissionChecker.sanitizeCreateInput       // Sanitize create data
permissionChecker.sanitizeUpdateInput(doc)  // Sanitize update data
permissionChecker.sanitizedQuery.read       // Sanitize read query
```

**Features**:
- Field-level filtering
- Conditional permissions
- Query sanitization
- Input validation

### Populate Builder Service

**Purpose**: Build complex populate queries for relations

**Key Methods**:
```typescript
populateBuilder(uid)
  .populateFromQuery(query)    // Parse query params
  .populateDeep(depth)          // Deep population
  .countRelations(opts)         // Count related items
  .build()                      // Build final populate object
```

**Features**:
- Configurable depth
- Relation counting
- Query optimization
- Circular reference handling

---

## Technology Stack

| Component | Technology |
|-----------|-----------|
| **Backend** | Node.js, TypeScript, Koa |
| **Frontend** | React, TypeScript, Styled Components |
| **Database** | Strapi Document Service (abstraction) |
| **Validation** | Yup schemas |
| **Testing** | Jest, React Testing Library |
| **API Spec** | OpenAPI 3.0.2 |

---

## Design Patterns

### 1. Service Layer Pattern
- Business logic encapsulated in services
- Controllers remain thin (request/response handling only)
- Services are reusable across different entry points

### 2. Repository Pattern
- Document Manager abstracts database operations
- Consistent interface across different content types
- Decoupled from specific database implementation

### 3. Decorator Pattern
- Permission Checker wraps operations with permission checks
- Populate Builder enhances queries with relation population
- Metadata service adds additional information to responses

### 4. Factory Pattern
- Permission Checker created per request with context
- Populate Builder instantiated per content type
- Service factories for dependency injection

### 5. Strategy Pattern
- Different strategies for collection types vs single types
- Locale handling strategies
- Publishing strategies (draft/publish)

---

## Integration Points

### Strapi Core Integration

```typescript
// Document Service
strapi.documents(uid).findOne()
strapi.documents(uid).create()
strapi.documents(uid).update()

// Database Query
strapi.db.query(uid).findMany()
strapi.db.query(uid).count()

// Event System
strapi.eventHub.emit('entry.create')
strapi.eventHub.on('entry.update')

// Plugin API
strapi.plugin('content-manager').service('document-manager')
strapi.plugin('content-manager').controller('collection-types')
```

### Plugin Extension Points

```typescript
// Extend controllers
strapi.plugin('content-manager').controller('collection-types').find = async (ctx) => {
  // Custom implementation
};

// Extend services
strapi.plugin('content-manager').service('document-manager').create = async (...args) => {
  // Custom implementation
};

// Add middlewares
strapi.plugin('content-manager').routes.admin.routes.push({
  method: 'GET',
  path: '/custom-endpoint',
  handler: 'custom.handler',
  config: {
    middlewares: ['custom-middleware'],
  },
});
```

---

## Performance Considerations

### Query Optimization

1. **Deep Populate Limits**
   - Default depth: 1 level
   - Configurable via populate builder
   - Prevents N+1 query problems

2. **Pagination**
   - Default page size: 10
   - Max limit: 1000
   - Cursor-based pagination support

3. **Relation Counting**
   - Efficient count queries
   - Cached counts where possible
   - Selective counting (only when needed)

### Caching Strategy

- No built-in caching (delegated to Strapi core)
- Populate configurations cached
- Permission checks cached per request
- Metadata calculations cached

### Database Indexes

Content Manager relies on Strapi's automatic index creation:
- `documentId` indexed
- `locale` indexed
- `publishedAt` indexed
- Composite indexes for common queries

---

## Security Features

### 1. Permission Validation
- All operations require permission checks
- Field-level access control
- Conditional permissions based on content

### 2. Input Sanitization
- Yup schema validation
- XSS prevention
- SQL injection prevention (via query builder)

### 3. CSRF Protection
- Token-based CSRF protection
- SameSite cookie policies

### 4. Rate Limiting
- Configurable rate limits
- Per-user and per-IP limits

### 5. Audit Logging
- Creator/updater tracking
- Timestamp tracking
- Event logging via Event Hub

---

## Error Handling

### Standard Error Types

```typescript
// Forbidden (403)
throw new errors.ForbiddenError();

// Not Found (404)
throw new errors.NotFoundError();

// Validation Error (400)
throw new errors.ValidationError('Invalid input');

// Application Error (500)
throw new errors.ApplicationError('Something went wrong');
```

### Error Response Format

```json
{
  "error": {
    "status": 400,
    "name": "ValidationError",
    "message": "Invalid input",
    "details": {
      "field": "title",
      "errors": ["Title is required"]
    }
  }
}
```

---

## Monitoring & Metrics

### Key Metrics to Track

1. **Performance Metrics**
   - Response time (p50, p95, p99)
   - Database query time
   - Population depth impact

2. **Usage Metrics**
   - Requests per endpoint
   - Documents created/updated/deleted
   - Bulk operation usage

3. **Error Metrics**
   - Error rate by type
   - Permission denied rate
   - Validation failure rate

### Health Check Endpoint

```bash
GET /api/_health
```

Returns service health status and basic metrics.

---

## Migration Guide

### From v4 to v5

**Breaking Changes**:

1. **Entity Service → Document Service**
   ```typescript
   // v4
   strapi.entityService.findOne()
   
   // v5
   strapi.documents(uid).findOne()
   ```

2. **Draft/Publish Changes**
   - All new content starts as draft
   - Explicit publish operation required
   - `publishedAt` managed automatically

3. **Locale Handling**
   - Locale must be specified for i18n content types
   - Default locale used if not specified

**Migration Steps**:

1. Update service calls to use Document Service
2. Add explicit publish operations if needed
3. Review locale handling in code
4. Update tests to account for draft state
5. Verify permissions still work as expected

---

## Troubleshooting

### Common Issues

**1. Permission Denied Errors**
```
Solution: Check user roles and permissions in Admin Panel
→ Settings → Users & Permissions → Roles
```

**2. Relation Not Populated**
```
Solution: Use populate parameter or populate builder
→ Add ?populate=* to query or use populateDeep()
```

**3. Locale Not Found**
```
Solution: Ensure locale exists and is enabled
→ Settings → Internationalization
```

**4. Draft Not Saving**
```
Solution: Check if draft/publish is enabled for content type
→ Content-Type Builder → Advanced Settings
```

**5. Bulk Operations Failing**
```
Solution: Check bulk operation limits and permissions
→ Reduce batch size or check individual permissions
```

---

## Additional Resources

- **API Documentation**: [OpenAPI Spec](./01-api-reference.md)
- **User Guide**: [User Guide](./02-user-guide.md)
- **Developer Guide**: [Developer Guide](./03-developer-guide.md)
- **Architecture Diagrams**: [Architecture](./04-architecture.md)
- **Official Docs**: https://docs.strapi.io

---

**Last Updated**: December 12, 2025  
**Version**: 5.31.3  
**Maintained By**: Strapi Core Team
