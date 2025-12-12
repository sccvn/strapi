# C4 Component Diagram - Content Manager Plugin

## Overview

This document presents the C4 Component diagram for Strapi's Content Manager plugin, showing all major components, their dependencies, interactions, and responsibilities.

## Plugin Context

The Content Manager is a **core plugin** in Strapi that provides the primary interface and API for managing content across all content types. It's responsible for CRUD operations, validation, permissions, and content configuration.

**Location**: `packages/core/content-manager`  
**Type**: Core Plugin  
**Dependencies**: Core Services (Document Service, Permissions, Event Hub)

---

## C4 Component Diagram

```plantuml
@startuml C4_Component_ContentManager
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

LAYOUT_TOP_DOWN()

title Component Diagram - Content Manager Plugin (Strapi v5.31.3)

Container_Boundary(admin_ui, "Admin Panel") {
    Component_Ext(admin_content_list, "Content List View", "React", "Displays content entries")
    Component_Ext(admin_edit_view, "Edit View", "React", "Content editing interface")
    Component_Ext(admin_config, "Configuration UI", "React", "Content type settings")
}

Container_Boundary(content_manager, "Content Manager Plugin") {
    
    ' Controllers Layer
    Component(collection_controller, "Collection Types Controller", "TypeScript", "Handles requests for collection type CRUD operations")
    Component(single_controller, "Single Types Controller", "TypeScript", "Handles requests for single type operations")
    Component(relations_controller, "Relations Controller", "TypeScript", "Manages content relations and associations")
    Component(uid_controller, "UID Controller", "TypeScript", "Generates and validates unique identifiers")
    Component(components_controller, "Components Controller", "TypeScript", "Manages reusable component data")
    Component(init_controller, "Init Controller", "TypeScript", "Initializes content manager data")
    Component(content_types_controller, "Content Types Controller", "TypeScript", "Manages content type configurations")
    
    ' Services Layer
    Component(document_manager, "Document Manager Service", "TypeScript", "Central service for document CRUD operations")
    Component(permission_checker, "Permission Checker Service", "TypeScript", "Validates user permissions for operations")
    Component(populate_builder, "Populate Builder Service", "TypeScript", "Builds relation population queries")
    Component(configuration_service, "Configuration Service", "TypeScript", "Manages content type configurations")
    Component(data_mapper, "Data Mapper Service", "TypeScript", "Maps and transforms document data")
    Component(uid_service, "UID Service", "TypeScript", "Generates unique identifiers")
    Component(field_sizes_service, "Field Sizes Service", "TypeScript", "Calculates field storage sizes")
    Component(metrics_service, "Metrics Service", "TypeScript", "Tracks content metrics")
    Component(document_metadata, "Document Metadata Service", "TypeScript", "Manages document metadata")
    Component(permission_service, "Permission Service", "TypeScript", "Permission management and queries")
    Component(content_types_service, "Content Types Service", "TypeScript", "Content type schema operations")
    Component(components_service, "Components Service", "TypeScript", "Component schema management")
    
    ' Sub-modules
    Component(history_module, "History Module", "TypeScript", "Content version history and audit trail")
    Component(preview_module, "Preview Module", "TypeScript", "Content preview functionality")
    Component(homepage_module, "Homepage Module", "TypeScript", "Homepage customization")
    
    ' Middleware & Policies
    Component(routing_middleware, "Routing Middleware", "Koa", "Routes requests to appropriate controllers")
    Component(permission_policy, "Permission Policy", "Koa", "Enforces permission checks on routes")
    
    ' Utilities
    Component(validation_utils, "Validation Utils", "TypeScript", "Input validation schemas")
    Component(configuration_utils, "Configuration Utils", "TypeScript", "Layout, metadata, and settings")
    Component(populate_utils, "Populate Utils", "TypeScript", "Relation population helpers")
    Component(clone_utils, "Clone Utils", "TypeScript", "Document cloning utilities")
}

Container_Boundary(core_services, "Strapi Core Services") {
    Component_Ext(document_service, "Document Service", "TypeScript", "Low-level document operations")
    Component_Ext(entity_service, "Entity Service (Legacy)", "TypeScript", "Legacy content API")
    Component_Ext(permission_engine, "Permission Engine", "RBAC", "Role-based access control")
    Component_Ext(event_hub, "Event Hub", "EventEmitter", "Lifecycle hooks and events")
    Component_Ext(schema_service, "Schema Service", "TypeScript", "Content type schema management")
}

ContainerDb_Ext(database, "Database", "PostgreSQL/MySQL", "Content storage")

' Admin UI to Controllers
Rel(admin_content_list, collection_controller, "GET /collection-types/:uid", "REST API")
Rel(admin_edit_view, collection_controller, "POST/PUT /collection-types/:uid", "REST API")
Rel(admin_edit_view, single_controller, "GET/PUT /single-types/:uid", "REST API")
Rel(admin_edit_view, relations_controller, "GET /relations/:model/:id", "REST API")
Rel(admin_edit_view, uid_controller, "POST /uid/generate", "REST API")
Rel(admin_config, content_types_controller, "GET/PUT /content-types/:uid/configuration", "REST API")

' Middleware & Policy Layer
Rel(collection_controller, routing_middleware, "Routes through", "Middleware chain")
Rel(single_controller, routing_middleware, "Routes through", "Middleware chain")
Rel(routing_middleware, permission_policy, "Enforces", "Policy check")

' Controllers to Services
Rel(collection_controller, document_manager, "Delegates to", "Service call")
Rel(single_controller, document_manager, "Delegates to", "Service call")
Rel(relations_controller, document_manager, "Queries relations", "Service call")
Rel(uid_controller, uid_service, "Generates UID", "Service call")
Rel(components_controller, components_service, "Manages components", "Service call")
Rel(content_types_controller, configuration_service, "Gets/Sets config", "Service call")

' Service Layer Interactions
Rel(document_manager, permission_checker, "Checks permissions", "Before operations")
Rel(document_manager, populate_builder, "Builds populate", "For relations")
Rel(document_manager, data_mapper, "Transforms data", "Input/Output")
Rel(document_manager, document_metadata, "Manages metadata", "Document info")

Rel(permission_checker, permission_service, "Queries permissions", "User access")
Rel(permission_checker, permission_engine, "Validates", "RBAC check")

Rel(populate_builder, populate_utils, "Uses", "Helper functions")
Rel(configuration_service, configuration_utils, "Uses", "Layout/metadata")
Rel(document_manager, validation_utils, "Validates input", "Schema validation")

Rel(collection_controller, clone_utils, "Clones documents", "Duplication")

' Sub-modules
Rel(collection_controller, history_module, "Tracks versions", "History API")
Rel(single_controller, history_module, "Tracks changes", "Audit trail")
Rel(collection_controller, preview_module, "Generates preview", "Preview API")
Rel(content_types_controller, homepage_module, "Customizes", "Homepage data")

' Services to Core
Rel(document_manager, document_service, "CRUD operations", "Core API")
Rel(document_manager, event_hub, "Emits events", "Lifecycle hooks")
Rel(permission_service, permission_engine, "Queries", "Permission data")
Rel(configuration_service, schema_service, "Reads schema", "Content type structure")
Rel(content_types_service, schema_service, "Updates schema", "Schema modifications")

' Core to Database
Rel(document_service, database, "Reads/Writes", "SQL queries")

SHOW_LEGEND()

@enduml
```

