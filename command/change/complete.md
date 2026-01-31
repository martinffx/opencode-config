---
description: Merge delta into spec and close epic (complete a change)
agent: scaffold
model: opencode/glm-4.7
temperature: 0.2
---

# Complete Change: $ARGUMENTS

Expected format: `$ARGUMENTS` = `<feature> <change-name>`
Example: `/change/complete auth add-oauth`

## Step 1: Validate Prerequisites

@context verify change exists and all tasks complete.

Parse arguments:
- Feature name: first argument
- Change name: remaining arguments (joined with hyphens)

Check change exists:
- If `docs/changes/<feature>/<change>/` does NOT exist → ERROR: "Change not found"

Read proposal to get epic ID:
```bash
grep "Epic:" docs/changes/<feature>/<change>/proposal.md
```

Check all tasks in epic are closed:
```bash
bd list --epic <epic-id> --status open --json
```
- If any open tasks → ERROR: "Epic has {{count}} open tasks. Complete them first: /spec/work <feature> <change>"

## Step 2: Read Delta Specification

@context load delta to understand changes.

Read `docs/changes/<feature>/<change>/delta.md`

Parse sections:
- ADDED Requirements
- ADDED Technical Design
- MODIFIED Requirements
- MODIFIED Technical Design
- REMOVED

Extract structured changes for merging.

## Step 3: Apply Delta to Spec

@scaffold merge delta into spec.md following structured rules.

Read current spec:
- `docs/spec/<feature>/spec.md`

Apply changes:

### ADDED Sections
Append new content to appropriate sections:

**ADDED Requirements:**
- Add to Requirements section (after existing requirements)
- Preserve formatting and structure

**ADDED Technical Design:**
- Entities → append to Domain Model / Entities section
- Services → append to Services section
- Endpoints → append to API Endpoints section
- Events → append to Events section
- Database → append to Data Persistence section

### MODIFIED Sections
Replace existing sections with new text:

**MODIFIED Requirements:**
- Find section by name/heading
- Replace entire section with new text from delta

**MODIFIED Technical Design:**
- Find design element by name
- Replace with new text from delta

### REMOVED Sections
Delete referenced sections:

- Find section by type and name
- Remove entirely from spec

### Update Implementation Notes
Add completion entry:
```markdown
## Implementation Notes
- <change-name> completed ({{date}})
  - Epic: <epic-id>
  - {{summary of changes}}
```

Write updated spec back to `docs/spec/<feature>/spec.md`

## Step 4: Close Beads Epic

@context close epic and archive change.

Close epic:
```bash
bd close <epic-id> --reason "Completed <change-name>, merged to spec"
```

Verify closure:
```bash
bd show <epic-id>
```

## Step 5: Archive Change

Delete change directory:
```bash
rm -rf docs/changes/<feature>/<change>/
```

**Rationale:** Git history preserves the change. Deleting keeps docs/ clean.

## ✅ Change Complete

Merged delta into: `docs/spec/<feature>/spec.md`
Closed epic: <epic-id>
Archived change: <feature>/<change>

Changes applied:
- {{added_count}} additions
- {{modified_count}} modifications
- {{removed_count}} removals

The spec now reflects:
- Updated requirements
- Updated technical design
- Implementation notes with completion timestamp

**Next steps:**

1. Review updated spec: `docs/spec/<feature>/spec.md`
2. Check feature status: `/spec/status <feature>`
3. Propose new changes: `/change/propose <feature> <new-change>`
4. Overall product status: `/product/status`
