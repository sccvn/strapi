# Lead Software Architect - Architecture Extraction Summary

## 🎯 Overview

Custom prompts have been added to enable the **Lead Software Architect** agent to extract comprehensive low-level architecture documentation from the Strapi codebase.

## 📦 What Was Added

### New Prompt File

**File**: [.github/prompts/lead-software-architect-extraction.prompt.md](.github/prompts/lead-software-architect-extraction.prompt.md)

**Size**: ~5,000 lines of comprehensive prompts and examples

**Coverage**: 10 specialized extraction prompts

---

## 📊 10 Extraction Prompts

### 1. C4 Component Diagram Extraction
- **Purpose**: Extract component-level architecture
- **Output**: PlantUML C4 Component diagrams
- **Shows**: Components, dependencies, interactions, responsibilities
- **Example**: Content Manager plugin components

### 2. C4 Container Diagram Extraction
- **Purpose**: Extract container-level architecture
- **Output**: PlantUML C4 Container diagrams
- **Shows**: Deployable units, technologies, protocols, external systems
- **Example**: Full Strapi application containers

### 3. Sequence Diagram Extraction
- **Purpose**: Extract workflow interactions
- **Output**: PlantUML sequence diagrams
- **Shows**: Actors, flow, timing, error paths
- **Example**: Content creation workflow from HTTP request to database

### 4. ERD (Entity Relationship Diagram) Extraction
- **Purpose**: Extract database schema
- **Output**: PlantUML ERD diagrams
- **Shows**: Entities, attributes, relationships, constraints
- **Example**: Content Manager data model

### 5. Class Diagram Extraction
- **Purpose**: Extract object-oriented design
- **Output**: PlantUML class diagrams
- **Shows**: Classes, properties, methods, relationships, patterns
- **Example**: Plugin system class hierarchy

### 6. Communication Diagram Extraction
- **Purpose**: Extract object collaboration
- **Output**: PlantUML communication diagrams
- **Shows**: Objects, messages, data flow, collaboration
- **Example**: Request processing flow

### 7. State Machine Diagram Extraction
- **Purpose**: Extract entity lifecycles
- **Output**: PlantUML state diagrams
- **Shows**: States, transitions, guards, actions
- **Example**: Document lifecycle states

### 8. Architecture Patterns Extraction
- **Purpose**: Extract high-level patterns
- **Output**: Markdown documentation with diagrams
- **Patterns**: Layered, Plugin, Event-Driven, Repository, etc.
- **Details**: Location, purpose, structure, code, benefits, trade-offs

### 9. Design Patterns Extraction
- **Purpose**: Extract GoF design patterns
- **Output**: Markdown documentation with UML
- **Patterns**: Factory, Builder, Decorator, Observer, etc.
- **Details**: Category, intent, participants, code, diagrams, consequences

### 10. Algorithm Extraction
- **Purpose**: Extract core algorithms
- **Output**: Markdown with pseudocode and implementation
- **Details**: Purpose, complexity, pseudocode, code, optimizations, edge cases
- **Examples**: Deep population, permission checking, query building

---

## 🚀 How to Use

### Basic Usage

```bash
@lead-software-architect extract [DIAGRAM_TYPE] for [COMPONENT_NAME]
```

### Examples

#### Extract C4 Component Diagram
```bash
@lead-software-architect analyze packages/core/content-manager and extract the C4 Component diagram showing all major components, their dependencies, interactions, and responsibilities in PlantUML format
```

#### Extract Sequence Diagram
```bash
@lead-software-architect extract the sequence diagram for content creation workflow showing all actors, the complete flow from HTTP request to database persistence, timing, and error handling in PlantUML format
```

#### Extract ERD
```bash
@lead-software-architect extract the ERD diagram for the content-manager plugin showing all entities, attributes, relationships, and constraints in PlantUML format
```

#### Extract Architecture Patterns
```bash
@lead-software-architect analyze packages/core/strapi and extract all architecture patterns including their implementation location, purpose, structure, code examples, benefits, and trade-offs
```

#### Extract Design Patterns
```bash
@lead-software-architect analyze the Strapi plugin system and extract all design patterns with their category, intent, participants, code examples, UML diagrams, and consequences
```

