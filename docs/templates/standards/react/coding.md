React Coding Standards

## Stub-Driven TDD Approach

### 3-Step Process

1. **Create Stub** - Component/function signatures that throw custom errors or return null
2. **Write Test** - Test against stub expecting error/null, then write tests for actual behavior
3. **Implement** - Replace stub with working code to make tests pass

### Refined TDD Flow

1. **Stub** - Component throws `NotImplementedError` or returns placeholder
2. **Red** - Write test, run failing test
3. **Green** - Implement component, run passing tests
4. **Refactor** - Clean up code

### Implementation Order

Always implement in dependency order (bottom-up):

```
UI Components → Custom Hooks → Containers → Pages
```

## Stub Pattern (React)

### Custom NotImplementedError

```typescript
export class NotImplementedError extends Error {
  constructor(componentName: string, methodName: string) {
    super(`Not Implemented: ${componentName}.${methodName}`);
    this.name = "NotImplementedError";
  }
}
```

### UI Component Stub

```typescript
import { Button } from "@/components/ui/button";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { User } from "../types";

interface UserDisplayProps {
  user: User;
  onEdit: () => void;
}

export function UserDisplay({ user, onEdit }: UserDisplayProps) {
  throw new NotImplementedError("UserDisplay", "render");

  // Stub implementation (will be replaced)
  return (
    <Card>
      <CardHeader>
        <CardTitle>User Details</CardTitle>
      </CardHeader>
      <CardContent>
        <div>User: {user.name}</div>
        <Button onClick={onEdit}>Edit</Button>
      </CardContent>
    </Card>
  );
}
```

### Custom Hook Stub

```typescript
import { useState } from "react";
import { User, CreateUserRequest, UpdateUserRequest } from "../types";

export function useUsers() {
  throw new NotImplementedError("useUsers", "hook implementation");

  // Stub implementation (will be replaced)
  const [users, setUsers] = useState<User[]>([]);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const createUser = async (data: CreateUserRequest): Promise<User> => {
    throw new NotImplementedError("useUsers", "createUser");
  };

  const updateUser = async (data: UpdateUserRequest): Promise<User> => {
    throw new NotImplementedError("useUsers", "updateUser");
  };

  const deleteUser = async (id: number): Promise<void> => {
    throw new NotImplementedError("useUsers", "deleteUser");
  };

  return {
    users,
    isLoading,
    error,
    createUser,
    updateUser,
    deleteUser,
  };
}
```

### Container Component Stub

```typescript
import { useState } from "react";
import { UserForm } from "../components/UserForm";
import { UserDisplay } from "../components/UserDisplay";
import { User } from "../types";

interface UserContainerProps {
  user: User;
  onRefresh: () => void;
}

export function UserContainer({ user, onRefresh }: UserContainerProps) {
  throw new NotImplementedError("UserContainer", "render");

  // Stub implementation (will be replaced)
  const [isEditing, setIsEditing] = useState(false);

  const handleUpdate = async (userData: Partial<User>): Promise<void> => {
    throw new NotImplementedError("UserContainer", "handleUpdate");
  };

  const handleEdit = () => {
    setIsEditing(true);
  };

  const handleCancel = () => {
    setIsEditing(false);
  };

  return (
    <div className="user-container">
      {isEditing ? (
        <UserForm user={user} onSubmit={handleUpdate} onCancel={handleCancel} />
      ) : (
        <UserDisplay user={user} onEdit={handleEdit} />
      )}
    </div>
  );
}
```

### Page Component Stub

```typescript
import { useParams } from "react-router-dom";
import { UserContainer } from "../containers/UserContainer";
import { User } from "../types";

export function UserPage() {
  throw new NotImplementedError("UserPage", "render");

  // Stub implementation (will be replaced)
  const { id } = useParams<{ id: string }>();

  // Mock data for stub
  const mockUser: User = {
    id: Number(id),
    name: "Mock User",
    email: "mock@example.com",
    createdAt: "2023-01-01T00:00:00Z",
  };

  const handleRefresh = () => {
    throw new NotImplementedError("UserPage", "handleRefresh");
  };

  return (
    <div className="user-page">
      <UserContainer user={mockUser} onRefresh={handleRefresh} />
    </div>
  );
}
```

