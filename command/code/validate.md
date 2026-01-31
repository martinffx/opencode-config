---
description: Validate code changes with build, lint, and test pipeline
tools: Read, Glob, Grep, Write, Edit, Bash
model: opencode/minimax-m2.1
temperature: 0.1
permissions:
  read: allow
  write: ask
  bash: allow
---

# Validate Changes

## Step 1: Quality Pipeline

Main agent execute comprehensive validation pipeline.

Running validation checks:

**Build check:**
!`npm run build || echo "❌ Build failed"`

**Lint check:**
!`npm run lint:fix || echo "❌ Lint errors remain"`

**Type check:**
!`npm run type-check || echo "❌ Type errors found"`

**Test check:**
!`npm test || echo "❌ Tests failed"`

**Coverage check:**
!`npm run test:coverage || echo "❌ Coverage below threshold"`

## Step 2: Results Analysis

Main agent analyze validation results and provide specific guidance.

Based on the validation results above:

**If all passed:**
✅ **Ready to commit!** All quality gates passed.

- Build compiles successfully
- Code style meets standards
- All tests passing with good coverage
- No type errors detected

**If any failed:**
❌ **Fix required** - Review the specific errors above and address:

**Build failures** → Check for:

- TypeScript compilation errors
- Missing dependencies or imports
- Syntax errors or invalid code

**Lint errors** → Address:

- Code style inconsistencies
- Unused variables or imports
- Code complexity issues

**Type errors** → Fix:

- Type mismatches or missing types
- Incorrect interface implementations
- Generic type constraints

**Test failures** → Resolve:

- Failing unit or integration tests
- Test setup or teardown issues
- Update test expectations if behavior changed

**Coverage issues** → Improve:

- Add tests for uncovered code paths
- Test edge cases and error conditions
- Ensure critical business logic is tested

## Step 3: Recommendations

**Next actions:**

- Fix the specific issues identified above
- Run `/code-validate` again to verify fixes
- Consider `/code-review` for additional quality assessment
- Ready to `/commit` once all validations pass

**Quality gates:**

- ✅ Build: Code compiles without errors
- ✅ Lint: Code meets style standards
- ✅ Types: Type safety maintained
- ✅ Tests: All tests pass with adequate coverage
