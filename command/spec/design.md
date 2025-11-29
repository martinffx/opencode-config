---
description: Generate technical design from specification
agent: architect
model: anthropic/claude-opus-4-5
temperature: 0.2
---

# Technical Design: $ARGUMENTS

## Step 1: Load Requirements & Standards

@context read specification and standards files.

Read files:

- `docs/spec/$ARGUMENTS/spec.md` → feature requirements
- `docs/standards/coding.md` → TDD implementation patterns and layered architecture
- `docs/standards/architecture.md` → Architecture patterns and design principles

@context7 enhance with latest framework documentation for detected stack.

Fetch current documentation for:
- Framework-specific architecture patterns
- Latest best practices for detected technology stack
- Current design patterns and conventions

Extract key decisions needed:

```yaml
requirements:
  entities: [identify from user stories]
  data_persistence: [what needs storing]
  api_needed: [external access required?]
  ui_components: [user-facing elements]
  business_rules: [validation/constraints]
```

## Step 2: Analyze Technical Needs

@architect analyze technical requirements from specification.

From `docs/spec/$ARGUMENTS/spec.md`, use sequential thinking to determine:

1. **Domain Entities**

   - Core entity: $ARGUMENTS
   - Related entities: [from spec]

2. **Data Requirements**

   - Persistence needed: [yes/no]
   - Data volume: [expected scale]
   - Access patterns: [how data is queried]

3. **API Requirements**

   - External exposure: [yes/no]
   - Operations: [CRUD/custom actions]

4. **Component Dependencies**
   - Depends on: [existing features]
   - Required by: [future features]

## Step 3: Generate Technical Design

@architect apply architectural standards to create comprehensive technical design.

Use sequential thinking to apply standards and create design:

### Component Architecture

```
Router → Service (Domain) → Repository → Database
                ↓
              Entity
```

```
Router → Service (Domain) → Client → External API
                ↓
              Entity
```

```
Consumer → Service (Domain) → Producer
                ↓
              Entity
```

```
Consumer → Service (Domain) → Repository → Database
                ↓
              Entity
```

```
Router → Service (Domain) → Producer
                ↓
              Entity
```

### Domain Model (if needed)

```yaml
entity: ${ARGUMENTS}Entity
methods:
  - fromRequest(dto): validate and transform from API
  - toRecord(): prepare for database
  - toResponse(): format for API response
  - validate(): business rules enforcement
```

### Data Persistence (if needed)

```yaml
database: [from standards]
schema:
  table: ${ARGUMENTS}s
  fields: [based on requirements]
  indexes: [for query patterns]
```

### API Specification (if needed)

```yaml
endpoints:
  - GET /api/${ARGUMENTS}s
  - GET /api/${ARGUMENTS}s/{id}
  - POST /api/${ARGUMENTS}s
  - PUT /api/${ARGUMENTS}s/{id}
  - DELETE /api/${ARGUMENTS}s/{id}
```

### Events (if applicable)

```yaml
publishes:
  - ${ARGUMENTS}Created
  - ${ARGUMENTS}Updated
subscribes:
  - [events from dependencies]
```

## Step 4: Create Design Documentation

@scaffold generate design documentation from technical analysis.

Apply template: design template from standards

Generate: `docs/spec/$ARGUMENTS/design.md` with complete technical specification

## ✅ Design Complete

Created: `docs/spec/$ARGUMENTS/design.md`

The design includes:

- Domain model with DDD patterns
- Data persistence strategy
- API specification (if needed)
- Component structure following standards
- Event definitions for async communication
- Single source of truth for technical design

**Next step**: `/spec/plan $ARGUMENTS` to generate implementation tasks