## Testing Pattern (Vitest + MSW + React Testing Library)

### Step 1 - Test the Stub

```typescript
import { render, screen } from "@testing-library/react";
import { UserDisplay } from "../src/components/UserDisplay";
import { User } from "../src/types";
import { NotImplementedError } from "../src/errors";

describe("UserDisplay Stub Tests", () => {
  const mockUser: User = {
    id: 1,
    name: "Test User",
    email: "test@example.com",
    createdAt: "2023-01-01T00:00:00Z",
  };

  it("should throw NotImplementedError when rendering stub", () => {
    expect(() => {
      render(<UserDisplay user={mockUser} onEdit={() => {}} />);
    }).toThrow(NotImplementedError);
  });
});
```

### Step 2 - Test Expected Behavior (Red)

```typescript
describe("UserDisplay Behavior Tests", () => {
  const mockUser: User = {
    id: 1,
    name: "Test User",
    email: "test@example.com",
    createdAt: "2023-01-01T00:00:00Z",
  };

  it("should display user information", () => {
    render(<UserDisplay user={mockUser} onEdit={() => {}} />);

    expect(screen.getByText("Test User")).toBeInTheDocument();
    expect(screen.getByText("test@example.com")).toBeInTheDocument();
    expect(screen.getByText("Edit")).toBeInTheDocument();
  });

  it("should call onEdit when edit button is clicked", async () => {
    const mockOnEdit = vi.fn();

    render(<UserDisplay user={mockUser} onEdit={mockOnEdit} />);

    const editButton = screen.getByText("Edit");
    await userEvent.click(editButton);

    expect(mockOnEdit).toHaveBeenCalledTimes(1);
  });

  it("should format creation date correctly", () => {
    render(<UserDisplay user={mockUser} onEdit={() => {}} />);

    expect(screen.getByText("1/1/2023")).toBeInTheDocument();
  });
});
```

### Step 3 - Implementation (Green)

```typescript
// After implementing the component, tests should pass
```

### Step 4 - Refactor

```typescript
// Clean up code while maintaining test coverage
```

## React-Specific TDD Patterns

### Component Testing with React Testing Library

```typescript
import { render, screen, waitFor, fireEvent } from "@testing-library/react";
import { userEvent } from "@testing-library/user-event";
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";
import { UserForm } from "../src/components/UserForm";
import { User } from "../src/types";
import { server } from "../mocks/server";

const createQueryClient = () =>
  new QueryClient({
    defaultOptions: {
      queries: { retry: false },
      mutations: { retry: false },
    },
  });

const renderWithQueryClient = (component: React.ReactElement) => {
  const queryClient = createQueryClient();
  return render(
    <QueryClientProvider client={queryClient}>{component}</QueryClientProvider>
  );
};

describe("UserForm Component Tests", () => {
  const mockUser: User = {
    id: 1,
    name: "Test User",
    email: "test@example.com",
    createdAt: "2023-01-01T00:00:00Z",
  };

  it("should render form with user data", () => {
    renderWithQueryClient(
      <UserForm user={mockUser} onSubmit={vi.fn()} onCancel={vi.fn()} />
    );

    expect(screen.getByDisplayValue("Test User")).toBeInTheDocument();
    expect(screen.getByDisplayValue("test@example.com")).toBeInTheDocument();
    expect(screen.getByText("Save")).toBeInTheDocument();
    expect(screen.getByText("Cancel")).toBeInTheDocument();
  });

  it("should validate form fields", async () => {
    const mockOnSubmit = vi.fn();

    renderWithQueryClient(
      <UserForm user={mockUser} onSubmit={mockOnSubmit} onCancel={vi.fn()} />
    );

    // Clear name field
    const nameInput = screen.getByLabelText("Name");
    await userEvent.clear(nameInput);
    await userEvent.type(nameInput, "A"); // Too short

    // Submit form
    const saveButton = screen.getByText("Save");
    await userEvent.click(saveButton);

    // Should show validation error
    await waitFor(() => {
      expect(
        screen.getByText("Name must be at least 2 characters")
      ).toBeInTheDocument();
    });

    // Should not call onSubmit
    expect(mockOnSubmit).not.toHaveBeenCalled();
  });

  it("should call onSubmit with form data", async () => {
    const mockOnSubmit = vi.fn();

    renderWithQueryClient(
      <UserForm user={mockUser} onSubmit={mockOnSubmit} onCancel={vi.fn()} />
    );

    // Update form
    const nameInput = screen.getByLabelText("Name");
    await userEvent.clear(nameInput);
    await userEvent.type(nameInput, "Updated Name");

    // Submit form
    const saveButton = screen.getByText("Save");
    await userEvent.click(saveButton);

    // Should call onSubmit with updated data
    await waitFor(() => {
      expect(mockOnSubmit).toHaveBeenCalledWith({
        name: "Updated Name",
        email: "test@example.com",
      });
    });
  });
});
```

