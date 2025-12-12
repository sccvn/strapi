# GitHub Copilot Prompts - Quick Reference Guide

## Overview

This directory contains ready-to-use prompts for GitHub Copilot agents. Copy and customize these prompts for your Strapi development tasks.

## 📁 Files in This Directory

- **multi-agent-guide.prompt.md**: Comprehensive prompt library for all agents and workflows
- **lead-software-architect-extraction.prompt.md**: Architecture extraction prompts with PlantUML diagrams
- **INSTRUCTION.md**: This file - quick reference guide

## 🎯 Quick Reference by Task

### Requirements & Planning

```bash
# User Stories
@senior-business-analyst create user stories for [FEATURE] with acceptance criteria

# Content Type Modeling
@senior-business-analyst design Strapi content types for [FEATURE] with relationships

# Requirements Analysis
@senior-business-analyst analyze requirements for [FEATURE] including constraints and priorities
```

### Architecture & Design

```bash
# Extract Architecture
@principal-solution-architect extract architecture from [PACKAGE] with C4 diagrams

# Design New System
@principal-solution-architect design architecture for [FEATURE] integrating with Strapi

# Detailed Design
@lead-software-architect create detailed design and task breakdown for [FEATURE]

# Database Schema
@lead-software-architect design database schema for [FEATURE] with migrations

# Architecture Extraction (see lead-software-architect-extraction.prompt.md)
@lead-software-architect extract C4 Component diagram for [PACKAGE]
@lead-software-architect extract ERD for [DOMAIN]
@lead-software-architect extract sequence diagram for [WORKFLOW]
@lead-software-architect extract architecture patterns from [PACKAGE]
@lead-software-architect extract design patterns from [MODULE]
@lead-software-architect extract algorithms from [COMPONENT]
```

### Implementation

```bash
# TDD Implementation
@senior-software-engineer implement [COMPONENT] following TDD with 90% coverage

# Service Creation
@senior-software-engineer create Strapi service for [FUNCTIONALITY] with tests

# Controller Creation
@senior-software-engineer create controller for [ENDPOINTS] with validation

# Bug Fix
@senior-software-engineer fix bug #[ID] with root cause analysis and tests
```

### Testing

```bash
# Unit Tests
@senior-qa-engineer create unit tests for [COMPONENT] with 90% coverage

# Integration Tests
@senior-qa-engineer create integration tests for [API ENDPOINTS]

# E2E Tests
@senior-qa-engineer create E2E tests for [WORKFLOW] using Playwright

# Performance Tests
@senior-qa-engineer create K6 load tests for [ENDPOINT] targeting [METRICS]

# Security Tests
@senior-qa-engineer run security audit with Snyk and OWASP ZAP
```

### DevOps & Deployment

```bash
# CI/CD Pipeline
@senior-devops-engineer create GitHub Actions pipeline for [PROJECT]

# Docker Setup
@senior-devops-engineer create production Docker setup for Strapi

# Kubernetes Deployment
@senior-devops-engineer create K8s manifests for Strapi application

# Monitoring
@senior-devops-engineer setup Prometheus and Grafana monitoring for [APP]
```

### Documentation

```bash
# API Documentation
@senior-technical-writer create OpenAPI documentation for [ENDPOINTS]

# User Guide
@senior-technical-writer create user guide for [FEATURE] with screenshots

# Architecture Docs
@senior-technical-writer document architecture for [SYSTEM] with diagrams

# Migration Guide
@senior-technical-writer create migration guide from [OLD] to [NEW]
```

### Release Management

```bash
# Changelog
@senior-release-manager generate changelog for version [VERSION]

# Release Notes
@senior-release-manager create release notes for [VERSION] with features and fixes

# Versioning
@senior-release-manager determine version number for changes: [CHANGES]

# Release Planning
@senior-release-manager create release plan for [VERSION] with timeline
```

## 🔄 Multi-Agent Workflows

### Complete Feature

```bash
@senior-business-analyst analyze requirements for [FEATURE]
↓
@principal-solution-architect design architecture for [FEATURE]
↓
@lead-software-architect create detailed design and tasks for [FEATURE]
↓
@senior-software-engineer implement [FEATURE] with TDD
↓
@senior-qa-engineer test [FEATURE] comprehensively
↓
@senior-technical-writer document [FEATURE]
↓
@senior-devops-engineer deploy [FEATURE]
↓
@senior-release-manager release [VERSION] with [FEATURE]
```

