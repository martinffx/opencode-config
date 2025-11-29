---
description: Comprehensive code review as a Senior Engineer (specify branch or defaults to main)
agent: architect
model: anthropic/claude-haiku-4-5
temperature: 0.1
permissions:
  read: allow
  write: ask
  bash: allow
---

# Code Review

## Step 1: Gather Context

Target branch: $ARGUMENTS (defaults to main if not specified)

Changes overview:
!`git diff ${ARGUMENTS:-main} --name-status`

Current branch: !`git branch --show-current`

Recent commits being reviewed:
!`git log ${ARGUMENTS:-main}..HEAD --oneline -10`

Full diff for review:
!`git diff ${ARGUMENTS:-main}`

## Step 1.2: Read Project Standards
@read docs/standards/coding.md
@read docs/standards/architecture.md

## Step 2: Senior Engineer Review

@architect analyze all changes with senior engineer perspective.

Focus areas:
**🔴 CRITICAL:** Security vulnerabilities, data loss risks, breaking changes
**🟡 IMPORTANT:** Bugs, performance issues, maintainability concerns
**🟢 MINOR:** Style inconsistencies, naming improvements, optimizations

**Review Standards:**

- Follow docs/standards/coding.md (Stub-Driven TDD, testing patterns)
- Follow docs/standards/architecture.md (layered architecture)
- Validate boundary testing and anti-patterns per standards
- Ensure proper dependency injection and layer separation

Generate specific, actionable feedback with file locations and suggested improvements.

## Step 3: Review Report

**Summary:** Brief overview of changes and overall assessment

**Issues by Severity:**

- 🔴 **STANDARDS VIOLATIONS:** docs/standards/ non-compliance
- 🔴 `file:line` - Issue description → Suggested fix
- 🟡 `file:line` - Issue description → Suggested fix
- 🟢 `file:line` - Issue description → Suggested fix

**👍 Positive Aspects:** Highlight good practices and quality code

**Recommendations:**

- [ ] Required changes before merge
- [ ] Suggested improvements for future
- [ ] Testing recommendations

**Overall Assessment:** [APPROVE/REQUEST CHANGES/NEEDS DISCUSSION]
