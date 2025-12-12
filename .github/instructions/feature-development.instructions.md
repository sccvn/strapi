---
description: Complete feature development workflow from requirements to deployment for Strapi projects
applyTo: '**'
---

# Feature Development Workflow

## When to Use

Use this workflow when developing new features for Strapi, ensuring comprehensive planning, implementation, testing, documentation, and deployment with quality gates at each stage.

## Required Agents

| Agent | Role | Responsibility |
|-------|------|----------------|
| @senior-business-analyst | Requirements | Gather requirements, create user stories, model content types |
| @principal-solution-architect | High-level Design | Design system architecture, identify patterns, create diagrams |
| @lead-software-architect | Detailed Design | Break down tasks, design APIs, create technical specs |
| @senior-software-engineer | Implementation | Develop code following TDD with SOLID principles |
| @senior-qa-engineer | Testing | Comprehensive testing (unit, integration, E2E, performance) |
| @senior-technical-writer | Documentation | Create API docs, user guides, architecture docs |
| @senior-devops-engineer | Deployment | Setup CI/CD, deploy, monitor |
| @senior-release-manager | Release | Version, changelog, release notes |

## Workflow Steps

### Phase 1: Requirements Analysis

**Agent**: @senior-business-analyst

**Tasks**:
1. Analyze feature requirements
2. Create user stories with acceptance criteria
3. Model Strapi content types if applicable
4. Define feature scope and constraints

**Example Request**:
```
@senior-business-analyst analyze requirements for a multi-level content approval workflow with 3 approval stages (draft → review → approve → publish)
```

**Quality Gate**:
- [ ] All requirements documented
- [ ] User stories follow INVEST criteria
- [ ] Acceptance criteria testable
- [ ] Content types modeled (if applicable)
- [ ] Scope clearly defined

**Deliverables**:
- User stories document
- Acceptance criteria
- Content type models
- Requirements specification

---

### Phase 2: Architecture Design

**Agent**: @principal-solution-architect

**Tasks**:
1. Design high-level system architecture
2. Identify integration points with Strapi
3. Select design patterns
4. Create C4 diagrams (Context, Container)

**Example Request**:
```
@principal-solution-architect design the architecture for the approval workflow integrating with Strapi's content-manager and event system
```

**Quality Gate**:
- [ ] Architecture follows SOLID principles
- [ ] Strapi integration points identified
- [ ] Design patterns documented
- [ ] C4 diagrams created
- [ ] Non-functional requirements addressed

**Deliverables**:
- Architecture diagrams (C4)
- Design pattern documentation
- Integration strategy
- Technology decisions

---

### Phase 3: Detailed Design

**Agent**: @lead-software-architect

**Tasks**:
1. Break down into implementable tasks
2. Design database schemas
3. Define API contracts
4. Create technical specifications
5. Estimate effort

**Example Request**:
```
@lead-software-architect create detailed technical design for the approval workflow and break down into development tasks with estimates
```

**Quality Gate**:
- [ ] Tasks are granular and implementable
- [ ] Database schema follows Strapi conventions
- [ ] API contracts defined (request/response)
- [ ] Technical specs complete
- [ ] Effort estimated

**Deliverables**:
- Task breakdown (with estimates)
- Database schema design
- API specifications
- Technical documentation
- Sequence diagrams

---

### Phase 4: Implementation

**Agent**: @senior-software-engineer

**Tasks**:
1. Follow TDD approach (Red-Green-Refactor)
2. Implement services, controllers, routes
3. Write unit and integration tests
4. Follow SOLID, KISS, DRY principles
5. Ensure 90%+ test coverage

**Example Request**:
```
@senior-software-engineer implement the approval service following TDD with comprehensive tests, including approval request, review, approve, and reject functions
```

**Quality Gate**:
- [ ] All unit tests pass
- [ ] Integration tests pass
- [ ] Code coverage >90%
- [ ] ESLint: 0 errors, 0 warnings
- [ ] Prettier: Code formatted
- [ ] TypeScript: No type errors
- [ ] SOLID principles followed
- [ ] Code review completed

