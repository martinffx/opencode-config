# {{feature}} Specification

## User Story

{{user_story}}

## Acceptance Criteria

{{#each acceptance_criteria}}
{{increment @index}}. {{this}}
{{/each}}

## Business Rules

{{#each business_rules}}
- {{this}}
{{/each}}

## Scope

### Included
{{#each scope.included}}
- {{this}}
{{/each}}

### Excluded
{{#each scope.excluded}}
- {{this}}
{{/each}}

{{#if dependencies}}
## Dependencies

{{#each dependencies}}
- {{this}}
{{/each}}
{{/if}}

{{#if dependencies}}
## Dependencies

{{#each dependencies}}
- {{this}}
{{/each}}
{{/if}}

## Technical Design

### Architecture Pattern

{{#if design.endpoints}}
{{#if design.events}}
#### Hybrid (API + Events)
```
Router → Service → Repository → Database
         ↓                ↓
      Entity         Events
```
{{else}}
#### Standard CRUD API
```
Router → Service → Repository → Database
         ↓
      Entity
```
{{/if}}
{{else if design.events}}
#### Event-Driven
```
Consumer → Service → Repository → Database
           ↓
        Entity
```
{{else}}
#### Service Integration
```
Router → Service → Client → External API
         ↓
      Entity
```
{{/if}}

### Domain Model

{{#if design.entities}}
{{#each design.entities}}
#### {{this.name}}

**Methods:**
{{#each this.methods}}
- `{{this}}`
{{/each}}

{{/each}}
{{/if}}

{{#if design.services}}
### Services

{{#each design.services}}
- **{{this.name}}**: {{this.description}}
{{/each}}
{{/if}}

{{#if design.database}}
### Data Persistence

**Table**: `{{design.database.table}}`

**Schema:**
```sql
{{design.database.schema}}
```

{{#if design.database.indexes}}
**Indexes:**
{{#each design.database.indexes}}
- {{this}}
{{/each}}
{{/if}}
{{/if}}

{{#if design.endpoints}}
### API Endpoints

{{#each design.endpoints}}
- **{{this.method}} {{this.path}}**: {{this.description}}
{{/each}}
{{/if}}

{{#if design.events}}
### Events

{{#if design.events.publishes}}
**Publishes:**
{{#each design.events.publishes}}
- {{this}}
{{/each}}
{{/if}}

{{#if design.events.subscribes}}
**Subscribes:**
{{#each design.events.subscribes}}
- {{this}}
{{/each}}
{{/if}}
{{/if}}

## Implementation Notes

<!-- Added during/after implementation with timestamps -->

## Alignment

This feature aligns with: {{aligns_with}}
