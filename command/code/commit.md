---
description: Analyze changes and create a well-crafted git commit (optionally with custom message)
agent: coder
model: opencode/glm-4.6
temperature: 0.1
permissions:
  read: allow
  write: allow
  bash: allow
---

# Smart Git Commit

## Step 1: Analyze Repository State

@coder analyze current repository state and changes.

Current repository state:
- Git status: !`git status --porcelain`
- Staged changes: !`git diff --cached --name-status`
- Unstaged changes: !`git diff --name-status`
- Current branch: !`git branch --show-current`
- Recent commits: !`git log --oneline -3`

Analyze the changes to determine appropriate commit type, scope, and message structure.

## Step 2: Generate Commit Message

@coder create conventional commit message based on code changes.

**Conventional Commit Rules:**
- **feat(scope)**: New feature or component
- **fix(scope)**: Bug fixes and corrections
- **test(scope)**: Test additions or modifications
- **docs(scope)**: Documentation changes
- **style(scope)**: Formatting, semicolons, etc (no code change)
- **refactor(scope)**: Code restructuring without behavior change
- **perf(scope)**: Performance improvements
- **chore(scope)**: Build process, auxiliary tools, dependencies

**Message Format:** `type(scope): brief description`
- Keep subject under 50 characters
- Use imperative mood ("Add" not "Added")
- Lowercase subject line
- No period at end of subject

**Custom message handling:**
- If $ARGUMENTS provided: Use as commit message
- If no $ARGUMENTS: Generate conventional commit message

**Analysis Result:**
- Suggested message: `[generated conventional commit message]`
- Rationale: [Explanation of commit type and scope chosen]

## Step 3: Execute Commit

@coder execute the commit with quality checks.

**Pre-commit validation:**
- Ensure working directory is clean or has staged changes
- Verify no merge conflicts exist
- Check for large files or sensitive data

**Commit execution:**
- Stage all changes: `git add -A`
- Create commit with message: `git commit -m "[final message]"`
- Display commit hash and summary
- Provide next steps guidance

**Post-commit summary:**
- Commit created: [hash] [message]
- Files changed: [count]
- Branch: [current branch]
- Ready for: `git push` or continue development

## Quality Checks

**Before committing:**
- All changes are intentional and related
- No debug code, console.logs, or temporary files
- Commit represents a logical unit of work
- Message clearly describes the change

**Recommendations:**
- Run `/code-validate` before committing for quality gates
- Use `/code-review` for complex changes
- Consider feature branch workflow for major changes

---

**Usage**:
- `/commit` - Analyze changes and generate conventional commit
- `/commit "feat: add user authentication"` - Custom commit message
- `/commit "fix: resolve login validation bug"` - Specific fix message

**Workflow Integration**:
```bash
/spec-implement feature-name    # Implement with TDD
/code-validate                 # Ensure quality gates pass
/commit                        # Smart conventional commit
git push                       # Push to remote
```
