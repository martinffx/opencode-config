# TypeScript Coding Standards

## Stub-Driven TDD Approach

### 3-Step Process

1. **Create Stub** - Method signatures that throw custom `NotImplementedError`
2. **Write Test** - Test against stub expecting error, then write tests for actual behavior
3. **Implement** - Replace stub with working code to make tests pass

### Refined TDD Flow

1. **Stub** - Method throws `NotImplementedError`
2. **Red** - Write test, run failing test
3. **Green** - Implement method, run passing tests
4. **Refactor** - Clean up code

### Implementation Order

Always implement in dependency order (bottom-up):

```
Entity → Repository → Service → Route
```

## Stub Pattern (TypeScript)

### Custom NotImplementedError

```typescript
export class NotImplementedError extends Error {
  constructor(methodName: string) {
    super(`Not Implemented: ${methodName}`);
    this.name = "NotImplementedError";
  }
}
```

### Entity Stub

```typescript
import { CreateUserRequest, UserResponse } from "../schemas";

export class UserEntity {
  constructor(
    public id?: number,
    public email: string = "",
    public name: string = "",
    public createdAt?: Date
  ) {}

  static fromRequest(dto: CreateUserRequest): UserEntity {
    throw new NotImplementedError("UserEntity.fromRequest");
  }

  static fromRecord(record: Record<string, any>): UserEntity {
    throw new NotImplementedError("UserEntity.fromRecord");
  }

  toRecord(): Record<string, any> {
    throw new NotImplementedError("UserEntity.toRecord");
  }

  toResponse(): UserResponse {
    throw new NotImplementedError("UserEntity.toResponse");
  }

  validate(): ValidationResult {
    throw new NotImplementedError("UserEntity.validate");
  }
}

interface ValidationResult {
  isValid: boolean;
  errors: string[];
}
```

### Repository Stub

```typescript
export interface UserRepository {
  create(data: Record<string, any>): Promise<Record<string, any>>;
  findById(id: number): Promise<Record<string, any> | null>;
  findByEmail(email: string): Promise<Record<string, any> | null>;
  update(
    id: number,
    data: Record<string, any>
  ): Promise<Record<string, any> | null>;
  delete(id: number): Promise<boolean>;
}

export class DrizzleUserRepository implements UserRepository {
  constructor(private db: ReturnType<typeof drizzle>) {}

  async create(data: Record<string, any>): Promise<Record<string, any>> {
    throw new NotImplementedError("DrizzleUserRepository.create");
  }

  async findById(id: number): Promise<Record<string, any> | null> {
    throw new NotImplementedError("DrizzleUserRepository.findById");
  }

  async findByEmail(email: string): Promise<Record<string, any> | null> {
    throw new NotImplementedError("DrizzleUserRepository.findByEmail");
  }

  async update(
    id: number,
    data: Record<string, any>
  ): Promise<Record<string, any> | null> {
    throw new NotImplementedError("DrizzleUserRepository.update");
  }

  async delete(id: number): Promise<boolean> {
    throw new NotImplementedError("DrizzleUserRepository.delete");
  }
}
```

### Service Stub

```typescript
export class UserService {
  constructor(private repository: UserRepository) {}

  async createUser(entity: UserEntity): Promise<UserEntity> {
    throw new NotImplementedError("UserService.createUser");
  }

  async getUser(userId: number): Promise<UserEntity> {
    throw new NotImplementedError("UserService.getUser");
  }

  async updateUser(userId: number, entity: UserEntity): Promise<UserEntity> {
    throw new NotImplementedError("UserService.updateUser");
  }

  async deleteUser(userId: number): Promise<boolean> {
    throw new NotImplementedError("UserService.deleteUser");
  }
}
```

### Route Stub (Fastify)

