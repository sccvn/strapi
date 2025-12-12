# Content Manager API Documentation

## Overview

The Strapi Content Manager provides a powerful API for managing all content types in your application. It supports CRUD operations, filtering, sorting, pagination, and population of relations.

## Table of Contents

- [Features](#features)
- [Authentication](#authentication)
- [API Endpoints](#api-endpoints)
- [Query Parameters](#query-parameters)
- [Services](#services)
- [Permissions](#permissions)
- [Code Examples](#code-examples)
- [Error Handling](#error-handling)

## Features

- ✅ CRUD operations for all content types
- ✅ Advanced filtering and search
- ✅ Sorting and pagination
- ✅ Relation population
- ✅ Draft/publish workflow
- ✅ Localization support
- ✅ Version history
- ✅ Bulk operations
- ✅ Custom fields support

## Authentication

All Content Manager API requests require authentication using JWT tokens.

```javascript
// Get JWT token
const response = await fetch('http://localhost:1337/api/auth/local', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    identifier: 'user@example.com',
    password: 'password',
  }),
});

const { jwt } = await response.json();

// Use token in requests
const headers = {
  'Authorization': `Bearer ${jwt}`,
  'Content-Type': 'application/json',
};
```

## API Endpoints

### Get Collection Type Entries

**GET** `/api/:pluralApiId`

Retrieve multiple entries of a content type.

**Request:**

```bash
curl -X GET 'http://localhost:1337/api/articles?populate=*&sort=createdAt:desc&pagination[pageSize]=10' \
  -H 'Authorization: Bearer YOUR_JWT_TOKEN'
```

**JavaScript Example:**

```javascript
const response = await fetch(
  'http://localhost:1337/api/articles?' + new URLSearchParams({
    'populate': '*',
    'sort': 'createdAt:desc',
    'pagination[page]': '1',
    'pagination[pageSize]': '10',
  }),
  {
    headers: {
      'Authorization': `Bearer ${token}`,
    },
  }
);

const { data, meta } = await response.json();
```

**Response (200 OK):**

```json
{
  "data": [
    {
      "id": 1,
      "attributes": {
        "title": "Hello World",
        "content": "This is my first article",
        "slug": "hello-world",
        "createdAt": "2024-12-12T10:00:00.000Z",
        "updatedAt": "2024-12-12T10:00:00.000Z",
        "publishedAt": "2024-12-12T10:00:00.000Z",
        "author": {
          "data": {
            "id": 1,
            "attributes": {
              "name": "John Doe"
            }
          }
        }
      }
    }
  ],
  "meta": {
    "pagination": {
      "page": 1,
      "pageSize": 10,
      "pageCount": 5,
      "total": 42
    }
  }
}
```

### Get Single Entry

**GET** `/api/:pluralApiId/:id`

**Request:**

```bash
curl -X GET 'http://localhost:1337/api/articles/1?populate=*' \
  -H 'Authorization: Bearer YOUR_JWT_TOKEN'
```

**JavaScript Example:**

```javascript
const response = await fetch('http://localhost:1337/api/articles/1?populate=*', {
  headers: {
    'Authorization': `Bearer ${token}`,
  },
});

const { data } = await response.json();
```

**Response (200 OK):**

```json
{
  "data": {
    "id": 1,
    "attributes": {
      "title": "Hello World",
      "content": "This is my first article",
      "slug": "hello-world",
      "createdAt": "2024-12-12T10:00:00.000Z",
      "updatedAt": "2024-12-12T10:00:00.000Z",
      "publishedAt": "2024-12-12T10:00:00.000Z",
      "author": {
        "data": {
          "id": 1,
          "attributes": {
            "name": "John Doe",
            "email": "john@example.com"
          }
        }
      },
      "categories": {
        "data": [
          {
            "id": 1,
            "attributes": {
              "name": "Technology"
            }
          }
        ]
      }
    }
  }
}
```

### Create Entry

**POST** `/api/:pluralApiId`

**Request:**

```bash
curl -X POST http://localhost:1337/api/articles \
  -H 'Authorization: Bearer YOUR_JWT_TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
    "data": {
      "title": "New Article",
      "content": "Article content here",
      "slug": "new-article",
      "author": 1,
      "categories": [1, 2]
    }
  }'
```

**JavaScript Example:**

```javascript
const response = await fetch('http://localhost:1337/api/articles', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    data: {
      title: 'New Article',
      content: 'Article content here',
      slug: 'new-article',
      author: 1,
      categories: [1, 2],
    },
  }),
});

const { data } = await response.json();
```

**Response (201 Created):**

```json
{
  "data": {
    "id": 2,
    "attributes": {
      "title": "New Article",
      "content": "Article content here",
      "slug": "new-article",
      "createdAt": "2024-12-12T11:00:00.000Z",
      "updatedAt": "2024-12-12T11:00:00.000Z",
      "publishedAt": null
    }
  }
}
```

### Update Entry

**PUT** `/api/:pluralApiId/:id`

**Request:**

```bash
curl -X PUT http://localhost:1337/api/articles/1 \
  -H 'Authorization: Bearer YOUR_JWT_TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
    "data": {
      "title": "Updated Title",
      "content": "Updated content"
    }
  }'
```

**JavaScript Example:**

```javascript
const response = await fetch('http://localhost:1337/api/articles/1', {
  method: 'PUT',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    data: {
      title: 'Updated Title',
      content: 'Updated content',
    },
  }),
});

const { data } = await response.json();
```

**Response (200 OK):**

```json
{
  "data": {
    "id": 1,
    "attributes": {
      "title": "Updated Title",
      "content": "Updated content",
      "slug": "hello-world",
      "createdAt": "2024-12-12T10:00:00.000Z",
      "updatedAt": "2024-12-12T12:00:00.000Z",
      "publishedAt": "2024-12-12T10:00:00.000Z"
    }
  }
}
```

### Delete Entry

**DELETE** `/api/:pluralApiId/:id`

**Request:**

```bash
curl -X DELETE http://localhost:1337/api/articles/1 \
  -H 'Authorization: Bearer YOUR_JWT_TOKEN'
```

**JavaScript Example:**

```javascript
const response = await fetch('http://localhost:1337/api/articles/1', {
  method: 'DELETE',
  headers: {
    'Authorization': `Bearer ${token}`,
  },
});

const { data } = await response.json();
```

**Response (200 OK):**

```json
{
  "data": {
    "id": 1,
    "attributes": {
      "title": "Deleted Article",
      "deletedAt": "2024-12-12T13:00:00.000Z"
    }
  }
}
```

## Query Parameters

### Filtering

Filter entries based on field values.

**Operators:**

| Operator | Description | Example |
|----------|-------------|---------|
| `$eq` | Equal | `filters[title][$eq]=Hello` |
| `$ne` | Not equal | `filters[title][$ne]=Hello` |
| `$lt` | Less than | `filters[price][$lt]=100` |
| `$lte` | Less than or equal | `filters[price][$lte]=100` |
| `$gt` | Greater than | `filters[price][$gt]=50` |
| `$gte` | Greater than or equal | `filters[price][$gte]=50` |
| `$in` | In array | `filters[category][$in][0]=tech&filters[category][$in][1]=news` |
| `$notIn` | Not in array | `filters[category][$notIn][0]=spam` |
| `$contains` | Contains (case-sensitive) | `filters[title][$contains]=world` |
| `$notContains` | Not contains | `filters[title][$notContains]=spam` |
| `$containsi` | Contains (case-insensitive) | `filters[title][$containsi]=WORLD` |
| `$notContainsi` | Not contains (case-insensitive) | `filters[title][$notContainsi]=SPAM` |
| `$null` | Is null | `filters[publishedAt][$null]=true` |
| `$notNull` | Is not null | `filters[publishedAt][$notNull]=true` |
| `$startsWith` | Starts with | `filters[slug][$startsWith]=hello` |
| `$endsWith` | Ends with | `filters[slug][$endsWith]=world` |

**Examples:**

```javascript
// Simple filter
const url = 'http://localhost:1337/api/articles?filters[title][$eq]=Hello World';

// Multiple filters (AND)
const params = new URLSearchParams({
  'filters[title][$contains]': 'world',
  'filters[publishedAt][$notNull]': 'true',
});

// Complex filter with OR
const complexFilter = {
  filters: {
    $or: [
      { title: { $contains: 'react' } },
      { title: { $contains: 'vue' } },
    ],
  },
};

const response = await fetch(
  `http://localhost:1337/api/articles?${new URLSearchParams({
    filters: JSON.stringify(complexFilter.filters),
  })}`,
  { headers }
);
```

### Sorting

Sort entries by field values.

**Examples:**

```javascript
// Single field ascending
'?sort=title:asc'

// Single field descending
'?sort=createdAt:desc'

// Multiple fields
'?sort[0]=title:asc&sort[1]=createdAt:desc'

// Using JavaScript
const params = new URLSearchParams({
  'sort[0]': 'publishedAt:desc',
  'sort[1]': 'title:asc',
});
```

### Pagination

Control the number and page of results.

**Parameters:**

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `pagination[page]` | number | 1 | Page number |
| `pagination[pageSize]` | number | 25 | Number of entries per page |
| `pagination[start]` | number | 0 | Offset (alternative to page) |
| `pagination[limit]` | number | 25 | Limit (alternative to pageSize) |

**Examples:**

```javascript
// Page-based pagination
const params = new URLSearchParams({
  'pagination[page]': '2',
  'pagination[pageSize]': '10',
});

// Offset-based pagination
const offsetParams = new URLSearchParams({
  'pagination[start]': '20',
  'pagination[limit]': '10',
});
```

### Population

Populate relations and components.

**Examples:**

```javascript
// Populate all relations (1 level deep)
'?populate=*'

// Populate specific relation
'?populate=author'

// Populate multiple relations
'?populate[0]=author&populate[1]=categories'

// Deep populate (nested relations)
'?populate[author][populate][0]=avatar'

// Populate with filters
const params = {
  populate: {
    author: {
      fields: ['name', 'email'],
      populate: {
        avatar: true,
      },
    },
    categories: {
      filters: {
        featured: true,
      },
    },
  },
};
```

### Fields Selection

Select specific fields to return.

**Examples:**

```javascript
// Select specific fields
'?fields[0]=title&fields[1]=slug&fields[2]=createdAt'

// Using JavaScript
const params = new URLSearchParams({
  'fields[0]': 'title',
  'fields[1]': 'slug',
  'fields[2]': 'publishedAt',
});
```

## Services

### Entity Service

The Entity Service provides programmatic access to content.

```javascript
// In a Strapi controller or service

// Find many entries
const entries = await strapi.entityService.findMany('api::article.article', {
  filters: { publishedAt: { $notNull: true } },
  sort: { createdAt: 'desc' },
  populate: { author: true, categories: true },
  pagination: { page: 1, pageSize: 10 },
});

// Find one entry
const entry = await strapi.entityService.findOne('api::article.article', 1, {
  populate: { author: true },
});

// Create entry
const newEntry = await strapi.entityService.create('api::article.article', {
  data: {
    title: 'New Article',
    content: 'Content here',
    author: 1,
    publishedAt: new Date(),
  },
});

// Update entry
const updated = await strapi.entityService.update('api::article.article', 1, {
  data: {
    title: 'Updated Title',
  },
});

// Delete entry
await strapi.entityService.delete('api::article.article', 1);

// Count entries
const count = await strapi.entityService.count('api::article.article', {
  filters: { publishedAt: { $notNull: true } },
});
```

### Query Engine

For more advanced queries:

```javascript
const results = await strapi.db.query('api::article.article').findMany({
  where: {
    $and: [
      { publishedAt: { $notNull: true } },
      {
        $or: [
          { title: { $containsi: 'react' } },
          { content: { $containsi: 'react' } },
        ],
      },
    ],
  },
  orderBy: { publishedAt: 'desc' },
  populate: {
    author: {
      select: ['name', 'email'],
      populate: { avatar: true },
    },
  },
  offset: 0,
  limit: 10,
});
```

## Permissions

### Role-Based Access Control

```javascript
// Check permissions in controller
module.exports = {
  async find(ctx) {
    // Permission check is automatic via policies
    const entries = await strapi.entityService.findMany('api::article.article', {
      ...ctx.query,
    });
    
    return entries;
  },
  
  async create(ctx) {
    // Check custom permission
    if (!ctx.state.user.canCreate) {
      return ctx.forbidden('You cannot create articles');
    }
    
    const entry = await strapi.entityService.create('api::article.article', {
      data: ctx.request.body.data,
    });
    
    return entry;
  },
};
```

### Owner-Based Permissions

```javascript
// Filter by owner
const userArticles = await strapi.entityService.findMany('api::article.article', {
  filters: {
    author: ctx.state.user.id,
  },
});

// Check ownership before update
const article = await strapi.entityService.findOne('api::article.article', articleId);

if (article.author.id !== ctx.state.user.id) {
  return ctx.forbidden('You can only edit your own articles');
}
```

## Code Examples

### Complete CRUD Operations

```javascript
class ArticleService {
  constructor(token) {
    this.token = token;
    this.baseUrl = 'http://localhost:1337/api/articles';
    this.headers = {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json',
    };
  }

  async getAll(options = {}) {
    const params = new URLSearchParams({
      'populate': '*',
      'sort': 'createdAt:desc',
      'pagination[page]': options.page || '1',
      'pagination[pageSize]': options.pageSize || '25',
      ...options.filters,
    });

    const response = await fetch(`${this.baseUrl}?${params}`, {
      headers: this.headers,
    });

    if (!response.ok) {
      throw new Error(`Failed to fetch articles: ${response.statusText}`);
    }

    return response.json();
  }

  async getOne(id) {
    const response = await fetch(`${this.baseUrl}/${id}?populate=*`, {
      headers: this.headers,
    });

    if (!response.ok) {
      throw new Error(`Failed to fetch article: ${response.statusText}`);
    }

    return response.json();
  }

  async create(data) {
    const response = await fetch(this.baseUrl, {
      method: 'POST',
      headers: this.headers,
      body: JSON.stringify({ data }),
    });

    if (!response.ok) {
      throw new Error(`Failed to create article: ${response.statusText}`);
    }

    return response.json();
  }

  async update(id, data) {
    const response = await fetch(`${this.baseUrl}/${id}`, {
      method: 'PUT',
      headers: this.headers,
      body: JSON.stringify({ data }),
    });

    if (!response.ok) {
      throw new Error(`Failed to update article: ${response.statusText}`);
    }

    return response.json();
  }

  async delete(id) {
    const response = await fetch(`${this.baseUrl}/${id}`, {
      method: 'DELETE',
      headers: this.headers,
    });

    if (!response.ok) {
      throw new Error(`Failed to delete article: ${response.statusText}`);
    }

    return response.json();
  }

  async search(query) {
    const params = new URLSearchParams({
      'filters[$or][0][title][$containsi]': query,
      'filters[$or][1][content][$containsi]': query,
      'populate': '*',
    });

    const response = await fetch(`${this.baseUrl}?${params}`, {
      headers: this.headers,
    });

    return response.json();
  }
}

// Usage
const articleService = new ArticleService(token);

// Get all articles
const { data, meta } = await articleService.getAll({
  page: 1,
  pageSize: 10,
});

// Search articles
const searchResults = await articleService.search('react');

// Create article
const newArticle = await articleService.create({
  title: 'My Article',
  content: 'Article content',
  author: 1,
});

// Update article
await articleService.update(1, {
  title: 'Updated Title',
});

// Delete article
await articleService.delete(1);
```

### React Hook for Content Management

```javascript
import { useState, useEffect } from 'react';

function useContent(contentType, options = {}) {
  const [data, setData] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  const [pagination, setPagination] = useState(null);

  const fetchContent = async () => {
    setLoading(true);
    setError(null);

    try {
      const params = new URLSearchParams({
        'populate': options.populate || '*',
        'sort': options.sort || 'createdAt:desc',
        'pagination[page]': options.page || '1',
        'pagination[pageSize]': options.pageSize || '25',
      });

      if (options.filters) {
        Object.entries(options.filters).forEach(([key, value]) => {
          params.append(key, value);
        });
      }

      const response = await fetch(
        `http://localhost:1337/api/${contentType}?${params}`,
        {
          headers: {
            'Authorization': `Bearer ${localStorage.getItem('token')}`,
          },
        }
      );

      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }

      const result = await response.json();
      setData(result.data);
      setPagination(result.meta?.pagination);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    fetchContent();
  }, [contentType, JSON.stringify(options)]);

  return { data, loading, error, pagination, refetch: fetchContent };
}

