---
description: Implement feature following TDD and architectural standards
agent: coder
model: anthropic/claude-sonnet-4-5
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

**TDD Process (Stub-Red-Green-Refactor):**

1. **STUB** - Create method signatures that throw `NotImplementedError`/`todo!()`
   - Update todo: Mark "STUB: Create method signatures" as in_progress
2. **RED** - Write test expecting error, then tests for actual behavior
   - Update todo: Mark "STUB" complete, "RED: Write failing tests" as in_progress
3. **GREEN** - Implement methods to make tests pass
   - Update todo: Mark "RED" complete, "GREEN: Implement methods" as in_progress
4. **REFACTOR** - Clean up code while maintaining test coverage
   - Update todo: Mark "GREEN" complete, "REFACTOR: Improve structure" as in_progress
5. **REPEAT** - Continue cycle for each boundary requirement
   - Update todo: Mark "REFACTOR" complete, move to next requirement

**Implementation Standards:**

- Follow architectural patterns from design.md
- Maintain layer boundaries (Router → Service → Repository → Entity)
- Test at layer boundaries: HTTP→mocked service, service→mocked repo, repo→docker db, client→MSW/VCR
- Implement stub methods at boundaries first
- Write tests to validate desired behavior, asserting calls to mocks
- Don't test every method but instead validate behavior at boundaries
- Follow dependency order: Entity → Repository → Service → Router (bottom-up)
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

Implementation completed using Stub-Driven TDD methodology:

- Stub methods created with NotImplementedError/todo!() at boundaries
- Tests written to validate behavior at layer boundaries
- Code follows architectural standards and patterns
- Proper layer separation maintained with boundary testing
- Mocks used appropriately for service/repository testing
- Task progress tracking updated with completion status
- Todo list completed with all phases tracked
- Ready for next implementation task

**Next options:**

1. Continue next task: `/spec/implement $ARGUMENTS`
2. Check feature progress: `/spec/progress $ARGUMENTS`
3. Implement specific task: `/spec/implement $ARGUMENTS [task_id]`
4. Review completed work: `/code-review $ARGUMENTS`
5. View completed todo list: `/todo read`
