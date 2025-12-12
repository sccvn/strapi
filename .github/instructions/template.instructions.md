# GitHub Copilot Custom Instructions - Template

This template demonstrates the structure for custom instructions that coordinate multi-agent workflows in the Strapi project.

## Instruction Format

```markdown
---
description: Brief description of what this instruction does
applyTo: File pattern where this instruction applies (e.g., '**/*.ts', 'packages/core/**')
---

# Instruction Title

## Context
Provide context about when and why this instruction should be used.

## Agents to Use
List the specialized agents involved in this workflow.

## Workflow
Step-by-step process for completing the task.

## Quality Standards
Standards that must be met.

## Examples
Concrete examples of using this instruction.
```

## Common Instruction Patterns

### Pattern 1: Feature Development Instruction

```markdown
---
description: Complete workflow for developing new Strapi features with quality gates
applyTo: '**'
---

# Feature Development Workflow

## Context
Use this instruction when developing new features for Strapi from requirements to deployment.

## Agents Required
1. @senior-business-analyst - Requirements analysis
2. @principal-solution-architect - High-level design
3. @lead-software-architect - Detailed design
4. @senior-software-engineer - Implementation
5. @senior-qa-engineer - Testing
6. @senior-technical-writer - Documentation
7. @senior-devops-engineer - Deployment
8. @senior-release-manager - Release preparation

## Workflow Steps

### Step 1: Requirements (Business Analyst)
- Gather and analyze requirements
- Create user stories with acceptance criteria
- Model content types if needed
- Define feature scope

**Output**: User stories document, acceptance criteria, content type models

### Step 2: High-level Design (Solution Architect)
- Design system architecture
- Identify design patterns
- Create C4 diagrams
- Define integration points

**Output**: Architecture diagrams, design pattern documentation

### Step 3: Detailed Design (Lead Architect)
- Break down into implementable tasks
- Define database schemas
- Design APIs and interfaces
- Create technical specifications

**Output**: Task breakdown, technical specs, API contracts

### Step 4: Implementation (Software Engineer)
- Follow TDD approach (Red-Green-Refactor)
- Implement services, controllers, routes
- Write unit and integration tests
- Follow SOLID, KISS, DRY principles

**Output**: Implemented code, unit tests (90%+ coverage)

### Step 5: Testing (QA Engineer)
- Run comprehensive test suites
- Perform E2E testing with Playwright
- Execute performance tests (K6/JMeter)
- Security scanning (Snyk, OWASP ZAP)

**Output**: Test reports, performance metrics, security scan results

### Step 6: Documentation (Technical Writer)
- Create API documentation (OpenAPI)
- Write user guides
- Update architecture documentation
- Create migration guides if needed

**Output**: API docs, user guides, technical documentation

### Step 7: Deployment (DevOps Engineer)
- Update CI/CD pipeline
- Create/update Docker images
- Deploy to staging environment
- Setup monitoring and alerts

**Output**: Deployed application, monitoring dashboards

### Step 8: Release (Release Manager)
- Update version numbers
- Generate changelog
- Create release notes
- Prepare communication materials

**Output**: Release notes, changelog, version tags

## Quality Gates

Each step must meet quality standards before proceeding:

- **Requirements**: Clear, testable, complete
- **Design**: Follows SOLID, patterns documented
- **Code**: Tests pass, coverage >90%, no lint errors
- **Testing**: All tests pass, no critical bugs
- **Documentation**: Accurate, complete, accessible
- **Deployment**: Successful, monitored, rollback tested
- **Release**: Properly versioned, documented, communicated

## Example Usage

\`\`\`
Feature: Content Approval Workflow

Step 1:
@senior-business-analyst create user stories for a content approval workflow with request, review, approve/reject capabilities

Step 2:
@principal-solution-architect design the high-level architecture for the approval workflow integrating with Strapi's content-manager

Step 3:
@lead-software-architect create detailed technical design and break down into development tasks

Step 4:
@senior-software-engineer implement the approval service following TDD with comprehensive tests

Step 5:
@senior-qa-engineer create integration tests and E2E tests for the approval workflow

Step 6:
@senior-technical-writer create API documentation and user guide for the approval feature

Step 7:
@senior-devops-engineer add approval plugin to CI/CD pipeline and deploy to staging

Step 8:
@senior-release-manager prepare release notes for v1.5.0 including the approval feature
\`\`\`
```