---

## Component Descriptions

### Controllers Layer

Controllers handle HTTP requests and delegate business logic to services. They follow the thin controller, fat service pattern.

#### Collection Types Controller
- **File**: `server/src/controllers/collection-types.ts`
- **Responsibilities**:
  - Find all entries for a collection type
  - Find one entry by ID
  - Create new entry
  - Update existing entry
  - Delete entry
  - Publish/unpublish draft entries
  - Clone entries
- **Endpoints**: `/api/content-manager/collection-types/:uid`
- **HTTP Methods**: GET, POST, PUT, DELETE

#### Single Types Controller
- **File**: `server/src/controllers/single-types.ts`
- **Responsibilities**:
  - Get single type entry
  - Update single type entry
  - Publish/unpublish single type
- **Endpoints**: `/api/content-manager/single-types/:uid`
- **HTTP Methods**: GET, PUT

#### Relations Controller
- **File**: `server/src/controllers/relations.ts`
- **Responsibilities**:
  - Find related entries
  - Count related entries
  - Navigate relations (oneToOne, oneToMany, manyToMany)
- **Endpoints**: `/api/content-manager/relations/:model/:id/:field`
- **HTTP Methods**: GET

#### UID Controller
- **File**: `server/src/controllers/uid.ts`
- **Responsibilities**:
  - Generate unique slug/UID from text
  - Validate UID availability
  - Handle UID conflicts