#### Extract Algorithms
```bash
@lead-software-architect analyze the Strapi Document Service and extract all core algorithms with their purpose, complexity, pseudocode, implementation, optimizations, and edge case handling
```

---

## 📋 Complete Architecture Extraction Workflow

Use these prompts sequentially for comprehensive documentation:

```bash
# Step 1: High-Level Architecture
@lead-software-architect extract C4 Container diagram for full Strapi application

# Step 2: Component-Level Architecture
@lead-software-architect extract C4 Component diagram for content-manager package

# Step 3: Key Workflows
@lead-software-architect extract sequence diagram for content creation workflow
@lead-software-architect extract sequence diagram for user authentication workflow

# Step 4: Data Model
@lead-software-architect extract ERD for core content types
@lead-software-architect extract ERD for users-permissions plugin

# Step 5: Object Design
@lead-software-architect extract class diagram for plugin system
@lead-software-architect extract class diagram for document service

# Step 6: State Management
@lead-software-architect extract state machine diagram for document lifecycle
@lead-software-architect extract state machine diagram for user session

# Step 7: Patterns & Algorithms
@lead-software-architect extract architecture patterns from packages/core/strapi
@lead-software-architect extract design patterns from plugin system
@lead-software-architect extract algorithms from document service
@lead-software-architect extract algorithms from permission engine
```

---

## 📖 Documentation Structure

### Each Prompt Includes

1. **Prompt Template**: Copy-paste ready format
2. **Example Usage**: Real-world usage example
3. **Expected Output**: Sample output structure
4. **PlantUML Code**: Diagram syntax examples

### Output Quality Standards

All extracted documentation includes:
- **Complete PlantUML code**: Ready to render
- **Code examples**: Real code from Strapi codebase
- **Detailed annotations**: Notes explaining key aspects
- **Complexity analysis**: Big-O notation where applicable
- **Best practices**: How patterns/algorithms are applied

---

## 🎨 PlantUML Diagram Types Supported

| Diagram Type | Purpose | Complexity |
|--------------|---------|------------|
| C4 Component | Component architecture | Medium |
| C4 Container | System architecture | Low |
| Sequence | Workflow interactions | High |
| ERD | Database schema | Medium |
| Class | OOP design | High |
| Communication | Object collaboration | Medium |
| State Machine | Entity lifecycle | Medium |

---

## 💡 Best Practices

### 1. Start High-Level, Drill Down
```
Container → Component → Class → Sequence
```

### 2. Be Specific
```
❌ @lead-software-architect extract architecture
✅ @lead-software-architect extract C4 Component diagram for content-manager
```

### 3. Request Details
```
@lead-software-architect extract sequence diagram for content creation WITH error handling flows and timing annotations
```

### 4. Iterate
```
# First pass - overview
@lead-software-architect extract class diagram for plugin system

# Second pass - details
@lead-software-architect extract class diagram for ContentManagerPlugin with all methods and properties
```

### 5. Combine Multiple Diagrams
```
@lead-software-architect extract for content approval workflow:
1. Sequence diagram for approval flow
2. State machine for approval states
3. ERD for approval tables
4. Class diagram for approval service
```

---

## 📊 Pattern Categories Covered

### Architecture Patterns
- Layered Architecture
- Plugin Architecture
- Event-Driven Architecture
- Repository Pattern
- Service Layer Pattern
- Dependency Injection
- CQRS

### Design Patterns (GoF)
**Creational**:
- Factory Method
- Abstract Factory
- Builder
- Singleton
- Prototype

**Structural**:
- Adapter
- Decorator
- Facade
- Proxy
- Composite
- Bridge

**Behavioral**:
- Strategy
- Observer
- Command
- Template Method
- Chain of Responsibility
- Iterator
- State

### Algorithms
- Deep Population (relation loading)
- Permission Checking (RBAC)
- Query Building
- Content Validation
- Document Transformation
- Search & Filtering
- Sorting & Pagination

---

## 🔗 Integration with Existing System

### Updated Files

1. **[.github/prompts/INSTRUCTION.md](.github/prompts/INSTRUCTION.md)**
   - Added reference to extraction prompts
   - Included quick examples in Architecture & Design section

