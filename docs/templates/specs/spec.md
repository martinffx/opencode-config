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

## Diagrams

<!-- Add sequence diagrams, entity relationship diagrams, or flow diagrams here if needed -->

## Alignment

This feature aligns with: {{aligns_with}}
