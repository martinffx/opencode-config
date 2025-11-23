# Product Roadmap: {{project_name}}

**Last Updated**: {{updated_date}}

## Current Status

- **Active Features**: {{active_feature_count}}
- **Completed Features**: {{completed_feature_count}}

## Next 3 Priorities

### 1. {{priorities.0.name}}

{{priorities.0.description}}

- **Status**: {{priorities.0.status}}
- **Why Now**: {{priorities.0.reasoning}}
- **Effort**: {{priorities.0.estimated_effort}}
{{#if priorities.0.dependencies}}
- **Dependencies**: {{#each priorities.0.dependencies}}{{this}}{{#unless @last}}, {{/unless}}{{/each}}
{{/if}}

### 2. {{priorities.1.name}}

{{priorities.1.description}}

- **Status**: {{priorities.1.status}}
- **Why Now**: {{priorities.1.reasoning}}
- **Effort**: {{priorities.1.estimated_effort}}
{{#if priorities.1.dependencies}}
- **Dependencies**: {{#each priorities.1.dependencies}}{{this}}{{#unless @last}}, {{/unless}}{{/each}}
{{/if}}

### 3. {{priorities.2.name}}

{{priorities.2.description}}

- **Status**: {{priorities.2.status}}
- **Why Now**: {{priorities.2.reasoning}}
- **Effort**: {{priorities.2.estimated_effort}}
{{#if priorities.2.dependencies}}
- **Dependencies**: {{#each priorities.2.dependencies}}{{this}}{{#unless @last}}, {{/unless}}{{/each}}
{{/if}}
