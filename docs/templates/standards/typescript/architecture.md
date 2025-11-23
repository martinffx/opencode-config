# TypeScript Architecture Standards

## Layered Architecture Patterns

### Pattern 1: Standard CRUD API (Fastify)

```
Client → Route → Service (Domain) → Repository → Database
                     ↓
                  Entity
```

### Pattern 2: External API Integration

```
Client → Route → Service (Domain) → Client → External API
                     ↓
                  Entity
```

### Pattern 3: Event-Driven (Consumer)

```
Event Source → Consumer → Service (Domain) → Repository → Database
                            ↓
                         Entity
```

### Pattern 4: Event-Driven (Producer)

```
Client → Route → Service (Domain) → Producer → Event Broker
                     ↓
                  Entity
```

## TypeScript-Specific Layer Implementation

### Route Layer (Fastify)

```typescript
import { FastifyInstance, FastifyRequest, FastifyReply } from "fastify";
import { UserService } from "./service";
import { UserEntity } from "./entities";
import { CreateUserRequest, UserResponse } from "./schemas";

export async function userRoutes(
  fastify: FastifyInstance,
  service: UserService
) {
  // POST /users
  fastify.post(
    "/users",
    {
      schema: {
        body: CreateUserRequest,
        response: { 201: UserResponse },
      },
    },
    async (request: FastifyRequest, reply: FastifyReply) => {
      try {
        const entity = UserEntity.fromRequest(
          request.body as CreateUserRequest
        );
        const result = await service.createUser(entity);
        return reply.status(201).send(result.toResponse());
      } catch (error) {
        if (error instanceof ValidationError) {
          return reply
            .status(422)
            .send({ error: "validation", message: error.message });
        }
        if (error instanceof BusinessRuleError) {
          return reply
            .status(400)
            .send({ error: "business_rule", message: error.message });
        }
        throw error;
      }
    }
  );

  // GET /users/:id
  fastify.get(
    "/users/:id",
    {
      schema: {
        params: { type: "object", properties: { id: { type: "number" } } },
        response: { 200: UserResponse },
      },
    },
    async (request: FastifyRequest, reply: FastifyReply) => {
      const { id } = request.params as { id: number };
      const result = await service.getUser(id);
      return reply.send(result.toResponse());
    }
  );
}
```

### Service Layer (Domain Logic)

```typescript
import { UserRepository } from "./repositories";
import { UserEntity } from "./entities";

export class UserService {
  constructor(private repository: UserRepository) {}

  async createUser(entity: UserEntity): Promise<UserEntity> {
    // Validate business rules
    const validation = entity.validate();
    if (!validation.isValid) {
      throw new ValidationError(validation.errors.join(", "));
    }

    // Check duplicates
    const existing = await this.repository.findByEmail(entity.email);
    if (existing) {
      throw new BusinessRuleError("Email already exists");
    }

    // Persist and return
    const record = entity.toRecord();
    const created = await this.repository.create(record);
    return UserEntity.fromRecord(created);
  }

  async getUser(userId: number): Promise<UserEntity> {
    const record = await this.repository.findById(userId);
    if (!record) {
      throw new NotFoundError("User not found");
    }
    return UserEntity.fromRecord(record);
  }
}
```

### Entity Layer (Domain Model)

```typescript
import { Type, Static } from "@sinclair/typebox";
import { CreateUserRequest, UserResponse } from "./schemas";

export class UserEntity {
  constructor(
    public id?: number,
    public email: string = "",
    public name: string = "",
    public createdAt?: Date
  ) {}

  static fromRequest(dto: CreateUserRequest): UserEntity {
    return new UserEntity(
      undefined,
      dto.email.toLowerCase().trim(),
      dto.name.trim()
    );
  }

  static fromRecord(record: Record<string, any>): UserEntity {
    return new UserEntity(
      record.id,
      record.email,
      record.name,
      record.createdAt ? new Date(record.createdAt) : undefined
    );
  }

  toRecord(): Record<string, any> {
    return {
      email: this.email,
      name: this.name,
      createdAt: this.createdAt || new Date(),
    };
  }

  toResponse(): UserResponse {
    return {
      id: this.id,
      email: this.email,
      name: this.name,
      createdAt: this.createdAt,
    };
  }

  validate(): ValidationResult {
    const errors: string[] = [];

    // Email validation
    if (!this.email) {
      errors.push("Email is required");
    } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(this.email)) {
      errors.push("Invalid email format");
    }

    // Name validation
    if (!this.name || this.name.trim().length < 2) {
      errors.push("Name must be at least 2 characters");
    }

    return {
      isValid: errors.length === 0,
      errors,
    };
  }
}

interface ValidationResult {
  isValid: boolean;
  errors: string[];
}
```