### Quick Bug Fix

```bash
@principal-solution-architect analyze bug #[ID]
↓
@senior-software-engineer fix bug #[ID] with tests
↓
@senior-qa-engineer verify fix for bug #[ID]
↓
@senior-release-manager create hotfix release
```

### Performance Optimization

```bash
@principal-solution-architect identify bottlenecks in [COMPONENT]
↓
@lead-software-architect design optimization for [COMPONENT]
↓
@senior-software-engineer implement optimizations
↓
@senior-qa-engineer benchmark performance improvements
↓
@senior-devops-engineer monitor production performance
```

## 💡 Prompt Templates

### Template: New Feature

```
Following feature-development workflow, create [FEATURE_NAME] for Strapi.

Requirements:
- [Requirement 1]
- [Requirement 2]
- [Requirement 3]

Constraints:
- [Constraint 1]
- [Constraint 2]

Acceptance Criteria:
- [Criteria 1]
- [Criteria 2]

Please coordinate all agents from requirements to release.
```

### Template: Bug Fix

```
Fix bug #[BUG_ID]: [BUG_DESCRIPTION]

Current behavior:
- [What's happening]

Expected behavior:
- [What should happen]

Steps to reproduce:
1. [Step 1]
2. [Step 2]
3. [Step 3]

Please analyze root cause, implement fix with tests, and verify.
```

### Template: Performance Issue

```
Optimize performance of [COMPONENT/ENDPOINT].

Current metrics:
- [Metric 1]: [Current value]
- [Metric 2]: [Current value]

Target metrics:
- [Metric 1]: [Target value]
- [Metric 2]: [Target value]

Please analyze, design optimization, implement, and benchmark.
```

### Template: Architecture Documentation

```
Document the architecture of [PACKAGE/MODULE].

Focus areas:
- [Area 1]
- [Area 2]
- [Area 3]

Please extract architecture, document patterns, and create diagrams.
```

## 📝 Prompt Best Practices

### ✅ Good Prompts

**Specific and Detailed**:
```
@senior-software-engineer implement content approval service with:
- Request approval method
- Approve/reject methods
- Email notifications
- Audit trail
Following TDD with 90% coverage
```

**With Context**:
```
@senior-qa-engineer create E2E tests for approval workflow.

User roles: Editor (requests), Reviewer (approves)
Workflow: Create content → Request approval → Review → Approve/Reject
Use Playwright with existing test infrastructure
```

**Clear Deliverables**:
```
@senior-technical-writer create API documentation including:
1. OpenAPI 3.0 specification
2. Request/response examples for each endpoint
3. Authentication guide
4. Error code reference
5. Postman collection
```

### ❌ Poor Prompts

**Too Vague**:
```
@senior-software-engineer create a service
```

**No Context**:
```
@senior-qa-engineer test the API
```

**Unclear Expectations**:
```
@senior-technical-writer write docs
```

## 🎨 Customizing Prompts

### Add Project Context

```
@senior-business-analyst analyze requirements for [FEATURE] in our [PROJECT_TYPE] project.

Project context:
- Strapi version: [VERSION]
- Database: [DATABASE]
- Deployment: [PLATFORM]
- Team size: [SIZE]
- Timeline: [TIMELINE]
```

### Specify Quality Standards

```
@senior-software-engineer implement [COMPONENT] with:

Quality requirements:
- SOLID principles
- 95% test coverage
- ESLint: 0 errors
- TypeScript strict mode
- SonarQube grade A
- Snyk: 0 high/critical vulnerabilities
```

### Reference Existing Code

```
@senior-software-engineer implement [NEW_FEATURE] following the pattern used in [EXISTING_FILE].

Existing pattern:
- [Pattern description or file path]

Apply the same pattern to:
- [New component]
```

## 📊 Common Scenarios

### Scenario 1: New Plugin

```bash
# Requirements
@senior-business-analyst analyze requirements for [PLUGIN_NAME] plugin

# Architecture
@principal-solution-architect design plugin architecture integrating with Strapi

# Implementation
@senior-software-engineer create Strapi plugin [PLUGIN_NAME] with admin UI

# Testing
@senior-qa-engineer test [PLUGIN_NAME] plugin comprehensively

# Documentation
@senior-technical-writer create plugin documentation and usage guide

# Publish
@senior-release-manager prepare plugin release for npm
```

