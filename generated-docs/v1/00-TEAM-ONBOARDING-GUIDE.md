# Strapi v5.31.3 - Team Onboarding Guide

**Generated**: December 12, 2025  
**Version**: v1.0  
**Purpose**: Comprehensive guide for development teams to understand Strapi's architecture, design patterns, and codebase

---

## 📋 Table of Contents

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Learning Path](#learning-path)
4. [Documentation Structure](#documentation-structure)
5. [Team Tasks & Assignments](#team-tasks--assignments)
6. [Architecture Understanding Checklist](#architecture-understanding-checklist)
7. [Quick Reference](#quick-reference)
8. [Next Steps](#next-steps)

---

## Introduction

Welcome to the Strapi development team! This guide will help you systematically understand the Strapi codebase, architecture, and design patterns. All documentation in this folder (v1) has been generated from analyzing the actual Strapi v5.31.3 codebase.

### What is Strapi?

Strapi is an open-source headless CMS built with:
- **Backend**: Node.js 20-24, TypeScript, Koa.js
- **Frontend**: React 18, Redux
- **Database**: PostgreSQL/MySQL/SQLite with custom ORM
- **Architecture**: Monorepo with 40+ packages
- **Build System**: Rollup + SWC

### Why This Guide?

- **Faster onboarding**: Structured learning path reduces ramp-up time from weeks to days
- **Comprehensive coverage**: All major architectural components documented
- **Code-based**: Generated from actual source code, not assumptions
- **Team-oriented**: Designed for coordinated team learning

---

## Prerequisites

### Required Knowledge
- ✅ JavaScript/TypeScript proficiency
- ✅ Node.js and npm/yarn
- ✅ Git and version control
- ✅ REST API concepts
- ✅ Database basics (SQL)

### Recommended Knowledge
- 🔵 React (for admin panel development)
- 🔵 Koa.js or Express.js
- 🔵 Design patterns (Gang of Four)
- 🔵 Architectural patterns
- 🔵 Docker and containerization

### Tools to Install
```bash
# Node.js (via nvm)
nvm install 20
nvm use 20

# Yarn
npm install -g yarn

# PostgreSQL (for development)
docker run -d -p 5432:5432 \\
  -e POSTGRES_DB=strapi \\
  -e POSTGRES_USER=strapi \\
  -e POSTGRES_PASSWORD=strapi \\
  postgres:15

# Clone repository
git clone https://github.com/strapi/strapi.git
cd strapi
yarn install
```

---

## Learning Path

Follow this recommended sequence for maximum understanding:

### Week 1: System Architecture (Days 1-2)

**Goal**: Understand high-level system structure

| Day | Document | Time | Focus |
|-----|----------|------|-------|
| 1 | [01-c4-container-diagram.md](./01-c4-container-diagram.md) | 2-3h | System containers, deployment |
| 1 | [02-c4-component-content-manager.md](./02-c4-component-content-manager.md) | 2-3h | Internal plugin architecture |
| 2 | [03-sequence-content-creation.md](./03-sequence-content-creation.md) | 3-4h | Request lifecycle, workflows |

**Activities**:
- Draw the architecture on whiteboard
- Trace a request through the system
- Identify integration points

**Checkpoint**: Can you explain the flow from "user creates content" to "data in database"?

### Week 1: Data Model (Days 3-4)

**Goal**: Understand database schema and relationships

| Day | Document | Time | Focus |
|-----|----------|------|-------|
| 3 | [04-erd-core-types.md](./04-erd-core-types.md) | 4-5h | Database tables, relationships |
| 4 | Review + Practice | 3-4h | Write queries, explore schema |

**Activities**:
- Connect to development database
- Explore tables with SQL queries
- Understand document versioning
- Study component join tables

**Checkpoint**: Can you explain draft/publish workflow and component relationships?

### Week 2: Architecture Patterns (Days 5-7)

**Goal**: Master architectural patterns used in Strapi

| Day | Document | Time | Focus |
|-----|----------|------|-------|
| 5 | [05-architecture-patterns.md](./05-architecture-patterns.md) | 3-4h | DI, Plugins, Events |
| 6 | Continue above | 3-4h | Middleware, Services, Registry |
| 7 | Hands-on Practice | 4-5h | Implement simple plugin |

**Activities**:
- Study each of 10 architecture patterns
- Find examples in codebase
- Create a simple plugin using patterns

**Checkpoint**: Can you create a plugin that listens to events and uses the service layer?

### Week 2: Design Patterns (Days 8-10)

**Goal**: Recognize GoF patterns in implementation

| Day | Document | Time | Focus |
|-----|----------|------|-------|
| 8 | [06-design-patterns.md](./06-design-patterns.md) | 4-5h | Factory, Strategy, Observer |
| 9 | Continue above | 3-4h | Builder, Decorator, Facade |
| 10 | Code Review | 3-4h | Find patterns in codebase |

**Activities**:
- Study 12+ design patterns
- Locate implementations in source
- Understand when each is used

**Checkpoint**: Can you identify and explain design patterns when reviewing code?

### Week 3: Algorithms & Implementation (Days 11-15)

**Goal**: Deep dive into core algorithms

| Day | Document | Time | Focus |
|-----|----------|------|-------|
| 11 | [07-algorithms-document-service.md](./07-algorithms-document-service.md) | 4-5h | Populate algorithm |
| 12 | Continue above | 4-5h | Permission resolution |
| 13 | Continue above | 4-5h | Lifecycle management |
| 14 | Hands-on | 4-6h | Implement features |
| 15 | Code Review | 3-4h | Team code review |

**Activities**:
- Understand populate depth control
- Study permission checking flow
- Trace lifecycle hook execution
- Implement a custom service

**Checkpoint**: Can you implement a service with proper lifecycle hooks and permissions?

---

## Documentation Structure

All documentation is in `generated-docs/v1/`:

```
generated-docs/v1/
├── 00-TEAM-ONBOARDING-GUIDE.md       ← You are here
├── 01-c4-container-diagram.md        ← System architecture
├── 02-c4-component-content-manager.md ← Plugin internals
├── 03-sequence-content-creation.md    ← Request workflows
├── 04-erd-core-types.md              ← Database schema
├── 05-architecture-patterns.md        ← 10 architectural patterns
├── 06-design-patterns.md             ← GoF design patterns
├── 07-algorithms-document-service.md  ← Core algorithms
└── README.md                          ← Overview
```

### Document Types

| Icon | Type | Purpose |
|------|------|---------|
| 📐 | Diagrams | PlantUML diagrams for architecture visualization |
| 📊 | Schema | Database and data structure documentation |
| 🏗️ | Patterns | Reusable solution templates |
| ⚙️ | Algorithms | Step-by-step computational processes |
| 💡 | Examples | Real code examples from Strapi |

---

## Team Tasks & Assignments

### Role-Based Learning Tracks

#### Backend Developers

**Primary Focus**:
1. ✅ 01-c4-container-diagram.md (API Server, Plugin System)
2. ✅ 04-erd-core-types.md (Complete database understanding)
3. ✅ 05-architecture-patterns.md (Service Layer, Event-Driven)
4. ✅ 06-design-patterns.md (Factory, Strategy, Repository)
5. ✅ 07-algorithms-document-service.md (All algorithms)

**Tasks**:
- Week 1: Database schema mastery
- Week 2: Service layer implementation
- Week 3: Build custom API endpoint with tests

#### Frontend Developers

**Primary Focus**:
1. ✅ 01-c4-container-diagram.md (Admin Panel architecture)
2. ✅ 02-c4-component-content-manager.md (React component structure)
3. ✅ 03-sequence-content-creation.md (Client-server interaction)
4. ✅ 06-design-patterns.md (Observer, Facade for React)

**Tasks**:
- Week 1: Admin panel component architecture
- Week 2: State management with Redux
- Week 3: Create custom admin panel plugin

#### Full-Stack Developers

**Primary Focus**:
- All documents in sequence

**Tasks**:
- Week 1: System architecture end-to-end
- Week 2: Patterns and best practices
- Week 3: Full feature implementation (backend + frontend)

#### DevOps Engineers

**Primary Focus**:
1. ✅ 01-c4-container-diagram.md (Deployment architecture)
2. ✅ 04-erd-core-types.md (Database scaling)
3. ✅ 05-architecture-patterns.md (Configuration, Providers)

**Tasks**:
- Week 1: Understand deployment topology
- Week 2: Database optimization and indexing
- Week 3: CI/CD pipeline improvements

#### QA Engineers

**Primary Focus**:
1. ✅ 03-sequence-content-creation.md (Test scenarios)
2. ✅ 04-erd-core-types.md (Data validation)
3. ✅ 07-algorithms-document-service.md (Edge cases)

**Tasks**:
- Week 1: Map test coverage to architecture
- Week 2: Create integration test suites
- Week 3: Performance testing of core algorithms

---

## Architecture Understanding Checklist

Use this checklist to track your learning progress:

### System Architecture (Week 1)

- [ ] Can draw C4 Container diagram from memory
- [ ] Understand all 15 system containers
- [ ] Know external system integrations (S3, OAuth, etc.)
- [ ] Understand content-manager plugin structure
- [ ] Can trace request from React to Database
- [ ] Know all lifecycle hook stages
- [ ] Understand error handling flow

### Database & Data Model (Week 1)

- [ ] Know all core admin tables (users, roles, permissions)
- [ ] Understand content management tables (history, workflows)
- [ ] Master draft/publish mechanism
- [ ] Understand component join table structure
- [ ] Know all standard columns (documentId, createdAt, etc.)
- [ ] Can write complex queries with relations
- [ ] Understand index strategy

### Architecture Patterns (Week 2)

- [ ] Dependency Injection Container pattern
- [ ] Plugin Architecture pattern
- [ ] Event-Driven Architecture with EventHub
- [ ] Middleware Chain (Koa.js)
- [ ] Service Layer pattern
- [ ] Registry Pattern for entity management
- [ ] Provider Pattern for pluggable services
- [ ] Lifecycle Hooks pattern
- [ ] Configuration Management pattern
- [ ] Request Context pattern (AsyncLocalStorage)

### Design Patterns (Week 2)

- [ ] Factory Pattern (createCoreService, etc.)
- [ ] Strategy Pattern (authentication strategies)
- [ ] Observer Pattern (EventHub, listeners)
- [ ] Builder Pattern (populate builder, query builder)
- [ ] Repository Pattern (document repository)
- [ ] Decorator Pattern (middleware wrapping)
- [ ] Facade Pattern (high-level APIs)
- [ ] Singleton Pattern (container services)
- [ ] Chain of Responsibility (middleware chain)
- [ ] Template Method (base controller/service)

### Core Algorithms (Week 3)

- [ ] Populate algorithm with depth control
- [ ] Permission resolution algorithm
- [ ] Lifecycle hook execution order
- [ ] Document versioning algorithm
- [ ] Component creation/update algorithm
- [ ] Query transformation algorithm
- [ ] Data mapper (API ↔ Database)

---

## Quick Reference

### Key Files to Study

```
packages/core/
├── core/src/
│   ├── Strapi.ts                    # Main Strapi class
│   ├── container.ts                 # DI Container
│   ├── factories.ts                 # Factory functions
│   ├── services/
│   │   ├── event-hub.ts            # Event system
│   │   ├── document-service/       # Document CRUD
│   │   └── auth/                   # Authentication
│   ├── registries/                 # Registry pattern
│   └── middlewares/                # Core middlewares
├── content-manager/
│   └── server/src/
│       ├── controllers/            # Content controllers
│       └── services/               # Content services
└── strapi/src/
    └── node/core/
        └── plugins.ts              # Plugin loading
```

### Important Commands

```bash
# Development
yarn develop                  # Start in dev mode
yarn watch                   # Watch all packages
yarn build                   # Build all packages

# Testing
yarn test:unit               # Unit tests
yarn test:e2e                # E2E tests
yarn test:api                # API tests

# Linting
yarn lint                    # ESLint
yarn format                  # Prettier

# Database
yarn strapi migration:run    # Run migrations
yarn strapi console          # Interactive console
```

### Useful Environment Variables

```bash
# .env
NODE_ENV=development
DATABASE_CLIENT=postgres
DATABASE_HOST=localhost
DATABASE_PORT=5432
DATABASE_NAME=strapi
DATABASE_USERNAME=strapi
DATABASE_PASSWORD=strapi

ADMIN_JWT_SECRET=your_secret
API_TOKEN_SALT=your_salt
JWT_SECRET=your_jwt_secret

# Features
STRAPI_TELEMETRY_DISABLED=true
```

### Code Navigation Tips

1. **Find a service**: Search for `strapi.service('api::article.article')`
2. **Find a controller**: Search for `strapi.controller('api::article.article')`
3. **Find lifecycle hooks**: Search in `src/api/{name}/content-types/{type}/lifecycles.js`
4. **Find plugin registration**: Search for `register({ strapi })`
5. **Find event emissions**: Search for `strapi.eventHub.emit(`

---

## Team Collaboration Tasks

### Pair Programming Sessions

Schedule these pair sessions during onboarding:

| Week | Session | Topics | Duration |
|------|---------|--------|----------|
| 1 | Architecture walkthrough | C4 diagrams, request flow | 2h |
| 1 | Database deep dive | Schema, migrations, queries | 2h |
| 2 | Pattern implementation | Create a plugin together | 3h |
| 2 | Code review | Review PR using patterns | 2h |
| 3 | Feature development | Build end-to-end feature | 4h |
| 3 | Testing workshop | Write comprehensive tests | 3h |

### Knowledge Sharing

**Weekly Team Meetings** (1 hour each):
- Week 1: Present system architecture findings
- Week 2: Demonstrate pattern implementations
- Week 3: Share custom feature built

**Documentation Contributions**:
- Each team member documents one component they studied
- Create troubleshooting guides for common issues
- Build internal knowledge base

### Code Review Practice

**Review Checklist**:
- [ ] Follows architectural patterns documented
- [ ] Uses appropriate design patterns
- [ ] Implements lifecycle hooks correctly
- [ ] Has comprehensive tests (90%+ coverage)
- [ ] Follows TypeScript strict mode
- [ ] ESLint passes (0 errors, 0 warnings)
- [ ] Security: No SQL injection, XSS prevention
- [ ] Performance: Queries optimized, indexes used

---

## Next Steps

### After Completing Onboarding (Week 4+)

1. **Contribute to Strapi**
   - Find "good first issue" on GitHub
   - Submit pull request following contribution guidelines
   - Engage with Strapi community

2. **Build Internal Tools**
   - Custom admin panel plugins
   - Automation scripts
   - Developer tools

3. **Mentoring**
   - Help onboard next team members
   - Share your learnings
   - Improve this guide

4. **Advanced Topics**
   - Strapi Enterprise Edition features
   - Performance optimization
   - Security hardening
   - Scalability patterns

### Recommended Resources

**Official Documentation**:
- https://docs.strapi.io - Official Strapi docs
- https://strapi.io/blog - Strapi blog with tutorials

**Community**:
- https://discord.strapi.io - Strapi Discord
- https://forum.strapi.io - Community forum
- https://github.com/strapi/strapi - Source code

**Learning Resources**:
- "Design Patterns" by Gang of Four
- "Clean Architecture" by Robert C. Martin
- "Node.js Design Patterns" by Mario Casciaro

---

## Troubleshooting Common Issues

### Issue: "Cannot find module '@strapi/...'"
**Solution**: Run `yarn install` in project root

### Issue: Database connection failed
**Solution**: 
1. Check PostgreSQL is running: `docker ps`
2. Verify credentials in `.env`
3. Check network connectivity

### Issue: Port 1337 already in use
**Solution**: 
```bash
lsof -ti:1337 | xargs kill -9
```

### Issue: TypeScript errors
**Solution**:
```bash
yarn build         # Rebuild all packages
yarn clean         # Clean build artifacts
yarn install       # Reinstall dependencies
```

### Issue: Tests failing
**Solution**:
1. Clear test database: `yarn test:clean`
2. Rebuild: `yarn build`
3. Run specific test: `yarn test:unit path/to/test.test.ts`

---

## Contact & Support

### Internal Team Contacts

- **Architecture Questions**: @lead-architect
- **Backend Issues**: @backend-team-lead
- **Frontend Issues**: @frontend-team-lead
- **DevOps Support**: @devops-lead
- **QA Coordination**: @qa-lead

### External Resources

- **Strapi Support**: support@strapi.io
- **Enterprise Support**: enterprise@strapi.io
- **Security Issues**: security@strapi.io

---

## Appendix: Glossary

| Term | Definition |
|------|------------|
| **Content Type** | Schema definition for user-created content (e.g., Article, Product) |
| **Component** | Reusable data structure shared across content types |
| **Document** | Instance of a content type (e.g., specific article) |
| **documentId** | UUID identifying all versions of a document |
| **Draft/Publish** | Dual-state content (draft + published versions) |
| **UID** | Unique Identifier in format `api::name.name` or `plugin::name.name` |
| **Strapi Instance** | Running Strapi application (`strapi` object) |
| **Service Layer** | Business logic separated from controllers |
| **Middleware** | Function processing requests in Koa.js chain |
| **Policy** | Authorization middleware |
| **Lifecycle Hook** | Function called before/after CRUD operations |
| **EventHub** | Central event bus for publish-subscribe messaging |
| **Provider** | Pluggable implementation (email, upload, etc.) |
| **Registry** | Collection manager for similar entities |
| **Repository** | Data access layer |

---

**Version**: v1.0  
**Last Updated**: December 12, 2025  
**Maintained By**: Architecture Team  
**License**: Same as Strapi (MIT/SEE License)

---

## Feedback

This guide is continuously improved. Please provide feedback:
- What sections were most helpful?
- What was confusing or needs clarification?
- What additional topics should be covered?

Submit feedback via:
- Internal wiki discussion
- Team Slack channel #architecture
- Direct message to architecture team

---

**Ready to start? Begin with [01-c4-container-diagram.md](./01-c4-container-diagram.md)!** 🚀
