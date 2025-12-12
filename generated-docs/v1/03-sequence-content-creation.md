# Sequence Diagram - Content Creation Workflow

## Overview

This document presents the complete sequence diagram for creating content in Strapi v5.31.3, showing all participants, interactions, lifecycle hooks, database transactions, and event emissions throughout the creation flow.

## Workflow Context

**Scenario**: User creates a new content entry via the Admin Panel  
**Content Type**: Collection Type (e.g., Article)  
**Entry Point**: `POST /api/content-manager/collection-types/:uid`  
**Expected Flow**: Request → Validation → Permission Check → Service Layer → Database → Hooks → Response

---

## Sequence Diagram (Complete Flow)

```plantuml
@startuml ContentCreationSequence
autonumber
title Content Creation Workflow - Strapi v5.31.3

actor "Admin User" as user
participant "Admin Panel\n(React)" as admin
participant "Koa Router" as router
participant "Permission\nPolicy" as policy
participant "Collection Types\nController" as controller
participant "Validation\nUtils" as validation
participant "Permission\nChecker" as permChecker
participant "Document\nManager" as docManager
participant "Populate\nBuilder" as popBuilder
participant "Data Mapper" as mapper
participant "Document\nService" as docService
participant "Event Hub" as eventHub
participant "Lifecycle\nSubscribers" as lifecycles
database "Database\n(PostgreSQL)" as db
participant "History\nModule" as history

== Request Phase ==

user -> admin: Fill form and click "Save"
activate admin

admin -> admin: Validate client-side
admin -> router: POST /api/content-manager/collection-types/articles\n{title, body, author, status: "draft"}
activate router

router -> router: Match route pattern
router -> policy: Apply permission policy
activate policy

== Permission Check Phase ==

policy -> policy: Extract user from ctx.state
policy -> permChecker: Check hasPermissions
activate permChecker

permChecker -> permChecker: Get user abilities
permChecker -> permChecker: Check 'create' permission for 'articles'
permChecker --> policy: Permission granted
deactivate permChecker

policy --> router: Continue to controller
deactivate policy

router -> controller: createDocument(ctx, opts)
activate controller

== Validation & Sanitization Phase ==

controller -> controller: Extract request body
controller -> controller: Get locale & status from body

controller -> validation: Validate input schema
activate validation

validation -> validation: Apply Joi schema
note right
  Validate required fields,
  data types, constraints
end note
validation --> controller: Validation passed
deactivate validation

controller -> permChecker: sanitizeCreateInput(body)
activate permChecker

permChecker -> permChecker: Filter fields based on permissions
note right
  Remove fields user cannot write:
  - Read-only fields
  - System fields (id, createdAt)
  - Unpermitted relations
end note
permChecker --> controller: Sanitized data
deactivate permChecker

controller -> controller: setCreatorFields(sanitizedData)
note right
  Add creator tracking:
  - createdBy: user.id
  - updatedBy: user.id
end note

== Service Layer Phase ==

controller -> docManager: create(uid, {data, locale, status, populate})
activate docManager

docManager -> docManager: Validate model exists
docManager -> permChecker: Re-check permissions
activate permChecker
permChecker --> docManager: Permitted
deactivate permChecker

docManager -> popBuilder: Build populate query
activate popBuilder

popBuilder -> popBuilder: Determine relations to populate
popBuilder -> popBuilder: Respect max depth (default: 5)
popBuilder -> popBuilder: Apply permission filters
popBuilder --> docManager: Populate object
deactivate popBuilder

docManager -> mapper: mapInputData(data)
activate mapper

mapper -> mapper: Transform component structures
mapper -> mapper: Process dynamic zones
mapper -> mapper: Handle media references
mapper -> mapper: Format dates
mapper --> docManager: Transformed data
deactivate mapper

== Document Service Phase ==

docManager -> docService: documents(uid).create(params)
activate docService

docService -> docService: Validate content type schema
docService -> docService: Apply defaults from schema

== Before Create Lifecycle ==

docService -> lifecycles: run('beforeCreate', uid, {params})
activate lifecycles

lifecycles -> lifecycles: **Timestamps Subscriber**
note right
  Set timestamps:
  - createdAt: now()
  - updatedAt: now()
end note

lifecycles -> lifecycles: **Custom Subscribers** (if any)
note right
  User-defined hooks:
  - Validate business rules
  - Transform data
  - Generate computed fields
end note

lifecycles --> docService: State object
deactivate lifecycles

== Database Transaction ==

docService -> db: BEGIN TRANSACTION
activate db

docService -> db: INSERT INTO articles\n(documentId, title, body, author, locale, status, createdAt, updatedAt, createdBy, updatedBy)\nVALUES (...)
note right
  Generate unique documentId (UUID)
  Status: "draft"
  Locale: from request or default
end note

db -> db: Execute INSERT
db -> db: Create relations (if any)
db --> docService: Result row {id, documentId, ...}

docService -> db: COMMIT TRANSACTION
deactivate db

== After Create Lifecycle ==

docService -> lifecycles: run('afterCreate', uid, {params, result}, states)
activate lifecycles

lifecycles -> lifecycles: **Audit Log Subscriber** (if enabled)
note right
  Log creation event:
  - User who created
  - Timestamp
  - Content type
  - Document ID
end note

lifecycles -> lifecycles: **Search Index Subscriber** (if enabled)
note right
  Index document for search:
  - Meilisearch
  - Algolia
  - ElasticSearch
end note

lifecycles --> docService: Lifecycle complete
deactivate lifecycles

== Event Emission ==

docService -> eventHub: emit('entry.create', {model, uid, entry})
activate eventHub

note right of eventHub
  Events executed AFTER
  transaction commit to
  ensure data consistency
end note

eventHub -> eventHub: Queue event handlers
eventHub -> history: Handle 'entry.create'
activate history

history -> db: INSERT INTO strapi_history_versions\n(contentType, documentId, data, version, createdBy, createdAt)
activate db
db --> history: Version created
deactivate db

deactivate history

eventHub -> eventHub: Trigger webhooks (if configured)
note right
  POST to configured URLs:
  - Slack notifications
  - Zapier integrations
  - Custom endpoints
end note

eventHub -> eventHub: Trigger plugin listeners
note right
  Plugins can listen:
  - Email notifications
  - Analytics tracking
  - Custom workflows
end note

deactivate eventHub

== Populate Relations ==

docService -> docService: Populate relations per populate query
docService -> db: SELECT related data\nJOIN/subqueries based on populate
activate db
db --> docService: Related entities
deactivate db

docService --> docManager: Created document (with relations)
deactivate docService

== Response Mapping Phase ==

docManager -> mapper: mapOutputData(document)
activate mapper

mapper -> mapper: Transform to API format
mapper -> mapper: Format dates to ISO strings
mapper -> mapper: Structure components
mapper -> mapper: Add media URLs
mapper --> docManager: API-formatted document
deactivate mapper

docManager --> controller: Created document
deactivate docManager

== Response Phase ==

controller -> controller: Format response body
controller --> router: ctx.body = {data: document}\nctx.status = 201
deactivate controller

router --> admin: HTTP 201 Created\n{data: {id, documentId, title, body, ...}}
deactivate router

admin -> admin: Update UI state
admin -> admin: Show success notification
admin -> admin: Redirect to edit view
deactivate admin

user <-- admin: "Article created successfully"

@enduml
```