### Pattern 2: Bug Fix Instruction

```markdown
---
description: Systematic approach to fixing bugs with root cause analysis and prevention
applyTo: '**'
---

# Bug Fix Workflow

## Context
Use when fixing bugs in existing Strapi code.

## Agents Required
1. @principal-solution-architect - Root cause analysis
2. @senior-software-engineer - Fix implementation
3. @senior-qa-engineer - Verification and regression testing

## Workflow Steps

### Step 1: Analyze (Solution Architect)
\`\`\`
@principal-solution-architect analyze bug #123 - pagination fails with >10000 records, identify root cause and architectural issues
\`\`\`

**Output**: Root cause analysis, architectural recommendations

### Step 2: Fix (Software Engineer)
\`\`\`
@senior-software-engineer implement fix for pagination bug with TDD approach, ensuring tests cover edge cases
\`\`\`

**Output**: Bug fix code, unit tests, integration tests

### Step 3: Verify (QA Engineer)
\`\`\`
@senior-qa-engineer verify the pagination fix and run regression tests to ensure no new issues introduced
\`\`\`

**Output**: Test results, regression test report

## Quality Standards
- Root cause identified and documented
- Fix includes tests preventing regression
- No new bugs introduced
- Performance impact assessed

## Example
\`\`\`
Bug: Memory leak in file upload
1. @principal-solution-architect → Identified improper stream handling
2. @senior-software-engineer → Implemented proper cleanup with tests
3. @senior-qa-engineer → Verified fix and ran memory profiling
\`\`\`
```

### Pattern 3: Performance Optimization Instruction

```markdown
---
description: Systematic performance optimization with measurement and validation
applyTo: '**'
---

# Performance Optimization Workflow

## Context
Use when optimizing performance of Strapi applications.

## Agents Required
1. @principal-solution-architect - Bottleneck identification
2. @lead-software-architect - Optimization design
3. @senior-software-engineer - Implementation
4. @senior-qa-engineer - Performance testing
5. @senior-devops-engineer - Production monitoring

## Workflow Steps

### Step 1: Identify Bottlenecks
\`\`\`
@principal-solution-architect analyze the content-manager performance and identify bottlenecks using profiling data
\`\`\`

### Step 2: Design Optimization
\`\`\`
@lead-software-architect design optimization strategy including caching, query optimization, and indexing
\`\`\`

### Step 3: Implement
\`\`\`
@senior-software-engineer implement the caching layer and database query optimizations
\`\`\`

### Step 4: Performance Test
\`\`\`
@senior-qa-engineer create K6 load tests and measure performance improvements
\`\`\`

### Step 5: Monitor
\`\`\`
@senior-devops-engineer setup Prometheus metrics and Grafana dashboards for monitoring
\`\`\`

## Quality Standards
- Baseline metrics collected
- Measurable improvement (>20%)
- No functionality regression
- Production monitoring in place

## Example
\`\`\`
Target: Improve API response time
Baseline: p95 = 800ms
Goal: p95 < 400ms
Result: p95 = 350ms (56% improvement)
\`\`\`
```

### Pattern 4: Architecture Documentation Instruction

