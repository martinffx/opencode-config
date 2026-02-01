---
description: Implement next ready task using layer boundary testing
tools: Read, Glob, Grep, Write, Edit, Bash
---

# Implementation: $ARGUMENTS

Optional format: `<feature>` or empty (any ready task)

**Note:** Implementation is loop-based with user approval for each task.

## Step 1: Check Beads and Find Ready Task

@clerk check Beads installation and find next ready task.

Check Beads:
```bash
bd --version
```
- If command fails → ERROR: "Beads not found. Install with: npm install -g @beads/bd, then run: bd init"

Find ready task:

**If feature specified:**
```bash
bd ready --label <feature> --json
```

**If no arguments:**
```bash
bd ready --json
```

Parse result:
- If no ready tasks → REPORT: "No ready tasks. Feature complete or check for blocked tasks."
- If ready tasks found → Select first task

## Step 2: Display Task and Get Approval

Display task details:

```
Next ready task:
  Description: <task-description>
  Layer: <router|service|repository|entity>
  Feature: <feature-name>
  Dependencies: [all resolved or blocked list]
```

Ask user:
```
Ready to implement this task? [y/n/q]
```

**If user enters 'y' or 'yes':**
- Continue to Step 3

**If user enters 'n' or 'no':**
- Output: "Work paused. Run /spec/work <feature> to resume when ready."
- Exit

**If user enters 'q' or 'quit':**
- Output: "Exiting implementation loop. Re-run /spec/work to continue later."
- Exit

## Step 3: Mark Task and Load Context

@clerk mark task in progress:
```bash
bd update <task-id> --status in_progress
```

@clerk load all necessary context for implementation.

Read specifications:
- `docs/spec/<feature>/spec.md` → requirements, design, implementation notes
- `docs/spec/<feature>/plan.json` → layer breakdown, effort estimates

Read standards:
- `docs/standards/coding.md` → implementation patterns and testing
- `docs/standards/architecture.md` → layered architecture patterns

Extract from spec:
- Entity definitions and methods
- Service operations
- Repository methods
- API endpoints
- Database schema
- Events

## Step 4: Implement Using Stub→Test→Fix Pattern

**Main agent implements task** following layer boundary testing approach.

### Layer Boundary Testing Pattern

Test at boundaries, not every method:
- **Router**: HTTP request → mock Service → HTTP response
- **Service**: Entity in → mock Repository → Entity out
- **Repository**: Entity → real database → Entity
- **Client**: Request → MSW/VCR mock → Response

### Implementation Pattern: Stub→Test→Fix

#### STUB Phase
Create method signatures that throw NotImplementedError:

```typescript
// Example
async createUser(entity: UserEntity): Promise<UserEntity> {
  throw new Error('Not implemented');
}
```

```python
# Example
def create_user(self, entity: UserEntity) -> UserEntity:
    raise NotImplementedError("create_user not implemented")
```

#### TEST Phase
Write tests for layer boundary behavior:

**Router tests:**
```typescript
// Test real HTTP → mocked service
it('POST /users creates user', async () => {
  const mockService = { createUser: jest.fn().mockResolvedValue(userEntity) };
  const response = await app.inject({
    method: 'POST',
    url: '/users',
    payload: { email: 'test@example.com', name: 'Test' }
  });
  expect(response.statusCode).toBe(201);
  expect(mockService.createUser).toHaveBeenCalled();
});
```

**Service tests:**
```typescript
// Test real entity → mocked repository
it('createUser validates and persists', async () => {
  const mockRepo = { create: jest.fn(), findByEmail: jest.fn() };
  const service = new UserService(mockRepo);
  const entity = UserEntity.fromRequest({ email: 'test@example.com', name: 'Test' });
  await service.createUser(entity);
  expect(mockRepo.findByEmail).toHaveBeenCalledWith('test@example.com');
  expect(mockRepo.create).toHaveBeenCalled();
});
```

