---
description: Create feature specification through structured requirements interview
agent: oracle
subtask: true
---

# Create Feature Spec: $ARGUMENTS

Create a new feature specification through structured requirements gathering using templates.

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
- Store mode in variable: `brownfield = true`

If no code found:
- Set mode: GREENFIELD
- Store mode in variable: `brownfield = false`

## Step 3: Gather Requirements via Structured Interview

@oracle conduct structured interview.

### User Story

Format: "As a [user type], I want to [action] so that [benefit]"

Your user story for $ARGUMENTS:
[Wait for response → store as USER_TYPE, ACTION, BENEFIT]

### Acceptance Criteria (3-5 specific, testable conditions)

What makes this feature complete? Examples:
- User can X with Y result
- System does Z when W happens
- Data shows correct values

Your criteria:
1. [Wait for response]
2. [Wait for response]
3. [Wait for response]
[Collect into array: ACCEPTANCE_CRITERIA]

### Business Rules (constraints, validation rules)

What rules must be enforced?
- Validation requirements
- Access controls
- Limits or constraints

Your rules:
[Wait for response]
[Collect into array: BUSINESS_RULES]

### Scope Definition

**What's included in this feature?**
[Wait for response]
[Collect into array: INCLUDED]

**What's explicitly NOT included?** (Mark for future phases)
[Wait for response]
[Collect into array: EXCLUDED]

### Confirm Understanding

"So to confirm:
- Users will [workflow from user story]
- Success means [acceptance criteria]
- We must enforce [business rules]
- In scope: [included], Out of scope: [excluded]

Correct?"
[Wait for confirmation]

## Step 4: Create Specification Document from Template

@clerk read spec.md template and fill with gathered requirements.

Read template:
```bash
~/.config/opencode/assets/templates/spec.md
```

Replace placeholders:
- `{{FEATURE_NAME}}` → $ARGUMENTS
- `{{USER_TYPE}}` → user type from interview
- `{{ACTION}}` → action from interview
- `{{BENEFIT}}` → benefit from interview
- `{{CRITERION_X}}` → acceptance criteria items
- `{{RULE_X}}` → business rules
- `{{INCLUDED_X}}` → included scope items
- `{{EXCLUDED_X}}` → excluded scope items
- `{{ENTITY_NAME}}` → $ARGUMENTS (or derived name)
- `{{PATTERN_NAME}}` → placeholder for design phase
- etc.

Create directory:
```bash
mkdir -p docs/spec/$ARGUMENTS
```

Write filled spec.md to:
```bash
docs/spec/$ARGUMENTS/spec.md
```

## Step 5: Optional: Save Requirements to JSON

If user requests, save structured requirements to requirements.json format:

```bash
cat > docs/spec/$ARGUMENTS/requirements.json <<'EOF'
{
  "feature": "$ARGUMENTS",
  "date": "$(date +%Y-%m-%d)",
  "mode": "$(if test $brownfield = true; then echo "brownfield"; else echo "greenfield"; fi)",
  "user_story": {
    "as_a": "USER_TYPE",
    "i_want_to": "ACTION",
    "so_that": "BENEFIT"
  },
  "acceptance_criteria": [
    "CRITERION_1",
    "CRITERION_2",
    "CRITERION_3"
  ],
  "business_rules": [
    "RULE_1",
    "RULE_2"
  ],
  "scope": {
    "included": ["INCLUDED_1", "INCLUDED_2"],
    "excluded": ["EXCLUDED_1", "EXCLUDED_2"]
  },
  "brownfield_context": {
    "code_found": "$(if test $brownfield = true; then echo "yes"; else echo "no"; fi)",
    "existing_files": ["FILE_1", "FILE_2"],
    "current_state": {
      "what_works": "DESCRIPTION",
      "what_is_broken": "DESCRIPTION",
      "what_is_missing": "DESCRIPTION",
      "what_should_change": "DESCRIPTION"
    }
  }
}
EOF
```

## ✅ Requirements Complete

Created: `docs/spec/$ARGUMENTS/spec.md`

Contains:
- User story (from interview)
- Acceptance criteria ({{count}} items)
- Business rules ({{count}} items)
- Scope definition

**Optional:** `docs/spec/$ARGUMENTS/requirements.json` with structured requirements

**Next step:** Add technical design
```
/spec/design $ARGUMENTS
```