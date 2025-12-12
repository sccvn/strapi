# Getting Started with Strapi

## Welcome to Strapi!

This guide will help you get started with Strapi, from understanding what it is to creating your first content types and building your first API.

## Table of Contents

- [What is Strapi?](#what-is-strapi)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [First Steps](#first-steps)
- [Creating Content Types](#creating-content-types)
- [Adding Content](#adding-content)
- [Using the API](#using-the-api)
- [Next Steps](#next-steps)

## What is Strapi?

Strapi is an open-source headless CMS (Content Management System) that gives developers the freedom to choose their favorite tools and frameworks while allowing editors to manage and distribute their content.

### Key Features

- **Headless CMS**: API-first, use any frontend framework
- **Customizable Admin Panel**: Manage your content easily
- **Content Type Builder**: Create data structures visually
- **Role-Based Access Control**: Fine-grained permissions
- **RESTful and GraphQL APIs**: Choose your preferred API style
- **Plugin System**: Extend functionality
- **Media Library**: Manage your assets
- **Internationalization**: Multi-language content

### Use Cases

- **Websites & Web Apps**: Blogs, corporate websites, portfolios
- **Mobile Apps**: Content backend for iOS and Android
- **E-commerce**: Product catalogs, inventories
- **Multi-platform Content**: Distribute to web, mobile, IoT
- **Internal Tools**: Admin panels, dashboards

## Prerequisites

Before you begin, make sure you have:

### Required

- **Node.js**: version 18.x or 20.x
- **npm** (6.x or higher) or **yarn**
- A **code editor** (VS Code, Sublime Text, etc.)

### Optional

- **Database**: PostgreSQL, MySQL, MariaDB, or SQLite (default)
- **Git**: For version control

### Check Your Installation

```bash
# Check Node.js version
node --version
# Should show v18.x.x or v20.x.x

# Check npm version
npm --version
# Should show 6.x.x or higher
```

## Installation

### Create a New Project

**Using npx (recommended):**

```bash
npx create-strapi-app@latest my-project
```

**Using npm:**

```bash
npm create strapi-app@latest my-project
```

**Using yarn:**

```bash
yarn create strapi-app my-project
```

### Installation Options

You'll be asked to choose:

1. **Installation type**:
   - **Quickstart**: SQLite database (recommended for beginners)
   - **Custom**: Choose your database

2. **Project template**:
   - **Blank**: Start from scratch
   - **Blog**: Pre-configured blog template
   - **E-commerce**: Product catalog template

**For this guide, choose Quickstart.**

### Installation Process

```bash
Creating a new Strapi application at /path/to/my-project

Creating files...
✔ Created package.json
✔ Created .env
✔ Created database configuration

Installing dependencies...
⠋ Installing dependencies with npm

Dependencies installed successfully!

Starting your application...
✔ Your application was created!

Available commands in your project:
  npm run develop
    Start Strapi in watch mode.

  npm run start
    Start Strapi without watch mode.

  npm run build
    Build Strapi admin panel.
```

### Start the Application

```bash
cd my-project
npm run develop
```

Strapi will:
1. Build the admin panel
2. Start the server
3. Open your browser to http://localhost:1337/admin

## First Steps

### 1. Create Admin Account

When you first access http://localhost:1337/admin, you'll see a registration form:

1. **Enter your details**:
   - First name
   - Last name
   - Email (will be your username)
   - Password (min. 8 characters)

2. **Click "Let's start"**

You're now logged into the Strapi admin panel!

### 2. Explore the Admin Panel

The admin panel has several main sections:

**Left Sidebar:**
- **Content Manager**: Create and manage content
- **Content-Type Builder**: Design your data structure
- **Media Library**: Manage files and images
- **Plugins**: Configure and manage plugins
- **Settings**: General settings, roles, API tokens

**Top Bar:**
- Your profile
- Notifications
- Help and documentation links

### 3. Understanding the Interface

**Dashboard**: Overview of your application

**Content Manager**: 
- Browse content types
- Create/edit/delete entries
- Filter and search content

**Content-Type Builder**:
- Create collection types (multiple entries)
- Create single types (one entry)
- Add fields to content types

## Creating Content Types

Let's create a simple **Article** content type:

### Step 1: Open Content-Type Builder

1. Click **Content-Type Builder** in the left sidebar
2. Click **Create new collection type**

### Step 2: Name Your Content Type

1. **Display name**: `Article`
2. **API ID (singular)**: `article` (auto-generated)
3. **API ID (plural)**: `articles` (auto-generated)

Click **Continue**

### Step 3: Add Fields

Add the following fields:

#### Title Field

1. Click **Add another field**
2. Select **Text**
3. **Name**: `title`
4. **Type**: Short text
5. Click **Advanced Settings**:
   - ✓ Required field
   - ✓ Unique field
6. Click **Finish**

#### Content Field

1. Click **Add another field**
2. Select **Rich Text**
3. **Name**: `content`
4. Click **Advanced Settings**:
   - ✓ Required field
5. Click **Finish**

#### Author Field

1. Click **Add another field**
2. Select **Text**
3. **Name**: `author`
4. **Type**: Short text
5. Click **Advanced Settings**:
   - ✓ Required field
6. Click **Finish**

#### Cover Image Field

1. Click **Add another field**
2. Select **Media**
3. **Name**: `cover`
4. **Type**: Single media
5. Click **Finish**

#### Published Date Field

1. Click **Add another field**
2. Select **Date**
3. **Name**: `publishedAt`
4. **Type**: Date & time
5. Click **Finish**

### Step 4: Save Content Type

1. Click **Save**
2. Server will restart automatically
3. Wait for "Successfully restarted" notification

## Adding Content

### Step 1: Navigate to Content Manager

1. Click **Content Manager** in sidebar
2. Click **Article** under Collection Types
3. Click **Create new entry** button

### Step 2: Fill in Article Data

**Title**: "Getting Started with Strapi"

**Content**: 
```
Strapi is an amazing headless CMS that makes building APIs super easy. 
In this article, we'll explore how to get started with Strapi and create 
your first content types.
```

**Author**: "John Doe"

**Cover**: Click to upload an image from your computer

**Published At**: Select current date and time

### Step 3: Save and Publish

1. Click **Save** (top right)
2. Click **Publish** (the entry is now live!)

### Create More Entries

Create 2-3 more articles to have sample data for testing the API.

## Using the API

### Step 1: Make API Accessible

By default, the API is protected. Let's make it public for testing:

1. Go to **Settings** → **Roles** → **Public**
2. Scroll to **Permissions**
3. Expand **Article**
4. Check the boxes for:
   - ✓ find (get all articles)
   - ✓ findOne (get single article)
5. Click **Save**

### Step 2: Access the API

Open your browser or use curl:

**Get all articles:**
```bash
# Browser
http://localhost:1337/api/articles

# curl
curl http://localhost:1337/api/articles
```

**Response:**
```json
{
  "data": [
    {
      "id": 1,
      "attributes": {
        "title": "Getting Started with Strapi",
        "content": "Strapi is an amazing...",
        "author": "John Doe",
        "publishedAt": "2024-12-12T10:00:00.000Z",
        "createdAt": "2024-12-12T10:00:00.000Z",
        "updatedAt": "2024-12-12T10:00:00.000Z"
      }
    }
  ],
  "meta": {
    "pagination": {
      "page": 1,
      "pageSize": 25,
      "pageCount": 1,
      "total": 1
    }
  }
}
```

**Get single article:**
```bash
# Browser
http://localhost:1337/api/articles/1

# curl
curl http://localhost:1337/api/articles/1
```

### Step 3: Test with JavaScript

Create a simple HTML file:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Strapi Test</title>
</head>
<body>
  <h1>Articles</h1>
  <div id="articles"></div>

  <script>
    // Fetch articles from Strapi API
    fetch('http://localhost:1337/api/articles')
      .then(response => response.json())
      .then(({ data }) => {
        const articlesDiv = document.getElementById('articles');
        
        data.forEach(article => {
          const { title, author, content } = article.attributes;
          
          articlesDiv.innerHTML += `
            <article>
              <h2>${title}</h2>
              <p><strong>By ${author}</strong></p>
              <p>${content}</p>
            </article>
            <hr>
          `;
        });
      })
      .catch(error => console.error('Error:', error));
  </script>
</body>
</html>
```

Open this file in your browser to see your articles!

### Step 4: Populate Relations

If you want to include the cover image:

```bash
# Add ?populate=* to include all relations
http://localhost:1337/api/articles?populate=*

# Or specify fields
http://localhost:1337/api/articles?populate=cover
```

## Next Steps

Congratulations! You've created your first Strapi application. Here are some next steps:

### 1. Learn More About Content Types

- **Relations**: Connect content types together
- **Components**: Reusable fields
- **Dynamic Zones**: Flexible content sections

**Tutorial**: [Content Type Builder Guide](./content-management.md)

### 2. Secure Your API

- Create authenticated endpoints
- Set up API tokens
- Configure role-based permissions

**Tutorial**: See Settings documentation

### 3. Customize the Admin Panel

- Change theme
- Add custom fields
- Create custom views

### 4. Add Plugins

Popular plugins:
- **GraphQL**: Add GraphQL API
- **Documentation**: Auto-generate API docs
- **i18n**: Internationalization
- **Sentry**: Error tracking

### 5. Deploy Your Application

Options:
- **Strapi Cloud**: Official hosting
- **Heroku**: Easy deployment
- **AWS**: Full control
- **DigitalOcean**: Cost-effective
- **Vercel/Netlify**: Frontend + Strapi

### 6. Connect a Frontend

Build a frontend with:
- **React**: Next.js, Create React App
- **Vue**: Nuxt.js, Vue CLI
- **Angular**: Angular CLI
- **Mobile**: React Native, Flutter

### 7. Explore Advanced Features

- **Webhooks**: Trigger actions on events
- **Lifecycle hooks**: Custom logic
- **Custom routes**: Add endpoints
- **Email**: Send emails from Strapi
- **Cron jobs**: Scheduled tasks

## Common Commands

```bash
# Development mode (auto-reload)
npm run develop

# Production mode
npm run start

# Build admin panel
npm run build

# Run in production
NODE_ENV=production npm run start
```

## Getting Help

### Documentation

- **Official Docs**: https://docs.strapi.io
- **API Reference**: https://docs.strapi.io/dev-docs/api/rest
- **Guides**: https://strapi.io/resource-center

### Community

- **Discord**: https://discord.strapi.io
- **Forum**: https://forum.strapi.io
- **GitHub**: https://github.com/strapi/strapi

### Support

- **Enterprise Support**: Available for business plans
- **Community Support**: Free via Discord and Forum
- **Stack Overflow**: Tag `strapi`

## Troubleshooting

### Port Already in Use

If port 1337 is taken:

```bash
# Use a different port
PORT=3000 npm run develop
```

### Admin Panel Won't Load

1. Clear browser cache
2. Rebuild admin:
   ```bash
   npm run build
   npm run develop
   ```

### Database Connection Issues

Check your database configuration in `config/database.js`

### Permission Errors

On Linux/Mac, you might need to use `sudo` for some npm commands or change file permissions.

## Congratulations! 🎉

You've successfully:
- ✅ Installed Strapi
- ✅ Created your first admin user
- ✅ Built a content type
- ✅ Added content
- ✅ Accessed the API
- ✅ Tested with JavaScript

You're now ready to build amazing applications with Strapi!

---

**Last Updated**: December 12, 2024  
**Strapi Version**: v5.x  
**Difficulty**: Beginner
