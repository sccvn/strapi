# Code Style and Conventions

## TypeScript Configuration
- **Target**: ES2020
- **Module**: ESNext with CommonJS output
- **Strict mode**: Enabled in most packages
- **JSX**: React runtime (automatic)
- Each package has its own `tsconfig.json` extending from base configs

## Code Formatting (Prettier)

Configuration in `.prettierrc.js`:
```javascript
{
  endOfLine: 'lf',
  semi: true,
  singleQuote: true,
  tabWidth: 2,
  trailingComma: 'es5',
  printWidth: 100,
  arrowParens: 'always',
}
```

### Key Rules
- **Line endings**: LF (Unix style)
- **Semicolons**: Required
- **Quotes**: Single quotes for strings
- **Indentation**: 2 spaces
- **Trailing commas**: ES5 style (no trailing comma in function parameters)
- **Max line length**: 100 characters
- **Arrow functions**: Always use parentheses for parameters

## ESLint Configuration

Base: Airbnb + TypeScript + React
- `@strapi/eslint-config` package provides shared config
- Airbnb style guide extended
- TypeScript-specific rules
- React and React Hooks rules
- Prettier integration (no conflicts)

### Notable Plugins
- `eslint-plugin-import` - Import/export validation
- `eslint-plugin-react` - React best practices
- `eslint-plugin-react-hooks` - Hooks rules
- `eslint-plugin-check-file` - File naming conventions
- `eslint-plugin-testing-library` - Testing rules
- `eslint-plugin-jest-dom` - Jest DOM rules

## Naming Conventions

### Files and Directories
- **kebab-case** for most files and directories
- **PascalCase** for React components (e.g., `StrapiApp.tsx`)
- **camelCase** for utilities and services
- Test files: `*.test.ts`, `*.test.js`, `*.test.api.js`
- Config files: Descriptive names (e.g., `jest.config.js`, `rollup.config.mjs`)

### Code Naming
- **camelCase** for variables, functions, methods
- **PascalCase** for classes, React components, TypeScript types/interfaces
- **UPPER_SNAKE_CASE** for constants
- **Prefix with `_`** for private/internal members (convention, not enforced)

### Package Naming
- Scoped packages: `@strapi/package-name`
- Core packages: `@strapi/strapi`, `@strapi/admin`, `@strapi/database`
- Plugins: `@strapi/plugin-*` (e.g., `@strapi/plugin-i18n`)
- Providers: `@strapi/provider-*` (e.g., `@strapi/provider-upload-aws-s3`)

## Documentation Conventions

### Code Comments
- **JSDoc** for public APIs, interfaces, and complex functions
- Type annotations preferred over comments where TypeScript is sufficient
- TODO comments: `// TODO: description`
- Deprecated markers: `@deprecated` JSDoc tag

### Type Definitions
- Strong typing preferred
- Use `@strapi/types` package for shared types
- Define interfaces for public APIs
- Use type inference where obvious
- Avoid `any` - use `unknown` if type is truly unknown

## Git Conventions

### Commit Messages
- **Commitlint** with conventional commits
- Format: `<type>(<scope>): <subject>`
- Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`
- Scope: Package or feature name
- Example: `feat(content-manager): add bulk delete functionality`

### Branches
- `main` - Stable release branch
- `develop` - Development branch (active development happens here)
- Feature branches: `feat/description`
- Fix branches: `fix/description`
- Fork from `develop`, PR back to `develop`

### Pull Requests
- Must pass all CI checks
- Must be signed with CLA (Contributor License Agreement)
- Code review required
- Tests required for new features
- Update documentation if needed

## Import/Export Conventions

### Import Order (enforced by ESLint)
1. Node.js built-in modules
2. External dependencies
3. Internal @strapi packages
4. Relative imports
5. Type imports (if using `import type`)

### Module Exports
- Use named exports for utilities and services
- Default export for main class or component
- Barrel exports in `index.ts` files for clean API surface
- Package exports defined in `package.json` `exports` field

## Testing Conventions

### File Organization
- Test files co-located with source: `__tests__/` directory or `*.test.ts` suffix
- Mock files: `__mocks__/` directory
- Test utilities: `tests/` at root or in admin-test-utils

### Test Structure
- **Describe blocks** for grouping related tests
- **It/test blocks** for individual test cases
- **Arrange-Act-Assert** pattern
- **Factory functions** for test data creation

### Naming
- Test descriptions: Plain English, descriptive
- Example: `it('should create a new user with valid data')`
- Mock files: Same name as mocked module
