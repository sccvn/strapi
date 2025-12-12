# Strapi Plugin Architecture Documentation

## Overview

This document describes the architecture of Strapi's plugin system, including the design patterns, component structure, and best practices for plugin development.

## Table of Contents

- [Architecture Overview](#architecture-overview)
- [Plugin Structure](#plugin-structure)
- [Core Components](#core-components)
- [Design Patterns](#design-patterns)
- [Plugin Lifecycle](#plugin-lifecycle)
- [Extension Points](#extension-points)
- [Best Practices](#best-practices)

## Architecture Overview

Strapi uses a modular plugin architecture that allows for extensibility and customization. The system follows a layered architecture pattern:

```
┌─────────────────────────────────────────────┐
│          Application Layer                   │
│  (Admin Panel, API Routes, GraphQL)         │
└─────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────┐
│          Plugin Layer                        │
│  (Content Manager, Upload, Users, etc.)     │
└─────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────┐
│          Core Layer                          │
│  (Strapi Core, Database, Services)          │
└─────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────┐
│          Infrastructure Layer                │
│  (Node.js, Database, File System)           │
└─────────────────────────────────────────────┘
```

### Key Architectural Principles

1. **Modularity**: Plugins are self-contained modules
2. **Extensibility**: Core functionality can be extended
3. **Separation of Concerns**: Clear separation between server and admin
4. **Convention over Configuration**: Sensible defaults
5. **Dependency Injection**: Services are injected

## Plugin Structure

### Directory Structure

```
my-plugin/
├── admin/                      # Admin panel code
│   ├── src/
│   │   ├── components/        # React components
│   │   ├── pages/             # Admin pages
│   │   ├── hooks/             # React hooks
│   │   ├── utils/             # Utility functions
│   │   └── index.js           # Entry point
│   └── package.json
├── server/                     # Backend code
│   ├── src/
│   │   ├── controllers/       # Request handlers
│   │   ├── services/          # Business logic
│   │   ├── routes/            # API routes
│   │   ├── middlewares/       # Custom middlewares
│   │   ├── policies/          # Authorization policies
│   │   ├── content-types/     # Content type schemas
│   │   ├── config/            # Plugin configuration
│   │   └── index.js           # Plugin registration
│   └── package.json
├── strapi-server.js           # Server entry point
├── strapi-admin.js            # Admin entry point
└── package.json               # Plugin metadata
```

### Upload Plugin Structure Example

```
@strapi/plugin-upload/
├── admin/
│   └── src/
│       ├── components/
│       │   ├── AssetCard/
│       │   ├── AssetDialog/
│       │   ├── FolderCard/
│       │   └── UploadArea/
│       ├── pages/
│       │   ├── MediaLibrary/
│       │   └── Settings/
│       ├── hooks/
│       │   ├── useAssets.js
│       │   ├── useFolders.js
│       │   └── useUpload.js
│       └── index.js
├── server/
│   └── src/
│       ├── controllers/
│       │   ├── admin-upload.js
│       │   ├── admin-folder.js
│       │   └── content-api.js
│       ├── services/
│       │   ├── upload.ts
│       │   ├── folder.ts
│       │   ├── file.ts
│       │   ├── image-manipulation.ts
│       │   ├── provider.ts
│       │   └── ai-metadata.ts
│       ├── routes/
│       │   ├── admin.js
│       │   └── content-api.js
│       ├── middlewares/
│       │   └── upload.js
│       ├── content-types/
│       │   ├── file/
│       │   └── folder/
│       └── config/
│           └── schema.js
└── package.json
```

## Core Components

### 1. Controllers

Controllers handle HTTP requests and responses.

**Pattern**: MVC (Model-View-Controller)

```javascript
// server/src/controllers/upload.js
module.exports = {
  async upload(ctx) {
    const { files } = ctx.request.files;
    const { fileInfo } = ctx.request.body;

    const uploadService = strapi.plugin('upload').service('upload');
    
    const uploadedFiles = await uploadService.upload({
      data: fileInfo,
      files,
    });

    ctx.body = uploadedFiles;
  },

  async find(ctx) {
    const fileService = strapi.plugin('upload').service('file');
    
    const files = await fileService.findMany(ctx.query);
    
    ctx.body = files;
  },
};
```

**Responsibilities**:
- Request validation
- Response formatting
- Delegate to services
- Error handling

### 2. Services

Services contain business logic.

**Pattern**: Service Layer Pattern

```typescript
// server/src/services/upload.ts
export default ({ strapi }) => ({
  async upload({ data, files }) {
    // 1. Validate files
    await this.validateFiles(files);

    // 2. Process files
    const processedFiles = await this.processFiles(files);

    // 3. Store files
    const provider = strapi.plugin('upload').service('provider');
    const storedFiles = await provider.upload(processedFiles);

    // 4. Save metadata to database
    const savedFiles = await this.saveFilesMetadata(storedFiles, data);

    // 5. Generate responsive formats (images)
    await this.generateFormats(savedFiles);

    return savedFiles;
  },

  async validateFiles(files) {
    // Validation logic
  },

  async processFiles(files) {
    // Processing logic
  },

  async saveFilesMetadata(files, data) {
    // Database operations
  },

  async generateFormats(files) {
    // Image manipulation
  },
});
```

**Responsibilities**:
- Business logic
- Data transformation
- Database operations
- External service integration

### 3. Routes

Routes define API endpoints.

**Pattern**: RESTful API Design

```javascript
// server/src/routes/admin.js
module.exports = {
  type: 'admin',
  routes: [
    {
      method: 'POST',
      path: '/upload',
      handler: 'admin-upload.upload',
      config: {
        policies: ['admin::isAuthenticatedAdmin'],
      },
    },
    {
      method: 'GET',
      path: '/files',
      handler: 'admin-upload.find',
      config: {
        policies: ['admin::isAuthenticatedAdmin'],
      },
    },
    {
      method: 'GET',
      path: '/files/:id',
      handler: 'admin-upload.findOne',
      config: {
        policies: ['admin::isAuthenticatedAdmin'],
      },
    },
    {
      method: 'DELETE',
      path: '/files/:id',
      handler: 'admin-upload.destroy',
      config: {
        policies: ['admin::isAuthenticatedAdmin'],
      },
    },
  ],
};
```

### 4. Middlewares

Middlewares process requests before they reach controllers.

**Pattern**: Chain of Responsibility

```javascript
// server/src/middlewares/upload.js
module.exports = (config, { strapi }) => {
  return async (ctx, next) => {
    // 1. Check file size
    const maxSize = config.sizeLimit || 50000000; // 50MB default
    
    if (ctx.request.files?.files) {
      const files = Array.isArray(ctx.request.files.files)
        ? ctx.request.files.files
        : [ctx.request.files.files];

      for (const file of files) {
        if (file.size > maxSize) {
          return ctx.badRequest('File size exceeds limit', {
            maxSize,
            fileSize: file.size,
          });
        }
      }
    }

    // 2. Validate file types
    const allowedTypes = config.allowedTypes || ['image', 'video', 'audio', 'file'];
    // Validation logic...

    // 3. Continue to next middleware/controller
    await next();
  };
};
```

### 5. Policies

Policies control access to routes.

**Pattern**: Policy Pattern

```javascript
// server/src/policies/can-upload.js
module.exports = (policyContext, config, { strapi }) => {
  const { userAbility } = policyContext.state;

  // Check if user has upload permission
  const canUpload = userAbility.can('upload', 'plugin::upload.file');

  if (!canUpload) {
    return false;
  }

  // Check quota
  const quota = config.quota || Infinity;
  const used = await strapi.plugin('upload').service('metrics').getUsedStorage();

  if (used >= quota) {
    return false;
  }

  return true;
};
```

### 6. Content Types

Content types define data models.

**Pattern**: Schema Definition

```javascript
// server/src/content-types/file/schema.json
{
  "kind": "collectionType",
  "collectionName": "files",
  "info": {
    "singularName": "file",
    "pluralName": "files",
    "displayName": "File",
    "description": "Uploaded files"
  },
  "options": {},
  "pluginOptions": {},
  "attributes": {
    "name": {
      "type": "string",
      "required": true
    },
    "alternativeText": {
      "type": "string"
    },
    "caption": {
      "type": "string"
    },
    "width": {
      "type": "integer"
    },
    "height": {
      "type": "integer"
    },
    "formats": {
      "type": "json"
    },
    "hash": {
      "type": "string",
      "required": true
    },
    "ext": {
      "type": "string"
    },
    "mime": {
      "type": "string",
      "required": true
    },
    "size": {
      "type": "decimal",
      "required": true
    },
    "url": {
      "type": "string",
      "required": true
    },
    "previewUrl": {
      "type": "string"
    },
    "provider": {
      "type": "string",
      "required": true
    },
    "folder": {
      "type": "relation",
      "relation": "manyToOne",
      "target": "plugin::upload.folder",
      "inversedBy": "files"
    }
  }
}
```

## Design Patterns

### 1. Plugin Pattern

Plugins are self-contained modules that extend Strapi functionality.

```javascript
// strapi-server.js
module.exports = {
  register({ strapi }) {
    // Register custom functionality
    strapi.customFields.register({
      name: 'file-selector',
      plugin: 'upload',
      type: 'media',
    });
  },

  bootstrap({ strapi }) {
    // Initialize plugin
    const provider = strapi.config.get('plugin.upload.provider');
    strapi.plugin('upload').service('provider').init(provider);
  },
};
```

### 2. Provider Pattern

Providers allow swapping implementations (e.g., local vs S3 storage).

```javascript
// server/src/services/provider.ts
export default ({ strapi }) => {
  let provider;

  return {
    init(config) {
      const providerName = config.provider || 'local';
      const ProviderModule = require(`./providers/${providerName}`);
      
      provider = ProviderModule.init(config.providerOptions);
      
      return provider;
    },

    async upload(file) {
      return provider.upload(file);
    },

    async delete(file) {
      return provider.delete(file);
    },

    getProviderName() {
      return provider.name;
    },
  };
};
```

### 3. Factory Pattern

Used for creating instances dynamically.

```javascript
// server/src/services/image-manipulation.ts
const sharp = require('sharp');

const formats = {
  thumbnail: { width: 245, height: 156 },
  small: { width: 500, height: 319 },
  medium: { width: 750, height: 478 },
  large: { width: 1000, height: 638 },
};

export default ({ strapi }) => ({
  async generateResponsiveFormats(file) {
    const generatedFormats = {};

    for (const [formatName, dimensions] of Object.entries(formats)) {
      if (file.width > dimensions.width || file.height > dimensions.height) {
        const resized = await this.resize(file, dimensions);
        generatedFormats[formatName] = resized;
      }
    }

    return generatedFormats;
  },

  async resize(file, { width, height }) {
    const buffer = await sharp(file.path)
      .resize(width, height, { fit: 'inside', withoutEnlargement: true })
      .toBuffer();

    return {
      buffer,
      width,
      height,
      size: buffer.length,
    };
  },
});
```

### 4. Repository Pattern

Abstracts data access.

```javascript
// server/src/services/file.ts
export default ({ strapi }) => ({
  async findMany(params) {
    return strapi.entityService.findMany('plugin::upload.file', {
      ...params,
    });
  },

  async findOne(id, params) {
    return strapi.entityService.findOne('plugin::upload.file', id, {
      ...params,
    });
  },

  async create(data) {
    return strapi.entityService.create('plugin::upload.file', {
      data,
    });
  },

  async update(id, data) {
    return strapi.entityService.update('plugin::upload.file', id, {
      data,
    });
  },

  async delete(id) {
    return strapi.entityService.delete('plugin::upload.file', id);
  },
});
```

### 5. Observer Pattern

For event handling.

```javascript
// server/src/services/upload.ts
export default ({ strapi }) => ({
  async upload({ data, files }) {
    const uploadedFiles = await this.processUpload(files, data);

    // Emit event
    strapi.eventHub.emit('upload.file-created', {
      files: uploadedFiles,
    });

    return uploadedFiles;
  },
});

// In another service or plugin
strapi.eventHub.on('upload.file-created', ({ files }) => {
  // Generate AI metadata
  files.forEach(async (file) => {
    if (file.mime.startsWith('image/')) {
      await strapi.plugin('upload').service('ai-metadata').generate(file.id);
    }
  });
});
```

### 6. Dependency Injection

Services are injected automatically.

```javascript
// Services are accessed via strapi object
const uploadService = strapi.plugin('upload').service('upload');
const fileService = strapi.plugin('upload').service('file');
const providerService = strapi.plugin('upload').service('provider');

// Or within a plugin
module.exports = ({ strapi }) => ({
  async myFunction() {
    const uploadService = strapi.plugin('upload').service('upload');
    return uploadService.upload({ ... });
  },
});
```

## Plugin Lifecycle

### 1. Registration Phase

```javascript
// strapi-server.js
module.exports = {
  register({ strapi }) {
    // Called when Strapi loads the plugin
    // Register custom fields, policies, middlewares
    
    strapi.customFields.register({
      name: 'my-custom-field',
      plugin: 'my-plugin',
      type: 'string',
    });
  },
};
```

### 2. Bootstrap Phase

```javascript
// strapi-server.js
module.exports = {
  bootstrap({ strapi }) {
    // Called after all plugins are loaded
    // Initialize connections, start services
    
    const provider = strapi.config.get('plugin.upload.provider');
    strapi.plugin('upload').service('provider').init(provider);
    
    // Register event listeners
    strapi.eventHub.on('entry.create', async ({ model, entry }) => {
      // Handle entry creation
    });
  },
};
```

### 3. Runtime Phase

Normal operation where routes handle requests.

### 4. Destroy Phase

```javascript
// strapi-server.js
module.exports = {
  async destroy({ strapi }) {
    // Called when Strapi is shutting down
    // Clean up connections, close resources
    
    const provider = strapi.plugin('upload').service('provider');
    await provider.cleanup();
  },
};
```

## Extension Points

### 1. Extend Services

```javascript
// config/plugins.js
module.exports = {
  upload: {
    config: {
      // Configuration
    },
  },
};

// extensions/upload/strapi-server.js
module.exports = (plugin) => {
  // Extend upload service
  const uploadService = plugin.services.upload;
  
  plugin.services.upload = ({ strapi }) => ({
    ...uploadService({ strapi }),
    
    async customUpload(file) {
      // Custom logic
      const result = await uploadService({ strapi }).upload(file);
      
      // Additional processing
      await this.notifyAdmin(result);
      
      return result;
    },
    
    async notifyAdmin(file) {
      // Send notification
    },
  });

  return plugin;
};
```

### 2. Extend Controllers

```javascript
// extensions/upload/strapi-server.js
module.exports = (plugin) => {
  plugin.controllers.upload.customAction = async (ctx) => {
    // Custom controller action
    ctx.body = { message: 'Custom action' };
  };

  return plugin;
};
```

### 3. Add Routes

```javascript
// extensions/upload/strapi-server.js
module.exports = (plugin) => {
  plugin.routes['content-api'].routes.push({
    method: 'GET',
    path: '/custom-endpoint',
    handler: 'upload.customAction',
  });

  return plugin;
};
```

### 4. Add Middlewares

```javascript
// extensions/upload/strapi-server.js
module.exports = (plugin) => {
  plugin.middlewares.customMiddleware = (config, { strapi }) => {
    return async (ctx, next) => {
      // Middleware logic
      await next();
    };
  };

  return plugin;
};
```

## Best Practices

### 1. Separation of Concerns

```
✅ Good: Business logic in services
❌ Bad: Business logic in controllers

✅ Good: Data access in repository/service
❌ Bad: Direct database queries in controllers
```

### 2. Error Handling

```javascript
// In service
async upload(file) {
  try {
    await this.validateFile(file);
    return await this.processUpload(file);
  } catch (error) {
    strapi.log.error('Upload failed:', error);
    throw error;
  }
}

// In controller
async upload(ctx) {
  try {
    const result = await strapi.plugin('upload').service('upload').upload(ctx.request.files);
    ctx.body = result;
  } catch (error) {
    if (error instanceof ValidationError) {
      return ctx.badRequest('Validation failed', { details: error.details });
    }
    return ctx.internalServerError('Upload failed');
  }
}
```

### 3. Configuration Management

```javascript
// config/plugins.js
module.exports = ({ env }) => ({
  upload: {
    config: {
      provider: env('UPLOAD_PROVIDER', 'local'),
      sizeLimit: env.int('UPLOAD_SIZE_LIMIT', 50000000),
      providerOptions: {
        // Provider-specific options
      },
    },
  },
});
```

### 4. Testing

```javascript
// server/src/services/__tests__/upload.test.ts
describe('Upload Service', () => {
  it('should upload file successfully', async () => {
    const file = {
      name: 'test.jpg',
      path: '/tmp/test.jpg',
      size: 12345,
    };

    const uploadService = strapi.plugin('upload').service('upload');
    const result = await uploadService.upload({ files: file });

    expect(result).toBeDefined();
    expect(result.name).toBe('test.jpg');
  });
});
```

### 5. Documentation

```javascript
/**
 * Upload file to storage
 * @param {Object} options - Upload options
 * @param {File|File[]} options.files - File(s) to upload
 * @param {Object} options.data - Additional file metadata
 * @returns {Promise<File[]>} Uploaded files
 * @throws {ValidationError} If file validation fails
 * @throws {StorageError} If storage provider fails
 */
async upload({ files, data }) {
  // Implementation
}
```

## Related Documentation

- [Upload API Documentation](../api/upload-api.md)
- [Content Manager API](../api/content-manager-api.md)
- [Database Schema](./database-schema.md)
- [Design Patterns](./design-patterns.md)

---

**Last Updated**: December 12, 2024  
**Architecture Version**: v5.x  
**For**: Developers and Architects
