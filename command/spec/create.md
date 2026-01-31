---
description: Create feature specification through structured requirements interview
agent: oracle
model: opencode/glm-4.7
temperature: 0.3
tools: Read, Glob, Grep, Write, Edit, Bash
---

# Create Feature Spec: $ARGUMENTS

Create a new feature specification through structured requirements gathering.

## Step 1: Validate Prerequisites

@clerk check for existing spec and Beads installation.

Check if spec already exists:
- If `docs/spec/$ARGUMENTS/spec.md` exists → ERROR: "Spec already exists. Edit spec.md directly and re-run /spec/design and /spec/plan."

Check Beads installation:
```bash
bd --version
```
- If command fails → ERROR: "Beads not found. Install with: npm install -g @beads/bd, then run: bd init"

## Step 2: Detect Existing Code (Brownfield Awareness)

@clerk search for existing code using multiple strategies.

Search for code in these patterns (stop at first match):

1. **Feature-named directory:**
   - `src/$ARGUMENTS/`
   - `lib/$ARGUMENTS/`
   - `app/$ARGUMENTS/`
   - `packages/$ARGUMENTS/`

2. **Layer-based with feature subdirectory:**
   - `src/{routes,services,repositories,entities}/$ARGUMENTS.*`
   - `src/{routers,handlers}/$ARGUMENTS.*`

3. **Layer-based with feature in filename:**
   - `src/routes/$ARGUMENTS.{ts,py,rs,js}`
   - `src/services/$ARGUMENTS*Service.{ts,py,rs,js}`
   - `src/repositories/$ARGUMENTS*Repository.{ts,py,rs,js}`

If code found:
- Set mode: BROWNFIELD
- Note: "Existing code detected. This spec will document current implementation."

If no code found:
- Set mode: GREENFIELD

## Step 3: Gather Requirements via Structured Interview

@oracle conduct structured interview.

### User Story

Format: "As a [user type], I want to [action] so that [benefit]"

Your user story for $ARGUMENTS:
[Wait for response]

### Acceptance Criteria (3-5 specific, testable conditions)

What makes this feature complete? Examples:
- User can X with Y result
- System does Z when W happens
- Data shows correct values

Your criteria:
1. [Wait for response]
2. [Wait for response]
3. [Wait for response]

### Business Rules (constraints, validation rules)

What rules must be enforced?
- Validation requirements
- Access controls
- Limits or constraints

Your rules:
[Wait for response]

### Scope Definition

**What's included in this feature?**
[Wait for response]

**What's explicitly NOT included?** (Mark for future phases)
[Wait for response]

### Confirm Understanding

"So to confirm:
- Users will [workflow from user story]
- Success means [acceptance criteria]
- We must enforce [business rules]
- In scope: [included], Out of scope: [excluded]

Correct?"
[Wait for confirmation]

## Step 4: Create Initial Specification Document

@clerk create spec.md with Requirements section only.

Create `docs/spec/$ARGUMENTS/spec.md`:

```markdown
# $ARGUMENTS

## Requirements

### User Story
[User story from interview]

### Acceptance Criteria
- [Criterion 1]
- [Criterion 2]
- [Criterion 3]

### Business Rules
- [Rule 1]
- [Rule 2]

### Scope

#### Included
- [Item 1]
- [Item 2]

#### Excluded (Future Phases)
- [Item 1]
- [Item 2]

## Technical Design
*To be added via `/spec/design $ARGUMENTS`*

## Implementation Notes
*To be updated during `/spec/work $ARGUMENTS`*
```

Create directory structure:
```bash
mkdir -p docs/spec/$ARGUMENTS
```

Write spec.md to file.

## ✅ Requirements Complete

Created: `docs/spec/$ARGUMENTS/spec.md`

Contains:
- User story
- Acceptance criteria ({{count}} items)
- Business rules
- Scope definition

**Next step:** Add technical design
```
/spec/design $ARGUMENTS
```