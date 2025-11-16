---
description: Generate technical design from specification
agent: architect
model: openrouter/moonshotai/kimi-k2-thinking
temperature: 0.2
---

# Technical Design: $ARGUMENTS

## Step 1: Load Requirements & Standards

@context read specification and standards files.

Read files:
- `docs/spec/$ARGUMENTS/*.md` → feature requirements
- `docs/spec/$ARGUMENTS/*.json` → feature requirements
- `docs/standards/tech.md` → architecture (event-driven microservices)
- `docs/standards/patterns/` → design patterns

Extract key decisions needed:
```yaml
requirements:
  entities: [identify from user stories]
  data_persistence: [what needs storing]
  api_needed: [external access required?]
  ui_components: [user-facing elements]
  business_rules: [validation/constraints]
```

### Output: `docs/spec/$ARGUMENTS/design.json`
```json
{
  "requirements": {
    "entities": {...},
    "data_persistence": {...},
    "api_needed": {...},
    "components": {...},
    "domains": {...},
    "business_rules": {...}
  }
}
```

## Step 2: Analyze Technical Needs

@architect analyze technical requirements from design.json.

From `docs/spec/$ARGUMENTS/design.json`, use sequential thinking to determine:

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

### Output: `docs/spec/$ARGUMENTS/design.json`
```json
{
  "technical_needs": {
    "domain_model": {
      "entities": {...},
      "services": {...}
    },
    "persistence": {...},
    "router": {...},
    "events": {...},
    "dependencies": {...}
  }
}
```

## Step 3: Generate Technical Design

@architect apply architectural standards to create comprehensive technical design.

Read `docs/spec/$ARGUMENTS/design.json` for existing context.
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

### Output: `docs/spec/$ARGUMENTS/design.json`
```json
{
  "design": {
    "domain_model": {
      "entities": {...},
      "services": {...}
    },
    "database": {...},
    "endpoints": {...},
    "events": {...},
    "dependencies": {...}
  }
}
```

## Step 4: Create Design Documentation

@scaffold generate design documentation from technical analysis.

Read: `docs/spec/$ARGUMENTS/design.json`

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

**Next step**: `/spec-plan $ARGUMENTS` to generate implementation tasks