### Custom Hook Testing

```typescript
import { renderHook, waitFor } from "@testing-library/react";
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";
import { useUsers } from "../src/hooks/useUsers";
import { server } from "../mocks/server";
import { rest } from "msw";

const wrapper = ({ children }: { children: React.ReactNode }) => {
  const queryClient = new QueryClient({
    defaultOptions: { queries: { retry: false } },
  });
  return (
    <QueryClientProvider client={queryClient}>{children}</QueryClientProvider>
  );
};

describe("useUsers Hook Tests", () => {
  beforeEach(() => {
    server.listen();
  });

  afterEach(() => {
    server.resetHandlers();
  });

  afterAll(() => {
    server.close();
  });

  it("should fetch users successfully", async () => {
    const { result } = renderHook(() => useUsers(), { wrapper });

    expect(result.current.isLoading).toBe(true);

    await waitFor(() => {
      expect(result.current.isLoading).toBe(false);
      expect(result.current.users).toHaveLength(2);
      expect(result.current.error).toBeNull();
    });
  });

  it("should create user successfully", async () => {
    const { result } = renderHook(() => useUsers(), { wrapper });

    await waitFor(() => {
      expect(result.current.isLoading).toBe(false);
    });

    const newUser = {
      name: "New User",
      email: "new@example.com",
    };

    await result.current.createUser(newUser);

    await waitFor(() => {
      expect(result.current.users).toHaveLength(3);
      expect(
        result.current.users.some((u) => u.email === "new@example.com")
      ).toBe(true);
    });
  });

  it("should handle API errors", async () => {
    server.use(
      rest.get("/api/v1/users", (req, res, ctx) => {
        return res(ctx.status(500), ctx.json({ error: "Server error" }));
      })
    );

    const { result } = renderHook(() => useUsers(), { wrapper });

    await waitFor(() => {
      expect(result.current.isLoading).toBe(false);
      expect(result.current.error).toBeTruthy();
      expect(result.current.users).toHaveLength(0);
    });
  });
});
```

### Integration Testing with MSW

