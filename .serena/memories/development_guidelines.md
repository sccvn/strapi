# Development Guidelines and Best Practices

## Plugin Development

### Plugin Structure
```
my-plugin/
├── strapi-server.js      # Server-side entry
├── strapi-admin.js       # Admin panel entry
├── server/
│   ├── controllers/
│   ├── services/
│   ├── routes/
│   ├── policies/
│   └── middlewares/
├── admin/
│   └── src/
│       ├── index.tsx
│       ├── components/
│       └── pages/
└── package.json
```

### Plugin Lifecycle
1. **Register Phase**: Register services, controllers, content types
2. **Bootstrap Phase**: Initialize, set up cron jobs, event listeners

### Custom Field Development
- Register in `register()` lifecycle
- Define both server and admin implementations
- Provide data type mapping

## API Development

### RESTful API Patterns
- **Collection endpoints**: `/api/articles`
- **Single resource**: `/api/articles/:id`
- **Relations**: `/api/articles/:id/author`
- **Nested routes**: Use populate parameter instead

### Query Parameters
- `filters` - Filter results
- `populate` - Load relations
- `sort` - Sort results
- `pagination` - Paginate results
- `fields` - Select specific fields
- `publicationState` - Draft/published filter

### Response Format
```typescript
{
  data: { ... },           // Single entity or array
  meta: {
    pagination: { ... }    // If paginated
  }
}
```

## Database Best Practices

### Transaction Usage
- Always use transactions for multi-step operations
- Use `trx` parameter in entity service methods
- Rollback on any error

### Query Optimization
- Avoid N+1 queries - use populate wisely
- Use select to limit fields
- Index frequently queried fields
- Batch operations when possible

### Schema Design
- Keep components focused and reusable
- Use relations appropriately (oneToMany, manyToMany)
- Consider cascade delete implications
- Use unique constraints where needed

## Frontend (Admin Panel) Development

### React Best Practices
- Use hooks, avoid class components
- Keep components small and focused
- Use Design System components
- Memoize expensive computations
- Avoid inline function definitions in JSX

### State Management
- Use Redux for global state
- Use RTK Query for API calls
- Local state for component-specific data
- Context for theme, locale

### Styling
- Use styled-components
- Follow Design System tokens
- Responsive design
- Accessibility (ARIA labels, keyboard navigation)

### Internationalization
- Use `useIntl()` hook
- Define translations in `en.json`, etc.
- Support RTL languages

## Security Best Practices

### Input Validation
- Validate all user input
- Use entity validator
- Sanitize before database operations
- Use prepared statements (ORM handles this)

### Authentication & Authorization
- Always check permissions in controllers
- Use `strapi.auth.verify()` for custom auth
- Don't expose sensitive data in API responses
- Rate limiting for public endpoints

### Data Sanitization
- Use built-in sanitizers
- Remove forbidden fields before output
- Validate file uploads
- XSS prevention in admin panel

## Performance Best Practices

### Backend
- Use database indexes
- Implement caching where appropriate
- Lazy load relations
- Use streaming for large datasets
- Connection pooling (configured in database)

### Frontend
- Code splitting (lazy loading routes)
- Image optimization
- Debounce/throttle user input
- Virtual scrolling for long lists
- Memoization (React.memo, useMemo)

## Testing Best Practices

### Unit Tests
- Test business logic in services
- Mock external dependencies
- Use factories for test data
- Test edge cases and error handling

### Integration Tests
- Test API endpoints
- Use test database
- Clean database between tests
- Test permissions

### E2E Tests
- Test critical user flows
- Use Playwright
- Run in CI/CD pipeline
- Keep tests fast and reliable

## Error Handling

### Server-Side
- Use custom error classes from `@strapi/utils/errors`
- Provide meaningful error messages
- Log errors appropriately
- Don't expose sensitive info in production

### Client-Side
- Display user-friendly error messages
- Log errors to console in development
- Use error boundaries in React
- Provide recovery options

## Migration Best Practices

### Content Type Changes
- Test migrations on copy of production data
- Provide rollback capability
- Document breaking changes
- Version migrations chronologically

### Data Migrations
- Use batching for large datasets
- Provide progress logging
- Make idempotent
- Test thoroughly before production

## Configuration Management

### Environment Variables
- Use `.env` files (gitignored)
- Provide `.env.example` template
- Document all variables
- Use different configs per environment

### Configuration Files
- Keep production configs minimal
- Override defaults only when needed
- Document non-obvious settings
- Use TypeScript for type safety

## Monorepo Workflow

### Working Across Packages
- Use `yarn` for workspace operations
- Build dependencies before dependent packages
- Use Nx caching for faster builds
- Link packages in development

### Publishing
- Use Lerna for version management
- Follow semantic versioning
- Update CHANGELOGs
- Test before publishing

## Code Review Guidelines

### As Author
- Keep PRs focused and small
- Write descriptive PR descriptions
- Self-review before requesting review
- Respond promptly to feedback

### As Reviewer
- Be constructive and respectful
- Check for security issues
- Verify tests are adequate
- Ensure documentation is updated
- Test locally if significant change

## Deprecation Process

### Marking as Deprecated
1. Add `@deprecated` JSDoc tag
2. Provide migration path in docs
3. Add console warning
4. Update CHANGELOG
5. Remove in next major version

### Migration Support
- Provide automated migration tools when possible
- Document step-by-step migration
- Support old APIs for at least one major version
- Provide deprecation timeline

## Accessibility (a11y)

### Admin Panel
- Use semantic HTML
- Provide ARIA labels
- Keyboard navigation support
- Screen reader compatibility
- Sufficient color contrast
- Focus indicators

## Internationalization (i18n)

### Admin Panel
- Extract all strings to translation files
- Support pluralization
- Format dates/numbers per locale
- Test with RTL languages

### Content
- Use i18n plugin for content translation
- Design schema for translated content
- Consider locale fallbacks

## Documentation

### Code Documentation
- JSDoc for public APIs
- Inline comments for complex logic
- README for each package
- Architecture decision records (ADRs)

### User Documentation
- Keep docs in sync with code
- Provide examples
- Include troubleshooting section
- API reference auto-generated from code