- **Endpoints**: `/api/content-manager/uid/generate`
- **HTTP Methods**: POST

#### Components Controller
- **File**: `server/src/controllers/components.ts`
- **Responsibilities**:
  - List all components
  - Get component configuration
- **Endpoints**: `/api/content-manager/components`
- **HTTP Methods**: GET

#### Content Types Controller
- **File**: `server/src/controllers/content-types.ts`
- **Responsibilities**:
  - Get content type configuration
  - Update content type settings
  - Retrieve metadata and layouts
- **Endpoints**: `/api/content-manager/content-types/:uid/configuration`
- **HTTP Methods**: GET, PUT

---

### Services Layer

Services contain business logic and interact with core Strapi services.

#### Document Manager Service
- **File**: `server/src/services/document-manager.ts`
- **Responsibilities**:
  - **Primary orchestrator** for all document operations
  - Coordinates between permissions, validation, and data access
  - Handles CRUD with business rules
  - Manages draft/publish workflow
  - Triggers lifecycle hooks
- **Key Methods**:
  - `find(uid, params)` - Find documents with filters
  - `findOne(uid, documentId, params)` - Find single document
  - `create(uid, data)` - Create new document
  - `update(uid, documentId, data)` - Update document
  - `delete(uid, documentId)` - Delete document
  - `publish(uid, documentId)` - Publish draft
  - `unpublish(uid, documentId)` - Unpublish to draft
  - `clone(uid, documentId)` - Duplicate document

#### Permission Checker Service
- **File**: `server/src/services/permission-checker.ts`
- **Responsibilities**:
  - Check if user can perform action on content type
  - Filter fields based on permissions
  - Validate create/read/update/delete permissions
  - Handle field-level permissions
- **Integration**: Called before every operation in Document Manager

#### Populate Builder Service
- **File**: `server/src/services/populate-builder.ts`
- **Responsibilities**:
  - Build populate queries for relations
  - Respect max depth limits
  - Handle circular relations
  - Optimize nested populates
- **Output**: Populate object for Document Service API

#### Configuration Service
- **File**: `server/src/services/configuration.ts`
- **Responsibilities**:
  - Get/set content type settings
  - Manage layouts (list view, edit view)
  - Handle metadata (field descriptions, placeholders)
  - Store user preferences per content type
- **Persistence**: Stored in Strapi database

#### Data Mapper Service
- **File**: `server/src/services/data-mapper.ts`
- **Responsibilities**:
  - Transform input data to database format
  - Transform database results to API format
  - Handle component data mapping
  - Process dynamic zones
- **Transformations**:
  - Date formatting
  - Relation ID extraction
  - Component nesting
  - Media file references

#### UID Service
- **File**: `server/src/services/uid.ts`
- **Responsibilities**:
  - Generate URL-friendly slugs
  - Check UID uniqueness
  - Handle conflicts (append numbers)
  - Support custom UID targets
- **Algorithm**: Slugify + uniqueness check + incremental suffix

#### Document Metadata Service
- **File**: `server/src/services/document-metadata.ts`
- **Responsibilities**:
  - Get available locales for document
  - Get available statuses (draft, published)
  - Retrieve document versions
  - Manage content history metadata

#### Permission Service
- **File**: `server/src/services/permission.ts`
- **Responsibilities**:
  - Query user permissions for content types
  - Register permissions for content manager
  - Handle admin vs non-admin permissions
  - Define permission actions (create, read, update, delete, publish)

