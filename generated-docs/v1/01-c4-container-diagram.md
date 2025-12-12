# C4 Container Diagram - Strapi CMS

## Overview

This document presents the C4 Container diagram for the complete Strapi CMS system, showing all deployable units, their technologies, inter-container communication protocols, and external system integrations.

## System Context

Strapi is an open-source headless CMS that provides a content management admin panel and REST/GraphQL APIs for content delivery to frontend applications.

**Version**: 5.31.3  
**Architecture**: Monolithic Node.js application with plugin-based extensibility

---

## C4 Container Diagram

```plantuml
@startuml C4_Container_Strapi
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

LAYOUT_WITH_LEGEND()

title Container Diagram - Strapi CMS (v5.31.3)

Person(content_creator, "Content Creator", "Admin user who creates and manages content")
Person(content_editor, "Content Editor", "User who reviews and approves content")
Person(developer, "Developer", "Configures Strapi and customizes functionality")
Person(api_consumer, "API Consumer", "Frontend application or mobile app")

System_Boundary(strapi_system, "Strapi CMS") {
    Container(admin_panel, "Admin Panel", "React 18, Redux Toolkit, styled-components", "Web-based UI for content management, user management, and system configuration")
    
    Container(api_server, "API Server", "Node.js 20-24, Koa.js, TypeScript", "Provides REST and GraphQL APIs for content delivery and management")
    
    Container(plugin_system, "Plugin System", "TypeScript, JavaScript", "Extensibility layer supporting official and custom plugins")
    
    Container(core_services, "Core Services", "TypeScript", "Document Service, Entity Service, Event Hub, Auth, Permissions")
    
    Container(content_manager, "Content Manager", "TypeScript", "Handles CRUD operations for content types with validation")
    
    Container(user_permissions, "Users & Permissions", "TypeScript", "Authentication, authorization, JWT, role-based access control")
    
    Container(upload_service, "Upload Service", "TypeScript", "File upload, media library, image processing")
    
    Container(database_layer, "Database Layer", "Custom ORM @strapi/database", "Query builder, migrations, transactions, relation management")
    
    ContainerDb(database, "Database", "PostgreSQL/MySQL/SQLite", "Stores content, users, permissions, configuration")
    
    Container(graphql_plugin, "GraphQL Plugin", "TypeScript, graphql-js", "Provides GraphQL API alongside REST")
    
    Container(i18n_plugin, "I18n Plugin", "TypeScript", "Internationalization support for multi-language content")
    
    Container(review_workflows, "Review Workflows", "TypeScript", "Content approval workflows with stages")
    
    Container(content_releases, "Content Releases", "TypeScript", "Scheduled publishing and content bundling")
}

System_Ext(storage_s3, "AWS S3", "Cloud object storage for media files")
System_Ext(storage_cloudinary, "Cloudinary", "Image and video management service")
System_Ext(email_smtp, "Email Provider", "SMTP/SendGrid/Mailgun/SES for transactional emails")
System_Ext(auth_oauth, "OAuth Provider", "Google/GitHub/Facebook for SSO")
System_Ext(cdn, "CDN", "CloudFlare/AWS CloudFront for static asset delivery")
System_Ext(monitoring, "Monitoring", "Sentry for error tracking, custom analytics")

' User interactions
Rel(content_creator, admin_panel, "Uses", "HTTPS, Browser")
Rel(content_editor, admin_panel, "Reviews content", "HTTPS, Browser")
Rel(developer, admin_panel, "Configures", "HTTPS, Browser")
Rel(api_consumer, api_server, "Fetches content", "REST/GraphQL API, HTTPS")

' Internal container relationships
Rel(admin_panel, api_server, "Calls", "REST API, HTTPS")
Rel(api_server, core_services, "Uses", "Function calls")
Rel(api_server, plugin_system, "Loads", "Dynamic imports")
Rel(plugin_system, content_manager, "Extends", "Plugin hooks")
Rel(plugin_system, user_permissions, "Extends", "Auth strategies")
Rel(plugin_system, graphql_plugin, "Includes", "GraphQL schema")
Rel(plugin_system, i18n_plugin, "Includes", "Locale handling")
Rel(plugin_system, review_workflows, "Includes", "Approval stages")
Rel(plugin_system, content_releases, "Includes", "Scheduling")

Rel(core_services, database_layer, "Uses", "Query builder API")
Rel(content_manager, core_services, "Delegates to", "Document Service")
Rel(user_permissions, core_services, "Uses", "Auth Service")
Rel(upload_service, core_services, "Uses", "Event Hub")
Rel(graphql_plugin, core_services, "Queries", "Document Service")
Rel(i18n_plugin, core_services, "Uses", "Entity Service")
Rel(review_workflows, core_services, "Uses", "Event Hub")
Rel(content_releases, core_services, "Uses", "Cron Service")

Rel(database_layer, database, "Reads/Writes", "SQL queries, Connection pool")

' External system relationships
Rel(upload_service, storage_s3, "Uploads", "AWS SDK v3")
Rel(upload_service, storage_cloudinary, "Uploads", "Cloudinary SDK")
Rel(user_permissions, email_smtp, "Sends emails", "SMTP protocol")
Rel(user_permissions, auth_oauth, "Authenticates", "OAuth 2.0")
Rel(admin_panel, cdn, "Loads assets", "HTTPS")
Rel(api_server, monitoring, "Reports errors", "Sentry SDK")

SHOW_LEGEND()

@enduml
```