// Usage in component
function ArticleList() {
  const { data, loading, error, pagination, refetch } = useContent('articles', {
    page: 1,
    pageSize: 10,
    sort: 'publishedAt:desc',
    filters: {
      'filters[publishedAt][$notNull]': 'true',
    },
  });

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div>
      {data.map(article => (
        <div key={article.id}>
          <h2>{article.attributes.title}</h2>
          <p>{article.attributes.content}</p>
        </div>
      ))}
      
      {pagination && (
        <div>
          Page {pagination.page} of {pagination.pageCount}
        </div>
      )}
    </div>
  );
}
```

## Error Handling

### Common Errors

| Status Code | Error | Description |
|-------------|-------|-------------|
| 400 | Bad Request | Invalid request data or parameters |
| 401 | Unauthorized | Missing or invalid authentication |
| 403 | Forbidden | Insufficient permissions |
| 404 | Not Found | Entry or content type doesn't exist |
| 500 | Internal Server Error | Server error |

### Error Response Format

```json
{
  "error": {
    "status": 400,
    "name": "ValidationError",
    "message": "Invalid request data",
    "details": {
      "errors": [
        {
          "path": ["title"],
          "message": "Title is required",
          "name": "ValidationError"
        }
      ]
    }
  }
}
```

### Handling Errors

```javascript
async function safeApiCall(url, options) {
  try {
    const response = await fetch(url, options);
    
    if (!response.ok) {
      const error = await response.json();
      
      switch (error.error.status) {
        case 400:
          console.error('Validation errors:', error.error.details);
          break;
        case 401:
          // Redirect to login
          window.location.href = '/login';
          break;
        case 403:
          alert('You do not have permission to perform this action');
          break;
        case 404:
          alert('Content not found');
          break;
        default:
          alert('An error occurred. Please try again.');
      }
      
      throw error;
    }
    
    return response.json();
  } catch (err) {
    console.error('API call failed:', err);
    throw err;
  }
}
```

## Best Practices

1. **Always populate only what you need** to reduce response size
2. **Use pagination** for large datasets
3. **Implement proper error handling** for all API calls
4. **Cache responses** when appropriate
5. **Use filters** to reduce data transfer
6. **Validate data** before sending to API
7. **Handle loading states** in UI
8. **Implement retry logic** for failed requests
9. **Use field selection** to get only required fields
10. **Monitor API performance** and optimize queries

## Related Documentation

- [Upload API](./upload-api.md)
- [Authentication API](./authentication-api.md)
- [Content Management User Guide](../user-guides/content-management.md)
- [Database Schema](../architecture/database-schema.md)

---

**Last Updated**: December 12, 2024  
**API Version**: v5.x  
**Module**: @strapi/plugin-content-manager