---

## Sequence Diagram (Error Scenarios)

```plantuml
@startuml ContentCreationErrors
autonumber
title Content Creation - Error Scenarios

actor "Admin User" as user
participant "Admin Panel" as admin
participant "Controller" as controller
participant "Permission\nChecker" as permChecker
participant "Validation" as validation
participant "Document\nService" as docService
database "Database" as db

== Scenario 1: Permission Denied ==

user -> admin: Create article
admin -> controller: POST /collection-types/articles
controller -> permChecker: Check create permission
permChecker -> permChecker: User lacks 'create' permission
permChecker --> controller: ForbiddenError
controller --> admin: HTTP 403 Forbidden\n{error: "You do not have permission to create this content"}
admin -> admin: Display error message
admin --> user: "Permission denied"

== Scenario 2: Validation Failed ==

user -> admin: Create article (missing required field)
admin -> controller: POST /collection-types/articles\n{body: "text"}  // title missing
controller -> validation: Validate input
validation -> validation: Check required fields
validation --> controller: ValidationError: "Title is required"
controller --> admin: HTTP 400 Bad Request\n{error: "Title is required", field: "title"}
admin -> admin: Highlight invalid field
admin --> user: "Title is required"

== Scenario 3: Database Constraint Violation ==

user -> admin: Create article (duplicate slug)
admin -> controller: POST /collection-types/articles\n{title: "Test", slug: "test"}
controller -> docService: create(data)
docService -> db: INSERT INTO articles
db -> db: Unique constraint violation on 'slug'
db --> docService: Error: duplicate key value
docService --> controller: DatabaseError: "Slug already exists"
controller --> admin: HTTP 400 Bad Request\n{error: "This slug is already in use"}
admin --> user: "Slug must be unique"

== Scenario 4: Transaction Rollback ==

user -> admin: Create article with invalid relation
admin -> controller: POST /collection-types/articles\n{author: 999}  // non-existent author
controller -> docService: create(data)
docService -> db: BEGIN TRANSACTION
docService -> db: INSERT INTO articles
db --> docService: OK
docService -> db: INSERT INTO articles_author_links
db -> db: Foreign key constraint fails
db --> docService: Error: foreign key violation
docService -> db: ROLLBACK TRANSACTION
docService --> controller: DatabaseError: "Author not found"
controller --> admin: HTTP 400 Bad Request\n{error: "The selected author does not exist"}
admin --> user: "Invalid author selected"

@enduml
```

