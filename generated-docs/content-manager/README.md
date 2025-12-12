# Strapi Content Manager - Complete Documentation

Comprehensive technical documentation, API reference, and user guides for the Strapi Content Manager plugin.

**Version**: 5.31.3  
**Last Updated**: December 12, 2025  
**Plugin**: `@strapi/plugin-content-manager`

---

## 📚 Documentation Overview

This directory contains complete documentation for the Strapi Content Manager feature, organized for different audiences:

### For All Users
- **[00-overview.md](./00-overview.md)** - High-level overview, features, and architecture

### For Content Editors & Administrators
- **[02-user-guide.md](./02-user-guide.md)** - Step-by-step guide for managing content
  - Creating and editing content
  - Publishing workflows
  - Localization
  - Media management
  - Relations and components
  - Troubleshooting

### For Developers
- **[01-api-reference.md](./01-api-reference.md)** - Complete REST API documentation
  - All endpoints with examples
  - Request/response formats
  - Query parameters
  - Error handling
  
- **[03-developer-guide.md](./03-developer-guide.md)** - Developer integration guide
  - Service API documentation
  - Extending Content Manager
  - Permission system
  - Hooks and lifecycles
  - Testing strategies
  - Code examples

### For Architects
- **[04-architecture.md](./04-architecture.md)** - System architecture documentation
  - C4 diagrams (Context, Container, Component)
  - Sequence diagrams
  - Data models and ERD
  - Design patterns
  - Security architecture
  - Performance optimization

---

## 🎯 Quick Start

### For Content Editors

**Creating Your First Article:**

1. Navigate to **Content Manager** in the sidebar
2. Click **Collection Types → Articles**
3. Click **"+ Create new entry"**
4. Fill in the form:
   - Title: "My First Article"
   - Content: Your article text
   - Author: Select yourself
5. Click **"Save"** to create a draft
6. Click **"Publish"** when ready

