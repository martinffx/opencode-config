---
description: Implement feature following TDD and architectural standards
agent: coder
model: opencode/glm-4.6
temperature: 0.1
---

# Implementation: $ARGUMENTS

## Step 1: Load Current Progress

@context retrieve task list and current implementation status.

Load files:

- `docs/spec/$ARGUMENTS/tasks.md` - Complete task breakdown with progress tracking
- `docs/spec/$ARGUMENTS/design.md` - Technical specifications
- `docs/standards/coding.md` - TDD implementation guidelines

Identify next task to implement or specific task if provided.

## Step 2: Implement with TDD

@coder implement the selected task using strict test-driven development.

**TDD Process (Red-Green-Refactor):**

1. **RED** - Write failing test for new functionality
2. **GREEN** - Write minimal code to pass the test
3. **REFACTOR** - Improve code structure without changing behavior
4. **REPEAT** - Continue cycle for each requirement

**Implementation Standards:**

- Follow architectural patterns from design.md
- Maintain layer boundaries (Router → Service → Repository → Entity)
- Implement proper error handling and validation
- Use dependency injection for testability
- Write self-documenting code with clear naming
- Ensure test coverage > 80%

**Quality Checklist:**

- [ ] All tests passing (no red tests)
- [ ] Code follows project conventions
- [ ] Proper error handling implemented
- [ ] Input validation where required
- [ ] Types/interfaces properly defined
- [ ] Documentation updated if needed

## Step 3: Update Task Status

@scaffold update progress tracking with completed work.

Update files:

- `docs/spec/$ARGUMENTS/tasks.md` - Mark task complete, update progress percentage
- Add any new files created to task tracking
- Identify next available task for implementation

## ✅ Task Complete

Implementation completed using TDD methodology:

- Tests written first and all passing
- Code follows architectural standards and patterns
- Proper layer separation maintained
- Task progress tracking updated with completion status
- Ready for next implementation task

**Next options:**

1. Continue next task: `/spec/implement $ARGUMENTS`
2. Check feature progress: `/spec/progress $ARGUMENTS`
3. Implement specific task: `/spec/implement $ARGUMENTS [task_id]`
4. Review completed work: `/code-review $ARGUMENTS`
