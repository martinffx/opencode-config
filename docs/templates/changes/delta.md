# Delta: {{feature}}/{{change}}

## ADDED Requirements

{{#if added_requirements}}
{{#each added_requirements}}
### {{this.name}}

{{this.description}}

#### Scenarios
{{#each this.scenarios}}
- WHEN {{this.condition}} THEN {{this.outcome}}
{{/each}}

{{/each}}
{{else}}
<!-- No new requirements -->
{{/if}}

## ADDED Technical Design

{{#if added_design}}
{{#if added_design.entities}}
### Entities

{{#each added_design.entities}}
#### {{this.name}}

**Methods:**
{{#each this.methods}}
- `{{this}}`
{{/each}}

{{/each}}
{{/if}}

{{#if added_design.services}}
### Services

{{#each added_design.services}}
- **{{this.name}}**: {{this.description}}
{{/each}}
{{/if}}

{{#if added_design.endpoints}}
### API Endpoints

{{#each added_design.endpoints}}
- **{{this.method}} {{this.path}}**: {{this.description}}
{{/each}}
{{/if}}

{{#if added_design.events}}
### Events

{{#if added_design.events.publishes}}
**Publishes:**
{{#each added_design.events.publishes}}
- {{this}}
{{/each}}
{{/if}}

{{#if added_design.events.subscribes}}
**Subscribes:**
{{#each added_design.events.subscribes}}
- {{this}}
{{/each}}
{{/if}}
{{/if}}

{{else}}
<!-- No new design elements -->
{{/if}}

## MODIFIED Requirements

{{#if modified_requirements}}
{{#each modified_requirements}}
### {{this.name}}

<!-- Full replacement text -->
{{this.new_text}}

{{/each}}
{{else}}
<!-- No modified requirements -->
{{/if}}

## MODIFIED Technical Design

{{#if modified_design}}
{{#each modified_design}}
### {{this.section}}

<!-- Full replacement text -->
{{this.new_text}}

{{/each}}
{{else}}
<!-- No modified design elements -->
{{/if}}

## REMOVED

{{#if removed}}
{{#each removed}}
### {{this.type}}: {{this.name}}

<!-- {{this.reason}} -->

{{/each}}
{{else}}
<!-- Nothing removed -->
{{/if}}
