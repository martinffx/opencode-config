# {{project_name}}

## Product Definition

{{product_definition}}

## Target Users

{{target_users}}

## Core Features

{{#each core_features}}
{{increment @index}}. **{{this.name}}**: {{this.description}}
{{/each}}

## Technology Stack

**Frontend**: {{#each tech_stack.frontend}}{{this}}{{#unless @last}}, {{/unless}}{{/each}}

**Backend**: {{#each tech_stack.backend}}{{this}}{{#unless @last}}, {{/unless}}{{/each}}

**Database**: {{#each tech_stack.database}}{{this}}{{#unless @last}}, {{/unless}}{{/each}}

**Infrastructure**: {{#each tech_stack.infrastructure}}{{this}}{{#unless @last}}, {{/unless}}{{/each}}

## Success Metrics

{{#each success_metrics}}
- **{{@key}}**: {{this}}
{{/each}}

---

**Created**: {{created_date}}
**Last Updated**: {{updated_date}}