---

## Container Descriptions

### Admin Panel
- **Technology**: React 18, Redux Toolkit, styled-components, react-router
- **Purpose**: Web-based administration interface
- **Key Features**:
  - Content editing with rich text editor
  - Media library management
  - User and role management
  - Content type builder (visual schema editor)
  - Plugin marketplace integration
  - Customizable dashboard
- **Deployment**: Single-page application served by API Server
- **Port**: Typically 1337 (dev), behind reverse proxy in production

### API Server
- **Technology**: Node.js (v20-24), Koa.js, TypeScript
- **Purpose**: Central request handler and API gateway
- **Key Features**:
  - REST API endpoints (auto-generated from content types)
  - Request routing and middleware chain
  - Authentication and authorization
  - Rate limiting and CORS
  - WebSocket support for real-time features
- **Deployment**: Single Node.js process or clustered
- **Scaling**: Horizontal scaling supported with session store

### Plugin System
- **Technology**: TypeScript, JavaScript ES Modules
- **Purpose**: Extensibility mechanism
- **Plugin Types**:
  - **Core Plugins**: content-manager, upload, users-permissions, etc.
  - **Official Plugins**: graphql, i18n, documentation, sentry
  - **Custom Plugins**: User-developed extensions
- **Plugin Lifecycle**: register() → bootstrap() → destroy()
- **Extension Points**: Admin UI, Server routes, Services, Content types

### Core Services
- **Document Service**: New unified API for content operations (v5+)
- **Entity Service**: Legacy content API (deprecated)
- **Event Hub**: Publish-subscribe for lifecycle hooks
- **Auth Service**: Authentication strategies and JWT
- **Request Context**: Async local storage for request state
- **Webhook Runner**: Trigger external webhooks on events
- **Cron Service**: Scheduled task execution
- **Worker Queue**: Background job processing

### Content Manager
- **Purpose**: CRUD operations for all content types
- **Features**:
  - Content type validation against schema
  - Draft/publish workflow
  - Version history
  - Component and dynamic zone support
  - Permission-based field filtering
- **API**: Exposes REST endpoints at `/api/:contentType`

### Users & Permissions
- **Purpose**: Authentication and authorization
- **Auth Strategies**:
  - Local (username/password with bcrypt)
  - JWT (stateless authentication)
  - OAuth providers (Google, GitHub, Facebook)
  - API tokens for machine-to-machine
- **RBAC**: Role-based access control with granular permissions
- **Features**:
  - User registration and password reset
  - Email verification
  - Permission matrix (per content type, per action)

### Upload Service
- **Purpose**: File upload and media management
- **Providers**:
  - **Local**: File system storage
  - **AWS S3**: Cloud storage
  - **Cloudinary**: Image/video CDN
- **Features**:
  - Image resizing and optimization
  - Multiple file format support
  - Folder organization
  - Media library search

### Database Layer
- **Technology**: Custom ORM (`@strapi/database`)
- **Supported Databases**:
  - PostgreSQL (recommended for production)
  - MySQL / MariaDB
  - SQLite (development only)
- **Features**:
  - Query builder with fluent API
  - Database migrations
  - Transaction support
  - Connection pooling
  - Multi-database support (enterprise)

### GraphQL Plugin
- **Technology**: graphql-js, Apollo Server integration
- **Purpose**: Provides GraphQL API alternative to REST
- **Features**:
  - Auto-generated schema from content types
  - Mutations for CRUD operations
  - Subscriptions for real-time updates
  - Customizable resolvers
- **Endpoint**: `/graphql` (with GraphQL Playground)

### I18n Plugin
- **Purpose**: Internationalization support
- **Features**:
  - Multi-language content
  - Locale management
  - Translation workflows
  - Per-field localization
  - Default locale fallback

### Review Workflows
- **Purpose**: Content approval process
- **Features**:
  - Configurable workflow stages
  - Reviewer assignment
  - Approval/rejection with comments
  - Email notifications
  - Audit trail

### Content Releases
- **Purpose**: Scheduled publishing
- **Features**:
  - Bundle multiple content items
  - Schedule release date/time
  - Preview before publish
  - Rollback support
  - Release history

---

## External Systems

### Storage Providers
- **AWS S3**: Industry-standard cloud object storage
- **Cloudinary**: Specialized for images/videos with transformations
- **Selection**: Configured via provider plugins

