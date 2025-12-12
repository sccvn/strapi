# Key Technologies and Dependencies

## Core Runtime

### Node.js and JavaScript
- **Node.js**: v20.x - v24.x (v24.x recommended)
- **JavaScript Standard**: ES2020+ with modern features
- **Module System**: ESM (import/export) and CommonJS dual support
- **Async Patterns**: Promises, async/await, no callbacks

### TypeScript
- **Version**: 5.x
- **Configuration**: Strict mode in most packages
- **Compilation**: SWC (fast) instead of tsc for builds
- **Type Checking**: tsc for type validation only

## Backend Framework

### Koa.js
- **Web Framework**: Koa 2.x
- **Middleware**: Async middleware support
- **Context Object**: `ctx` for request/response
- **Error Handling**: Error middleware pattern

### HTTP and Networking
- **Server**: Node.js built-in HTTP/HTTPS
- **Proxy Support**: global-agent for HTTP proxy
- **WebSockets**: For real-time features (limited use)

## Database

### ORM (Custom)
- **@strapi/database**: Custom-built ORM
- **Query Builder**: SQL builder with database abstraction
- **Migrations**: Custom migration system

### Supported Databases
- **PostgreSQL** (recommended for production)
- **MySQL** / **MariaDB**
- **SQLite** (development/testing)

### Database Drivers
- `pg` - PostgreSQL
- `mysql2` - MySQL/MariaDB
- `better-sqlite3` - SQLite

## Frontend (Admin Panel)

### React Ecosystem
- **React**: 18.x with hooks
- **React Router**: v6 for routing
- **React Query** / **RTK Query**: Data fetching
- **React Hook Form**: Form management

### State Management
- **Redux**: 4.x with Redux Toolkit
- **RTK Query**: API state management
- **React Context**: For theme, locale

### Styling
- **styled-components**: 5.x for CSS-in-JS
- **Design System**: Custom @strapi/design-system
- **Icons**: Custom icon system + Font Awesome

### UI Components
- **@strapi/design-system**: Custom component library
- **react-dnd**: Drag and drop
- **react-select**: Select components
- **date-fns**: Date formatting

## Build Tools

### Bundlers and Compilers
- **Rollup**: Package bundler (library mode)
- **SWC**: Fast TypeScript/JavaScript compiler
- **Babel**: Limited use, mostly replaced by SWC
- **Vite**: Development server for admin panel

### Task Runners
- **Nx**: Monorepo build orchestration
- **Lerna**: Package publishing and versioning
- **npm-run-all** / **run-s**: Script running

### Module Bundler Plugins
- `@rollup/plugin-node-resolve` - Node module resolution
- `@rollup/plugin-commonjs` - CommonJS to ESM
- `@rollup/plugin-json` - JSON import support
- `@rollup/plugin-swc` - SWC integration
- `@rollup/plugin-image` - Image imports

## Testing

### Test Frameworks
- **Jest**: 29.x for unit/integration tests
- **Playwright**: 1.x for E2E tests
- **@testing-library/react**: React component testing
- **@testing-library/user-event**: User interaction simulation

### Test Utilities
- **supertest**: HTTP assertion
- **msw**: API mocking
- **jest-dom**: DOM matchers
- **@strapi/admin-test-utils**: Custom test utilities

## Linting and Formatting

### Code Quality Tools
- **ESLint**: 8.x for linting
- **Prettier**: 3.x for formatting
- **@typescript-eslint**: TypeScript ESLint
- **lint-staged**: Pre-commit linting
- **husky**: Git hooks

### ESLint Plugins
- `eslint-config-airbnb` - Base style
- `eslint-config-prettier` - Prettier integration
- `eslint-plugin-react` - React rules
- `eslint-plugin-react-hooks` - Hooks rules
- `eslint-plugin-import` - Import/export rules
- `eslint-plugin-testing-library` - Testing rules

## Package Management

### Package Managers
- **Yarn**: v1.x (classic, workspaces)
- **npm**: Fallback, not recommended
- **pnpm**: Not officially supported

### Workspace Management
- **Yarn Workspaces**: Dependency hoisting
- **Lerna**: Version management, publishing
- **Nx**: Build caching, task orchestration