2. **[.github/prompts/multi-agent-guide.prompt.md](.github/prompts/multi-agent-guide.prompt.md)**
   - Added "Related Prompt Libraries" section
   - Linked to specialized extraction prompts

3. **[.github/prompts/lead-software-architect-extraction.prompt.md](.github/prompts/lead-software-architect-extraction.prompt.md)** *(NEW)*
   - Complete extraction prompt library
   - 10 specialized prompts with examples

---

## 📈 Expected Outcomes

### For Onboarding
- New developers understand architecture in days vs weeks
- Visual diagrams clarify complex relationships
- Patterns documented for consistency

### For Development
- Reference architecture for new features
- Design patterns catalog for reuse
- Algorithm analysis for optimization

### For Documentation
- Auto-generate architecture docs
- Keep documentation in sync with code
- Visual aids for technical writing

---

## 🎓 Learning Path

### Beginner
1. Start with C4 Container diagram (system overview)
2. Extract ERD for data understanding
3. Review architecture patterns

### Intermediate
1. Extract C4 Component diagrams per package
2. Study sequence diagrams for workflows
3. Analyze design patterns

### Advanced
1. Extract class diagrams for OOP design
2. Deep dive into algorithms
3. Optimize based on complexity analysis

---

## 🆘 Troubleshooting

### Issue: Output Too Generic
**Solution**: Be more specific in your prompt
```
❌ Extract architecture
✅ Extract C4 Component diagram for packages/core/content-manager showing service layer, controller layer, and their interactions
```

### Issue: Missing Code Examples
**Solution**: Explicitly request them
```
@lead-software-architect extract Factory pattern FROM plugin-loader.ts WITH real code examples
```

### Issue: Diagrams Not Rendering
**Solution**: Copy PlantUML code to [PlantUML Online Editor](http://www.plantuml.com/plantuml/)

### Issue: Too Much Detail
**Solution**: Set scope boundaries
```
@lead-software-architect extract class diagram for PluginLoader class ONLY, exclude nested classes
```

---

## 🚀 Quick Start

### Extract Everything (15 minutes)
```bash
# 1. System overview
@lead-software-architect extract C4 Container diagram for Strapi

# 2. Core architecture
@lead-software-architect extract C4 Component diagram for packages/core/strapi

# 3. Key workflow
@lead-software-architect extract sequence diagram for content creation

# 4. Data model
@lead-software-architect extract ERD for content-manager

# 5. Patterns
@lead-software-architect extract architecture patterns from packages/core/strapi
```

### Deep Dive on Plugin System (30 minutes)
```bash
# Architecture
@lead-software-architect extract C4 Component diagram for plugin system

# Class design
@lead-software-architect extract class diagram for plugin system with all relationships

# Lifecycle
@lead-software-architect extract state machine for plugin lifecycle

# Patterns
@lead-software-architect extract all design patterns from plugin system

# Algorithms
@lead-software-architect extract plugin loading algorithm from PluginLoader
```

---

## 📚 References

### PlantUML Resources
- [PlantUML Official](https://plantuml.com/)
- [C4-PlantUML](https://github.com/plantuml-stdlib/C4-PlantUML)
- [PlantUML Online Editor](http://www.plantuml.com/plantuml/)

### Architecture Documentation
- [C4 Model](https://c4model.com/)
- [UML Diagrams](https://www.uml-diagrams.org/)
- [Design Patterns](https://refactoring.guru/design-patterns)

### Strapi Documentation
- [Strapi Architecture](https://docs.strapi.io/dev-docs/backend-customization)
- [Plugin Development](https://docs.strapi.io/dev-docs/plugins-development)
- [Document Service](https://docs.strapi.io/dev-docs/api/document-service)

---

## ✅ Summary

You now have comprehensive prompts to extract:
- ✅ 2 types of C4 diagrams (Component, Container)
- ✅ 5 types of UML diagrams (Sequence, ERD, Class, Communication, State)
- ✅ Architecture patterns documentation
- ✅ Design patterns documentation
- ✅ Algorithm documentation with complexity

All outputs are in **PlantUML format** for easy visualization and integration into documentation.

---

**Created**: December 12, 2025  
**Agent**: Lead Software Architect  
**Strapi Version**: v5.x  
**Format**: PlantUML, Markdown