### Email Providers
- **SMTP**: Generic email sending (Gmail, etc.)
- **SendGrid**: Transactional email service
- **Mailgun**: Email API and SMTP
- **Amazon SES**: AWS Simple Email Service
- **Use Cases**: Password reset, user registration, notifications

### Authentication Providers
- **Google OAuth**: Social login
- **GitHub OAuth**: Developer-friendly auth
- **Facebook OAuth**: Social login
- **SAML**: Enterprise SSO (EE feature)

### CDN
- **Purpose**: Serve static admin panel assets
- **Providers**: CloudFlare, AWS CloudFront, Fastly
- **Benefits**: Reduced latency, DDoS protection

### Monitoring
- **Sentry**: Error tracking and performance monitoring
- **Custom Analytics**: Usage metrics, content stats
- **Logs**: Winston logger with configurable transports

---

## Communication Protocols

### Internal Communication
- **Admin ↔ API**: REST over HTTPS (JSON payloads)
- **API ↔ Services**: Direct function calls (in-process)
- **API ↔ Database**: SQL over TCP (connection pool)
- **Plugin ↔ Core**: Event hooks and dependency injection

### External Communication
- **API Consumer ↔ API**: REST/GraphQL over HTTPS
- **Upload ↔ S3**: AWS SDK v3 (HTTPS)
- **Auth ↔ OAuth**: OAuth 2.0 protocol (HTTPS)
- **Server ↔ Email**: SMTP or HTTP API
- **Server ↔ Monitoring**: HTTP(S) with SDK

---

## Deployment Patterns

### Single Server (Development)
```
+-------------------+
|   Strapi Server   |
| (Admin + API + DB)|
+-------------------+
```

### Production (Recommended)
```
+------------+     +------------------+     +--------------+
| Load       | --> | Strapi Instances | --> | PostgreSQL   |
| Balancer   |     | (Clustered)      |     | (Primary +   |
| (Nginx)    |     |                  |     | Replicas)    |
+------------+     +------------------+     +--------------+
                           |
                           v
                   +----------------+
                   | Redis          |
                   | (Session Store)|
                   +----------------+
```

### Cloud-Native (Kubernetes)
```
+--------+    +------------+    +----------+    +------------+
| CDN    | -> | Ingress    | -> | Strapi   | -> | RDS/       |
|        |    | Controller |    | Pods     |    | Cloud DB   |
+--------+    +------------+    +----------+    +------------+
                                      |
                                      v
                              +--------------+
                              | S3/Blob      |
                              | Storage      |
                              +--------------+
```

---

## Scalability Considerations

### Horizontal Scaling
- **Stateless API**: All state in database or Redis
- **Session Store**: Redis or PostgreSQL for multi-instance
- **File Uploads**: Cloud storage (S3/Cloudinary) for shared access
- **Load Balancer**: Distribute traffic across instances

### Database Scaling
- **Read Replicas**: PostgreSQL replication for read-heavy workloads
- **Connection Pooling**: PgBouncer or built-in pooling
- **Sharding**: Enterprise feature for multi-tenancy

### Caching
- **HTTP Cache**: CDN for static assets
- **API Cache**: Redis for frequently accessed content
- **Database Cache**: Query result caching

---

## Security Architecture

### Network Security
- **HTTPS Only**: TLS 1.2+ for all external communication
- **CORS**: Configurable cross-origin policies
- **Rate Limiting**: Prevent abuse (configurable per endpoint)
- **Helmet.js**: Security headers middleware

### Authentication
- **JWT**: Stateless token-based auth
- **Bcrypt**: Password hashing (cost factor 10)
- **Session**: Optional session-based auth with Redis

### Authorization
- **RBAC**: Fine-grained permissions per content type
- **Field-Level Security**: Hide/protect sensitive fields
- **API Tokens**: Scoped tokens for integrations

### Data Protection
- **Input Validation**: Joi schemas for request validation
- **SQL Injection**: Prevented by ORM parameterized queries
- **XSS Protection**: Content sanitization
- **CSRF**: Token-based protection for state-changing operations

---

## Technology Stack Summary

| Container | Framework/Library | Language | Version |
|-----------|------------------|----------|---------|
| Admin Panel | React | TypeScript | 18.x |
| API Server | Koa.js | TypeScript | 2.x |
| Database ORM | Custom | TypeScript | 5.x |
| Build Tool | Rollup + SWC | JavaScript | Latest |
| Test Framework | Jest + Playwright | JavaScript | 29.x / 1.x |
| Package Manager | Yarn Workspaces | - | 1.22.x |

---

## Generated Information

**Date**: December 12, 2025  
**Strapi Version**: 5.31.3  
**Analysis Method**: Codebase structure analysis + Memory files  
**Diagram Format**: PlantUML C4 Container  
**Validation**: Cross-referenced with official documentation
