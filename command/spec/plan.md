---
description: Generate implementation tasks from technical design
agent: architect
model: opencode/glm-4.6
temperature: 0.2
---

# Task Breakdown: $ARGUMENTS

## Step 1: Generate Implementation Tasks

@architect breakdown the design into TDD implementation tasks.

Read:

- `docs/spec/$ARGUMENTS/spec.md`
- `docs/spec/$ARGUMENTS/design.md`

Analyze technical design components and generate tasks:

- Create tasks in TDD order (Entity → Repository → Service → Router)
- Define task dependencies and phases
- Estimate effort with AI assistance
- Organize for parallel development where possible

### Output: `docs/spec/$ARGUMENTS/plan.json`

```json
{
  "phases": {
    "phase1_entity": {
      "entity_stubs": {...},
      "entity_tests": {...},
      "entity_implementation": {...}
    },
    "phase2_repository": {
      "repository_stubs": {...},
      "repository_tests": {...},
      "repository_implementation": {...}
    },
    "phase3_service": {
      "service_stubs": {...},
      "service_tests": {...},
      "service_implementation": {...}
    },
    "phase4_router": {
      "router_stubs": {...},
      "router_tests": {...},
      "router_implementation": {...}
    }
  }
}
```

## Step 2: Prioritize Implementation Tasks

@product prioritize tasks based on dependencies and time to value.

Read:

- `docs/spec/$ARGUMENTS/plan.json`

Strategic analysis:

- Analyze dependencies between tasks
- Order tasks for most efficient development flow
- Organize tasks so next steps are always clear
- Ensure dependent and independent tasks are identified
- Optimize for multiple AI agents working independently

### Output: `docs/spec/$ARGUMENTS/plan.json`

Update task order with strategic prioritization:

```json
{
  "execution_order": {
    "parallel_ready": ["entity_stubs", "database_setup"],
    "phase1": ["entity_tests", "entity_implementation"],
    "phase2": ["repository_stubs", "repository_tests"],
    "phase3": ["service_stubs", "service_tests"],
    "phase4": ["router_stubs", "router_tests"],
    "integration": ["integration_tests", "documentation"]
  },
  "dependencies": {...},
  "estimates": {...}
}
```

## Step 3: Create Task Documentation

@scaffold generate task documentation from implementation plan.

Read:

- `docs/spec/$ARGUMENTS/plan.json`

Apply templates:

- Task list template from standards
- Status tracking template from standards

Create file in `docs/spec/$ARGUMENTS/`:

- `tasks.md` - Complete implementation task list with embedded progress tracking

## ✅ Task Breakdown Complete

Generated comprehensive implementation plan:

- Tasks organized by TDD and layered architecture principles
- Progress tracking embedded in tasks.md
- Time estimates calculated with AI assistance multipliers
- Dependency analysis completed for parallel development
- Ready to begin stub-driven implementation

**Next step**: `/spec/implement $ARGUMENTS` to start TDD implementation