### Repository Layer (Drizzle)

```typescript
import { drizzle } from "drizzle-orm/postgres-js";
import postgres from "postgres";
import { users } from "../db/schema";
import { eq } from "drizzle-orm";
import { UserEntity } from "../entities";

export class UserRepository {
  constructor(private db: ReturnType<typeof drizzle>) {}

  async create(data: Record<string, any>): Promise<Record<string, any>> {
    const [user] = await this.db.insert(users).values(data).returning();
    return user;
  }

  async findById(id: number): Promise<Record<string, any> | null> {
    const [user] = await this.db.select().from(users).where(eq(users.id, id));
    return user || null;
  }

  async findByEmail(email: string): Promise<Record<string, any> | null> {
    const [user] = await this.db
      .select()
      .from(users)
      .where(eq(users.email, email.toLowerCase()));
    return user || null;
  }

  async update(
    id: number,
    data: Record<string, any>
  ): Promise<Record<string, any> | null> {
    const [user] = await this.db
      .update(users)
      .set(data)
      .where(eq(users.id, id))
      .returning();
    return user || null;
  }

  async delete(id: number): Promise<boolean> {
    const result = await this.db.delete(users).where(eq(users.id, id));
    return result.rowCount > 0;
  }
}
```

## Dependency Injection (Fastify)

### Application Setup

```typescript
import fastify from "fastify";
import postgres from "postgres";
import { drizzle } from "drizzle-orm/postgres-js";
import { UserService } from "./domains/users/services";
import { UserRepository } from "./domains/users/repositories";
import { userRoutes } from "./domains/users/routes";

const app = fastify({ logger: true });

// Database setup
const connectionString = process.env.DATABASE_URL!;
const client = postgres(connectionString);
const db = drizzle(client);

// Dependency injection
const userRepository = new UserRepository(db);
const userService = new UserService(userRepository);

// Register routes
app.register(
  async (fastify) => {
    await userRoutes(fastify, userService);
  },
  { prefix: "/api/v1" }
);

export default app;
```

## Database Integration (Drizzle + PostgreSQL)

### Schema Definition

```typescript
import { pgTable, serial, varchar, timestamp } from "drizzle-orm/pg-core";

export const users = pgTable("users", {
  id: serial("id").primaryKey(),
  email: varchar("email", { length: 255 }).notNull().unique(),
  name: varchar("name", { length: 255 }).notNull(),
  createdAt: timestamp("created_at").defaultNow(),
});

export type User = typeof users.$inferSelect;
export type NewUser = typeof users.$inferInsert;
```

### Database Connection

```typescript
import postgres from "postgres";
import { drizzle } from "drizzle-orm/postgres-js";
import * as schema from "./schema";

export class Database {
  private static instance: Database;
  public db: ReturnType<typeof drizzle>;
  public client: ReturnType<typeof postgres>;

  private constructor() {
    this.client = postgres(process.env.DATABASE_URL!);
    this.db = drizzle(this.client, { schema });
  }

  static getInstance(): Database {
    if (!Database.instance) {
      Database.instance = new Database();
    }
    return Database.instance;
  }

  async close() {
    await this.client.end();
  }
}
```

## Schema Validation (Typebox)

### Request/Response Schemas

```typescript
import { Type } from "@sinclair/typebox";

export const CreateUserRequest = Type.Object({
  email: Type.String({ format: "email" }),
  name: Type.String({ minLength: 2 }),
});

export const UserResponse = Type.Object({
  id: Type.Number(),
  email: Type.String({ format: "email" }),
  name: Type.String(),
  createdAt: Type.Optional(Type.String({ format: "date-time" })),
});

export const UpdateUserRequest = Type.Partial(CreateUserRequest);

export type CreateUserRequest = Static<typeof CreateUserRequest>;
export type UserResponse = Static<typeof UserResponse>;
export type UpdateUserRequest = Static<typeof UpdateUserRequest>;
```

## External API Integration with undici mock

### Client Layer with undici Mock

