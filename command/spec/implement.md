---
description: Implement feature following TDD and architectural standards
agent: coder
model: opencode/glm-4.6
temperature: 0.1
---

# Implementation: $ARGUMENTS

## Step 1: Load Current Progress & Initialize Todo List

@context retrieve task list and current implementation status.

Load files:

- `docs/spec/$ARGUMENTS/tasks.md` - Complete task breakdown with progress tracking
- `docs/spec/$ARGUMENTS/design.md` - Technical specifications
- `docs/standards/coding.md` - TDD implementation guidelines

@todowrite create implementation todo list from tasks.md.

Initialize todo structure:
- Main task: Implement $ARGUMENTS feature
- Sub-tasks: TDD cycle phases (RED → GREEN → REFACTOR)
- Quality gates: Convert checklist to todo items
- Progress tracking: Set current task as in_progress

Identify next task to implement or specific task if provided.

## Step 2: Implement with TDD & Track Progress

@coder implement the selected task using strict test-driven development.

@todowrite update todo list with current TDD phase.

**TDD Process (Red-Green-Refactor):**

1. **RED** - Write failing test for new functionality
   - Update todo: Mark "RED: Write failing test" as in_progress
2. **GREEN** - Write minimal code to pass the test
   - Update todo: Mark "RED" complete, "GREEN: Implement minimal code" as in_progress
3. **REFACTOR** - Improve code structure without changing behavior
   - Update todo: Mark "GREEN" complete, "REFACTOR: Improve structure" as in_progress
4. **REPEAT** - Continue cycle for each requirement
   - Update todo: Mark "REFACTOR" complete, move to next requirement

**Implementation Standards:**

- Follow architectural patterns from design.md
- Maintain layer boundaries (Router → Service → Repository → Entity)
- Implement proper error handling and validation
- Use dependency injection for testability
- Write self-documenting code with clear naming
- Ensure test coverage > 80%

**Quality Gates (Todo Items):**

- [ ] All tests passing (no red tests)
- [ ] Code follows project conventions
- [ ] Proper error handling implemented
- [ ] Input validation where required
- [ ] Types/interfaces properly defined
- [ ] Documentation updated if needed

@todowrite update quality gate items as they pass validation.

## Step 3: Update Task Status & Complete Todo List

@scaffold update progress tracking with completed work.

@todowrite mark implementation task as completed.

Update files:

- `docs/spec/$ARGUMENTS/tasks.md` - Mark task complete, update progress percentage
- Add any new files created to task tracking
- Identify next available task for implementation

Update todo list:
- Mark main implementation task as completed
- Verify all TDD phases completed
- Confirm all quality gates passed
- Archive completed todo list for reference

## ✅ Task Complete

Implementation completed using TDD methodology:

- Tests written first and all passing
- Code follows architectural standards and patterns
- Proper layer separation maintained
- Task progress tracking updated with completion status
- Todo list completed with all phases tracked
- Ready for next implementation task

**Next options:**

1. Continue next task: `/spec/implement $ARGUMENTS`
2. Check feature progress: `/spec/progress $ARGUMENTS`
3. Implement specific task: `/spec/implement $ARGUMENTS [task_id]`
4. Review completed work: `/code-review $ARGUMENTS`
5. View completed todo list: `/todo read`