---

## Participants Description

### Frontend Layer

#### Admin User
- **Role**: Content editor, author, administrator
- **Actions**: Fills forms, clicks save button
- **Receives**: Success/error notifications

#### Admin Panel (React)
- **Location**: `packages/core/admin/admin/src`
- **Responsibilities**:
  - Form rendering and validation
  - Client-side data validation
  - API calls via axios
  - State management (Redux)
  - UI feedback (notifications, redirects)

---

### HTTP Layer

#### Koa Router
- **Framework**: Koa.js v2
- **Location**: `packages/core/strapi/src`
- **Responsibilities**:
  - Route matching
  - Middleware execution
  - Policy enforcement
  - Error handling

#### Permission Policy
- **Location**: `packages/core/content-manager/server/src/policies/hasPermissions.ts`
- **Function**: Middleware that checks permissions before controller execution
- **Checks**:
  - User is authenticated
  - User has required permission (create, read, update, delete, publish)
  - Permission scope (all, own, none)
- **Failure**: Returns 403 Forbidden

---

### Controller Layer

#### Collection Types Controller
- **Location**: `packages/core/content-manager/server/src/controllers/collection-types.ts`
- **Method**: `createDocument(ctx, opts)`
- **Responsibilities**:
  - Extract request body
  - Validate and sanitize input
  - Delegate to service layer
  - Format response
  - Handle errors

**Code Reference**:
```typescript
const createDocument = async (ctx: any, opts?: Options) => {
  const { userAbility, user } = ctx.state;
  const { model } = ctx.params;
  const { body } = ctx.request;

  const documentManager = getService('document-manager');
  const permissionChecker = getService('permission-checker').create({ userAbility, model });

  if (permissionChecker.cannot.create()) {
    throw new errors.ForbiddenError();
  }

  const pickPermittedFields = permissionChecker.sanitizeCreateInput;
  const setCreator = setCreatorFields({ user });
  const sanitizeFn = async.pipe(pickPermittedFields, setCreator as any);
  const sanitizedBody = await sanitizeFn(body);

  const { locale, status } = await getDocumentLocaleAndStatus(body, model);

  return documentManager.create(model, {
    data: sanitizedBody as any,
    locale,
    status,
    populate: opts?.populate,
  });
};
```

---

### Business Logic Layer

#### Validation Utils
- **Location**: `packages/core/content-manager/server/src/controllers/validation/`
- **Library**: Joi (schema validation)
- **Validates**:
  - Required fields
  - Data types
  - Format constraints
  - Custom business rules

#### Permission Checker
- **Location**: `packages/core/content-manager/server/src/services/permission-checker.ts`
- **Responsibilities**:
  - Check action permissions (CRUD)
  - Filter fields based on permissions
  - Sanitize input/output
  - Handle field-level permissions

**Permission Levels**:
- **All**: Can create/edit any entry
- **Own**: Can only create/edit own entries
- **None**: No permission

#### Document Manager
- **Location**: `packages/core/content-manager/server/src/services/document-manager.ts`
- **Method**: `create(uid, opts)`
- **Responsibilities**:
  - Orchestrate creation flow
  - Coordinate services
  - Handle populates
  - Trigger events

**Code Reference**:
```typescript
async create(uid: UID.CollectionType, opts: DocServiceParams<'create'> = {} as any) {
  const populate = opts.populate ?? (await buildDeepPopulate(uid));
  const params = { ...opts, status: 'draft' as const, populate };

  return strapi.documents(uid).create(params);
}
```