```typescript
import { Client } from "undici";
import { URL } from "url";

export class ExternalAPIClient {
  private client: Client;

  constructor(private baseUrl: string) {
    this.client = new Client(baseUrl);
  }

  async getUserData(userId: number): Promise<Record<string, any>> {
    const response = await this.client.request({
      method: "GET",
      path: `/users/${userId}`,
      headers: {
        "Content-Type": "application/json",
      },
    });

    if (response.statusCode !== 200) {
      throw new Error(`HTTP ${response.statusCode}: ${response.statusCode}`);
    }

    return response.body.json() as Promise<Record<string, any>>;
  }

  async close() {
    this.client.close();
  }
}

// Mock for testing
export class MockExternalAPIClient extends ExternalAPIClient {
  private mockData = new Map<number, Record<string, any>>();

  constructor() {
    super("http://mock");
  }

  setMockData(userId: number, data: Record<string, any>) {
    this.mockData.set(userId, data);
  }

  async getUserData(userId: number): Promise<Record<string, any>> {
    const data = this.mockData.get(userId);
    if (!data) {
      throw new Error(`Mock data not found for user ${userId}`);
    }
    return data;
  }
}
```

## Error Handling (TypeScript)

### Custom Error Classes

```typescript
export class AppError extends Error {
  constructor(
    public message: string,
    public statusCode: number = 500,
    public code: string = "internal_error"
  ) {
    super(message);
    this.name = "AppError";
  }
}

export class ValidationError extends AppError {
  constructor(message: string) {
    super(message, 422, "validation_error");
    this.name = "ValidationError";
  }
}

export class BusinessRuleError extends AppError {
  constructor(message: string) {
    super(message, 400, "business_rule_error");
    this.name = "BusinessRuleError";
  }
}

export class NotFoundError extends AppError {
  constructor(message: string) {
    super(message, 404, "not_found_error");
    this.name = "NotFoundError";
  }
}
```

### Error Handling Middleware

```typescript
import { FastifyError, FastifyRequest, FastifyReply } from "fastify";

export function errorHandler(
  error: FastifyError,
  request: FastifyRequest,
  reply: FastifyReply
) {
  if (error instanceof AppError) {
    return reply.status(error.statusCode).send({
      error: error.code,
      message: error.message,
    });
  }

  // Handle other errors
  request.log.error(error);
  return reply.status(500).send({
    error: "internal_error",
    message: "Internal server error",
  });
}
```

## Docker Test Database Setup

### Test Database Helper

```typescript
import { execSync } from "child_process";
import postgres from "postgres";
import { drizzle } from "drizzle-orm/postgres-js";
import { migrate } from "drizzle-orm/postgres-js/migrator";
import { users } from "../src/db/schema";

export class TestDatabase {
  private static instance: TestDatabase;
  public db: ReturnType<typeof drizzle>;
  public client: ReturnType<typeof postgres>;
  public containerId: string;

  private constructor() {
    // Start PostgreSQL container
    this.containerId = execSync(
      "docker run -d -e POSTGRES_DB=test_db -e POSTGRES_USER=test_user -e POSTGRES_PASSWORD=test_pass -p 5432 postgres:15"
    )
      .toString()
      .trim();

    // Wait for database to be ready
    execSync("sleep 10");

    // Get container port
    const port = execSync(`docker port ${this.containerId} 5432`)
      .toString()
      .split(":")[1]
      .trim();

    // Connect to test database
    const connectionString = `postgresql://test_user:test_pass@localhost:${port}/test_db`;
    this.client = postgres(connectionString);
    this.db = drizzle(this.client);

    // Run migrations
    migrate(this.db, { migrationsFolder: "drizzle" });
  }

  static async getInstance(): Promise<TestDatabase> {
    if (!TestDatabase.instance) {
      TestDatabase.instance = new TestDatabase();
    }
    return TestDatabase.instance;
  }

  async cleanup() {
    await this.db.delete(users);
  }

  async close() {
    await this.client.end();
    execSync(`docker stop ${this.containerId}`);
    execSync(`docker rm ${this.containerId}`);
  }
}
```

## Integration Testing with Fastify

### Integration Test Example

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

  it("should create user successfully", async () => {
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

  it("should get user by id", async () => {
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

  it("should return 404 for non-existent user", async () => {
    const response = await app.inject({
      method: "GET",
      url: "/api/v1/users/999",
    });

    expect(response.statusCode).toBe(404);
  });
});
```

---

**Version**: {{version}}
**Last Updated**: {{updated_date}}