See: [User Guide](./02-user-guide.md#creating-new-content)

---

### For Developers

**Fetching Content via API:**

```bash
# Get all published articles
curl -X GET "http://localhost:1337/api/articles?publicationState=live" \
  -H "Authorization: Bearer YOUR_API_TOKEN"

# Get a specific article
curl -X GET "http://localhost:1337/api/articles/1" \
  -H "Authorization: Bearer YOUR_API_TOKEN"
```

**Using the Document Manager Service:**

```typescript
// In your Strapi code
const articles = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .findMany(
    {
      filters: { title: { $contains: 'Strapi' } },
      sort: 'publishedAt:desc',
      populate: { author: true },
      status: 'published'
    },
    'api::article.article'
  );

console.log(articles);
```

See: [Developer Guide](./03-developer-guide.md) and [API Reference](./01-api-reference.md)

---

## 🔑 Key Features

### ✅ Content Management
- **CRUD Operations**: Create, Read, Update, Delete for all content types
- **Draft/Publish Workflow**: Save drafts, publish when ready
- **Version History**: Track changes over time
- **Bulk Operations**: Publish, delete, or modify multiple entries at once

### 🌍 Localization (i18n)
- **Multi-language Support**: Create content in multiple locales
- **Locale-specific Drafts**: Each locale can have draft and published versions
- **Locale Switching**: Easy navigation between translations

### 🔐 Permissions & Security
- **Role-Based Access Control (RBAC)**: Fine-grained permissions
- **Field-Level Permissions**: Control access to specific fields
- **Conditional Permissions**: Dynamic rules based on content
- **JWT Authentication**: Secure API access

### 📸 Media Management
- **File Upload**: Images, videos, documents
- **Media Library**: Organize and reuse assets
- **Multiple Providers**: Local, S3, Cloudinary, etc.

### 🔗 Relations & Components
- **All Relation Types**: One-to-One, One-to-Many, Many-to-Many
- **Reusable Components**: Build modular content
- **Dynamic Zones**: Mix different component types
- **Nested Components**: Deep component structures

### 🚀 Performance
- **Optimized Queries**: Efficient database operations
- **Pagination**: Handle large datasets
- **Selective Population**: Load only what you need
- **Caching Support**: Redis, CDN integration

---

## 📖 Documentation Index

### Getting Started
- [Overview & Architecture](./00-overview.md#architecture-overview)
- [Core Features](./00-overview.md#core-features)
- [Quick Setup](./02-user-guide.md#getting-started)

### Content Management
- [Creating Content](./02-user-guide.md#creating-new-content)
- [Editing Content](./02-user-guide.md#editing-content)
- [Publishing Workflow](./02-user-guide.md#working-with-drafts)
- [Bulk Operations](./02-user-guide.md#bulk-operations)

### Localization
- [Multi-language Content](./02-user-guide.md#localization-i18n)
- [Creating Locales](./02-user-guide.md#creating-localized-content)
- [Managing Translations](./02-user-guide.md#switching-between-locales)

### API Usage
- [REST API Endpoints](./01-api-reference.md#collection-types-api)
- [Query Parameters](./01-api-reference.md#common-parameters)
- [Filtering & Sorting](./01-api-reference.md#filter-parameter)
- [Pagination](./01-api-reference.md#common-parameters)

### Development
- [Service API](./03-developer-guide.md#service-api)
- [Extending Content Manager](./03-developer-guide.md#extending-content-manager)
- [Permission System](./03-developer-guide.md#permission-system)
- [Hooks & Lifecycles](./03-developer-guide.md#hooks-and-lifecycles)
- [Testing](./03-developer-guide.md#testing)

### Architecture
- [System Architecture](./04-architecture.md#system-architecture)
- [Component Diagrams](./04-architecture.md#component-diagrams)
- [Data Models](./04-architecture.md#data-models)
- [Design Patterns](./04-architecture.md#design-patterns)
- [Security Architecture](./04-architecture.md#security-architecture)

### Troubleshooting
- [Common Issues](./02-user-guide.md#troubleshooting)
- [Permission Problems](./02-user-guide.md#cannot-publish-content)
- [API Errors](./01-api-reference.md#error-responses)
- [Performance Issues](./03-developer-guide.md#performance-optimization)

---

## 🔧 Technical Stack

| Component | Technology |
|-----------|-----------|
| **Backend** | Node.js 18+, TypeScript |
| **Framework** | Koa.js |
| **Frontend** | React 18, TypeScript |
| **Database** | PostgreSQL, MySQL, SQLite, MongoDB |
| **Validation** | Yup |
| **Testing** | Jest, React Testing Library, Playwright |
| **API** | REST, GraphQL |

---

## 📚 API Quick Reference

### Collection Types

```bash
# List all documents
GET /content-manager/collection-types/:model

# Get single document
GET /content-manager/collection-types/:model/:id

# Create document
POST /content-manager/collection-types/:model

# Update document
PUT /content-manager/collection-types/:model/:id

# Delete document
DELETE /content-manager/collection-types/:model/:id

# Publish document
POST /content-manager/collection-types/:model/:id/actions/publish

# Unpublish document
POST /content-manager/collection-types/:model/:id/actions/unpublish

# Clone document
POST /content-manager/collection-types/:model/clone/:id

# Bulk actions
POST /content-manager/collection-types/:model/actions/bulkDelete
POST /content-manager/collection-types/:model/actions/bulkPublish
```

### Single Types

```bash
# Get single type
GET /content-manager/single-types/:model

# Update single type
PUT /content-manager/single-types/:model

# Publish single type
POST /content-manager/single-types/:model/actions/publish

# Unpublish single type
POST /content-manager/single-types/:model/actions/unpublish
```

See: [Complete API Reference](./01-api-reference.md)

---

## 💡 Common Use Cases

### Use Case 1: Blog Platform

```typescript
// Fetch latest published blog posts
const posts = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .findPage(
    {
      page: 1,
      pageSize: 10,
      sort: 'publishedAt:desc',
      filters: { publishedAt: { $notNull: true } },
      populate: { 
        author: { 
          fields: ['firstname', 'lastname', 'email'],
          populate: { avatar: true }
        },
        categories: true,
        coverImage: true
      },
      status: 'published'
    },
    'api::article.article'
  );

// Access results
posts.results.forEach(post => {
  console.log(post.title);
  console.log(post.author.firstname);
  console.log(post.categories.map(c => c.name));
});

// Pagination info
console.log(posts.pagination);
// { page: 1, pageSize: 10, pageCount: 5, total: 42 }
```

---

### Use Case 2: E-commerce Product Catalog

```typescript
// Filter products by category and price
const products = await strapi
  .plugin('content-manager')
  .service('document-manager')
  .findMany(
    {
      filters: {
        category: { id: { $in: [1, 2, 3] } },
        price: { $gte: 10, $lte: 100 },
        stock: { $gt: 0 }
      },
      sort: 'price:asc',
      populate: {
        images: true,
        category: { fields: ['name'] },
        variants: true
      },
      status: 'published'
    },
    'api::product.product'
  );

// Group by category
const byCategory = products.reduce((acc, product) => {
  const catName = product.category.name;
  acc[catName] = acc[catName] || [];
  acc[catName].push(product);
  return acc;
}, {});
```

---

### Use Case 3: Multi-language Documentation

```typescript
// Get documentation in all available locales
const locales = ['en', 'fr', 'es', 'de'];
const docs = {};

for (const locale of locales) {
  docs[locale] = await strapi
    .plugin('content-manager')
    .service('document-manager')
    .findOne('homepage', 'api::page.page', {
      locale,
      status: 'published',
      populate: { 
        sections: { 
          populate: { 
            content: true 
          } 
        } 
      }
    });
}

// Access localized content
console.log(docs.en.title); // "Welcome"
console.log(docs.fr.title); // "Bienvenue"
console.log(docs.es.title); // "Bienvenido"
```

---

## 🔐 Security Best Practices

### 1. API Token Security

```typescript
// ✅ GOOD: Use environment variables
const token = process.env.STRAPI_API_TOKEN;

// ❌ BAD: Hardcoded tokens
const token = 'abc123...'; // Never do this!
```

---

### 2. Permission Validation

```typescript
// ✅ GOOD: Always check permissions
const checker = strapi.plugin('content-manager')
  .service('permission-checker')
  .create({ userAbility, model });

if (checker.cannot.update(document)) {
  throw new ForbiddenError();
}

// ❌ BAD: Skip permission checks
// await update(document); // Dangerous!
```

---

### 3. Input Sanitization

```typescript
// ✅ GOOD: Sanitize user input
const sanitized = await permissionChecker.sanitizeCreateInput(data);
await create(sanitized);

// ❌ BAD: Trust user input
// await create(req.body); // Vulnerable!
```

---

## 🚀 Performance Tips

### 1. Use Pagination

```typescript
// ✅ GOOD: Paginate large result sets
const { results, pagination } = await findPage({
  page: 1,
  pageSize: 20
}, uid);

// ❌ BAD: Fetch all records
// const all = await findMany({}, uid); // Could be thousands!
```

---

### 2. Selective Population

```typescript
// ✅ GOOD: Populate only what you need
const article = await findOne(id, uid, {
  populate: {
    author: {
      fields: ['id', 'name'],
      populate: { avatar: { fields: ['url'] } }
    }
  }
});

// ❌ BAD: Deep population
// populate: '*' or deep: 5 // Expensive!
```

---

### 3. Database Indexing

```json
// schema.json
{
  "attributes": {
    "slug": {
      "type": "string",
      "unique": true  // Creates index
    },
    "publishedAt": {
      "type": "datetime"  // Auto-indexed
    }
  }
}
```

---

## 📊 Migration Guides

### Migrating from Strapi v4 to v5

**Key Changes:**

1. **Entity Service → Document Service**
   ```typescript
   // v4
   strapi.entityService.findOne(...)
   
   // v5
   strapi.documents(uid).findOne(...)
   ```

2. **Draft/Publish Behavior**
   - v4: Content published by default
   - v5: Content created as draft, explicit publish required

3. **Locale Handling**
   - v4: Locale as query filter
   - v5: Locale as parameter

See: [Complete Migration Guide](./03-developer-guide.md#migration-guide)

---

## 🤝 Contributing

Found an issue or want to improve the documentation?

1. **Report Issues**: [GitHub Issues](https://github.com/strapi/strapi/issues)
2. **Suggest Improvements**: [Forum](https://forum.strapi.io)
3. **Join Community**: [Discord](https://discord.strapi.io)

---

## 📞 Support

### Community Support
- 📚 **Documentation**: https://docs.strapi.io
- 💬 **Forum**: https://forum.strapi.io
- 💭 **Discord**: https://discord.strapi.io
- 🐛 **GitHub**: https://github.com/strapi/strapi

### Enterprise Support
- 📧 **Email**: support@strapi.io
- 🏢 **Enterprise Plans**: https://strapi.io/pricing

---

## 📝 Changelog

### v5.31.3 (Current)
- Documentation updates
- Bug fixes for file upload
- Performance improvements

### v5.x Major Changes
- Document Service API
- Enhanced draft/publish workflow
- Improved localization
- Better TypeScript support

See: [Full Changelog](https://github.com/strapi/strapi/releases)

---

## 📄 License

Strapi is licensed under the MIT License.

---

## 🌟 Resources

### Official Links
- **Website**: https://strapi.io
- **Documentation**: https://docs.strapi.io
- **Blog**: https://strapi.io/blog
- **GitHub**: https://github.com/strapi/strapi

### Tutorials
- [Building a Blog with Strapi](https://docs.strapi.io/dev-docs/quick-start)
- [E-commerce with Strapi](https://strapi.io/blog/strapi-ecommerce-tutorial)
- [Multi-language Sites](https://docs.strapi.io/dev-docs/i18n)

### Community
- [Awesome Strapi](https://github.com/strapi/awesome-strapi)
- [Community Plugins](https://market.strapi.io)
- [User Showcase](https://strapi.io/showcase)

---

**Last Updated**: December 12, 2025  
**Documentation Version**: 1.0.0  
**Strapi Version**: 5.31.3

---

## 📖 Table of Contents

- [Overview](./00-overview.md)
- [API Reference](./01-api-reference.md)
- [User Guide](./02-user-guide.md)
- [Developer Guide](./03-developer-guide.md)
- [Architecture](./04-architecture.md)

**Happy Content Managing! 🚀**