```typescript
import { FastifyInstance, FastifyRequest, FastifyReply } from "fastify";
import { UserService } from "./service";
import { CreateUserRequest, UserResponse } from "./schemas";

export async function userRoutes(
  fastify: FastifyInstance,
  service: UserService
) {
  fastify.post(
    "/users",
    {
      schema: {
        body: CreateUserRequest,
        response: { 201: UserResponse },
      },
    },
    async (request: FastifyRequest, reply: FastifyReply) => {
      throw new NotImplementedError("createUser route");
    }
  );

  fastify.get(
    "/users/:id",
    {
      schema: {
        params: { type: "object", properties: { id: { type: "number" } } },
        response: { 200: UserResponse },
      },
    },
    async (request: FastifyRequest, reply: FastifyReply) => {
      throw new NotImplementedError("getUser route");
    }
  );

  fastify.put(
    "/users/:id",
    {
      schema: {
        params: { type: "object", properties: { id: { type: "number" } } },
        body: CreateUserRequest,
        response: { 200: UserResponse },
      },
    },
    async (request: FastifyRequest, reply: FastifyReply) => {
      throw new NotImplementedError("updateUser route");
    }
  );

  fastify.delete(
    "/users/:id",
    {
      schema: {
        params: { type: "object", properties: { id: { type: "number" } } },
      },
    },
    async (request: FastifyRequest, reply: FastifyReply) => {
      throw new NotImplementedError("deleteUser route");
    }
  );
}
```

## Testing Pattern (bun test + undici mock)

### Step 1 - Test the Stub

```typescript
import { UserEntity } from "../src/entities";
import { CreateUserRequest } from "../src/schemas";
import { NotImplementedError } from "../src/errors";

describe("UserEntity Stub Tests", () => {
  it("should throw NotImplementedError for fromRequest", () => {
    const request: CreateUserRequest = {
      email: "test@example.com",
      name: "Test User",
    };

    expect(() => UserEntity.fromRequest(request)).toThrow(NotImplementedError);
    expect(() => UserEntity.fromRequest(request)).toThrow(
      "Not Implemented: UserEntity.fromRequest"
    );
  });

  it("should throw NotImplementedError for validate", () => {
    const entity = new UserEntity(undefined, "test@example.com", "Test User");

    expect(() => entity.validate()).toThrow(NotImplementedError);
    expect(() => entity.validate()).toThrow(
      "Not Implemented: UserEntity.validate"
    );
  });
});
```

### Step 2 - Test Expected Behavior (Red)

```typescript
describe("UserEntity Behavior Tests", () => {
  it("should create entity from request successfully", () => {
    const request: CreateUserRequest = {
      email: "test@example.com",
      name: "Test User",
    };

    const entity = UserEntity.fromRequest(request);

    expect(entity.email).toBe("test@example.com");
    expect(entity.name).toBe("Test User");
    expect(entity.id).toBeUndefined();
  });

  it("should validate entity with invalid email", () => {
    const entity = new UserEntity(undefined, "invalid-email", "Test User");
    const result = entity.validate();

    expect(result.isValid).toBe(false);
    expect(result.errors).toContain("Invalid email format");
  });

  it("should validate entity with missing name", () => {
    const entity = new UserEntity(undefined, "test@example.com", "");
    const result = entity.validate();

    expect(result.isValid).toBe(false);
    expect(result.errors).toContain("Name must be at least 2 characters");
  });
});
```

### Step 3 - Implementation (Green)

```typescript
// After implementing the methods, tests should pass
```

### Step 4 - Refactor

```typescript
// Clean up code while maintaining test coverage
```

## TypeScript-Specific TDD Patterns

### Async Testing with bun test

