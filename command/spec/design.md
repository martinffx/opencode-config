---
description: Generate technical design from requirements following project standards
agent: architect
subtask: true
---

# Design Feature: $ARGUMENTS

Create technical design based on requirements in spec.md.

## Step 1: Read Requirements

@clerk read spec.md Requirements section.

File: `docs/spec/$ARGUMENTS/spec.md`

Extract:
- User story context
- Acceptance criteria
- Business rules
- Scope boundaries

## Step 2: Load Project Standards

@clerk retrieve architectural patterns from project standards docs.

Read standards:
- `docs/standards/architecture.md` → layered patterns, API guidelines, testing strategy
- `docs/standards/coding.md` → TDD workflow, error handling, conventions

Extract context on:
- Layer structure (Router → Service → Repository → Entity)
- API conventions (REST/GraphQL)
- Database patterns (ORM, query builders)
- Error handling patterns
- Testing strategies (Stub→Test→Fix pattern)
- Layer boundary testing approach

## Step 3: Generate Technical Design

@architect create technical design based on requirements.

### Determine Architecture Pattern

Based on requirements, select pattern:

- **Standard CRUD API**: Router → Service → Repository → Database
- **External API**: Router → Service → Client → External API
- **Event-Driven Consumer**: Consumer → Service → Repository → Database
- **Event-Driven Producer**: Router → Service → Producer → Event Broker
- **Hybrid**: Combination of above

**Context:** Design only what's needed. Don't force all layers.

### Design Components

**Domain Model (Entities)**
- Entity class definitions
- Methods: fromRequest, toRecord, toResponse, validate
- Business rule enforcement
- Examples based on acceptance criteria

**Services (Business Logic)**
- Service operations needed
- Orchestration logic
- Transaction requirements
- Business rule validation

**Data Persistence (if needed)**
- Database schema
- Indexes
- Relationships
- Migration considerations

**API Endpoints (if external exposure needed)**
- REST endpoints
- Request/response contracts
- Error responses
- Authentication/authorization

**Events (if async communication needed)**
- Published events
- Subscribed events
- Event schemas

### Component Data Flow

```
Request → Router → Service → Repository → Database
            ↓         ↓           ↓
     Entity.fromRequest()    Entity.toRecord()
```

Component responsibilities:
- **Router**: HTTP handling, creates Entity from request
- **Service**: Orchestration, business logic
- **Repository**: Data access, Entity.toRecord() for database format
- **Entity**: Business rules, validation, transformations

## Step 4: Update Specification Document

@clerk add Technical Design section to spec.md.

Read current spec.md (Requirements section only).

Add Technical Design section:

```markdown
## Technical Design

### Architecture Pattern
[Selected: Standard CRUD API / Event-Driven / Hybrid]

### Domain Model

[Entity 1]
\`\`\`typescript
class Entity1 {
  static fromRequest(data): Entity1
  static fromRecord(record): Entity1
  toResponse(): ResponseType
  validate(): ValidationResult
}
[code snippet example]
\`\`\`

[Entity 2]
[Same structure]

### Services

[Service 1]: Business operations and orchestration

[Service 2]: Business operations and orchestration

### Data Persistence

- Database: [type]
- Schema: [tables, columns, relationships]
- Indexes: [performance optimization]

### API Endpoints

(if external exposure needed)

- POST /api/resource
  - Request: [contract]
  - Response: [contract]
  - Errors: [validation, business rules]

[Additional endpoints...]

### Events

(if event-driven)

- Published: [EventTypes]
- Subscribed: [EventTypes]
```

Write updated spec.md back to file.

## ✅ Technical Design Complete

Updated: `docs/spec/$ARGUMENTS/spec.md`

Added:
- Architecture pattern selection
- Domain model with entities
- Services (business operations)
- Data persistence (if needed)
- API endpoints (if needed)
- Events (if applicable)

**Next step:** Create implementation plan
\`\`\`
/spec/plan $ARGUMENTS
\`\`\`
