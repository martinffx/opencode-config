---
description: Create implementation plan and Beads tasks with user review
agent: oracle
model: opencode/glm-4.7
temperature: 0.4
tools: Read, Glob, Grep, Write, Edit, Bash
---

# Plan Feature Implementation: $ARGUMENTS

Create implementation plan.json, get user approval, generate Beads tasks.

## Step 1: Read Full Specification

@clerk read spec.md (Requirements + Technical Design).

File: `docs/spec/$ARGUMENTS/spec.md`

Extract:
- Requirements (acceptance criteria, business rules)
- Technical Design (architecture, domain model, services, APIs)
- Layers needed (entity, repository, service, router)

## Step 2: Create Implementation Plan

@oracle create plan.json with task breakdown.

### Determine Layers

Based on Technical Design, identify layers to implement:

```json
{
  "feature": "$ARGUMENTS",
  "created_at": "ISO_TIMESTAMP",
  "status": "pending_approval",
  "total_estimated_hours": 8,
  "layers": [
    {
      "name": "entity",
      "title": "Business Logic Layer",
      "description": "Domain model with validation",
      "effort": "small",
      "estimated_hours": 1,
      "tasks": [
        "Create entity class with fromRequest, toRecord, validate",
        "Add business rule validation",
        "Test entity behavior"
      ],
      "blocking": []
    },
    {
      "name": "repository",
      "title": "Data Access Layer", 
      "description": "Database persistence",
      "effort": "small",
      "estimated_hours": 2,
      "tasks": [
        "Create repository with CRUD operations",
        "Implement query building",
        "Add database migration",
        "Test repository"
      ],
      "blocking": ["entity"]
    },
    {
      "name": "service",
      "title": "Business Service Layer",
      "description": "Orchestration and transactions",
      "effort": "medium",
      "estimated_hours": 3,
      "tasks": [
        "Create service with business operations",
        "Implement transaction handling",
        "Add error handling",
        "Test service behavior"
      ],
      "blocking": ["repository"]
    },
    {
      "name": "router",
      "title": "HTTP Interface Layer",
      "description": "API endpoints",
      "effort": "small",
      "estimated_hours": 2,
      "tasks": [
        "Create HTTP endpoints",
        "Implement request/response handling",
        "Add authentication",
        "Test API"
      ],
      "blocking": ["service"]
    }
  ]
}
```

**Note:** Only include layers actually needed. If design has no database, skip repository. No external API? Skip router.

## Step 3: Write Plan File

@clerk write plan.json to `docs/spec/$ARGUMENTS/plan.json`

## Step 4: Display Plan to User

@clerk display plan.json with pretty formatting:

```json
{
  "feature": "user-auth",
  "created_at": "2024-01-31T10:30:00Z",
  "status": "pending_approval",
  "total_estimated_hours": 8,
  "layers": [...]
}
```

## Step 5: Get User Approval

Ask user:

**Review this implementation plan:**

**Total Estimated Time:** 8 hours

**Layers:**
1. **entity** (1 hour) - Business Logic Layer
2. **repository** (2 hours) - Data Access Layer  
3. **service** (3 hours) - Business Service Layer
4. **router** (2 hours) - HTTP Interface Layer

**Proceed with this plan?** [y/n]

If user enters "y" or "yes":
- Proceed to Step 6

If user enters "n" or "no":
- Output: "Plan canceled. Edit spec.md and re-run /spec/plan when ready."
- Exit

## Step 6: Create Beads Tasks

@architect create Beads epic and tasks with dependencies.

### Create Epic

```bash
bd create "Feature: $ARGUMENTS" -t epic -p 1 -l feature,$ARGUMENTS
```

Store epic_id in plan.json.

### Create Tasks per Layer

For each layer in plan.json:

```bash
# Entity layer
bd create "$ARGUMENTS entity + validation" -p 1 -l $ARGUMENTS

# Repository layer
bd create "$ARGUMENTS repository" -p 1 -l $ARGUMENTS

# Service layer
bd create "$ARGUMENTS service" -p 1 -l $ARGUMENTS

# Router layer
bd create "$ARGUMENTS router" -p 1 -l $ARGUMENTS
```

### Add Dependencies

```bash
bd dep add <repo-id> <entity-id> --type blocks
bd dep add <service-id> <repo-id> --type blocks  
bd dep add <router-id> <service-id> --type blocks
```

**Note:** Only create tasks for layers present in design.

### Update Plan Status

Update plan.json:

```json
{
  "feature": "$ARGUMENTS",
  "created_at": "...",
  "status": "approved",
  "beads_epic_id": "EPIC_ID",
  "total_tasks": 4,
  ...
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

**Note:** To modify the plan later, edit spec.md (Requirements or Technical Design) and re-run `/spec/plan $ARGUMENTS`. The plan.json and Beads tasks will be updated accordingly.