### Scenario 2: API Endpoint

```bash
# Design
@lead-software-architect design REST API for [RESOURCE]

# Implement
@senior-software-engineer create controller and routes for [RESOURCE] API

# Test
@senior-qa-engineer create integration tests for [RESOURCE] endpoints

# Document
@senior-technical-writer create OpenAPI spec for [RESOURCE] API
```

### Scenario 3: Database Migration

```bash
# Design
@lead-software-architect design database schema changes for [FEATURE]

# Implement
@senior-software-engineer create database migration for [CHANGES]

# Test
@senior-qa-engineer test migration up and down with data integrity checks

# Document
@senior-technical-writer create migration guide with backup instructions
```

### Scenario 4: Security Patch

```bash
# Analyze
@principal-solution-architect analyze security vulnerability CVE-[ID]

# Fix
@senior-software-engineer patch vulnerability CVE-[ID] following OWASP guidelines

# Test
@senior-qa-engineer run security tests and verify patch

# Deploy
@senior-devops-engineer deploy security patch with zero downtime

# Communicate
@senior-release-manager create security advisory for CVE-[ID]
```

## 🚀 Advanced Techniques

### Chaining with Context

```bash
# Step 1: Get architecture
ARCH=$(copilot "@principal-solution-architect extract architecture from content-manager")

# Step 2: Use architecture in design
copilot "@lead-software-architect design optimization for content-manager based on: $ARCH"

# Step 3: Implement based on design
copilot "@senior-software-engineer implement optimization following the design"
```

### Conditional Workflows

```bash
# Analyze first
@principal-solution-architect is this a database or code performance issue in [COMPONENT]?

# Then branch based on answer:
# If database:
@lead-software-architect design query optimization strategy

# If code:
@lead-software-architect design algorithm optimization strategy
```

### Iterative Refinement

```bash
# Version 1
@senior-software-engineer implement basic [FEATURE]

# Review and iterate
@lead-software-architect review implementation and suggest improvements

# Version 2
@senior-software-engineer refactor [FEATURE] with improvements

# Verify
@senior-qa-engineer verify improvements meet requirements
```

## 📚 Resources

### Documentation
- **Agent Guide**: `../agents/INSTRUCTION.md`
- **Instruction Guide**: `../instructions/INSTRUCTION.md`
- **Workflow Templates**: `../instructions/*.instructions.md`

### Strapi Resources
- **Strapi Docs**: https://docs.strapi.io
- **Strapi GitHub**: https://github.com/strapi/strapi
- **Plugin Docs**: https://docs.strapi.io/dev-docs/plugins-development

### Testing Resources
- **Jest**: https://jestjs.io
- **Playwright**: https://playwright.dev
- **K6**: https://k6.io

### DevOps Resources
- **Docker**: https://docs.docker.com
- **Kubernetes**: https://kubernetes.io/docs
- **GitHub Actions**: https://docs.github.com/actions

## 🆘 Troubleshooting

### Agent Not Responding

**Issue**: Agent doesn't understand request  
**Solution**: Add more context, be more specific, reference examples

**Issue**: Agent gives generic response  
**Solution**: Specify Strapi version, reference existing code, set clear deliverables

### Quality Issues

**Issue**: Code doesn't meet standards  
**Solution**: Explicitly state quality requirements in prompt

**Issue**: Tests insufficient  
**Solution**: Request specific coverage percentage and test types

### Workflow Problems

**Issue**: Agents conflicting  
**Solution**: Define clear handoff points, use sequential execution

**Issue**: Missing dependencies  
**Solution**: Check task order, ensure prerequisites completed

## 💬 Getting Help

1. **Check Documentation**: Review agent and instruction docs
2. **Try Examples**: Use prompt examples from this guide
3. **Iterate**: Refine prompts based on responses
4. **Ask Specific Questions**: Include context and expected output
5. **Reference Work**: Link to previous agent outputs

## 📞 Support

For issues or questions:
- Review this guide and linked documentation
- Check the multi-agent guide for detailed examples
- Consult Strapi documentation for technical questions
- Open an issue in the repository for bugs

---

**Last Updated**: December 11, 2025  
**Prompt Guide Version**: 1.0.0  
**Compatible Agents**: v1.0.0