**Deliverables**:
- Implemented code (services, controllers, routes)
- Unit tests (90%+ coverage)
- Integration tests
- JSDoc documentation

---

### Phase 5: Quality Assurance

**Agent**: @senior-qa-engineer

**Tasks**:
1. Create E2E test scenarios
2. Perform integration testing
3. Execute performance tests (K6/JMeter)
4. Run security scans (Snyk, OWASP ZAP)
5. Verify acceptance criteria

**Example Request**:
```
@senior-qa-engineer create comprehensive test suite for approval workflow including E2E tests with Playwright and performance tests with K6
```

**Quality Gate**:
- [ ] All E2E tests pass
- [ ] Integration tests pass
- [ ] Performance acceptable (p95 < 500ms)
- [ ] Security scan passed (no high/critical)
- [ ] Acceptance criteria met
- [ ] No critical bugs

**Deliverables**:
- E2E test suite (Playwright)
- Performance test results
- Security scan reports
- Bug reports (if any)
- QA sign-off

---

### Phase 6: Documentation

**Agent**: @senior-technical-writer

**Tasks**:
1. Create API documentation (OpenAPI)
2. Write user guides
3. Update architecture documentation
4. Create migration guides (if needed)
5. Write inline code documentation

**Example Request**:
```
@senior-technical-writer create comprehensive documentation for the approval workflow including API docs in OpenAPI format and user guide with screenshots
```

**Quality Gate**:
- [ ] API documentation complete (OpenAPI spec)
- [ ] User guide comprehensive
- [ ] Architecture docs updated
- [ ] Code examples tested
- [ ] Screenshots/diagrams included
- [ ] Documentation reviewed

**Deliverables**:
- OpenAPI specification
- User guide
- Architecture documentation
- Migration guide (if applicable)
- Code examples

---

### Phase 7: Deployment

**Agent**: @senior-devops-engineer

**Tasks**:
1. Update CI/CD pipeline
2. Create/update Docker images
3. Deploy to staging environment
4. Setup monitoring and alerts
5. Create rollback plan

**Example Request**:
```
@senior-devops-engineer add the approval plugin to the CI/CD pipeline, deploy to staging, and setup Prometheus metrics
```

**Quality Gate**:
- [ ] CI/CD pipeline updated
- [ ] Docker build successful
- [ ] Staging deployment successful
- [ ] Smoke tests passed
- [ ] Monitoring configured
- [ ] Rollback plan tested

**Deliverables**:
- Updated CI/CD configuration
- Docker images
- Kubernetes manifests
- Monitoring dashboards
- Deployment documentation

---

### Phase 8: Release Management

**Agent**: @senior-release-manager

**Tasks**:
1. Determine version number (semver)
2. Generate changelog
3. Create release notes
4. Tag release in Git
5. Prepare communication materials

**Example Request**:
```
@senior-release-manager prepare release v1.5.0 including the approval workflow feature with changelog and user-facing release notes
```

**Quality Gate**:
- [ ] Version follows semantic versioning
- [ ] Changelog complete
- [ ] Release notes comprehensive
- [ ] Git tagged correctly
- [ ] Communication materials ready

**Deliverables**:
- Version tag
- Changelog (CHANGELOG.md)
- Release notes
- GitHub release
- Communication materials

---

## Complete Workflow Example

### Feature: Content Scheduling