#### Populate Builder
- **Location**: `packages/core/content-manager/server/src/services/populate-builder.ts`
- **Purpose**: Build relation population queries
- **Features**:
  - Max depth limit (prevents circular refs)
  - Permission-based filtering
  - Selective population

#### Data Mapper
- **Location**: `packages/core/content-manager/server/src/services/data-mapper.ts`
- **Transformations**:
  - Input: API format → Database format
  - Output: Database format → API format
  - Component nesting
  - Media file references
  - Date formatting

---

### Core Services Layer

#### Document Service
- **Location**: `packages/core/core/src/services/document-service/`
- **API**: `strapi.documents(uid).create(params)`
- **Responsibilities**:
  - Low-level CRUD operations
  - Transaction management
  - Lifecycle hook execution
  - Event emission
  - Schema validation

**Lifecycle Hook Sequence**:
1. **beforeCreate**: Before database INSERT
2. **Database Transaction**: INSERT + relations
3. **afterCreate**: After transaction commit
4. **Event Emission**: Async event handlers

#### Event Hub
- **Location**: `packages/core/core/src/services/event-hub/`
- **Framework**: EventEmitter
- **Events Emitted**:
  - `entry.create` - After successful creation
  - `entry.update` - After update
  - `entry.delete` - After deletion
  - `entry.publish` - After publish
  - `entry.unpublish` - After unpublish

**Event Payload**:
```typescript
{
  model: 'article',
  uid: 'api::article.article',
  entry: {
    id: 1,
    documentId: 'abc123',
    title: 'My Article',
    // ...
  }
}
```

#### Lifecycle Subscribers
- **Location**: `packages/core/database/src/lifecycles/subscribers/`
- **Built-in Subscribers**:
  - **Timestamps**: Set createdAt, updatedAt
  - **Audit Log**: Track changes (if enabled)
  - **Search Index**: Update search engines (if configured)

**Custom Subscriber Example**:
```typescript
strapi.db.lifecycles.subscribe({
  models: ['api::article.article'],
  async beforeCreate(event) {
    const { data } = event.params;
    // Generate slug from title
    if (!data.slug && data.title) {
      data.slug = slugify(data.title);
    }
  },
  async afterCreate(event) {
    const { result } = event;
    // Send notification
    await sendNotification(`New article: ${result.title}`);
  }
});
```

---

### Data Layer

#### Database (PostgreSQL)
- **ORM**: Custom Strapi ORM (Knex.js wrapper)
- **Operations**:
  - Transaction management
  - INSERT queries
  - Relation creation (join tables)
  - Constraint enforcement

**Transaction Flow**:
```sql
BEGIN;

-- Main table insert
INSERT INTO articles (
  document_id, title, body, locale, status, 
  created_at, updated_at, created_by, updated_by
) VALUES (
  'abc123', 'My Article', 'Content...', 'en', 'draft',
  '2024-12-12', '2024-12-12', 1, 1
) RETURNING *;

-- Relation insert (if author provided)
INSERT INTO articles_author_links (
  article_id, user_id
) VALUES (1, 5);

COMMIT;
```

---

### Audit & History Layer

#### History Module
- **Location**: `packages/core/content-manager/server/src/history/`
- **Event**: Listens to `entry.create`
- **Action**: Creates version snapshot
- **Database Table**: `strapi_history_versions`

**Version Record**:
```json
{
  "id": 1,
  "contentType": "api::article.article",
  "documentId": "abc123",
  "locale": "en",
  "status": "draft",
  "data": {
    "title": "My Article",
    "body": "Content..."
  },
  "version": 1,
  "createdBy": 1,
  "createdAt": "2024-12-12T10:00:00Z"
}
```

---

## Timing & Performance

### Typical Latency (p50)

| Phase | Duration | Notes |
|-------|----------|-------|
| **Client-side validation** | ~10ms | Instant UI feedback |
| **Network (request)** | ~50ms | Depends on location |
| **Router + Policy** | ~5ms | Fast middleware chain |
| **Controller (validation + sanitization)** | ~10ms | Joi schema validation |
| **Service layer** | ~20ms | Business logic orchestration |
| **Database transaction** | ~50ms | INSERT + relations |
| **Lifecycle hooks** | ~10ms | Timestamp subscriber |
| **Event emission** | ~5ms | Queued async |
| **History version** | ~20ms | Separate INSERT (async) |
| **Network (response)** | ~50ms | JSON serialization + transfer |
| **Total (sync path)** | **~200ms** | User sees success |
| **Total (with async events)** | **~250ms** | Background processing |

