# Content Manager API Reference

Complete REST API documentation for Strapi Content Manager plugin.

**Base URL**: `/content-manager`  
**Version**: 5.31.3  
**Protocol**: HTTP/HTTPS  
**Authentication**: JWT Bearer Token

---

## Table of Contents

1. [Authentication](#authentication)
2. [Content Types API](#content-types-api)
3. [Collection Types API](#collection-types-api)
4. [Single Types API](#single-types-api)
5. [Components API](#components-api)
6. [Relations API](#relations-api)
7. [History API](#history-api)
8. [Preview API](#preview-api)
9. [Common Parameters](#common-parameters)
10. [Error Responses](#error-responses)

---

## Authentication

All Content Manager API endpoints require authentication via JWT token.

### Headers

```http
Authorization: Bearer <jwt_token>
Content-Type: application/json
```

### Example

```bash
curl -H "Authorization: Bearer eyJhbGc..." \
     -H "Content-Type: application/json" \
     http://localhost:1337/content-manager/collection-types/api::article.article
```

---

## Content Types API

Manage content type metadata and configuration.

### List Content Types

**Endpoint**: `GET /content-manager/content-types`

**Description**: Retrieve all content types available to the user.

**Query Parameters**:
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kind` | string | No | Filter by kind: `collectionType` or `singleType` |

**Response**: `200 OK`

```json
{
  "data": [
    {
      "uid": "api::article.article",
      "apiID": "article",
      "schema": {
        "displayName": "Article",
        "singularName": "article",
        "pluralName": "articles",
        "kind": "collectionType",
        "attributes": {
          "title": {
            "type": "string",
            "required": true
          },
          "content": {
            "type": "richtext"
          },
          "publishedAt": {
            "type": "datetime"
          }
        }
      },
      "isDisplayed": true
    }
  ]
}
```

**Example**:

```bash
# List all content types
curl -X GET "http://localhost:1337/content-manager/content-types" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"

# List only collection types
curl -X GET "http://localhost:1337/content-manager/content-types?kind=collectionType" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

---

### Get Content Type Configuration

**Endpoint**: `GET /content-manager/content-types/:uid/configuration`

**Description**: Get configuration for a specific content type including layout settings.

**Path Parameters**:
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `uid` | string | Yes | Content type UID (e.g., `api::article.article`) |

**Response**: `200 OK`

```json
{
  "data": {
    "contentType": {
      "uid": "api::article.article",
      "settings": {
        "bulkable": true,
        "filterable": true,
        "searchable": true,
        "pageSize": 10,
        "mainField": "title",
        "defaultSortBy": "title",
        "defaultSortOrder": "ASC"
      },
      "metadatas": {
        "title": {
          "edit": {
            "label": "Title",
            "description": "",
            "placeholder": "",
            "visible": true,
            "editable": true
          },
          "list": {
            "label": "Title",
            "searchable": true,
            "sortable": true
          }
        }
      },
      "layouts": {
        "list": ["title", "publishedAt", "createdAt"],
        "edit": [
          [
            {
              "name": "title",
              "size": 6
            }
          ]
        ]
      }
    },
    "components": {}
  }
}
```

**Example**:

```bash
curl -X GET "http://localhost:1337/content-manager/content-types/api::article.article/configuration" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

---

### Update Content Type Configuration

**Endpoint**: `PUT /content-manager/content-types/:uid/configuration`

**Description**: Update configuration for a content type.

**Request Body**:

```json
{
  "settings": {
    "pageSize": 20,
    "defaultSortBy": "createdAt",
    "defaultSortOrder": "DESC"
  },
  "layouts": {
    "list": ["title", "author", "publishedAt"],
    "edit": [
      [
        { "name": "title", "size": 12 }
      ],
      [
        { "name": "content", "size": 12 }
      ]
    ]
  }
}
```

**Response**: `200 OK`

```json
{
  "data": {
    "uid": "api::article.article",
    "settings": {
      "pageSize": 20,
      "defaultSortBy": "createdAt",
      "defaultSortOrder": "DESC"
    }
  }
}
```

---

## Collection Types API

Manage documents for collection type content.

### List Documents

**Endpoint**: `GET /content-manager/collection-types/:model`

**Description**: Retrieve paginated list of documents.

**Path Parameters**:
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `model` | string | Yes | Content type UID |

**Query Parameters**:
| Parameter | Type | Required | Description | Default |
|-----------|------|----------|-------------|---------|
| `page` | number | No | Page number | 1 |
| `pageSize` | number | No | Items per page | 10 |
| `sort` | string | No | Sort field and order (e.g., `title:asc`) | `id:desc` |
| `filters` | object | No | Filter criteria (JSON) | `{}` |
| `locale` | string | No | Locale code | Default locale |
| `status` | string | No | `draft` or `published` | `draft` |
| `populate` | string/object | No | Relations to populate | `{}` |

**Response**: `200 OK`

```json
{
  "results": [
    {
      "id": 1,
      "documentId": "xyz123",
      "title": "My First Article",
      "content": "Article content...",
      "locale": "en",
      "publishedAt": null,
      "createdAt": "2025-12-12T10:00:00.000Z",
      "updatedAt": "2025-12-12T10:00:00.000Z",
      "createdBy": {
        "id": 1,
        "firstname": "John",
        "lastname": "Doe"
      }
    }
  ],
  "pagination": {
    "page": 1,
    "pageSize": 10,
    "pageCount": 5,
    "total": 42
  }
}
```

**Examples**:

```bash
# Basic list
curl -X GET "http://localhost:1337/content-manager/collection-types/api::article.article" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"

# With pagination
curl -X GET "http://localhost:1337/content-manager/collection-types/api::article.article?page=2&pageSize=20" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"

# With sorting
curl -X GET "http://localhost:1337/content-manager/collection-types/api::article.article?sort=title:asc" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"

# With filters
curl -X GET "http://localhost:1337/content-manager/collection-types/api::article.article?filters[title][$contains]=strapi" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"

# Published documents only
curl -X GET "http://localhost:1337/content-manager/collection-types/api::article.article?status=published" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"

# With populated relations
curl -X GET "http://localhost:1337/content-manager/collection-types/api::article.article?populate=*" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

---

### Get Single Document

**Endpoint**: `GET /content-manager/collection-types/:model/:id`

**Description**: Retrieve a single document by ID.

**Path Parameters**:
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `model` | string | Yes | Content type UID |
| `id` | string | Yes | Document ID or documentId |

**Query Parameters**:
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locale` | string | No | Locale code |
| `status` | string | No | `draft` or `published` |
| `populate` | string/object | No | Relations to populate |

**Response**: `200 OK`

```json
{
  "id": 1,
  "documentId": "xyz123",
  "title": "My Article",
  "content": "Content here...",
  "author": {
    "id": 1,
    "name": "John Doe"
  },
  "locale": "en",
  "publishedAt": "2025-12-12T10:00:00.000Z",
  "createdAt": "2025-12-12T09:00:00.000Z",
  "updatedAt": "2025-12-12T10:00:00.000Z"
}
```

**Example**:

```bash
curl -X GET "http://localhost:1337/content-manager/collection-types/api::article.article/xyz123" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

---

### Create Document

**Endpoint**: `POST /content-manager/collection-types/:model`

**Description**: Create a new document (always created as draft).

**Path Parameters**:
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `model` | string | Yes | Content type UID |

**Request Body**:

```json
{
  "title": "New Article",
  "content": "Article content...",
  "locale": "en",
  "author": 1
}
```

**Response**: `201 Created`

```json
{
  "id": 1,
  "documentId": "abc456",
  "title": "New Article",
  "content": "Article content...",
  "locale": "en",
  "publishedAt": null,
  "createdAt": "2025-12-12T10:30:00.000Z",
  "updatedAt": "2025-12-12T10:30:00.000Z"
}
```

**Example**:

```bash
curl -X POST "http://localhost:1337/content-manager/collection-types/api::article.article" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "My New Article",
    "content": "This is the content",
    "locale": "en"
  }'
```

---

### Update Document

**Endpoint**: `PUT /content-manager/collection-types/:model/:id`

**Description**: Update an existing document (updates draft version).

**Path Parameters**:
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `model` | string | Yes | Content type UID |
| `id` | string | Yes | Document ID or documentId |

**Request Body**:

```json
{
  "title": "Updated Title",
  "content": "Updated content...",
  "locale": "en"
}
```

**Response**: `200 OK`

```json
{
  "id": 1,
  "documentId": "abc456",
  "title": "Updated Title",
  "content": "Updated content...",
  "locale": "en",
  "publishedAt": null,
  "updatedAt": "2025-12-12T11:00:00.000Z"
}
```

**Example**:

```bash
curl -X PUT "http://localhost:1337/content-manager/collection-types/api::article.article/abc456" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Updated Article Title"
  }'
```

---

### Delete Document

**Endpoint**: `DELETE /content-manager/collection-types/:model/:id`

**Description**: Delete a document (all locales and versions).

**Path Parameters**:
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `model` | string | Yes | Content type UID |
| `id` | string | Yes | Document ID or documentId |

**Response**: `200 OK`

```json
{
  "documentId": "abc456",
  "entries": [
    {
      "id": 1,
      "documentId": "abc456",
      "locale": "en"
    }
  ]
}
```

**Example**:

```bash
curl -X DELETE "http://localhost:1337/content-manager/collection-types/api::article.article/abc456" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

---

### Publish Document

**Endpoint**: `POST /content-manager/collection-types/:model/:id/actions/publish`

**Description**: Publish a draft document.

**Path Parameters**:
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `model` | string | Yes | Content type UID |
| `id` | string | Yes | Document ID or documentId |

**Request Body** (optional):

```json
{
  "locale": "en"
}
```

**Response**: `200 OK`

```json
{
  "id": 1,
  "documentId": "abc456",
  "title": "Published Article",
  "publishedAt": "2025-12-12T12:00:00.000Z",
  "locale": "en"
}
```

**Example**:

```bash
curl -X POST "http://localhost:1337/content-manager/collection-types/api::article.article/abc456/actions/publish" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"locale": "en"}'
```

---

### Unpublish Document

**Endpoint**: `POST /content-manager/collection-types/:model/:id/actions/unpublish`

**Description**: Unpublish a published document.

**Response**: `200 OK`

```json
{
  "id": 1,
  "documentId": "abc456",
  "publishedAt": null
}
```

**Example**:

```bash
curl -X POST "http://localhost:1337/content-manager/collection-types/api::article.article/abc456/actions/unpublish" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

---

### Clone Document

**Endpoint**: `POST /content-manager/collection-types/:model/clone/:id`

**Description**: Clone/duplicate a document.

**Request Body** (optional):

```json
{
  "locale": "fr"
}
```

**Response**: `200 OK`

```json
{
  "id": 2,
  "documentId": "def789",
  "title": "My Article (copy)",
  "content": "Article content...",
  "locale": "fr",
  "publishedAt": null
}
```

**Example**:

```bash
curl -X POST "http://localhost:1337/content-manager/collection-types/api::article.article/clone/abc456" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"locale": "fr"}'
```

---

### Bulk Delete

**Endpoint**: `POST /content-manager/collection-types/:model/actions/bulkDelete`

**Description**: Delete multiple documents at once.

**Request Body**:

```json
{
  "documentIds": ["abc456", "def789", "ghi012"]
}
```

**Response**: `200 OK`

```json
{
  "count": 3
}
```

**Example**:

```bash
curl -X POST "http://localhost:1337/content-manager/collection-types/api::article.article/actions/bulkDelete" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "documentIds": ["abc456", "def789"]
  }'
```

---

### Bulk Publish

**Endpoint**: `POST /content-manager/collection-types/:model/actions/bulkPublish`

**Description**: Publish multiple documents at once.

**Request Body**:

```json
{
  "documentIds": ["abc456", "def789"]
}
```

**Response**: `200 OK`

```json
{
  "count": 2
}
```

**Example**:

```bash
curl -X POST "http://localhost:1337/content-manager/collection-types/api::article.article/actions/bulkPublish" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "documentIds": ["abc456", "def789"]
  }'
```

---

## Single Types API

Manage single type content (only one instance per locale).

### Get Single Type

**Endpoint**: `GET /content-manager/single-types/:model`

**Query Parameters**:
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locale` | string | No | Locale code |
| `status` | string | No | `draft` or `published` |

**Response**: `200 OK`

```json
{
  "id": 1,
  "documentId": "single_homepage",
  "title": "Homepage",
  "hero": {
    "title": "Welcome",
    "description": "Welcome to our site"
  },
  "locale": "en",
  "publishedAt": "2025-12-12T10:00:00.000Z"
}
```

**Example**:

```bash
curl -X GET "http://localhost:1337/content-manager/single-types/api::homepage.homepage" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

---

### Update Single Type

**Endpoint**: `PUT /content-manager/single-types/:model`

**Request Body**:

```json
{
  "title": "Updated Homepage",
  "hero": {
    "title": "Welcome Back",
    "description": "Updated description"
  }
}
```

**Response**: `200 OK`

**Example**:

```bash
curl -X PUT "http://localhost:1337/content-manager/single-types/api::homepage.homepage" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Updated Homepage"
  }'
```

---

### Publish Single Type

**Endpoint**: `POST /content-manager/single-types/:model/actions/publish`

**Example**:

```bash
curl -X POST "http://localhost:1337/content-manager/single-types/api::homepage.homepage/actions/publish" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

---

## Components API

### List Components

**Endpoint**: `GET /content-manager/components`

**Response**: `200 OK`

```json
{
  "data": [
    {
      "uid": "shared.seo",
      "category": "shared",
      "schema": {
        "displayName": "SEO",
        "attributes": {
          "metaTitle": { "type": "string" },
          "metaDescription": { "type": "text" }
        }
      }
    }
  ]
}
```

---

## Relations API

### Get Available Relations

**Endpoint**: `GET /content-manager/relations/:model/:id/:fieldName`

**Description**: Get available items for a relation field.

**Query Parameters**:
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `page` | number | No | Page number |
| `pageSize` | number | No | Items per page |
| `filters` | object | No | Filter criteria |

**Response**: `200 OK`

```json
{
  "results": [
    {
      "id": 1,
      "documentId": "cat123",
      "name": "Technology",
      "publishedAt": "2025-12-12T10:00:00.000Z"
    }
  ],
  "pagination": {
    "page": 1,
    "pageSize": 10,
    "total": 5
  }
}
```

---

## Common Parameters

### Populate Parameter

**Deep Population**:
```
?populate=*                          # All direct relations
?populate[author][populate]=avatar   # Nested population
?populate[0]=author&populate[1]=category  # Multiple relations
```

**Example**:
```bash
curl -X GET "http://localhost:1337/content-manager/collection-types/api::article.article?populate[author][populate]=avatar" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

---

### Filter Parameter

**Operators**:
- `$eq`: Equal
- `$ne`: Not equal
- `$lt`: Less than
- `$lte`: Less than or equal
- `$gt`: Greater than
- `$gte`: Greater than or equal
- `$contains`: Contains (string)
- `$notContains`: Does not contain
- `$in`: In array
- `$notIn`: Not in array
- `$null`: Is null
- `$notNull`: Is not null

**Examples**:
```bash
# Equal
?filters[title][$eq]=Hello

# Contains
?filters[title][$contains]=Strapi

# Greater than
?filters[publishedAt][$gt]=2025-01-01

# Multiple filters
?filters[title][$contains]=Strapi&filters[publishedAt][$notNull]=true

# Nested filters
?filters[author][name][$eq]=John
```

---

### Sort Parameter

**Format**: `field:order`

**Examples**:
```bash
# Ascending
?sort=title:asc

# Descending
?sort=createdAt:desc

# Multiple fields
?sort[0]=title:asc&sort[1]=createdAt:desc
```

---

## Error Responses

### 400 Bad Request

```json
{
  "error": {
    "status": 400,
    "name": "ValidationError",
    "message": "Invalid request body",
    "details": {
      "errors": [
        {
          "path": ["title"],
          "message": "title is required",
          "name": "ValidationError"
        }
      ]
    }
  }
}
```

---

### 403 Forbidden

```json
{
  "error": {
    "status": 403,
    "name": "ForbiddenError",
    "message": "Forbidden"
  }
}
```

---

### 404 Not Found

```json
{
  "error": {
    "status": 404,
    "name": "NotFoundError",
    "message": "Document not found"
  }
}
```

---

### 500 Internal Server Error

```json
{
  "error": {
    "status": 500,
    "name": "ApplicationError",
    "message": "An error occurred"
  }
}
```

---

## Rate Limiting

Default rate limits:
- **10 requests per second** per IP
- **100 requests per minute** per user

Headers:
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1702387200
```

---

## Webhooks

Content Manager emits events that can trigger webhooks:

**Events**:
- `entry.create`
- `entry.update`
- `entry.delete`
- `entry.publish`
- `entry.unpublish`

**Configure** in Admin Panel → Settings → Webhooks

---

**Last Updated**: December 12, 2025  
**API Version**: 5.31.3