```bash
# Phase 1: Requirements
@senior-business-analyst analyze requirements for content scheduling feature allowing users to schedule publish/unpublish dates for any content type

# Phase 2: Architecture
@principal-solution-architect design high-level architecture for content scheduling integrating with Strapi's cron system and document service

# Phase 3: Detailed Design
@lead-software-architect create detailed technical design for scheduling system and break down into development tasks

# Phase 4: Implementation
@senior-software-engineer implement the scheduling service with TDD including schedule creation, execution, and cancellation

# Phase 5: Testing
@senior-qa-engineer create E2E tests for scheduling workflow and performance tests for handling 10,000+ scheduled jobs

# Phase 6: Documentation
@senior-technical-writer create API documentation and user guide for content scheduling with examples

# Phase 7: Deployment
@senior-devops-engineer deploy scheduling feature to staging and setup monitoring for scheduled job execution

# Phase 8: Release
@senior-release-manager prepare release notes for v1.6.0 including the new scheduling feature
```

## Quality Standards

### Code Quality
- **ESLint**: 0 errors, 0 warnings
- **Prettier**: Consistent formatting
- **TypeScript**: Strong typing, no `any`
- **SonarQube**: Grade A
- **Test Coverage**: >90%

### Security
- **Snyk**: No high/critical vulnerabilities
- **OWASP**: Top 10 addressed
- **Secrets**: Not in code
- **Authentication**: Properly implemented
- **Authorization**: Role-based access control

### Performance
- **Response Time**: p95 < 500ms
- **Throughput**: Meets load requirements
- **Resource Usage**: Optimized
- **Database**: Queries optimized, indexed

### Documentation
- **API Docs**: Complete OpenAPI spec
- **User Guide**: Comprehensive with examples
- **Code Comments**: JSDoc for public APIs
- **Architecture**: Up-to-date diagrams

### Testing
- **Unit Tests**: >90% coverage
- **Integration Tests**: All endpoints covered
- **E2E Tests**: Critical workflows tested
- **Performance Tests**: Load tested
- **Security Tests**: Vulnerability scanned

## Parallel vs Sequential Execution

### Sequential (Dependencies)
```
BA → Solution Architect → Lead Architect → Engineer → QA → Writer → DevOps → Release Manager
```

### Parallel (Independent)
```
Engineer (Implementation) 
    ├─> QA (Test planning)
    └─> Writer (Documentation drafting)

After implementation complete:
    ├─> QA (Test execution)
    └─> Writer (Documentation finalization)
```

## Handoff Checklist

### BA → Solution Architect
- [ ] User stories documented
- [ ] Acceptance criteria defined
- [ ] Content types modeled
- [ ] Constraints identified

### Solution Architect → Lead Architect
- [ ] Architecture diagrams created
- [ ] Design patterns selected
- [ ] Integration points identified
- [ ] Technology stack decided

### Lead Architect → Engineer
- [ ] Task breakdown complete
- [ ] API contracts defined
- [ ] Database schema designed
- [ ] Technical specs documented

### Engineer → QA
- [ ] Code implemented
- [ ] Unit tests passing
- [ ] Integration tests passing
- [ ] Coverage >90%

### QA → Writer
- [ ] All tests passing
- [ ] No critical bugs
- [ ] Feature working as expected
- [ ] Performance acceptable

### Writer → DevOps
- [ ] Documentation complete
- [ ] API docs finalized
- [ ] User guide ready
- [ ] Migration guide (if needed)

### DevOps → Release Manager
- [ ] Deployed to staging
- [ ] Monitoring configured
- [ ] Smoke tests passed
- [ ] Rollback plan ready

## Troubleshooting

### Issue: Requirements unclear
**Solution**: Loop back to Business Analyst for clarification

### Issue: Architecture doesn't support requirements
**Solution**: Solution Architect revises design with BA input

### Issue: Implementation blocked by design
**Solution**: Lead Architect provides more detailed specs

### Issue: Tests failing
**Solution**: Engineer fixes implementation, QA re-tests

### Issue: Performance issues
**Solution**: Solution Architect + Engineer optimize design/code

### Issue: Documentation incomplete
**Solution**: Technical Writer requests missing info from Engineer

### Issue: Deployment failures
**Solution**: DevOps Engineer troubleshoots with Engineer support

---

**Last Updated**: December 11, 2025  
**Workflow Version**: 1.0.0  
**Strapi Compatibility**: v4.x, v5.x