**Repository tests:**
```typescript
// Test real entity → real database (Docker)
it('create persists to database', async () => {
  const testDb = await TestDatabase.getInstance();
  const repo = new UserRepository(testDb.db);
  const entity = new UserEntity(undefined, 'test@example.com', 'Test');
  const result = await repo.create(entity.toRecord());
  expect(result.id).toBeDefined();
  expect(result.email).toBe('test@example.com');
});
```

#### FIX Phase
Implement methods to pass tests:

Follow dependency order: **Entity → Repository → Service → Router**

```typescript
// Example service implementation
async createUser(entity: UserEntity): Promise<UserEntity> {
  const validation = entity.validate();
  if (!validation.isValid) {
    throw new ValidationError(validation.errors.join(', '));
  }
  
  const existing = await this.repository.findByEmail(entity.email);
  if (existing) {
    throw new BusinessRuleError('Email already exists');
  }
  
  const record = entity.toRecord();
  const created = await this.repository.create(record);
  return UserEntity.fromRecord(created);
}
```

### Quality Checks

Before marking complete:
- [ ] All stubs replaced with implementations
- [ ] All tests passing
- [ ] Layer boundaries respected
- [ ] Proper error handling
- [ ] Types/interfaces defined
- [ ] No NotImplementedError remaining

## Step 5: Update Spec as Living Document

Main agent updates spec.md Implementation Notes section:

Read current spec.md.

Add to Implementation Notes:

```markdown
## Implementation Notes

- 2024-01-31 10:30: Entity created with validation
  - UserEntity.fromRequest, toRecord, validate methods
  - Tests: 5 passing

- 2024-01-31 11:15: Repository with findByEmail and create  
  - UserRepository with CRUD operations
  - Tests: 7 passing

- 2024-01-31 12:00: Service with validation and persistence
  - UserService createUser, authenticate methods
  - Tests: 4 passing

- 2024-01-31 13:30: Router endpoints POST /api/users and POST /api/auth
  - Auth handling added
  - Tests: HTTP integration tests passing
```

Write updated spec.md back to file.

**Living Document:** spec.md is continually updated as implementation progresses. It becomes the definitive record of what was built.

## Step 6: Mark Task Complete

@clerk verify all quality checks pass and close task.

Run tests:
```bash
npm test # or appropriate test command
```

Verify all passing, then close task:
```bash
bd close <task-id> --reason "Implemented <component> with layer boundary tests, spec.md updated"
```

## Step 7: Loop for Next Task

@clerk check for next ready task:

```bash
bd ready --label <feature> --json
```

If ready tasks found:
- **Loop back to Step 2** (display next task, get approval, implement)

If no ready tasks:
- **Proceed to Step 8**

If blocked tasks found:
- Output: "Blocked tasks detected. Check dependencies or re-prioritize."
- Show blocked task list
- Exit loop

## Step 8: Loop Complete

**Implementation Complete:**
```
Feature: <feature-name>
Total tasks completed: {{count}}
Remaining ready tasks: 0

spec.md updated with:
- Implementation Notes (living document)
- Current state of all layers
- Test coverage

Beads status:
- Epic: {{epic-id}}
- Tasks: {{completed}}/{{total}}
```

**Next steps:**

1. Review final implementation in spec.md
2. Run full test suite
3. Review feature: open spec.md and plan.json for summary
4. Update roadmap: `/product/update` if needed

## Example Workflow

```
$ /spec/work user-auth

Next ready task:
  Description: user-auth entity + validation
  Layer: entity
  Feature: user-auth
  Dependencies: [all resolved]

Ready to implement this task? [y/n/q]
> y

[Implementation proceeds...]

Task completed: entity layer
spec.md Implementation Notes updated

Next ready task:
  Description: user-auth repository
  Layer: repository  
  Feature: user-auth
  Dependencies: [entity]

Ready to implement this task? [y/n/q]
> y

... [continues until done or user quits]
```

## ✅ Feature Complete

Spec is a living document reflecting the actual implementation!