```typescript
import { UserService } from "../src/services";
import { UserEntity } from "../src/entities";
import { UserRepository } from "../src/repositories";

// Mock repository for unit tests
class MockUserRepository implements UserRepository {
  private users: Record<string, any>[] = [];
  private nextId = 1;

  async create(data: Record<string, any>): Promise<Record<string, any>> {
    const user = { id: this.nextId++, ...data, createdAt: new Date() };
    this.users.push(user);
    return user;
  }

  async findById(id: number): Promise<Record<string, any> | null> {
    return this.users.find((user) => user.id === id) || null;
  }

  async findByEmail(email: string): Promise<Record<string, any> | null> {
    return this.users.find((user) => user.email === email) || null;
  }

  async update(
    id: number,
    data: Record<string, any>
  ): Promise<Record<string, any> | null> {
    const index = this.users.findIndex((user) => user.id === id);
    if (index === -1) return null;

    this.users[index] = { ...this.users[index], ...data };
    return this.users[index];
  }

  async delete(id: number): Promise<boolean> {
    const index = this.users.findIndex((user) => user.id === id);
    if (index === -1) return false;

    this.users.splice(index, 1);
    return true;
  }
}

describe("UserService Unit Tests", () => {
  let service: UserService;
  let mockRepository: MockUserRepository;

  beforeEach(() => {
    mockRepository = new MockUserRepository();
    service = new UserService(mockRepository);
  });

  it("should create user successfully", async () => {
    // Arrange
    const entity = new UserEntity(undefined, "test@example.com", "Test User");

    // Act
    const result = await service.createUser(entity);

    // Assert
    expect(result.email).toBe("test@example.com");
    expect(result.name).toBe("Test User");
    expect(result.id).toBeDefined();
  });

  it("should throw error for duplicate email", async () => {
    // Arrange
    const entity = new UserEntity(undefined, "test@example.com", "Test User");

    // Create first user
    await service.createUser(entity);

    // Try to create duplicate
    const duplicateEntity = new UserEntity(
      undefined,
      "test@example.com",
      "Another User"
    );

    // Act & Assert
    await expect(service.createUser(duplicateEntity)).rejects.toThrow(
      "Email already exists"
    );
  });

  it("should get user by id", async () => {
    // Arrange
    const entity = new UserEntity(undefined, "test@example.com", "Test User");
    const created = await service.createUser(entity);

    // Act
    const result = await service.getUser(created.id!);

    // Assert
    expect(result.email).toBe("test@example.com");
    expect(result.id).toBe(created.id);
  });

  it("should throw error for non-existent user", async () => {
    // Act & Assert
    await expect(service.getUser(999)).rejects.toThrow("User not found");
  });
});
```

### Unit Tests with Mocks (Core Layer)

```typescript
import { jest } from "@bun/test";

describe("Service Unit Tests with Jest Mocks", () => {
  let service: UserService;
  let mockRepository: jest.Mocked<UserRepository>;

  beforeEach(() => {
    mockRepository = {
      create: jest.fn(),
      findById: jest.fn(),
      findByEmail: jest.fn(),
      update: jest.fn(),
      delete: jest.fn(),
    } as any;

    service = new UserService(mockRepository);
  });

  it("should create user with mocked repository", async () => {
    // Arrange
    const entity = new UserEntity(undefined, "test@example.com", "Test User");
    const mockRecord = {
      id: 1,
      email: "test@example.com",
      name: "Test User",
      createdAt: new Date(),
    };

    mockRepository.findByEmail.mockResolvedValue(null);
    mockRepository.create.mockResolvedValue(mockRecord);

    // Act
    const result = await service.createUser(entity);

    // Assert
    expect(result.email).toBe("test@example.com");
    expect(mockRepository.findByEmail).toHaveBeenCalledWith("test@example.com");
    expect(mockRepository.create).toHaveBeenCalledWith(entity.toRecord());
  });
});
```

### Integration Tests with Docker (Edge Layer)

```typescript
import { buildApp } from "../src/app";
import { TestDatabase } from "./test-database";

describe("User Integration Tests", () => {
  let app: ReturnType<typeof buildApp>;
  let testDb: TestDatabase;

  beforeAll(async () => {
    testDb = await TestDatabase.getInstance();
    app = buildApp();
  });

  afterAll(async () => {
    await testDb.close();
  });

  beforeEach(async () => {
    await testDb.cleanup();
  });

  it("should create user via HTTP endpoint", async () => {
    // Integration test: HTTP → Service → Repository → Database
    const response = await app.inject({
      method: "POST",
      url: "/api/v1/users",
      payload: {
        email: "test@example.com",
        name: "Test User",
      },
    });

    expect(response.statusCode).toBe(201);
    const json = response.json();
    expect(json.email).toBe("test@example.com");
    expect(json.name).toBe("Test User");
    expect(json.id).toBeDefined();
  });

  it("should get user via HTTP endpoint", async () => {
    // Create user first
    const createResponse = await app.inject({
      method: "POST",
      url: "/api/v1/users",
      payload: {
        email: "test@example.com",
        name: "Test User",
      },
    });

    const createdUser = createResponse.json();

    // Get user
    const getResponse = await app.inject({
      method: "GET",
      url: `/api/v1/users/${createdUser.id}`,
    });

    expect(getResponse.statusCode).toBe(200);
    const json = getResponse.json();
    expect(json.id).toBe(createdUser.id);
    expect(json.email).toBe("test@example.com");
  });
});
```

