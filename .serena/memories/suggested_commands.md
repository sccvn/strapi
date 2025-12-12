# Suggested Commands

## Initial Setup

### Install Dependencies
```bash
yarn install
```

### Setup Project (First Time)
```bash
yarn setup
```
This runs: `yarn && yarn clean && yarn build --skip-nx-cache`

## Development Commands

### Watch Mode (Auto-rebuild on changes)
```bash
yarn watch
```
Watches all packages and rebuilds on file changes using Nx.

### Start Example Application
```bash
cd examples/getstarted
yarn develop
```
See `examples/getstarted/README.md` for more details.

### Run Admin Panel in Development
```bash
# From the example app directory
yarn develop
```

## Build Commands

### Build All Packages
```bash
yarn build
```
Runs both `build:code` and `build:types` using Nx.

### Build Code Only
```bash
yarn build:code
```

### Build Types Only
```bash
yarn build:types
```

### Clean Build Artifacts
```bash
yarn clean
```

## Testing Commands

### Run All Tests
```bash
# Unit tests across all packages
yarn test:unit:all

# Frontend tests across all packages
yarn test:front:all

# API tests
yarn test:api

# E2E tests
yarn test:e2e --setup --concurrency=1

# CLI tests
yarn test:cli
```

### Run Specific Test Suites
```bash
# Unit tests (current package)
yarn test:unit

# Frontend tests (current package)
yarn test:front

# Watch mode for unit tests
yarn test:unit:watch

# Watch mode for frontend tests
yarn test:front:watch

# Update snapshots
yarn test:unit:update
yarn test:front:update
```

### E2E Testing
```bash
# Install Playwright browsers first (one-time)
yarn playwright install

# Run E2E tests with setup
yarn test:e2e --setup --concurrency=1

# Clean E2E test artifacts
yarn test:e2e:clean
```

### Test TypeScript Types
```bash
yarn test:ts
```

## Linting and Formatting

### Lint All Code
```bash
yarn lint
```

### Lint with Auto-fix
```bash
yarn lint:fix
```

### Format All Code
```bash
yarn format
```

### Format Code Files Only (JS/TS)
```bash
yarn format:code
```

### Format Other Files (MD/CSS/YAML)
```bash
yarn format:other
```

### Check Formatting (CI)
```bash
yarn prettier:check
```

## Git Workflow Commands

### Commit with Commitizen
```bash
yarn commit
```
Interactive commit message builder following conventional commits.

### Pre-commit Hooks
Husky automatically runs:
- Lint-staged (lints and formats changed files)
- Commitlint (validates commit message)

## Release and Publishing

### Create Release
```bash
yarn release
```
Runs `scripts/release.js` for version bumping and publishing.

## Utilities

### Generate Test App
```bash
yarn test:generate-app
```
Generates a test Strapi application for testing.

### Open API Documentation
```bash
yarn doc:api
```
Serves OpenAPI documentation.

## Package-Specific Commands

### Run Command in Specific Package (Nx)
```bash
# Lint specific package
nx run @strapi/admin:lint

# Build specific package
nx run @strapi/strapi:build

# Test specific package
nx run @strapi/core:test:unit
```

### Run Command in Multiple Packages (Nx)
```bash
# Run lint on multiple packages
nx run-many --target=lint --projects=@strapi/admin,@strapi/core

# Run all targets
nx run-many --target=build --all
```

## Docker Commands

### Development Docker Compose
```bash
docker-compose -f docker-compose.dev.yml up
```

### Test Docker Compose
```bash
docker-compose -f docker-compose.test.yml up
```

## Common System Commands (Linux)

### Find Files
```bash
find . -name "*.ts" -type f
```

### Search in Files (grep)
```bash
grep -r "searchTerm" packages/
```

### List Directory
```bash
ls -la
```

### Change Directory
```bash
cd packages/core/strapi
```

### Git Commands
```bash
git status
git add .
git commit -m "message"
git push origin branch-name
git checkout -b new-branch
git pull origin develop
```

### View Logs
```bash
tail -f logs/strapi.log
```

## Troubleshooting Commands

### Clear Node Modules and Reinstall
```bash
rm -rf node_modules
yarn install
```

### Clear Yarn Cache
```bash
yarn cache clean
```

### Rebuild Everything from Scratch
```bash
yarn clean
rm -rf node_modules
yarn install
yarn setup
```

### Check Nx Cache
```bash
nx reset
```
Clears Nx cache if builds are not working correctly.
