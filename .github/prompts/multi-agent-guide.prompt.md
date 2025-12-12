# Multi-Agent Prompt Guide for Strapi Development

## Overview

This guide provides ready-to-use prompts for coordinating the 8 specialized agents in your Strapi development workflow. Each prompt is designed to get optimal results from the respective agent.

## Table of Contents

1. [Business Analysis Prompts](#business-analysis-prompts)
2. [Architecture Design Prompts](#architecture-design-prompts)
3. [Software Design Prompts](#software-design-prompts)
4. [Implementation Prompts](#implementation-prompts)
5. [Testing Prompts](#testing-prompts)
6. [DevOps Prompts](#devops-prompts)
7. [Documentation Prompts](#documentation-prompts)
8. [Release Management Prompts](#release-management-prompts)
9. [Multi-Agent Workflows](#multi-agent-workflows)
10. [Related Prompt Libraries](#related-prompt-libraries)

---

## 📖 Related Prompt Libraries

### Specialized Prompt Files

- **[lead-software-architect-extraction.prompt.md](./lead-software-architect-extraction.prompt.md)**: 
  - 10 comprehensive prompts for low-level architecture extraction
  - C4 diagrams (Component, Container)
  - UML diagrams (Sequence, ERD, Class, Communication, State Machine)
  - Architecture patterns extraction with code examples
  - Design patterns extraction (GoF patterns)
  - Algorithm extraction with complexity analysis
  - All in PlantUML format with detailed examples and best practices

---

## Business Analysis Prompts

### Requirements Analysis

```
@senior-business-analyst analyze the requirements for [FEATURE_NAME] in our Strapi application. 

Context:
- Current system: [describe current state]
- User pain points: [list pain points]
- Business goals: [list goals]
- Constraints: [technical/business constraints]

Please provide:
1. Detailed requirements document
2. User stories with acceptance criteria
3. Content type models (if applicable)
4. Feature scope and priorities
```

**Example**:
```
@senior-business-analyst analyze the requirements for a multi-level content approval workflow.

Context:
- Current system: Content is published directly without review
- User pain points: No quality control, compliance issues, lack of editorial oversight
- Business goals: Ensure content quality, meet compliance requirements, improve collaboration
- Constraints: Must work with existing content types, 3 approval levels max, email notifications required

Please provide:
1. Detailed requirements document
2. User stories with acceptance criteria
3. Content type models for approval tracking
4. Feature scope and priorities
```

### User Story Creation

```
@senior-business-analyst create user stories for [FEATURE_NAME] following the Strapi content management context.

Roles involved:
- [list user roles, e.g., Editor, Reviewer, Admin]

User goals:
- [list what each role wants to achieve]

Include:
1. User stories in "As a... I want... So that..." format
2. Acceptance criteria for each story
3. Story point estimates
4. Priority levels (Must Have, Should Have, Nice to Have)
```

**Example**:
```
@senior-business-analyst create user stories for content scheduling feature.

Roles involved:
- Content Editor: Creates and manages content
- Marketing Manager: Plans content campaigns
- Admin: Configures scheduling rules

User goals:
- Schedule content publication for specific dates/times
- Bulk schedule multiple items
- Automatically unpublish content after a period
- View scheduled content calendar

Include:
1. User stories with acceptance criteria
2. Story point estimates
3. Priority levels
```

### Content Type Modeling

```
@senior-business-analyst design Strapi content types for [FEATURE_NAME].

Requirements:
- [list data requirements]

Relationships:
- [describe relationships between entities]

Please provide:
1. Content type schemas
2. Field definitions with validations
3. Relationship diagrams
4. Example content structures
```

---

## Architecture Design Prompts

### Architecture Extraction

```
@principal-solution-architect extract and document the architecture of [PACKAGE/MODULE] in the Strapi codebase.

Focus areas:
- [list specific areas to analyze]

Please provide:
1. C4 Context diagram
2. C4 Container diagram
3. Design patterns identified
4. Key architectural decisions
5. Integration points
6. Technology stack analysis
```

**Example**:
```
@principal-solution-architect extract and document the architecture of packages/core/content-manager.

Focus areas:
- Service layer organization
- Controller patterns
- State management
- API design
- Plugin integration

Please provide:
1. C4 Context and Container diagrams
2. Design patterns (MVC, Service Layer, etc.)
3. Key architectural decisions
4. Integration with core Strapi
5. Technology stack
```

### System Design

```
@principal-solution-architect design the high-level architecture for [FEATURE_NAME] integrating with Strapi.

Requirements:
- [key requirements]

Integration points:
- [Strapi services to integrate with]

Constraints:
- [technical constraints]

Please provide:
1. Architecture diagrams (C4)
2. Component breakdown
3. Data flow diagrams
4. Design patterns to use
5. Technology recommendations
6. Scalability considerations
```

**Example**:
```
@principal-solution-architect design the high-level architecture for a real-time collaboration feature.

Requirements:
- Multiple users editing simultaneously
- Real-time updates
- Conflict resolution
- Audit trail

Integration points:
- Strapi content-manager
- Event hub
- Webhook system

Constraints:
- Must support 50+ concurrent users
- < 100ms latency for updates
- Work with PostgreSQL

Please provide architecture design with WebSocket integration and operational transformation pattern.
```

---

## Software Design Prompts

### Detailed Design

```
@lead-software-architect create detailed technical design for [FEATURE_NAME].

High-level design:
- [reference architecture from Solution Architect]

Requirements:
- [detailed requirements]

Please provide:
1. Component design (classes, interfaces)
2. Database schema with migrations
3. API specifications (request/response)
4. Sequence diagrams for key flows
5. Error handling strategy
6. Security considerations
```

### Task Breakdown

```
@lead-software-architect break down [FEATURE_NAME] into implementable development tasks.

Design:
- [reference detailed design]

Please provide:
1. Task list with descriptions
2. Dependencies between tasks
3. Effort estimates (story points or hours)
4. Implementation order
5. Testing requirements per task
6. Risk assessment
```

**Example**:
```
@lead-software-architect break down the content approval workflow into development tasks.

Design:
- Service-based architecture
- REST API endpoints
- Email notification system
- Approval state machine

Please provide:
1. Granular task breakdown
2. Task dependencies
3. Effort estimates
4. Suggested implementation order (MVP first)
5. Testing strategy per task
```

---

## Implementation Prompts

### TDD Implementation

```
@senior-software-engineer implement [FEATURE/COMPONENT] following TDD approach.

Requirements:
- [detailed requirements]

Design:
- [technical specifications]

Please:
1. Write failing tests first (Red)
2. Implement minimal code to pass (Green)
3. Refactor for quality (Refactor)
4. Ensure 90%+ test coverage
5. Follow SOLID, KISS, DRY principles
6. Use Strapi conventions
```

**Example**:
```
@senior-software-engineer implement the approval service following TDD.

Requirements:
- Request approval for content
- Approve with comments
- Reject with reason
- Track approval history
- Send email notifications

Design:
- Service pattern with dependency injection
- Integration with Strapi document service
- Event emission on state changes

Please implement with comprehensive unit and integration tests, following Strapi's service patterns.
```

### Service Implementation

```
@senior-software-engineer create a Strapi service for [FUNCTIONALITY].

Service responsibilities:
- [list responsibilities]

Integration:
- [Strapi services to use]

Please provide:
1. Service implementation
2. Unit tests (90%+ coverage)
3. Integration tests
4. JSDoc documentation
5. Error handling
6. Input validation
```

### Controller Implementation

```
@senior-software-engineer create a Strapi controller for [ENDPOINTS].

Endpoints:
- [list endpoints with methods]

Service methods:
- [reference service methods]

Please provide:
1. Controller implementation
2. Route definitions
3. Request validation
4. Response formatting
5. Error handling
6. Integration tests
```

---

## Testing Prompts

### Unit Testing

```
@senior-qa-engineer create comprehensive unit tests for [COMPONENT/SERVICE].

Code to test:
- [reference implementation]

Coverage requirements:
- Minimum 90% code coverage
- Test all edge cases
- Test error scenarios

Please provide:
1. Unit test suite (Jest)
2. Mock setup for dependencies
3. Test coverage report
4. Edge case documentation
```

### Integration Testing

```
@senior-qa-engineer create integration tests for [API ENDPOINTS].

Endpoints:
- [list endpoints]

Test scenarios:
- Happy path
- Error cases
- Edge cases
- Authentication/authorization

Please provide:
1. Integration test suite
2. Test data factories
3. API test documentation
4. Coverage report
```

**Example**:
```
@senior-qa-engineer create integration tests for the approval API endpoints.

Endpoints:
- POST /api/approval/request
- POST /api/approval/:id/approve
- POST /api/approval/:id/reject
- GET /api/approval/pending
- GET /api/approval/:id

Test scenarios:
- Successful approval request
- Approval with valid permissions
- Rejection with reason
- Error cases (not found, forbidden, unauthorized)
- Concurrent requests

Please provide comprehensive integration tests with test database setup.
```

### E2E Testing

```
@senior-qa-engineer create E2E tests for [USER WORKFLOW] using Playwright.

User workflow:
- [describe step-by-step workflow]

Roles:
- [user roles involved]

Please provide:
1. Playwright test suite
2. Page object models
3. Test data setup
4. Visual regression tests (if applicable)
```

### Performance Testing

```
@senior-qa-engineer create performance tests for [FEATURE/ENDPOINT] using K6.

Performance requirements:
- [target metrics]

Load scenarios:
- [describe load patterns]

Please provide:
1. K6 test script
2. Load test scenarios (ramp-up, sustained, spike)
3. Performance baseline
4. Acceptance criteria
5. Test report template
```

---

## DevOps Prompts

### CI/CD Pipeline

```
@senior-devops-engineer create a GitHub Actions CI/CD pipeline for [PROJECT/FEATURE].

Pipeline requirements:
- [list requirements]

Stages needed:
- Code quality (ESLint, Prettier, TypeScript)
- Testing (unit, integration, E2E)
- Security scanning (Snyk, Trivy)
- Build (Docker)
- Deploy (staging, production)

Please provide:
1. GitHub Actions workflow YAML
2. Docker build configuration
3. Deployment scripts
4. Rollback procedure
```

### Docker Setup

```
@senior-devops-engineer create production-ready Docker setup for Strapi application.

Requirements:
- Multi-stage build
- Optimized image size
- Security best practices
- Health checks

Please provide:
1. Dockerfile (multi-stage)
2. docker-compose.yml (development)
3. .dockerignore
4. Docker security scan configuration
```

### Kubernetes Deployment

```
@senior-devops-engineer create Kubernetes manifests for Strapi application.

Requirements:
- [replica count, resources]

Services needed:
- Strapi application
- PostgreSQL database
- Redis cache

Please provide:
1. Deployment manifests
2. Service manifests
3. Ingress configuration
4. ConfigMaps and Secrets
5. HorizontalPodAutoscaler
6. PersistentVolumeClaims
```

### Monitoring Setup

```
@senior-devops-engineer setup monitoring for Strapi application.

Metrics needed:
- [list metrics]

Tools:
- Prometheus
- Grafana
- [other tools]

Please provide:
1. Prometheus configuration
2. Grafana dashboard JSON
3. Alert rules
4. Metrics exporters
5. Documentation
```

---

## Documentation Prompts

### API Documentation

```
@senior-technical-writer create API documentation for [ENDPOINTS] in OpenAPI format.

Endpoints:
- [list endpoints]

Include:
- Request/response schemas
- Authentication
- Error codes
- Examples

Please provide:
1. OpenAPI 3.0 specification
2. Request/response examples
3. Error documentation
4. Authentication guide
```

**Example**:
```
@senior-technical-writer create comprehensive API documentation for the approval workflow endpoints.

Endpoints:
- POST /api/approval/request
- POST /api/approval/:id/approve
- POST /api/approval/:id/reject
- GET /api/approval/pending
- GET /api/approval/:id

Include detailed examples, error codes, and authentication requirements in OpenAPI format.
```

### User Guide

```
@senior-technical-writer create a user guide for [FEATURE].

Target audience:
- [describe users]

Topics to cover:
- [list topics]

Please provide:
1. Step-by-step instructions
2. Screenshots/diagrams
3. Common use cases
4. Troubleshooting section
5. FAQ
```

### Architecture Documentation

```
@senior-technical-writer create architecture documentation for [SYSTEM/MODULE].

Architecture details:
- [reference architecture diagrams]

Design patterns:
- [reference patterns]

Please provide:
1. Architecture overview
2. Component descriptions
3. Design pattern explanations
4. Integration documentation
5. Deployment architecture
```

---

## Release Management Prompts

### Changelog Generation

```
@senior-release-manager generate changelog for version [VERSION].

Changes since last release:
- [list changes or reference commits]

Release type:
- [Major/Minor/Patch]

Please provide:
1. Changelog in Keep a Changelog format
2. Categorized changes (Added, Changed, Fixed, Deprecated, Security)
3. Migration notes (if needed)
```

### Release Notes

```
@senior-release-manager create release notes for version [VERSION].

Features:
- [list new features]

Bug fixes:
- [list fixes]

Breaking changes:
- [list breaking changes]

Target audience:
- [developers/end-users/both]

Please provide:
1. User-facing release notes
2. Upgrade instructions
3. Breaking change migration guide
4. Deprecation notices
```

**Example**:
```
@senior-release-manager create release notes for v1.5.0 including the content approval workflow.

Features:
- Content approval workflow
- Multi-language support
- Advanced search filters

Bug fixes:
- Pagination with large datasets
- Memory leak in uploads
- Timezone handling

Breaking changes:
- None

Please create comprehensive release notes for end-users with upgrade instructions.
```

---

## Multi-Agent Workflows

### Complete Feature Development

```bash
# Step 1: Requirements
@senior-business-analyst analyze requirements for [FEATURE_NAME]

# Step 2: High-level Architecture  
@principal-solution-architect design architecture for [FEATURE_NAME] based on the requirements

# Step 3: Detailed Design
@lead-software-architect create detailed design and task breakdown for [FEATURE_NAME]

# Step 4: Implementation
@senior-software-engineer implement [COMPONENT] following TDD

# Step 5: Testing
@senior-qa-engineer create comprehensive test suite for [COMPONENT]

# Step 6: Documentation
@senior-technical-writer create API docs and user guide for [FEATURE_NAME]

# Step 7: Deployment
@senior-devops-engineer setup CI/CD and deploy [FEATURE_NAME]

# Step 8: Release
@senior-release-manager prepare release notes for version [VERSION]
```

### Bug Investigation and Fix

```bash
# Step 1: Root Cause Analysis
@principal-solution-architect analyze bug [BUG_ID] and identify root cause

# Step 2: Fix Design
@lead-software-architect design the fix for [BUG_ID]

# Step 3: Implementation
@senior-software-engineer implement fix for [BUG_ID] with tests

# Step 4: Verification
@senior-qa-engineer verify fix and run regression tests for [BUG_ID]
```

### Performance Optimization

```bash
# Step 1: Identify Bottlenecks
@principal-solution-architect analyze performance and identify bottlenecks in [COMPONENT]

# Step 2: Design Optimization
@lead-software-architect design optimization strategy for [COMPONENT]

# Step 3: Implement
@senior-software-engineer implement performance optimizations for [COMPONENT]

# Step 4: Benchmark
@senior-qa-engineer run performance tests and compare before/after metrics

# Step 5: Monitor
@senior-devops-engineer setup monitoring for [COMPONENT] performance metrics
```

---

## Tips for Effective Prompts

### 1. Be Specific
❌ "Create tests"  
✅ "Create unit tests for the approval service with 90% coverage, testing happy path, error cases, and edge cases"

### 2. Provide Context
Include:
- Current state
- Requirements
- Constraints
- Expected output

### 3. Reference Previous Work
```
Based on the architecture designed by @principal-solution-architect, 
@senior-software-engineer implement the approval service...
```

### 4. Set Quality Standards
```
Please ensure:
- SOLID principles followed
- 90%+ test coverage
- ESLint passing
- TypeScript strict mode
- Strapi conventions
```

### 5. Request Specific Deliverables
```
Please provide:
1. Implementation code
2. Unit tests
3. Integration tests
4. JSDoc documentation
5. Usage examples
```

---

**Last Updated**: December 11, 2025  
**Prompt Guide Version**: 1.0.0
