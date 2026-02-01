---
description: Create implementation plan and Beads tasks with user review
agent: oracle
model: opencode/glm-4.7
temperature: 0.4
tools: Read, Glob, Grep, Write, Edit, Bash
---

# Plan Feature Implementation: $ARGUMENTS

Create implementation plan.json from requirements, design, standards, and context.

## Step 1: Read All Inputs

@clerk gather information from multiple sources.

**Read specification:**
```bash
cat docs/spec/$ARGUMENTS/spec.md
```
Extract: user story, acceptance criteria, business rules, technical design

**Read standards documents:**
```bash
cat docs/standards/architecture.md
cat docs/standards/coding.md
```
Extract: layer patterns, testing approach, TDD workflow

**Read product context:**
```bash
cat docs/product/product.md
```
Extract: product vision, constraints, tech stack (if applicable)

**Read template:**
```bash
cat ~/.config/opencode/assets/templates/plan.json
```

## Step 2: Analyze Technical Design

@oracle analyze design to determine implementation tasks.

From specification, identify:
- Entity names and validation requirements
- Service operations needed
- Repository CRUD patterns
- Router endpoints (if applicable)
- Any event producers/consumers

From standards/architecture.md:
- Understand layer responsibilities
- Dependency patterns (Router → Service → Repository → Entity)
- Testing boundaries

From standards/coding.md:
- Stub→Test→Fix pattern
- Layer boundary testing approach

## Step 3: Generate Task List

Based on design analysis, generate tasks array with dependencies.

For each layer present in design:

**Example task structure:**
```json
{
  "id": "task-1",
  "name": "Implement Entity: {{EntityName}}",
  "layer": "entity",
  "dependencies": [],
  "description": "Create entity with fromRequest, toRecord, toResponse, validate methods",
  "components": ["{{EntityName}}"]
}
```

**Tasks generation pattern:**
1. Entity tasks (always first, no dependencies)
2. Repository tasks (depends on entity)
3. Service tasks (depends on repository)
4. Router tasks (depends on service)
5. Other tasks as needed ( consumers, producers, etc)

**Only create tasks for layers actually present in design:**
- If no database → skip repository
- If no API endpoints → skip router
- If no events → skip producers/consumers

## Step 4: Fill Template with Extracted Information

@oracle create filled plan.json by substituting placeholders.

Using base template from `~/.config/opencode/assets/templates/plan.json`, replace:

- `{{FEATURE_NAME}}` → $ARGUMENTS
- `{{CHANGE_NAME}}` → derived from feature name or "Initial Implementation"
- `{{DATE}}` → current date (YYYY-MM-DD)
- `{{MOTIVATION}}` → derived from user story
- `{{EntityName}}` → actual entity name(s) from design
- `{{RepositoryName}}` → actual repository name(s) from design
- `{{ServiceName}}` → actual service name(s) from design
- `{{RouterName}}` → actual router name(s) from design
- `{{estimated_complexity}}` → "low" | "medium" | "high" based on task count

**Dependency chain:**
```
tasks[0].dependencies = []
tasks[1].dependencies = ["task-0"]
tasks[2].dependencies = ["task-1"]
...
```

## Step 5: Write Plan File

@clerk write filled plan.json to:
```bash
docs/spec/$ARGUMENTS/plan.json
```

## Step 6: Display Plan to User

Show filled plan.json with pretty formatting:

```
Implementation Plan for: $ARGUMENTS

Total tasks: 4
Estimated complexity: low

Tasks:
1. [entity] Implement Entity: User - 0 dependencies
   → Create User entity with fromRequest, toRecord, toResponse, validate

2. [repository] Implement Repository: UserRepository - depends on task-1
   → CRUD operations with query building

3. [service] Implement Service: UserService - depends on task-2
   → Business logic orchestration and transactions

4. [router] Implement Router: userRouter - depends on task-3
   → API endpoints for user operations
```

## Step 7: Get User Approval

Ask user:
```
Review this implementation plan:
- Total tasks: {{count}}
- Layers: {{layer_list}}
- Estimated complexity: {{complexity}}

Proceed with this plan? [y/n]
```

If user enters "y" or "yes":
- Proceed to Step 8

If user enters "n" or "no":
- Output: "Plan canceled. Edit spec.md and re-run /spec/plan when ready."
- Exit

## Step 8: Create Beads Tasks

@architect create Beads epic and tasks with dependencies.

### Create Epic
```bash
bd create "Feature: $ARGUMENTS" -t epic -p 1 -l feature,$ARGUMENTS
```
Store epic_id in plan.json.

### Create Tasks from Plan

For each task in plan.json tasks array:

```bash
# Extract task info from plan.json
bd create "$TASK_NAME" -p 1 -l $ARGUMENTS
```

Capture returned task ID.

### Add Dependencies Based on Plan

For each task that has dependencies:
```bash
for each dep in task.dependencies:
  bd dep add $current_task_id $dep_task_id --type blocks
```

**Match task dependencies with actual Beads task IDs.**

## Step 9: Update Plan Status

Add Beads information to plan.json:
```json
{
  "feature": "$ARGUMENTS",
  "status": "approved",
  "beads_epic_id": "EPIC_ID",
  "summary": {
    "total_tasks": 4,
    ...
  }
}
```

Write updated plan.json.

## ✅ Planning Complete

Approved plan: ✅
Beads epic created: EPIC_ID
Tasks created: {{count}}
plan.json saved: docs/spec/$ARGUMENTS/plan.json

**Next step:** Begin implementation
```
/spec/work $ARGUMENTS
```