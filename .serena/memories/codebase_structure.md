# Codebase Structure and Organization

## Package Organization

### Core Packages (`packages/core/`)

#### 1. **@strapi/strapi** (`packages/core/strapi`)
- **Main entry point** for Strapi applications
- Exports `createStrapi()` factory function
- CLI commands implementation
- Project bootstrapping
- Load mechanism for user applications

#### 2. **@strapi/core** (`packages/core/core`)
- **Core framework implementation**
- `Strapi` class - main orchestrator
- Container/DI system
- Loaders (plugins, APIs, middlewares, policies, etc.)
- Services: entity-service, document-service, event-hub, auth, etc.
- Core API generators
- Middleware stack
- Configuration management

#### 3. **@strapi/admin** (`packages/core/admin`)
- **Admin panel** React application
- Two parts: `admin/` (frontend) and `server/` (backend)
- Redux state management
- Design system components
- Plugin extension system
- RBAC implementation
- Enterprise Edition features

#### 4. **@strapi/database** (`packages/core/database`)
- **Custom ORM** implementation
- Query builder
- Transaction management
- Migration system
- Relation handling
- Multi-database support (PostgreSQL, MySQL, MariaDB, SQLite)

#### 5. **@strapi/content-manager** (`packages/core/content-manager`)
- **Content management** functionality
- CRUD operations UI and API
- List view, edit view components
- Relation management
- Media library integration
- Draft/publish system

#### 6. **@strapi/content-type-builder** (`packages/core/content-type-builder`)
- **Visual content type builder**
- Schema creation and editing
- Component builder
- Field type management
- Dynamic zone configuration

#### 7. **@strapi/upload** (`packages/core/upload`)
- **Media library** and file upload
- Provider system (local, S3, Cloudinary)
- Image processing
- Asset management
- Server and admin components

#### 8. **@strapi/permissions** (`packages/core/permissions`)
- **RBAC system**
- Permission engine
- Role management
- Policy enforcement
- Action-based permissions

#### 9. **@strapi/review-workflows** (`packages/core/review-workflows`)
- **Content review workflows** (Enterprise)
- Stage management
- Approval process
- Workflow configuration

#### 10. **@strapi/content-releases** (`packages/core/content-releases`)
- **Scheduled publishing** system
- Release management
- Entry scheduling
- Publication automation

#### 11. **@strapi/data-transfer** (`packages/core/data-transfer`)
- **Import/export** functionality
- Transfer engine
- Providers (remote Strapi, local file)
- Data streaming
- Diff handling

#### 12. **@strapi/email** (`packages/core/email`)
- **Email sending** functionality
- Provider system
- Template rendering
- Email configuration

#### 13. **@strapi/openapi** (`packages/core/openapi`)
- **OpenAPI specification** generation
- Swagger documentation
- API schema generation

#### 14. **@strapi/utils** (`packages/core/utils`)
- **Shared utilities** across packages
- Helper functions
- Common types
- Validation utilities

#### 15. **@strapi/types** (`packages/core/types`)
- **TypeScript type definitions**
- Core interfaces
- Plugin types
- Schema types

### Plugin Packages (`packages/plugins/`)

#### Official Plugins
- **@strapi/plugin-graphql** - GraphQL API support
- **@strapi/plugin-i18n** - Internationalization
- **@strapi/plugin-users-permissions** - User authentication and permissions
- **@strapi/plugin-documentation** - API documentation generation
- **@strapi/plugin-sentry** - Error tracking integration
- **@strapi/plugin-cloud** - Strapi Cloud integration
- **@strapi/plugin-color-picker** - Color picker field type

### Provider Packages (`packages/providers/`)

#### Email Providers
- `@strapi/provider-email-amazon-ses` - AWS SES
- `@strapi/provider-email-sendgrid` - SendGrid
- `@strapi/provider-email-mailgun` - Mailgun
- `@strapi/provider-email-nodemailer` - Nodemailer (generic SMTP)
- `@strapi/provider-email-sendmail` - Sendmail

#### Upload Providers
- `@strapi/provider-upload-aws-s3` - AWS S3
- `@strapi/provider-upload-cloudinary` - Cloudinary
- `@strapi/provider-upload-local` - Local filesystem

### CLI Packages (`packages/cli/`)

- **create-strapi-app** - Project scaffolding CLI
- **create-strapi** - Wrapper for project creation
- **cloud** - Strapi Cloud CLI

### Utility Packages (`packages/utils/`)

- **@strapi/logger** - Logging utilities
- **@strapi/typescript** - TypeScript utilities
- **@strapi/upgrade** - Upgrade utilities
- **@strapi/api-tests** - API testing utilities
- **eslint-config-custom** - ESLint configuration
- **tsconfig** - Shared TypeScript configs

### Generator Packages (`packages/generators/`)

- **@strapi/generators** - Code generation utilities

### Test Utilities

- **@strapi/admin-test-utils** - Admin panel test utilities

## Directory Structure Patterns

### Typical Package Structure
```
package-name/
├── package.json          # Package configuration
├── rollup.config.mjs     # Build configuration
├── tsconfig.json         # TypeScript config
├── .eslintrc.js          # Linting config
├── src/                  # Source code
│   ├── index.ts          # Main entry point
│   ├── services/         # Service layer
│   ├── controllers/      # Controllers
│   ├── routes/           # Route definitions
│   ├── utils/            # Utilities
│   └── types/            # Type definitions
├── dist/                 # Build output (gitignored)
├── __tests__/            # Tests
└── README.md
```

### Dual Package Structure (with admin)
```
package-name/
├── server/               # Backend code
│   ├── src/
│   ├── tsconfig.json
│   └── .eslintrc
├── admin/                # Frontend code
│   ├── src/
│   ├── tsconfig.json
│   └── .eslintrc
├── rollup.config.mjs
└── package.json
```

## Example Applications (`examples/`)

- **getstarted** - Primary development/testing app
- **kitchensink** - Feature showcase (JavaScript)
- **kitchensink-ts** - Feature showcase (TypeScript)
- **empty** - Minimal template
- **experimental-dev** - Experimental features

## Test Organization (`tests/`)

- `tests/api/` - API integration tests
- `tests/e2e/` - End-to-end tests (Playwright)
- `tests/cli/` - CLI tests
- `tests/helpers/` - Test helpers
- `tests/scripts/` - Test runner scripts
- `tests/utils/` - Test utilities

## Build Output Structure

Each package outputs to `dist/`:
```
dist/
├── index.js          # CommonJS entry
├── index.mjs         # ESM entry
├── index.d.ts        # Type definitions
└── [other modules]   # Preserves source structure
```

## Import Paths and Module Resolution

- **Workspace packages**: Use `workspace:*` protocol
- **Internal imports**: Use TypeScript path aliases or relative imports
- **Exports field**: Defined in package.json for subpath exports