### External API Testing with undici mock

```typescript
import { MockAgent, setGlobalDispatcher } from "undici";
import { ExternalAPIClient } from "../src/clients";

describe("External API Client Tests", () => {
  let mockAgent: MockAgent;
  let client: ExternalAPIClient;

  beforeEach(() => {
    mockAgent = new MockAgent();
    setGlobalDispatcher(mockAgent);
    client = new ExternalAPIClient("https://api.example.com");
  });

  afterEach(async () => {
    await mockAgent.close();
  });

  it("should fetch user data from external API", async () => {
    // Arrange
    const mockData = { id: 123, name: "Test User", email: "test@example.com" };

    const mockPool = mockAgent.get("https://api.example.com");
    mockPool
      .intercept({
        path: "/users/123",
        method: "GET",
      })
      .reply(200, JSON.stringify(mockData));

    // Act
    const result = await client.getUserData(123);

    // Assert
    expect(result).toEqual(mockData);
  });

  it("should handle API errors", async () => {
    // Arrange
    const mockPool = mockAgent.get("https://api.example.com");
    mockPool
      .intercept({
        path: "/users/999",
        method: "GET",
      })
      .reply(404, "Not Found");

    // Act & Assert
    await expect(client.getUserData(999)).rejects.toThrow("HTTP 404: 404");
  });
});
```

## Code Style Standards

### Directory Structure

```
src/
├── domains/{feature}/        # Feature modules
│   ├── entities/            # Domain entities
│   ├── services/            # Business logic
│   ├── repositories/        # Data access
│   ├── routes/              # API endpoints
│   ├── schemas.ts           # Typebox schemas
│   └── types.ts             # TypeScript types
├── shared/                   # Shared utilities
│   ├── database/
│   ├── errors/
│   └── types/
├── db/                       # Database schema and migrations
│   ├── schema.ts
│   └── migrations/
└── test/                     # Test utilities
    ├── helpers/
    └── fixtures/
```

### File Naming

- **Entities**: `user.ts` (class UserEntity)
- **Services**: `user.ts` (class UserService)
- **Repositories**: `user.ts` (interface UserRepository, class DrizzleUserRepository)
- **Routes**: `user.ts` (route functions)
- **Schemas**: `user.ts` (Typebox schemas)
- **Tests**: `user.test.ts` or `user.spec.ts`

### Naming Conventions

- **Classes/Interfaces**: PascalCase (`UserEntity`, `UserService`)
- **Functions/Methods**: camelCase (`createUser`, `findById`)
- **Variables**: camelCase (`userId`, `emailAddress`)
- **Constants**: SCREAMING_SNAKE_CASE (`MAX_RETRY_ATTEMPTS`, `API_BASE_URL`)
- **Files**: kebab-case or camelCase (`user.ts`, `userService.ts`)

### Type Annotations

```typescript
// Function with explicit types
export function createUser(email: string, name: string): UserEntity {
  return new UserEntity(undefined, email, name);
}

// Async function with Promise type
export async function findUserById(userId: number): Promise<UserEntity | null> {
  const record = await this.repository.findById(userId);
  return record ? UserEntity.fromRecord(record) : null;
}

// Generic function
export function validateEntity<T extends Entity>(entity: T): ValidationResult {
  return entity.validate();
}
```

### Import Order

