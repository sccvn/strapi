# Indexing and Caching Information

## Project Indexing Complete

This Strapi repository has been fully onboarded and indexed with the following information captured in memory files:

### Memory Files Created

1. **project_overview.md** - Project purpose, tech stack, repository structure, version info
2. **architecture_and_design_patterns.md** - Core architecture, design patterns, build system
3. **code_style_and_conventions.md** - TypeScript config, Prettier/ESLint rules, naming conventions, git workflow
4. **suggested_commands.md** - All development commands (build, test, lint, format, etc.)
5. **task_completion_checklist.md** - Step-by-step checklist for completing tasks
6. **codebase_structure.md** - Detailed package organization and directory structure
7. **core_algorithms.md** - Key algorithms and patterns (schema management, query building, permissions, etc.)
8. **development_guidelines.md** - Best practices for plugin development, API design, testing, security
9. **technologies_and_dependencies.md** - Complete list of technologies, libraries, and tools used
10. **quick_reference.md** - Quick start guide and common patterns
11. **indexing_and_caching.md** - This file

## Nx Build Caching

Strapi uses **Nx** for intelligent build caching:

### Nx Cache Locations
- `.nx/cache/` - Local build cache (gitignored)
- Remote cache can be configured for team sharing

### Cached Targets
- `build` - Full builds
- `build:code` - Code compilation
- `build:types` - TypeScript declarations
- `test:unit` - Unit test results
- `test:front` - Frontend test results
- `lint` - Linting results

### Cache Strategy
- **Input hashing**: Nx hashes source files and dependencies
- **Output caching**: Stores build artifacts
- **Incremental builds**: Only rebuilds changed packages
- **Dependency graph**: Understands package relationships

### Cache Management
```bash
# View Nx cache info
nx show projects

# Clear Nx cache
nx reset

# Run without cache
yarn build --skip-nx-cache
```

## Yarn Workspace Caching

### Dependency Caching
- `.yarn/cache/` - Yarn PnP cache (if enabled)
- `node_modules/` - Installed dependencies
- Yarn stores global cache at `~/.yarn/cache/`

### Cache Commands
```bash
# Clear Yarn cache
yarn cache clean

# Install from cache
yarn install --prefer-offline
```

## Build Artifact Locations

### Package Build Outputs
Each package builds to its own `dist/` directory:
- `packages/*/dist/` - Compiled code
- CommonJS (`.js`), ESM (`.mjs`), and TypeScript declarations (`.d.ts`)

### Ignored Files (Not Cached)
- `node_modules/`
- `dist/`
- `.nx/`
- `coverage/`
- `.cache/`
- Build artifacts in examples

## TypeScript Caching

### TSC Build Info
- `tsconfig.tsbuildinfo` files track incremental compilation
- Speeds up type checking
- Automatically managed by TypeScript

## Test Result Caching

### Jest Cache
- `.jest/cache/` - Jest cache directory
- Stores transform results
- Speeds up test runs

### Playwright Cache
- `~/.cache/ms-playwright/` - Browser binaries
- Shared across projects

## Source Code Indexing

### Language Server Protocol (LSP)
The codebase is indexed by TypeScript language server:
- Powers IntelliSense
- Enables go-to-definition
- Provides type checking

### Serena Indexing
This onboarding process created a symbolic index:
- Memory files for quick reference
- No need to re-read common information
- Optimized for future development tasks

## Performance Optimization

### For Faster Builds
1. Use Nx cache (enabled by default)
2. Build only what changed: `nx affected:build`
3. Parallelize: Nx runs tasks in parallel
4. Use watch mode for development: `yarn watch`

### For Faster Tests
1. Use Nx cache for test results
2. Run only affected tests: `nx affected:test`
3. Use `--maxWorkers` flag for Jest to control parallelism

### For Faster Development
1. Use watch mode: `yarn watch`
2. Work in example apps: Changes rebuild automatically
3. Use HMR in admin panel
4. Disable source maps in development if needed

## Repository Statistics

### Size Overview (Approximate)
- **Total Packages**: 40+ packages
- **Core Packages**: 15
- **Plugins**: 7 official
- **Providers**: 8
- **Examples**: 5
- **Lines of Code**: 500K+ (including tests and examples)

### Build Output Size
- Each package: 50KB - 5MB depending on package
- Admin panel build: ~5-10MB
- Total dist output: ~50-100MB

## Future Development Notes

### When Adding New Features
1. Consider impact on build cache
2. Update relevant memory files
3. Add tests (they get cached too)
4. Follow existing patterns for consistency

### When Refactoring
1. Nx graph helps identify dependencies
2. Run affected tests: `nx affected:test`
3. Verify build cache invalidates correctly

### When Upgrading Dependencies
1. Clear all caches after major upgrades
2. Run full test suite: `yarn test:unit:all`
3. Verify Nx cache works correctly

## Cache Invalidation

### When to Clear Cache
1. After pulling major changes
2. After dependency updates
3. If builds behave unexpectedly
4. After changing build configuration

### How to Clear All Caches
```bash
# Complete cache reset
yarn clean               # Clean build artifacts
nx reset                 # Clear Nx cache
yarn cache clean         # Clear Yarn cache
rm -rf node_modules      # Remove dependencies
yarn install             # Reinstall
yarn setup               # Full rebuild
```

## Monitoring and Debugging

### Build Performance
```bash
# Nx graph visualization
nx graph

# Analyze build
yarn build --verbose

# See what Nx is doing
NX_VERBOSE_LOGGING=true yarn build
```

### Cache Hits/Misses
- Nx shows cache hits in build output
- Green = cache hit
- Yellow = rebuilt

## Best Practices

1. **Don't commit cache directories** - Already in .gitignore
2. **Leverage Nx affected commands** - Only build what changed
3. **Use watch mode in development** - Faster feedback
4. **Clear cache if behavior is unexpected** - Fresh start
5. **Keep dependencies updated** - Better caching strategies

## Summary

This repository is now fully indexed and ready for development. All architectural patterns, algorithms, conventions, and best practices are documented in memory files. The build system is optimized with Nx caching for fast incremental builds. Future development tasks can reference these memories for quick context without re-reading the entire codebase.
