---
description: Create feature specification through structured requirements gathering
agent: analyst
model: opencode/glm-4.6
temperature: 0.3
---

# Create Feature Spec: $ARGUMENTS

## Step 1: Gather Requirements

I'll ask you questions to understand this feature. Please provide specific answers.

### User Story

**Format:** As a [user type], I want to [action] so that [benefit]
**Example:** "As a customer, I want to reset password so that I can regain access"

Your user story for $ARGUMENTS:

[Wait for response]

### Acceptance Criteria

**List 3-5 specific, testable conditions for success:**
**Example:**

- User receives email within 60 seconds
- Link expires after 24 hours
- Password meets security requirements

Your criteria:

[Wait for response]

### Business Rules

**What constraints or validation rules apply?**
**Example:** "Max 3 reset attempts per day", "Only verified emails"

Your rules:

[Wait for response]

### Scope

**What's included?** (list features)
**What's excluded?** (explicitly out of scope)

[Wait for response]

### Output: `docs/spec/$ARGUMENTS/requirements.json`

```json
{
  "raw_user_story": "[user's answer]",
  "raw_criteria": "[user's answer]",
  "raw_rules": "[user's answer]",
  "raw_scope": "[user's answer]"
}
```

## Step 2: Load Project Context

@context retrieve relevant project information for $ARGUMENTS feature.

First read: `docs/spec/$ARGUMENTS/requirements.json`

Use sequential thinking to analyze the following files and extract relevant context:

- `docs/product/product.md` → vision
- `docs/spec/*/` → existing features
- `docs/standards/` → patterns

Provide context on existing relevant code and features in the codebase.

### Output: `docs/spec/$ARGUMENTS/context.json`

```json
{
  "product_vision": "...",
  "existing_features": ["auth", "profile"],
  "architecture": {...}
}
```

## Step 3: Create Specification Files

@scaffold generate specification files from requirements and context.

Read: `docs/spec/$ARGUMENTS/requirements.json`
Read: `docs/spec/$ARGUMENTS/context.json`

Use sequential thinking to:

- Parse raw user story into proper format
- Structure acceptance criteria as testable items
- Align with existing patterns from context
- Check for conflicts with existing features
- Validate business rules against product vision

Apply template: spec template from standards

Create file in `docs/spec/$ARGUMENTS/`:

- `spec.md` - Complete feature specification

## ✅ Specification Complete

Created complete feature specification with:

- Structured requirements from analyst interview
- Project context integration
- Single source of truth specification
- Ready for technical design phase

**Next steps:**

1. Review the generated specification
2. Create technical design: `/spec/design $ARGUMENTS`
3. Or jump to planning: `/spec/plan $ARGUMENTS`