#### Content Types Service
- **File**: `server/src/services/content-types.ts`
- **Responsibilities**:
  - List all content types
  - Get content type schema
  - Determine if content type is displayable in CM
  - Handle single types vs collection types

#### Components Service
- **File**: `server/src/services/components.ts`
- **Responsibilities**:
  - List all components
  - Get component schema
  - Retrieve component category
  - Handle component configurations

#### Field Sizes Service
- **File**: `server/src/services/field-sizes.ts`
- **Responsibilities**:
  - Calculate maximum field sizes
  - Estimate database storage requirements
  - Warn about oversized fields

#### Metrics Service
- **File**: `server/src/services/metrics.ts`
- **Responsibilities**:
  - Track content creation metrics
  - Anonymous usage telemetry
  - Send metrics to Strapi analytics

---

### Sub-Modules

#### History Module
- **Location**: `server/src/history/`
- **Components**:
  - History Version Model
  - History Service (CRUD for versions)
  - History Controller (API endpoints)
  - Lifecycle Hooks (auto-save versions)
- **Features**:
  - Automatic version creation on update
  - Version restoration
  - Version comparison
  - Audit trail with user tracking
- **Database Table**: `strapi_history_versions`

#### Preview Module
- **Location**: `server/src/preview/`
- **Components**:
  - Preview Service
  - Preview Config Service
  - Preview Controller
- **Features**:
  - Generate preview URLs for draft content
  - Configure preview targets per content type
  - Token-based preview authentication

#### Homepage Module
- **Location**: `server/src/homepage/`
- **Components**:
  - Homepage Service
  - Homepage Controller
- **Features**:
  - Customize admin panel homepage
  - Recent content widgets
  - Quick actions

---

### Middleware & Policies

#### Routing Middleware
- **File**: `server/src/middlewares/routing.ts`
- **Purpose**: Route requests to collection-types vs single-types controllers
- **Logic**: Inspects content type kind (collection | single) and delegates

#### Permission Policy
- **File**: `server/src/policies/hasPermissions.ts`
- **Purpose**: Enforce permission checks before controller execution
- **Validates**:
  - User is authenticated
  - User has required permission for action
  - Action is allowed on content type
- **Failure**: Returns 403 Forbidden

---

### Utilities

#### Validation Utils
- **Location**: `server/src/controllers/validation/`
- **Files**:
  - `model-configuration.ts` - Joi schemas for configuration updates
  - `relations.ts` - Validate relation queries
  - `dimensions.ts` - Validate dimension queries
- **Purpose**: Input validation before processing

#### Configuration Utils
- **Location**: `server/src/services/utils/configuration/`
- **Files**:
  - `layouts.ts` - Generate default layouts for list/edit views
  - `metadatas.ts` - Generate field metadata
  - `attributes.ts` - Process content type attributes
  - `settings.ts` - Default settings per content type
- **Output**: Configuration objects for admin UI

#### Populate Utils
- **Location**: `server/src/services/utils/`
- **Files**:
  - `populate.ts` - Build populate objects
  - `query-populate.ts` - Query parameter parsing
  - `validatable-fields-populate.ts` - Determine which fields to populate for validation
- **Features**:
  - Max depth limit (default: 5)
  - Circular reference prevention
  - Permission-based filtering

#### Clone Utils
- **Location**: `server/src/controllers/utils/`
- **File**: `clone.ts`
- **Purpose**: Deep clone documents including components and relations
- **Handles**:
  - Component duplication
  - Unique field regeneration (e.g., slug)
  - Relation copying (copies IDs, not relations themselves)

---

## Data Flow

### Read Operation (Find All)

```
Admin UI
  ↓ GET /api/content-manager/collection-types/articles
Collection Types Controller
  ↓ find(params)
Routing Middleware
  ↓ routes to controller
Permission Policy
  ↓ checks hasPermissions
Document Manager Service
  ↓ calls
Permission Checker Service
  ↓ validates read permission
Populate Builder Service
  ↓ builds populate query
Document Service (Core)
  ↓ executes query
Database
  ↓ returns rows
Document Service
  ↓ maps to documents
Data Mapper Service
  ↓ transforms to API format
Collection Types Controller
  ↓ formats response
Admin UI (receives formatted data)
```

