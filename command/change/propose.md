---
description: Propose changes to existing feature (brownfield development)
agent: analyst
model: anthropic/claude-sonnet-4-5
temperature: 0.3
---

# Propose Change: $ARGUMENTS

Expected format: `$ARGUMENTS` = `<feature> <change-name>`
Example: `/change/propose auth add-oauth`

## Step 1: Validate Prerequisites

@context check for existing spec and Beads installation.

Parse arguments:
- Feature name: first argument
- Change name: remaining arguments (joined with hyphens)

Check if spec exists:
- If `docs/spec/<feature>/spec.md` does NOT exist → ERROR: "No spec found for <feature>. Use /spec/init to create."

Check Beads installation:
```bash
bd --version
```
- If command fails → ERROR: "Beads not found. Install with: npm install -g @beads/bd, then run: bd init"

## Step 2: Lightweight Requirements Interview

@analyst gather change requirements through focused questions.

**What change are you proposing?**
One sentence summary:
[Wait for response]

**Why is this change needed?**
Motivation and business context:
[Wait for response]

**What's in scope for this change?**
- What features/functionality will be added or modified?
- What's explicitly NOT included?

[Wait for response]

**Which layers will be affected?**
Ask user or infer from description:
- Entity (domain model changes)
- Repository (data access changes)
- Service (business logic changes)
- Router (API endpoint changes)
- Events (async communication)
- Client (external API integration)

[Wait for response]

**Testing approach:**
- Integration tests
- E2E tests
- Unit tests
- Other considerations

[Wait for response]

## Step 3: Load Context

@context retrieve feature spec and related information.

Read files:
- `docs/spec/<feature>/spec.md` → current specification
- `docs/templates/standards/{lang}/architecture.md` → architectural patterns
- `docs/templates/standards/{lang}/coding.md` → implementation guidelines

Understand current feature state to design appropriate changes.

## Step 4: Create Proposal Document

@scaffold generate proposal document.

Create directory: `docs/changes/<feature>/<change>/`

Create `docs/changes/<feature>/<change>/proposal.md` containing:

```markdown
# Proposal: <feature>/<change>

## Summary
[One sentence from interview]

## Motivation
[Business context from interview]

## Scope

### Included
- [Items from interview]

### Excluded
- [Items from interview]

### Layers Affected
- [Layers from interview]

## Testing Approach
[Strategy from interview]

## Beads Epic
Epic: [to be filled]
```

Apply template from `docs/templates/changes/proposal.md`

## Step 5: Generate Delta Specification

@architect design changes based on proposal and current spec.

Read:
- `docs/spec/<feature>/spec.md` → current state
- `docs/changes/<feature>/<change>/proposal.md` → desired changes
- `docs/templates/standards/` → patterns to follow

Design changes following architecture patterns.

Create `docs/changes/<feature>/<change>/delta.md` containing:

**ADDED Requirements:**
- New SHALL/SHOULD statements
- New scenarios (WHEN/THEN format)

**ADDED Technical Design:**
- New entities or entity methods
- New service operations
- New API endpoints
- New events
- New database schema elements

**MODIFIED Requirements:**
- Full replacement text for changed requirements

**MODIFIED Technical Design:**
- Full replacement text for changed design elements

**REMOVED:**
- References to requirements/design elements being removed

Apply template from `docs/templates/changes/delta.md`

## Step 6: Create Beads Epic with Contextual Tasks

@architect analyze delta and create dependency-aware tasks.

Based on layers affected in proposal, create tasks for **only those layers**:

Example (service + router):
```bash
bd create "Change: <feature>/<change>" -t epic -p 1 -l change,<feature>
bd create "<change> service methods" -p 1 -l <feature>
bd create "<change> router endpoints" -p 1 -l <feature>
bd dep add <router-id> <service-id> --type blocks
```

Example (full stack):
```bash
bd create "Change: <feature>/<change>" -t epic -p 1 -l change,<feature>
bd create "<change> entity updates" -p 1 -l <feature>
bd create "<change> repository methods" -p 1 -l <feature>
bd create "<change> service logic" -p 1 -l <feature>
bd create "<change> API endpoints" -p 1 -l <feature>
bd dep add <repo-id> <entity-id> --type blocks
bd dep add <service-id> <repo-id> --type blocks
bd dep add <router-id> <service-id> --type blocks
```

Example (simple, single task):
If proposal has `--simple` flag or change is trivial:
```bash
bd create "<change> implementation" -t task -p 2 -l <feature>
```

Store epic ID and update proposal.md with epic reference.

## Step 7: Handle Cross-Feature Dependencies

If this change depends on or affects other features:

Ask: "Does this change depend on changes to other features?"
[Wait for response]

If yes:
- Prompt to create separate proposals for affected features
- Link via Beads dependencies:
  ```bash
  bd dep add <this-epic> <other-epic> --type blocked-by
  ```

## ✅ Proposal Complete

Created:
- `docs/changes/<feature>/<change>/proposal.md`
- `docs/changes/<feature>/<change>/delta.md`
- Beads Epic: [epic-id] ({{task_count}} tasks)

The proposal includes:
- Business context and motivation
- Structured delta (ADDED/MODIFIED/REMOVED)
- Contextual task breakdown for affected layers
- Dependency-ordered implementation plan

**Next steps:**

1. Review the proposal and delta
2. Begin implementation: `/spec/work <feature> <change>`
3. Check progress: `/spec/status <feature>`
4. When complete: `/change/complete <feature> <change>`
