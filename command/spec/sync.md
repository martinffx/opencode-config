---
description: Update spec from code (retroactive sync when code was built without ceremony)
agent: architect
subtask: true
---

# Sync Spec from Code: $ARGUMENTS

## Step 1: Validate Prerequisites

@clerk check Beads installation.

```bash
bd --version
```
- If command fails → ERROR: "Beads not found. Install with: npm install -g @beads/bd, then run: bd init"

## Step 2: Analyze Existing Code

@architect examine implementation, reverse-engineer design.

Search and analyze code:
1. Entity classes (domain models with fromRequest, toRecord, validate)
2. Service classes (business logic operations)
3. Repository classes (data access patterns)
4. Router/handler files (API endpoints)
5. Event producers/consumers (if event-driven)

Extract:
- Data models and schemas
- API contracts
- Business rules in code
- Database interactions
- External integrations

## Step 3: Update Spec

@clerk update spec.md from code analysis.

Add missing Implementation Notes to `docs/spec/$ARGUMENTS/spec.md`:

```markdown
## Implementation Notes

### Entities
[Document discovered entities and their methods]

### Services
[Document discovered services and operations]

### Repository Methods
[Document data access patterns]

### API Endpoints
[Document discovered API routes]

### Database Schema
[Reverse-engineer from repository code]

### Business Rules
[Extract validation logic from code]
```

## Step 4: Discover Incomplete Work

@architect identify tasks that need completion.

Compare spec design vs actual code:
- Missing error handling?
- Incomplete tests?
- Unimplemented methods?
- Missing validation?

Create Beads for discovered tasks:
```bash
bd create "Fix missing validation" -t task -p 2 -l $ARGUMENTS
bd create "Add error handlers" -t task -p 2 -l $ARGUMENTS
bd dep add <new-id> <current-id> --type discovered-from
```

## Step 5: Document Sync

@clerk add documentation about sync to spec.

Add to Implementation Notes:
```markdown
### Sync History
- [Date]: Retroactively synced from existing code
- Discovered: [entities, services, etc]
- Created: [N] Beads for missing work
```

## ✅ Sync Complete

Updated: `docs/spec/$ARGUMENTS/spec.md`
Created: [N] Beads tasks for discovered work

**Next steps:**

1. Review discovered Beads tasks
2. Complete missing implementation: `/spec/work $ARGUMENTS`