### Write Operation (Create)

```
Admin UI
  ↓ POST /api/content-manager/collection-types/articles
Collection Types Controller
  ↓ create(data)
Validation Utils
  ↓ validates input schema
Permission Policy
  ↓ checks create permission
Document Manager Service
  ↓ orchestrates
Permission Checker Service
  ↓ validates fields
Data Mapper Service
  ↓ maps input to DB format
Document Service (Core)
  ↓ beforeCreate hook
Event Hub
  ↓ emits 'entry.beforeCreate'
Document Service
  ↓ INSERT query
Database
  ↓ returns created row
Document Service
  ↓ afterCreate hook
Event Hub
  ↓ emits 'entry.afterCreate'
History Module
  ↓ creates initial version
Data Mapper Service
  ↓ maps DB to API format
Admin UI (receives created document)
```

---

## Design Patterns

### 1. Service Layer Pattern
- Controllers delegate to services
- Services contain business logic
- Clear separation of concerns

### 2. Repository Pattern
- Services use Document Service as repository
- Abstracts database access
- Enables testing with mocks

### 3. Strategy Pattern
- Different controllers for collection vs single types
- Same interface, different implementations

### 4. Builder Pattern
- Populate Builder constructs complex query objects
- Configuration Utils build layouts/metadata

### 5. Observer Pattern
- Event Hub for lifecycle hooks
- History and Preview modules subscribe to events

### 6. Facade Pattern
- Document Manager provides simplified interface
- Hides complexity of permissions, validation, mapping

---

## Dependencies

### Internal (Strapi Core)
- `@strapi/database` - Database ORM
- `@strapi/permissions` - Permission engine
- `@strapi/utils` - Utility functions (sanitize, validate)
- `@strapi/types` - TypeScript types

### External (npm)
- `koa` - HTTP server framework
- `joi` - Input validation
- `lodash` - Utility functions
- `slugify` - UID generation

---

## API Endpoints Summary

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/collection-types/:uid` | GET | List entries |
| `/collection-types/:uid/:id` | GET | Get entry |
| `/collection-types/:uid` | POST | Create entry |
| `/collection-types/:uid/:id` | PUT | Update entry |
| `/collection-types/:uid/:id` | DELETE | Delete entry |
| `/collection-types/:uid/:id/actions/publish` | POST | Publish draft |
| `/collection-types/:uid/:id/actions/unpublish` | POST | Unpublish |
| `/collection-types/:uid/:sourceId/actions/clone` | POST | Clone entry |
| `/single-types/:uid` | GET | Get single type |
| `/single-types/:uid` | PUT | Update single type |
| `/relations/:model/:id/:field` | GET | Get relations |
| `/uid/generate` | POST | Generate UID |
| `/content-types/:uid/configuration` | GET | Get configuration |
| `/content-types/:uid/configuration` | PUT | Update configuration |
| `/components` | GET | List components |

---

## Performance Considerations

### Populate Optimization
- Max depth limit prevents deep nesting
- Permission filtering before populate
- Batch loading for relations

### Caching
- Configuration cached in memory
- Schema cached (invalidated on update)
- Permission queries cached per request

### Query Optimization
- Select only needed fields
- Use indexes on filtered columns
- Limit default page size (10-100)

---

## Testing

### Unit Tests
- Controllers: Mock services, test HTTP responses
- Services: Mock Document Service, test business logic
- Utils: Pure function testing

### Integration Tests
- Full request/response cycle
- Real database with test data
- Permission scenarios

### Coverage
- Target: >80% for all services
- Controllers: Response formatting
- Critical paths: CRUD operations with permissions

---

## Generated Information

**Date**: December 12, 2025  
**Package**: @strapi/plugin-content-manager  
**Version**: 5.31.3  
**Analysis Method**: Directory structure + Symbol overview  
**Diagram Format**: PlantUML C4 Component  
**Validation**: Cross-referenced with codebase structure