```typescript
import { render, screen, waitFor } from "@testing-library/react";
import { MemoryRouter, Routes, Route } from "react-router-dom";
import { UserPage } from "../src/pages/UserPage";
import { server } from "../mocks/server";
import { rest } from "msw";
import { User } from "../src/types";

describe("UserPage Integration Tests", () => {
  const mockUser: User = {
    id: 1,
    name: "Test User",
    email: "test@example.com",
    createdAt: "2023-01-01T00:00:00Z",
  };

  beforeEach(() => {
    server.listen();
  });

  afterEach(() => {
    server.resetHandlers();
  });

  afterAll(() => {
    server.close();
  });

  it("should display user page with data", async () => {
    server.use(
      rest.get("/api/v1/users/1", (req, res, ctx) => {
        return res(ctx.json(mockUser));
      })
    );

    render(
      <MemoryRouter initialEntries={["/users/1"]}>
        <Routes>
          <Route path="/users/:id" element={<UserPage />} />
        </Routes>
      </MemoryRouter>
    );

    expect(screen.getByText("Loading...")).toBeInTheDocument();

    await waitFor(() => {
      expect(screen.getByText("Test User")).toBeInTheDocument();
      expect(screen.getByText("test@example.com")).toBeInTheDocument();
    });
  });

  it("should handle user not found", async () => {
    server.use(
      rest.get("/api/v1/users/999", (req, res, ctx) => {
        return res(ctx.status(404), ctx.json({ error: "User not found" }));
      })
    );

    render(
      <MemoryRouter initialEntries={["/users/999"]}>
        <Routes>
          <Route path="/users/:id" element={<UserPage />} />
        </Routes>
      </MemoryRouter>
    );

    await waitFor(() => {
      expect(screen.getByText("User not found")).toBeInTheDocument();
    });
  });

  it("should handle API errors", async () => {
    server.use(
      rest.get("/api/v1/users/1", (req, res, ctx) => {
        return res(ctx.status(500), ctx.json({ error: "Server error" }));
      })
    );

    render(
      <MemoryRouter initialEntries={["/users/1"]}>
        <Routes>
          <Route path="/users/:id" element={<UserPage />} />
        </Routes>
      </MemoryRouter>
    );

    await waitFor(() => {
      expect(screen.getByText(/Error:/)).toBeInTheDocument();
    });
  });
});
```

## Code Style Standards

### Directory Structure

```
src/
├── components/              # Reusable UI components
│   ├── ui/                 # shadcn/ui components
│   └── feature/            # Feature-specific components
├── containers/              # Smart components
├── pages/                   # Route components
├── hooks/                   # Custom hooks
├── services/                # API services
├── stores/                  # Zustand stores
├── types/                   # TypeScript types
├── utils/                   # Utility functions
└── test/                    # Test utilities
    ├── mocks/
    ├── fixtures/
    └── helpers/
```

### File Naming

- **Components**: `UserDisplay.tsx`, `UserForm.tsx` (PascalCase)
- **Hooks**: `useUsers.ts`, `useUserForm.ts` (camelCase with use prefix)
- **Pages**: `UserPage.tsx` (PascalCase with Page suffix)
- **Containers**: `UserContainer.tsx` (PascalCase with Container suffix)
- **Services**: `userService.ts` (camelCase)
- **Types**: `user.ts` (camelCase)
- **Tests**: `UserDisplay.test.tsx`, `useUsers.test.ts` (camelCase with test suffix)

### Naming Conventions

- **Components**: PascalCase (`UserDisplay`, `UserForm`)
- **Hooks**: camelCase with `use` prefix (`useUsers`, `useUserForm`)
- **Functions**: camelCase (`handleUpdate`, `formatDate`)
- **Variables**: camelCase (`userName`, `isLoading`)
- **Constants**: SCREAMING_SNAKE_CASE (`API_BASE_URL`, `MAX_RETRY_ATTEMPTS`)
- **Props**: camelCase (`onEdit`, `isLoading`)

### TypeScript Patterns

```typescript
// Component props interface
interface UserDisplayProps {
  user: User;
  onEdit: () => void;
  className?: string;
}

// Generic component
interface ListProps<T> {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
  emptyMessage?: string;
}

export function List<T>({ items, renderItem, emptyMessage }: ListProps<T>) {
  // Implementation
}

// Hook return type
interface UseUsersReturn {
  users: User[];
  isLoading: boolean;
  error: string | null;
  createUser: (data: CreateUserRequest) => Promise<void>;
  updateUser: (data: UpdateUserRequest) => Promise<void>;
  deleteUser: (id: number) => Promise<void>;
}

export function useUsers(): UseUsersReturn {
  // Implementation
}
```

### Import Order

```typescript
// 1. React
import { useState, useEffect } from "react";
import { type NextPage } from "next";

// 2. Next.js/React Router
import { useRouter } from "next/router";
import { useParams } from "react-router-dom";

// 3. Third-party libraries
import { useQuery, useMutation } from "@tanstack/react-query";
import { zodResolver } from "@hookform/resolvers/zod";
import { Button } from "@/components/ui/button";
import { toast } from "sonner";

// 4. Internal application
import { UserContainer } from "../containers/UserContainer";
import { useUsers } from "../hooks/useUsers";
import { User } from "../types";
import { formatDate } from "../utils/date";
```

