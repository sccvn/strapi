# Upload Plugin API Documentation

## Overview

The Strapi Upload plugin provides a comprehensive API for managing file uploads, media library, and folder organization. It supports multiple upload providers and includes AI-powered metadata generation.

## Table of Contents

- [Features](#features)
- [Installation](#installation)
- [Configuration](#configuration)
- [API Endpoints](#api-endpoints)
- [Services](#services)
- [Upload Providers](#upload-providers)
- [AI Metadata](#ai-metadata)
- [Code Examples](#code-examples)
- [Error Handling](#error-handling)

## Features

- ✅ File upload with validation
- ✅ Image manipulation (resize, crop, optimize)
- ✅ Folder organization
- ✅ Multiple upload providers (local, AWS S3, Cloudinary, etc.)
- ✅ AI-powered metadata generation
- ✅ Responsive images (multiple formats)
- ✅ File access control
- ✅ Metrics and analytics
- ✅ GraphQL support

## Installation

The upload plugin is included by default in Strapi core.

```bash
# Already included in @strapi/strapi
npm install @strapi/plugin-upload
```

## Configuration

### Basic Configuration

```javascript
// config/plugins.js
module.exports = {
  upload: {
    config: {
      provider: 'local',
      providerOptions: {
        sizeLimit: 100000000, // 100MB
      },
      actionOptions: {
        upload: {},
        uploadStream: {},
        delete: {},
      },
    },
  },
};
```

### AWS S3 Provider

```javascript
// config/plugins.js
module.exports = {
  upload: {
    config: {
      provider: 'aws-s3',
      providerOptions: {
        accessKeyId: process.env.AWS_ACCESS_KEY_ID,
        secretAccessKey: process.env.AWS_ACCESS_SECRET,
        region: process.env.AWS_REGION,
        params: {
          Bucket: process.env.AWS_BUCKET,
        },
      },
    },
  },
};
```

### Cloudinary Provider

```javascript
// config/plugins.js
module.exports = {
  upload: {
    config: {
      provider: 'cloudinary',
      providerOptions: {
        cloud_name: process.env.CLOUDINARY_NAME,
        api_key: process.env.CLOUDINARY_KEY,
        api_secret: process.env.CLOUDINARY_SECRET,
      },
    },
  },
};
```

## API Endpoints

### Upload File

**POST** `/api/upload`

Upload one or more files.

**Request:**

```bash
curl -X POST http://localhost:1337/api/upload \
  -H 'Authorization: Bearer YOUR_JWT_TOKEN' \
  -F 'files=@/path/to/file.jpg' \
  -F 'fileInfo={"name":"Custom Name","alternativeText":"Alt text"}'
```

**JavaScript Example:**

```javascript
const formData = new FormData();
formData.append('files', fileInput.files[0]);
formData.append('fileInfo', JSON.stringify({
  name: 'My Image',
  alternativeText: 'Image description',
  caption: 'Image caption',
}));

const response = await fetch('http://localhost:1337/api/upload', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${token}`,
  },
  body: formData,
});

const data = await response.json();
```

**Response (200 OK):**

```json
[
  {
    "id": 1,
    "name": "file.jpg",
    "alternativeText": "Alt text",
    "caption": null,
    "width": 1920,
    "height": 1080,
    "formats": {
      "thumbnail": {
        "url": "/uploads/thumbnail_file_123.jpg",
        "width": 245,
        "height": 138
      },
      "large": {
        "url": "/uploads/large_file_123.jpg",
        "width": 1000,
        "height": 563
      }
    },
    "hash": "file_123",
    "ext": ".jpg",
    "mime": "image/jpeg",
    "size": 245.6,
    "url": "/uploads/file_123.jpg",
    "previewUrl": null,
    "provider": "local",
    "createdAt": "2024-12-12T10:00:00.000Z",
    "updatedAt": "2024-12-12T10:00:00.000Z"
  }
]
```

### Get Files

**GET** `/api/upload/files`

Retrieve a list of uploaded files.

**Query Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `filters` | object | Filter files by attributes |
| `sort` | string | Sort order (e.g., `name:asc`) |
| `pagination` | object | Pagination options |
| `populate` | string | Populate relations |

**Request:**

```bash
curl -X GET 'http://localhost:1337/api/upload/files?filters[name][$contains]=logo&pagination[pageSize]=10' \
  -H 'Authorization: Bearer YOUR_JWT_TOKEN'
```

**JavaScript Example:**

```javascript
const response = await fetch(
  'http://localhost:1337/api/upload/files?' + new URLSearchParams({
    'filters[mime][$contains]': 'image',
    'pagination[page]': '1',
    'pagination[pageSize]': '25',
    'sort': 'createdAt:desc',
  }),
  {
    headers: {
      'Authorization': `Bearer ${token}`,
    },
  }
);

const { results, pagination } = await response.json();
```

**Response (200 OK):**

```json
{
  "results": [
    {
      "id": 1,
      "name": "logo.png",
      "url": "/uploads/logo_123.png",
      "mime": "image/png",
      "size": 15.2,
      "createdAt": "2024-12-12T10:00:00.000Z"
    }
  ],
  "pagination": {
    "page": 1,
    "pageSize": 25,
    "pageCount": 5,
    "total": 120
  }
}
```

### Get File by ID

**GET** `/api/upload/files/:id`

**Request:**

```bash
curl -X GET http://localhost:1337/api/upload/files/1 \
  -H 'Authorization: Bearer YOUR_JWT_TOKEN'
```

**Response (200 OK):**

```json
{
  "id": 1,
  "name": "file.jpg",
  "alternativeText": "Alt text",
  "caption": null,
  "width": 1920,
  "height": 1080,
  "formats": { ... },
  "url": "/uploads/file_123.jpg",
  "createdAt": "2024-12-12T10:00:00.000Z",
  "updatedAt": "2024-12-12T10:00:00.000Z",
  "folder": {
    "id": 1,
    "name": "Images",
    "path": "/Images"
  }
}
```

### Delete File

**DELETE** `/api/upload/files/:id`

**Request:**

```bash
curl -X DELETE http://localhost:1337/api/upload/files/1 \
  -H 'Authorization: Bearer YOUR_JWT_TOKEN'
```

**Response (200 OK):**

```json
{
  "id": 1,
  "name": "file.jpg",
  "url": "/uploads/file_123.jpg"
}
```

### Folder Management

#### Create Folder

**POST** `/api/upload/folders`

```javascript
const response = await fetch('http://localhost:1337/api/upload/folders', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    name: 'Product Images',
    parent: 1, // optional parent folder ID
  }),
});
```

#### Get Folders

**GET** `/api/upload/folders`

```bash
curl -X GET http://localhost:1337/api/upload/folders \
  -H 'Authorization: Bearer YOUR_JWT_TOKEN'
```

## Services

### Upload Service

The upload service provides programmatic access to file operations.

```javascript
// In a Strapi controller or service
const uploadService = strapi.plugin('upload').service('upload');

// Upload a file
const uploadedFiles = await uploadService.upload({
  data: {
    fileInfo: {
      name: 'my-file.jpg',
      alternativeText: 'Description',
    },
  },
  files: {
    path: '/tmp/file.jpg',
    name: 'file.jpg',
    type: 'image/jpeg',
    size: 245600,
  },
});

// Delete a file
await uploadService.remove({ id: 1 });
```

### Image Manipulation Service

```javascript
const imageManipulation = strapi.plugin('upload').service('image-manipulation');

// Generate responsive formats
const formats = await imageManipulation.generateResponsiveFormats(file);

// Optimize image
const optimized = await imageManipulation.optimize(buffer);
```

### File Service

```javascript
const fileService = strapi.plugin('upload').service('file');

// Find files with filters
const files = await fileService.findMany({
  filters: { mime: { $contains: 'image' } },
  populate: ['folder', 'related'],
  pagination: { page: 1, pageSize: 25 },
});

// Find one file
const file = await fileService.findOne(1, { populate: ['folder'] });

// Update file
await fileService.update(1, {
  name: 'new-name.jpg',
  alternativeText: 'Updated description',
});
```

### Folder Service

```javascript
const folderService = strapi.plugin('upload').service('folder');

// Create folder
const folder = await folderService.create({
  name: 'Documents',
  parent: 1,
});

// Get folder structure
const structure = await folderService.getStructure();

// Delete folder
await folderService.delete(folderId);
```

## Upload Providers

### Creating a Custom Provider

```javascript
// providers/custom-provider.js
module.exports = {
  init(config) {
    return {
      async upload(file) {
        // Upload logic
        return { url: 'https://cdn.example.com/file.jpg' };
      },
      async delete(file) {
        // Delete logic
      },
      async uploadStream(file) {
        // Stream upload logic
      },
    };
  },
};
```

### Registering Provider

```javascript
// config/plugins.js
module.exports = {
  upload: {
    config: {
      provider: 'custom-provider',
      providerOptions: {
        apiKey: process.env.CUSTOM_API_KEY,
      },
    },
  },
};
```

## AI Metadata

The upload plugin supports AI-powered metadata generation using Strapi AI.

### Configuration

```javascript
// config/plugins.js
module.exports = {
  upload: {
    config: {
      aiMetadata: {
        enabled: true,
        provider: 'openai',
      },
    },
  },
};
```

### Using AI Metadata

```javascript
const aiMetadataService = strapi.plugin('upload').service('ai-metadata');

// Generate metadata for an image
const metadata = await aiMetadataService.generateMetadata(fileId);

// Result:
// {
//   alternativeText: "A sunset over mountains",
//   caption: "Beautiful mountain sunset with orange and pink sky",
//   tags: ["nature", "sunset", "mountains", "landscape"]
// }
```

## Code Examples

### Complete Upload Flow

```javascript
// Upload file with validation and processing
async function uploadFile(file) {
  try {
    // 1. Validate file
    const maxSize = 10 * 1024 * 1024; // 10MB
    if (file.size > maxSize) {
      throw new Error('File too large');
    }

    // 2. Create FormData
    const formData = new FormData();
    formData.append('files', file);
    formData.append('fileInfo', JSON.stringify({
      name: file.name,
      alternativeText: 'Auto-uploaded file',
    }));

    // 3. Upload
    const response = await fetch('http://localhost:1337/api/upload', {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${token}`,
      },
      body: formData,
    });

    if (!response.ok) {
      throw new Error(`Upload failed: ${response.statusText}`);
    }

    // 4. Get uploaded file data
    const [uploadedFile] = await response.json();

    // 5. Generate AI metadata (optional)
    if (uploadedFile.mime.startsWith('image/')) {
      await fetch(`http://localhost:1337/api/upload/files/${uploadedFile.id}/metadata`, {
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${token}`,
        },
      });
    }

    return uploadedFile;
  } catch (error) {
    console.error('Upload error:', error);
    throw error;
  }
}
```

### React Upload Component

```jsx
import { useState } from 'react';

function FileUploader() {
  const [uploading, setUploading] = useState(false);
  const [files, setFiles] = useState([]);

  const handleUpload = async (event) => {
    const file = event.target.files[0];
    if (!file) return;

    setUploading(true);

    try {
      const formData = new FormData();
      formData.append('files', file);

      const response = await fetch('http://localhost:1337/api/upload', {
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${localStorage.getItem('token')}`,
        },
        body: formData,
      });

      const [uploadedFile] = await response.json();
      setFiles([...files, uploadedFile]);
      alert('File uploaded successfully!');
    } catch (error) {
      console.error('Upload failed:', error);
      alert('Upload failed');
    } finally {
      setUploading(false);
    }
  };

  return (
    <div>
      <input
        type="file"
        onChange={handleUpload}
        disabled={uploading}
      />
      {uploading && <p>Uploading...</p>}
      
      <div>
        {files.map(file => (
          <div key={file.id}>
            <img src={file.formats?.thumbnail?.url || file.url} alt={file.name} />
            <p>{file.name}</p>
          </div>
        ))}
      </div>
    </div>
  );
}
```

### Backend Integration

```javascript
// In a Strapi controller
module.exports = {
  async uploadProductImage(ctx) {
    const { productId } = ctx.params;
    const { files } = ctx.request.files;

    // Upload file
    const uploadService = strapi.plugin('upload').service('upload');
    const [uploadedFile] = await uploadService.upload({
      data: {
        fileInfo: {
          name: `Product ${productId} Image`,
          folder: 1, // Product images folder
        },
      },
      files,
    });

    // Associate with product
    await strapi.entityService.update('api::product.product', productId, {
      data: {
        image: uploadedFile.id,
      },
    });

    return { success: true, file: uploadedFile };
  },
};
```

## Error Handling

### Common Errors

| Status Code | Error | Description |
|-------------|-------|-------------|
| 400 | Bad Request | Invalid file format or missing required fields |
| 401 | Unauthorized | Missing or invalid authentication token |
| 403 | Forbidden | User doesn't have permission to upload |
| 413 | Payload Too Large | File exceeds maximum size limit |
| 415 | Unsupported Media Type | File type not allowed |
| 500 | Internal Server Error | Server-side error during upload |

### Error Response Format

```json
{
  "error": {
    "status": 413,
    "name": "PayloadTooLargeError",
    "message": "File size exceeds maximum allowed size",
    "details": {
      "maxSize": 10485760,
      "fileSize": 15728640
    }
  }
}
```

### Handling Errors

```javascript
try {
  const response = await fetch('http://localhost:1337/api/upload', {
    method: 'POST',
    headers: { 'Authorization': `Bearer ${token}` },
    body: formData,
  });

  if (!response.ok) {
    const error = await response.json();
    
    switch (error.error.status) {
      case 413:
        alert('File is too large. Maximum size is 10MB.');
        break;
      case 415:
        alert('File type not supported. Please upload an image.');
        break;
      case 401:
        alert('Please log in to upload files.');
        break;
      default:
        alert(`Upload failed: ${error.error.message}`);
    }
    
    throw error;
  }

  const data = await response.json();
  return data;
} catch (error) {
  console.error('Upload error:', error);
  throw error;
}
```

## Performance Optimization

### Image Optimization

```javascript
// config/plugins.js
module.exports = {
  upload: {
    config: {
      breakpoints: {
        xlarge: 1920,
        large: 1000,
        medium: 750,
        small: 500,
        xsmall: 64,
      },
      sizeOptimization: true,
      responsiveDimensions: true,
    },
  },
};
```

### Lazy Loading

```jsx
<img
  src={file.formats?.thumbnail?.url}
  data-src={file.url}
  alt={file.alternativeText}
  loading="lazy"
/>
```

## Security

### File Type Validation

```javascript
// config/middlewares.js
module.exports = {
  upload: {
    config: {
      allowedExtensions: ['jpg', 'jpeg', 'png', 'gif', 'pdf', 'doc', 'docx'],
    },
  },
};
```

### Rate Limiting

```javascript
// config/middlewares.js
module.exports = [
  {
    name: 'strapi::ratelimit',
    config: {
      max: 10,
      interval: 60000, // 1 minute
    },
  },
];
```

## GraphQL

### Upload Mutation

```graphql
mutation UploadFile($file: Upload!) {
  upload(file: $file) {
    id
    name
    url
    mime
    size
  }
}
```

### Query Files

```graphql
query GetFiles($filters: FileFiltersInput, $pagination: PaginationArg) {
  uploadFiles(filters: $filters, pagination: $pagination) {
    data {
      id
      attributes {
        name
        url
        mime
        alternativeText
        formats
      }
    }
    meta {
      pagination {
        page
        pageSize
        total
      }
    }
  }
}
```

## Best Practices

1. **Always validate file types and sizes on both client and server**
2. **Use appropriate image formats** (WebP for web, JPEG for photos)
3. **Implement progress indicators** for large file uploads
4. **Generate responsive image formats** automatically
5. **Use folders** to organize media library
6. **Enable AI metadata** for better SEO and accessibility
7. **Set up CDN** for production deployments
8. **Implement proper error handling** and user feedback
9. **Use signed URLs** for private files
10. **Monitor storage usage** and set limits

## Troubleshooting

### Upload Timeout

Increase timeout in configuration:

```javascript
// config/server.js
module.exports = {
  upload: {
    timeout: 300000, // 5 minutes
  },
};
```

### Large File Uploads

Configure body parser:

```javascript
// config/middlewares.js
module.exports = [
  {
    name: 'strapi::body',
    config: {
      formLimit: '256mb',
      jsonLimit: '256mb',
      textLimit: '256mb',
      formidable: {
        maxFileSize: 250 * 1024 * 1024, // 250MB
      },
    },
  },
];
```

## Related Documentation

- [Content Manager API](./content-manager-api.md)
- [File Upload User Guide](../user-guides/file-upload.md)
- [Media Library Guide](../user-guides/media-library.md)
- [Plugin Architecture](../architecture/plugin-architecture.md)

---

**Last Updated**: December 12, 2024  
**API Version**: v5.x  
**Plugin Version**: @strapi/plugin-upload v5.x