```markdown
---
description: Extract and document architecture from existing codebase
applyTo: 'packages/**'
---

# Architecture Documentation Workflow

## Context
Use when documenting existing architecture or creating architecture documentation for new systems.

## Agents Required
1. @principal-solution-architect - Architecture extraction
2. @lead-software-architect - Technical details
3. @senior-technical-writer - Documentation creation

## Workflow Steps

### Step 1: Extract Architecture
\`\`\`
@principal-solution-architect extract the architecture from packages/core/admin and create C4 context and container diagrams
\`\`\`

### Step 2: Document Patterns
\`\`\`
@lead-software-architect document the design patterns, algorithms, and technical decisions in the admin package
\`\`\`

### Step 3: Create Documentation
\`\`\`
@senior-technical-writer create comprehensive architecture documentation with diagrams and explanations
\`\`\`

## Quality Standards
- All major components documented
- Design patterns identified
- C4 diagrams created (Context, Container, Component)
- Technical decisions explained
- Dependencies mapped

## Example
\`\`\`
Package: @strapi/plugin-content-manager
Output: 
- C4 Context Diagram
- C4 Container Diagram
- Design Patterns: MVC, Observer, Factory
- Component Documentation
- API Documentation
\`\`\`
```

### Pattern 5: Security Audit Instruction

```markdown
---
description: Comprehensive security audit and remediation workflow
applyTo: '**'
---

# Security Audit Workflow

## Context
Use when performing security audits or addressing security vulnerabilities.

## Agents Required
1. @principal-solution-architect - Security architecture review
2. @senior-software-engineer - Vulnerability remediation
3. @senior-qa-engineer - Security testing
4. @senior-devops-engineer - Infrastructure security

## Workflow Steps

### Step 1: Security Analysis
\`\`\`
@principal-solution-architect perform security architecture review focusing on authentication, authorization, and data protection
\`\`\`

### Step 2: Fix Vulnerabilities
\`\`\`
@senior-software-engineer fix identified security issues following OWASP guidelines
\`\`\`

### Step 3: Security Testing
\`\`\`
@senior-qa-engineer run OWASP ZAP scan, Snyk scan, and penetration testing
\`\`\`

### Step 4: Infrastructure Security
\`\`\`
@senior-devops-engineer review infrastructure security: secrets management, network policies, container security
\`\`\`

## Quality Standards
- OWASP Top 10 addressed
- Snyk scan: Zero high/critical vulnerabilities
- Secrets not in code
- Security headers configured
- Authentication/authorization tested

## Example
\`\`\`
Vulnerability: CVE-2024-XXXXX in authentication
1. Architecture review → Identified weak JWT validation
2. Fix → Implemented proper JWT verification
3. Security test → Verified fix, no new vulnerabilities
4. Infrastructure → Updated secrets management
\`\`\`
```

## Creating Custom Instructions

### Template Structure

1. **YAML Frontmatter**:
   - `description`: What the instruction does
   - `applyTo`: File pattern for when it applies

2. **Context Section**:
   - When to use this instruction
   - Prerequisites
   - Scope

3. **Agents Section**:
   - List required agents
   - Agent responsibilities

4. **Workflow Section**:
   - Step-by-step process
   - Expected outputs
   - Handoff points

5. **Quality Standards**:
   - Gates between steps
   - Acceptance criteria
   - Metrics

6. **Examples**:
   - Real-world usage
   - Expected inputs/outputs

### Best Practices

1. **Be Specific**: Clear, unambiguous instructions
2. **Define Outputs**: What each step produces
3. **Set Standards**: Quality gates between steps
4. **Provide Examples**: Real scenarios
5. **Keep Updated**: Maintain with Strapi versions

### File Naming

- Use descriptive names: `feature-development.instructions.md`
- Group by category: `testing-*.instructions.md`
- Version if needed: `deployment-v5.instructions.md`

## Usage in GitHub Copilot

Reference instructions in your requests:

```
Following the feature-development workflow, help me build a content scheduling feature
```

Or mention specific agents:

```
@senior-business-analyst @principal-solution-architect design a notification system for Strapi
```

---

**Last Updated**: December 11, 2025  
**Template Version**: 1.0.0