## Development Workflow

### Running Tests

```bash
# Run all tests
bun test

# Run with coverage
bun test --coverage

# Run specific test file
bun test UserDisplay.test.tsx

# Run in watch mode
bun test --watch

# Run integration tests only
bun test integration/

# Run component tests only
bun test components/
```

### Code Quality

```bash
# Type checking
bun tsc --noEmit

# Linting
bun run lint

# Formatting
bun run format

# Build
bun run build
```

### Development Server

```bash
# Run development server
bun run dev

# Run with environment variables
VITE_API_URL=http://localhost:3000 bun run dev

# Build for production
bun run build

# Preview production build
bun run preview
```

## React Patterns

### Custom Hook Best Practices

```typescript
// Good: Custom hook with clear responsibilities
export function useUserForm(user?: User) {
  const [isSubmitting, setIsSubmitting] = useState(false);

  const form = useForm<UserFormData>({
    resolver: zodResolver(userSchema),
    defaultValues: user
      ? {
          name: user.name,
          email: user.email,
        }
      : {
          name: "",
          email: "",
        },
  });

  const handleSubmit = async (data: UserFormData) => {
    setIsSubmitting(true);
    try {
      await onSubmit(data);
    } finally {
      setIsSubmitting(false);
    }
  };

  return {
    form,
    isSubmitting,
    handleSubmit,
  };
}

// Bad: Mixing concerns in hook
export function useUserFormBad() {
  // Don't mix UI logic with data logic
  const [showModal, setShowModal] = useState(false);
  const [users, setUsers] = useState<User[]>([]);

  // This should be in separate hooks
  return { showModal, users, setShowModal };
}
```

### Component Patterns

```typescript
// Good: Presentational component with clear props
export function UserDisplay({ user, onEdit, className }: UserDisplayProps) {
  return (
    <Card className={className}>
      <CardContent>
        <h2>{user.name}</h2>
        <p>{user.email}</p>
        <Button onClick={onEdit}>Edit</Button>
      </CardContent>
    </Card>
  );
}

// Good: Container component with business logic
export function UserContainer({ userId }: UserContainerProps) {
  const { data: user, isLoading, error } = useUser(userId);
  const updateUser = useUpdateUser();

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;
  if (!user) return <div>User not found</div>;

  const handleEdit = async (userData: Partial<User>) => {
    await updateUser.mutateAsync({ id: user.id, data: userData });
  };

  return <UserDisplay user={user} onEdit={handleEdit} />;
}
```

## Testing Strategy Summary

### Unit Tests (Core Layer)

- **Component Tests**: Test component rendering and interactions
- **Hook Tests**: Test custom hook logic and state management
- **Utility Tests**: Test pure functions and utilities

### Integration Tests (Edge Layer)

- **Page Tests**: Test full page rendering with routing
- **API Tests**: Test component integration with API using MSW
- **Flow Tests**: Test complete user flows across components

### Test Organization

```
tests/
├── unit/
│   ├── components/
│   ├── hooks/
│   └── utils/
├── integration/
│   ├── pages/
│   └── flows/
├── mocks/
│   ├── handlers.ts
│   └── data.ts
└── helpers/
    ├── render.tsx
    └── test-utils.ts
```

### MSW Mock Patterns

```typescript
// Request/response mocking
export const handlers = [
  rest.get("/api/v1/users", (req, res, ctx) => {
    return res(ctx.json(mockUsers));
  }),

  rest.post("/api/v1/users", async (req, res, ctx) => {
    const userData = await req.json();
    return res(ctx.status(201), ctx.json({ ...userData, id: Date.now() }));
  }),

  // Error scenarios
  rest.get("/api/v1/users/error", (req, res, ctx) => {
    return res(ctx.status(500), ctx.json({ error: "Server error" }));
  }),
];
```

---

**Version**: {{version}}
**Last Updated**: {{updated_date}}