```typescript
// 1. Node.js built-ins
import { URL } from "url";
import { execSync } from "child_process";

// 2. Third-party libraries
import fastify from "fastify";
import { Type, Static } from "@sinclair/typebox";
import { drizzle } from "drizzle-orm/postgres-js";
import { Client, MockAgent } from "undici";

// 3. Internal application
import { UserEntity } from "./entities";
import { UserRepository } from "./repositories";
import { ValidationError } from "../shared/errors";
```

## Development Workflow

### Running Tests

```bash
# Run all tests
bun test

# Run with coverage
bun test --coverage

# Run specific test file
bun test user.test.ts

# Run in watch mode
bun test --watch

# Run integration tests only
bun test integration/
```

### Code Quality

```bash
# Type checking
bun tsc --noEmit

# Linting
bun run lint

# Formatting
bun run format

# Security audit
bun audit
```

### Development Server

```bash
# Run development server
bun run dev

# Run with environment variables
DATABASE_URL=postgresql://user:pass@localhost/db bun run dev

# Build for production
bun run build

# Start production server
bun run start
```

## Async Patterns

### Async/Await Best Practices

```typescript
// Good: Use async for I/O operations
export async function createUser(
  this: UserService,
  entity: UserEntity
): Promise<UserEntity> {
  // Database call
  const existing = await this.repository.findByEmail(entity.email);

  // Business logic (sync)
  if (existing) {
    throw new BusinessRuleError("Email already exists");
  }

  // Database call
  const record = entity.toRecord();
  const created = await this.repository.create(record);

  return UserEntity.fromRecord(created);
}

// Bad: Mixing async and sync incorrectly
export function createUserBad(
  this: UserService,
  entity: UserEntity
): UserEntity {
  // This will not work - missing await
  const existing = this.repository.findByEmail(entity.email); // Returns Promise!
  if (existing) {
    throw new BusinessRuleError("Email already exists");
  }
  return entity;
}
```

### Error Handling in Async Context

```typescript
export class UserService {
  async createUser(entity: UserEntity): Promise<UserEntity> {
    try {
      // Validate
      entity.validate();

      // Check duplicates
      const existing = await this.repository.findByEmail(entity.email);
      if (existing) {
        throw new BusinessRuleError("Email already exists");
      }

      // Create
      const record = entity.toRecord();
      const created = await this.repository.create(record);

      return UserEntity.fromRecord(created);
    } catch (error) {
      if (error instanceof ValidationError) {
        throw error;
      }
      if (error instanceof BusinessRuleError) {
        throw error;
      }
      // Wrap unknown errors
      throw new AppError(`Failed to create user: ${error.message}`, 500);
    }
  }
}
```

## Testing Strategy Summary

### Unit Tests (Core Layer)

- **Service Tests**: Mock Repository/Client, test business logic
- **Entity Tests**: Test validation and transformation logic
- **Repository Tests**: Mock database, test data operations

### Integration Tests (Edge Layer)

- **HTTP Tests**: Fastify inject → Service → Repository → Database
- **External API Tests**: undici mock for external service calls
- **Database Tests**: Docker PostgreSQL containers

### Test Organization

```
tests/
├── unit/
│   ├── services/
│   ├── entities/
│   └── repositories/
├── integration/
│   ├── http/
│   ├── external-api/
│   └── database/
├── helpers/
│   ├── test-database.ts
│   └── mocks.ts
└── fixtures/
    └── users.json
```

### Mock Testing with undici

```typescript
import { MockAgent, setGlobalDispatcher } from "undici";

describe("Service with External API", () => {
  let mockAgent: MockAgent;

  beforeEach(() => {
    mockAgent = new MockAgent();
    setGlobalDispatcher(mockAgent);
  });

  it("should handle external API calls", async () => {
    const mockPool = mockAgent.get("https://api.example.com");
    mockPool
      .intercept({
        path: "/users/123",
        method: "GET",
      })
      .reply(200, JSON.stringify({ id: 123, name: "Test" }));

    // Test service that calls external API
  });
});
```

---

**Version**: {{version}}
**Last Updated**: {{updated_date}}