## CLI and Scaffolding

### CLI Tools
- **commander**: CLI framework
- **inquirer**: Interactive prompts
- **chalk**: Terminal colors
- **ora**: Spinners
- **boxen**: Terminal boxes
- **execa**: Process execution

## Logging and Monitoring

### Logging
- **@strapi/logger**: Custom logger (Winston-based)
- **winston**: Underlying logging library
- **pino**: Alternative fast logger (limited use)

### Monitoring (Optional)
- **@strapi/plugin-sentry**: Sentry integration
- **New Relic**: APM (user-configurable)

## Authentication

### Strategies
- **Local Strategy**: Email/password (custom)
- **JWT**: JSON Web Tokens (jsonwebtoken)
- **Sessions**: Koa session (koa-session)
- **OAuth**: Via users-permissions plugin

### Security Libraries
- **bcryptjs**: Password hashing
- **jsonwebtoken**: JWT creation/validation
- **koa-passport**: Passport.js for Koa
- **grant**: OAuth2 flows

## File Upload and Storage

### Local Storage
- **fs-extra**: Enhanced filesystem operations
- **multer**: Multipart form data parsing

### Cloud Storage
- **aws-sdk**: AWS S3 integration
- **@aws-sdk/client-s3**: AWS SDK v3
- **cloudinary**: Cloudinary integration

### Image Processing
- **sharp**: Image resizing, optimization
- **image-size**: Get image dimensions

## Email

### Email Sending
- **nodemailer**: Email transport
- **@sendgrid/mail**: SendGrid API
- **aws-sdk**: SES integration
- **mailgun.js**: Mailgun API

## Validation and Sanitization

### Validation Libraries
- **yup**: Schema validation
- **zod**: Alternative validation (limited use)
- **validator**: String validation

### Sanitization
- **xss**: XSS prevention
- **sanitize-html**: HTML sanitization
- **dompurify**: DOM sanitization (frontend)

## Utilities

### General Purpose
- **lodash**: Utility functions
- **date-fns**: Date manipulation
- **qs**: Query string parsing
- **axios**: HTTP client
- **fs-extra**: File system utilities

### String Processing
- **pluralize**: Pluralization
- **semver**: Semantic versioning
- **uuid**: UUID generation

### Performance
- **p-limit**: Concurrency control
- **p-retry**: Retry logic
- **lru-cache**: LRU caching

## Development Tools

### Watch and Reload
- **chokidar**: File watching
- **nodemon**: Process restart (limited use)
- **@swc-node/register**: On-the-fly TS execution

### Documentation
- **Docusaurus**: Documentation site
- **typedoc**: TypeScript API docs
- **jsdoc**: JavaScript API docs

## CI/CD

### GitHub Actions
- Automated testing
- Linting and formatting checks
- Build verification
- Deployment to Strapi Cloud

### Docker
- **docker**: Containerization
- **docker-compose**: Multi-container apps
- Dockerfiles in examples

## GraphQL (Plugin)

### GraphQL Stack
- **graphql**: GraphQL.js
- **apollo-server-koa**: Apollo Server for Koa
- **graphql-tools**: Schema utilities
- **dataloader**: N+1 query prevention

## Internationalization

### i18n Libraries
- **react-intl**: React i18n
- **format.js**: ICU message formatting
- **date-fns**: Locale-aware date formatting

## Performance and Optimization

### Caching
- **node-cache**: In-memory cache
- **lru-cache**: LRU cache
- Nx cache for builds

### Compression
- **koa-compress**: Response compression
- **sharp**: Image compression

## Version Control

### Git Tools
- **husky**: Git hooks
- **lint-staged**: Staged file processing
- **commitlint**: Commit message validation
- **@commitlint/config-conventional**: Conventional commits

## API Documentation

### OpenAPI/Swagger
- **swagger-ui-dist**: Swagger UI
- **openapi3-ts**: OpenAPI TypeScript types

## Miscellaneous

### Environment
- **dotenv**: Environment variables
- **cross-env**: Cross-platform env vars

### Process Management
- **pm2**: Production process manager (user choice)
- **node-cron**: Cron job scheduling

### Debugging
- **debug**: Debug logging
- **why-is-node-running**: Debug hanging processes
