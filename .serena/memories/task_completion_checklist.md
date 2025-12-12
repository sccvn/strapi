# Task Completion Checklist

When completing any development task in the Strapi repository, follow these steps:

## 1. Code Quality Checks

### Run Linting
```bash
yarn lint
```
- Fix any linting errors
- If auto-fix is safe: `yarn lint:fix`
- Ensure no ESLint errors remain

### Format Code
```bash
yarn format
```
- Formats all code according to Prettier config
- Alternatively, format only changed files (lint-staged does this automatically)

### Type Checking
```bash
yarn test:ts
```
- Ensure TypeScript compiles without errors
- Check type definitions are correct

## 2. Testing

### Run Relevant Tests

For **code changes**:
```bash
# Unit tests for affected packages
yarn test:unit

# If frontend changes
yarn test:front

# Update snapshots if needed (and verified)
yarn test:unit:update
```

For **API or integration changes**:
```bash
# Run API tests
yarn test:api
```

For **significant features**:
```bash
# Run E2E tests (may need Playwright installation first)
yarn playwright install  # First time only
yarn test:e2e --setup --concurrency=1
```

### Verify Tests Pass
- All relevant test suites must pass
- No skipped tests unless documented
- Coverage should not decrease significantly

## 3. Build Verification

### Build the Project
```bash
yarn build
```
- Ensure build completes without errors
- Check for any warnings that need addressing

### For Package-Specific Changes
```bash
# Build only affected package(s)
nx run @strapi/package-name:build
```

## 4. Documentation

### Update Documentation If Needed
- **Code comments**: Add/update JSDoc for public APIs
- **README files**: Update if behavior changes
- **Docs**: Update `docs/` if user-facing changes
- **Changelog**: Document breaking changes

### Check for Deprecated APIs
- If deprecating APIs, add `@deprecated` tags
- Provide migration path in comments

## 5. Git Workflow

### Commit Changes
```bash
# Stage changes
git add <files>

# Commit with conventional commit format
yarn commit
# OR manually:
git commit -m "type(scope): description"
```

Commit message format:
- `feat(scope)`: New feature
- `fix(scope)`: Bug fix
- `docs(scope)`: Documentation changes
- `refactor(scope)`: Code refactoring
- `test(scope)`: Test updates
- `chore(scope)`: Build/tooling changes

### Before Pushing
```bash
# Ensure on correct branch (develop)
git checkout develop
git pull origin develop

# Rebase if needed
git checkout your-branch
git rebase develop

# Push
git push origin your-branch
```

## 6. Pull Request Checklist

Before creating PR:
- [ ] All tests pass locally
- [ ] Code is linted and formatted
- [ ] Types are correct (TypeScript compiles)
- [ ] Build succeeds
- [ ] Documentation updated
- [ ] Commit messages follow conventions
- [ ] Branch is up-to-date with develop

PR Description should include:
- Summary of changes
- Related issue number (if any)
- Breaking changes (if any)
- How to test the changes
- Screenshots (if UI changes)

## 7. CI/CD Checks

After pushing, ensure CI passes:
- [ ] Linting checks
- [ ] Type checks
- [ ] Unit tests
- [ ] Integration tests
- [ ] Build tests

## 8. Review Process

- [ ] Sign CLA (first-time contributors)
- [ ] Address review comments
- [ ] Keep PR focused and small
- [ ] Rebase if requested
- [ ] Be responsive to feedback

## Quick Pre-Push Checklist

For a quick verification before pushing:

```bash
# 1. Format and lint
yarn format && yarn lint

# 2. Run unit tests
yarn test:unit

# 3. Build
yarn build

# 4. Commit and push
git add .
yarn commit
git push
```

## Package-Specific Tasks

When working on specific packages:

### Core Packages (`packages/core/*`)
- Test with example apps: `cd examples/getstarted && yarn develop`
- Verify backward compatibility
- Check for breaking changes

### Admin Panel (`packages/core/admin`)
- Run frontend tests: `yarn test:front`
- Check UI in browser
- Verify accessibility

### Plugins (`packages/plugins/*`)
- Test plugin in example app
- Update plugin documentation
- Check dependencies

### Providers (`packages/providers/*`)
- Test with actual service (AWS, etc.)
- Update provider docs
- Check environment variables

## Performance Considerations

- [ ] No unnecessary re-renders (React)
- [ ] Database queries optimized
- [ ] No memory leaks
- [ ] Bundle size impact acceptable

## Security Considerations

- [ ] No sensitive data in logs
- [ ] Input validation added
- [ ] SQL injection prevention
- [ ] XSS prevention (frontend)
- [ ] Dependencies updated and scanned