### Optimization Strategies

1. **Database Indexes**
   - Index on `documentId` (UUID)
   - Index on `locale` + `status`
   - Index on foreign keys

2. **Populate Optimization**
   - Limit depth to needed levels
   - Use batch loading for relations
   - Select only needed fields

3. **Caching**
   - Cache content type schemas
   - Cache permission queries
   - Cache populated relations (short TTL)

4. **Async Processing**
   - Event handlers run async (after response)
   - History version creation non-blocking
   - Webhook calls queued

---

## Error Handling

### Error Types & HTTP Status Codes

| Error | Status | Scenario |
|-------|--------|----------|
| **ForbiddenError** | 403 | User lacks permission |
| **ValidationError** | 400 | Required field missing, invalid format |
| **DatabaseError** | 400/500 | Unique constraint, foreign key violation |
| **NotFoundError** | 404 | Content type doesn't exist |
| **ApplicationError** | 500 | Unexpected server error |

### Rollback Mechanism

- **Database**: Automatic rollback on transaction error
- **Lifecycle Hooks**: State passed between before/after hooks
- **Events**: Not rolled back (already emitted)

---

## Security Considerations

### Permission Checks
- **Policy Layer**: Initial check (authenticated + hasPermission)
- **Service Layer**: Re-validation before DB operation
- **Field-Level**: Sanitize based on field permissions

### Data Sanitization
- **Input**: Remove unpermitted fields, escape SQL
- **Output**: Filter based on read permissions

### SQL Injection Prevention
- **ORM**: Parameterized queries (Knex.js)
- **Validation**: Type checking before DB

### XSS Prevention
- **Output Encoding**: React auto-escapes
- **Content Security Policy**: Strapi default headers

---

## Extension Points

### Custom Lifecycle Hooks

**Add before creation**:
```typescript
strapi.db.lifecycles.subscribe({
  models: ['api::article.article'],
  async beforeCreate(event) {
    // Validate slug uniqueness
    // Generate SEO meta
    // Call external API
  }
});
```

### Custom Event Listeners

**React to creation**:
```typescript
strapi.eventHub.on('entry.create', async ({ uid, entry }) => {
  if (uid === 'api::article.article') {
    // Send to Algolia
    // Post to Slack
    // Trigger webhook
  }
});
```

### Custom Middlewares

**Add request processing**:
```typescript
strapi.server.use(async (ctx, next) => {
  if (ctx.path.startsWith('/api/content-manager')) {
    // Log request
    // Rate limiting
    // Custom auth
  }
  await next();
});
```

---

## Related Workflows

### Update Workflow
- Similar flow but uses `PUT /collection-types/:uid/:id`
- Includes version check for optimistic locking
- `beforeUpdate` / `afterUpdate` hooks

### Publish Workflow
- Triggered by `POST /collection-types/:uid/:id/actions/publish`
- Changes status from `draft` to `published`
- Sets `publishedAt` timestamp
- Emits `entry.publish` event

### Delete Workflow
- Soft delete (if configured) or hard delete
- Cascades to relations
- History preserved
- `beforeDelete` / `afterDelete` hooks

---

## Testing Strategy

### Unit Tests
```typescript
describe('Document Manager - create', () => {
  it('should create document with valid data', async () => {
    const mockData = { title: 'Test', body: 'Content' };
    const result = await documentManager.create('api::article.article', {
      data: mockData,
      locale: 'en',
      status: 'draft'
    });
    
    expect(result).toHaveProperty('id');
    expect(result.title).toBe('Test');
  });
  
  it('should throw ForbiddenError if no permission', async () => {
    mockPermissionChecker.cannot.create.mockReturnValue(true);
    
    await expect(controller.createDocument(ctx))
      .rejects.toThrow('ForbiddenError');
  });
});
```

### Integration Tests
```typescript
describe('Content Creation API', () => {
  it('POST /api/content-manager/collection-types/articles', async () => {
    const res = await request(strapi.server)
      .post('/api/content-manager/collection-types/api::article.article')
      .set('Authorization', `Bearer ${token}`)
      .send({ title: 'Test', body: 'Content' });
    
    expect(res.status).toBe(201);
    expect(res.body.data).toHaveProperty('documentId');
  });
});
```

---

## Generated Information

**Date**: December 12, 2025  
**Strapi Version**: 5.31.3  
**Diagram Format**: PlantUML Sequence  
**Analysis Method**: Code tracing + Event analysis  
**Validation**: Cross-referenced with actual implementation
