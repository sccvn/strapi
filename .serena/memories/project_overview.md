# Strapi Project Overview

## Project Purpose
Strapi is an **open-source headless CMS** (Content Management System) that enables developers to create and manage content APIs. It's a free, self-hosted or cloud-based solution built entirely in JavaScript/TypeScript.

### Key Features
- **Headless CMS**: API-first approach, front-end agnostic
- **Self-hosted or Cloud**: Deploy anywhere (AWS, Azure, Google Cloud, DigitalOcean) or use Strapi Cloud
- **Multi-database support**: PostgreSQL, MySQL, MariaDB, SQLite
- **Modern Admin Panel**: Fully customizable and extensible
- **Plugin System**: Extensible architecture with official and community plugins
- **Enterprise Features**: Review workflows, content releases, permissions, internationalization
- **Built on**: Node.js, TypeScript, Koa.js framework, React (admin panel)

## Repository Structure
This is a **monorepo** managed with:
- **Lerna** (v5.7.0) - Multi-package management
- **Yarn Workspaces** - Dependency management
- **Nx** - Build orchestration and caching

### Main Directories
- `packages/core/` - Core Strapi packages (admin, strapi, database, content-manager, upload, etc.)
- `packages/plugins/` - Official plugins (graphql, i18n, users-permissions, cloud, documentation, sentry)
- `packages/providers/` - Provider implementations (email: SES, Mailgun, SendGrid; upload: S3, Cloudinary)
- `packages/cli/` - CLI tools (create-strapi-app, cloud CLI)
- `packages/utils/` - Utility packages (logger, typescript, upgrade)
- `packages/generators/` - Code generators
- `examples/` - Example applications for testing and development
- `tests/` - Test suites (API tests, E2E tests, CLI tests)
- `docs/` - Internal documentation (using Docusaurus)

## Version Information
- Current version: **5.31.3**
- Node.js support: **v20.x - v24.x** (recommended: v24.x)
- Package manager: **Yarn v1.2.0+** (preferred over npm)

## Tech Stack
- **Backend**: Node.js, TypeScript, Koa.js
- **Frontend (Admin)**: React, TypeScript
- **Database ORM**: Custom @strapi/database
- **Build Tools**: Rollup, SWC, Nx
- **Testing**: Jest, Playwright (E2E)
- **Linting**: ESLint (Airbnb config extended)
- **Formatting**: Prettier
- **Bundler**: Rollup with SWC for TypeScript compilation
