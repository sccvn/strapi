# Repository Quick Reference

## Essential Information

### Repository Basics
- **Name**: Strapi (Open-Source Headless CMS)
- **Version**: 5.31.3
- **Type**: Monorepo (Lerna + Yarn Workspaces + Nx)
- **Language**: TypeScript/JavaScript
- **License**: See LICENSE file
- **Branch**: develop (for development), main (for releases)

### Quick Start Commands
```bash
# Clone and setup
git clone <fork-url>
cd strapi
yarn install
yarn setup

# Development
cd examples/getstarted
yarn develop

# Watch mode (auto-rebuild)
yarn watch

# Testing
yarn test:unit
yarn test:front
yarn test:e2e --setup --concurrency=1

# Linting and formatting
yarn lint
yarn format

# Build
yarn build
```

### Project URLs
- **GitHub**: https://github.com/strapi/strapi
- **Documentation**: https://docs.strapi.io
- **Cloud**: https://cloud.strapi.io
- **Community**: https://discord.strapi.io

### Key Contacts
- **Email**: hi@strapi.io
- **Contributions**: contributions@strapi.io
- **Maintainer**: Strapi Solutions SAS

## Critical Files to Know

### Configuration
- `package.json` - Root workspace config, scripts
- `lerna.json` - Lerna configuration
- `nx.json` - Nx build configuration
- `.prettierrc.js` - Code formatting rules
- `lint-staged.config.js` - Pre-commit hooks
- `rollup.utils.mjs` - Shared build utilities

### Development
- `CONTRIBUTING.md` - Contribution guidelines
- `CODE_OF_CONDUCT.md` - Community standards
- `examples/getstarted/` - Main development app
- `tests/` - Test suites

### Documentation
- `README.md` - Project overview
- `docs/` - Internal developer docs (Docusaurus)
- Each package has its own README

## Core Packages Quick Map

| Package | Purpose | Location |
|---------|---------|----------|
| @strapi/strapi | Main entry point | packages/core/strapi |
| @strapi/core | Core framework | packages/core/core |
| @strapi/admin | Admin panel | packages/core/admin |
| @strapi/database | ORM | packages/core/database |
| @strapi/content-manager | Content CRUD | packages/core/content-manager |
| @strapi/upload | Media library | packages/core/upload |

## Common Development Patterns

### Adding a New Feature
1. Fork and create feature branch from `develop`
2. Make changes with tests
3. Run `yarn lint` and `yarn format`
4. Run `yarn test:unit` 
5. Build with `yarn build`
6. Commit with conventional commits
7. Create PR to `develop`

### Debugging
1. Use example apps: `cd examples/getstarted && yarn develop`
2. Add console.log or use debugger
3. Check logs in terminal
4. Use Chrome DevTools for admin panel

### Working with Plugins
- Server code: `server/` directory
- Admin code: `admin/` directory  
- Entry points: `strapi-server.js`, `strapi-admin.js`

## Testing Strategy

### Test Types
- **Unit**: Business logic, utilities
- **Integration**: API endpoints
- **E2E**: User flows (Playwright)
- **Frontend**: React components

### Running Tests
```bash
# All unit tests
yarn test:unit:all

# Specific package
cd packages/core/strapi
yarn test:unit

# Watch mode
yarn test:unit:watch

# E2E (after Playwright install)
yarn test:e2e
```

## Build System

### Nx Targets
- `build` - Build code and types
- `build:code` - Build code only
- `build:types` - Generate TypeScript declarations
- `lint` - Run ESLint
- `test:unit` - Run Jest tests
- `test:front` - Run frontend tests

### Nx Commands
```bash
# Build specific package
nx run @strapi/admin:build

# Build all packages
nx run-many --target=build --all

# Clear cache
nx reset
```

## Common Issues and Solutions

### Build Issues
- Clear node_modules: `rm -rf node_modules && yarn install`
- Clear build cache: `yarn clean && nx reset`
- Rebuild: `yarn setup`

### Test Issues
- Clear test artifacts: `yarn test:clean`
- Install Playwright browsers: `yarn playwright install`

### Linting Issues
- Auto-fix: `yarn lint:fix`
- Format all: `yarn format`

## Enterprise Edition (EE)

Some packages have `ee/` directories for Enterprise features:
- Review workflows
- Audit logs
- SSO
- Advanced RBAC

Check `strapi.EE` to detect EE mode.

## Key Environment Variables

- `NODE_ENV` - development/production
- `DATABASE_CLIENT` - postgres/mysql/sqlite
- `STRAPI_TELEMETRY_DISABLED` - Disable telemetry
- `IS_EE` - Enable Enterprise Edition features (internal testing)

## Helpful Tips

1. **Always work from develop branch**
2. **Run `yarn setup` after pulling major changes**
3. **Use `yarn watch` during development for auto-rebuild**
4. **Test with example apps before submitting PR**
5. **Follow conventional commit format**
6. **Sign CLA on first contribution**
7. **Keep PRs focused and small**
8. **Update tests and docs with code changes**

## Getting Help

- Discord: https://discord.strapi.io
- GitHub Issues: For bugs and features
- GitHub Discussions: For questions
- Documentation: https://docs.strapi.io
- RFCs: For major proposals (strapi/rfcs repo)
