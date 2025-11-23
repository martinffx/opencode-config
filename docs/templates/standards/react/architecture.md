# React Architecture Standards

## Component Architecture Patterns

### Pattern 1: Standard Feature Component
```
Page → Container → UI Components → Custom Hooks → API Layer
                     ↓
                  State
```

### Pattern 2: Data-Driven Component
```
Page → Query Hook → UI Components → State Management
                     ↓
                  Cache
```

### Pattern 3: Form Component
```
Page → Form Hook → Validation → UI Components → API Layer
                     ↓
                  State
```

### Pattern 4: Real-time Component
```
Page → WebSocket Hook → UI Components → State Management
                     ↓
                  Events
```

## React-Specific Layer Implementation

### Page Layer (Route Components)
```typescript
import { useQuery } from '@tanstack/react-query';
import { useParams } from 'react-router-dom';
import { UserContainer } from '../containers/UserContainer';
import { userService } from '../services/userService';

export function UserPage() {
  const { id } = useParams<{ id: string }>();
  
  const {
    data: user,
    isLoading,
    error,
    refetch
  } = useQuery({
    queryKey: ['user', id],
    queryFn: () => userService.getUser(Number(id)),
    enabled: !!id
  });

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;
  if (!user) return <div>User not found</div>;

  return (
    <div className="user-page">
      <UserContainer 
        user={user} 
        onRefresh={refetch}
      />
    </div>
  );
}
```

### Container Layer (Smart Components)
```typescript
import { useMutation, useQueryClient } from '@tanstack/react-query';
import { UserForm } from '../components/UserForm';
import { UserDisplay } from '../components/UserDisplay';
import { userService } from '../services/userService';
import { User } from '../types';
import { toast } from 'sonner';

interface UserContainerProps {
  user: User;
  onRefresh: () => void;
}

export function UserContainer({ user, onRefresh }: UserContainerProps) {
  const [isEditing, setIsEditing] = useState(false);
  const queryClient = useQueryClient();

  const updateUserMutation = useMutation({
    mutationFn: userService.updateUser,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['user', user.id] });
      setIsEditing(false);
      toast.success('User updated successfully');
      onRefresh();
    },
    onError: (error) => {
      toast.error(`Failed to update user: ${error.message}`);
    }
  });

  const handleUpdate = async (userData: Partial<User>) => {
    await updateUserMutation.mutateAsync({
      id: user.id,
      data: userData
    });
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
        <UserForm
          user={user}
          onSubmit={handleUpdate}
          onCancel={handleCancel}
          isLoading={updateUserMutation.isPending}
        />
      ) : (
        <UserDisplay
          user={user}
          onEdit={handleEdit}
        />
      )}
    </div>
  );
}
```

### UI Component Layer (Dumb Components)
```typescript
import { Button } from '@/components/ui/button';
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';
import { User } from '../types';

interface UserDisplayProps {
  user: User;
  onEdit: () => void;
}

export function UserDisplay({ user, onEdit }: UserDisplayProps) {
  return (
    <Card className="w-full max-w-md">
      <CardHeader>
        <CardTitle>User Details</CardTitle>
      </CardHeader>
      <CardContent className="space-y-4">
        <div>
          <label className="text-sm font-medium">Name</label>
          <p className="text-lg">{user.name}</p>
        </div>
        
        <div>
          <label className="text-sm font-medium">Email</label>
          <p className="text-lg">{user.email}</p>
        </div>
        
        <div>
          <label className="text-sm font-medium">Created</label>
          <p className="text-sm text-gray-500">
            {new Date(user.createdAt).toLocaleDateString()}
          </p>
        </div>
        
        <Button onClick={onEdit} className="w-full">
          Edit User
        </Button>
      </CardContent>
    </Card>
  );
}
```

### Form Component with Validation
```typescript
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Form, FormControl, FormField, FormItem, FormLabel, FormMessage } from '@/components/ui/form';
import { User } from '../types';

const userSchema = z.object({
  name: z.string().min(2, 'Name must be at least 2 characters'),
  email: z.string().email('Invalid email address')
});

type UserFormData = z.infer<typeof userSchema>;

interface UserFormProps {
  user: User;
  onSubmit: (data: UserFormData) => Promise<void>;
  onCancel: () => void;
  isLoading?: boolean;
}

export function UserForm({ user, onSubmit, onCancel, isLoading }: UserFormProps) {
  const form = useForm<UserFormData>({
    resolver: zodResolver(userSchema),
    defaultValues: {
      name: user.name,
      email: user.email
    }
  });

  const handleSubmit = async (data: UserFormData) => {
    await onSubmit(data);
  };

  return (
    <Form {...form}>
      <form onSubmit={form.handleSubmit(handleSubmit)} className="space-y-4">
        <FormField
          control={form.control}
          name="name"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Name</FormLabel>
              <FormControl>
                <Input placeholder="Enter name" {...field} />
              </FormControl>
              <FormMessage />
            </FormItem>
          )}
        />
        
        <FormField
          control={form.control}
          name="email"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Email</FormLabel>
              <FormControl>
                <Input type="email" placeholder="Enter email" {...field} />
              </FormControl>
              <FormMessage />
            </FormItem>
          )}
        />
        
        <div className="flex space-x-2">
          <Button type="submit" disabled={isLoading} className="flex-1">
            {isLoading ? 'Saving...' : 'Save'}
          </Button>
          <Button type="button" variant="outline" onClick={onCancel} className="flex-1">
            Cancel
          </Button>
        </div>
      </form>
    </Form>
  );
}
```

## Custom Hooks (Business Logic)

### Data Fetching Hook
```typescript
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { userService } from '../services/userService';
import { User, CreateUserRequest, UpdateUserRequest } from '../types';
import { toast } from 'sonner';

export function useUsers() {
  const queryClient = useQueryClient();

  // Get all users
  const usersQuery = useQuery({
    queryKey: ['users'],
    queryFn: userService.getUsers,
    staleTime: 5 * 60 * 1000, // 5 minutes
  });

  // Get single user
  const useUser = (id: number) => useQuery({
    queryKey: ['user', id],
    queryFn: () => userService.getUser(id),
    enabled: !!id,
  });

  // Create user mutation
  const createUserMutation = useMutation({
    mutationFn: userService.createUser,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['users'] });
      toast.success('User created successfully');
    },
    onError: (error) => {
      toast.error(`Failed to create user: ${error.message}`);
    }
  });

  // Update user mutation
  const updateUserMutation = useMutation({
    mutationFn: userService.updateUser,
    onSuccess: (_, variables) => {
      queryClient.invalidateQueries({ queryKey: ['users'] });
      queryClient.invalidateQueries({ queryKey: ['user', variables.id] });
      toast.success('User updated successfully');
    },
    onError: (error) => {
      toast.error(`Failed to update user: ${error.message}`);
    }
  });

  // Delete user mutation
  const deleteUserMutation = useMutation({
    mutationFn: userService.deleteUser,
    onSuccess: (_, userId) => {
      queryClient.invalidateQueries({ queryKey: ['users'] });
      queryClient.removeQueries({ queryKey: ['user', userId] });
      toast.success('User deleted successfully');
    },
    onError: (error) => {
      toast.error(`Failed to delete user: ${error.message}`);
    }
  });

  return {
    usersQuery,
    useUser,
    createUser: createUserMutation.mutateAsync,
    updateUser: updateUserMutation.mutateAsync,
    deleteUser: deleteUserMutation.mutateAsync,
    isCreating: createUserMutation.isPending,
    isUpdating: updateUserMutation.isPending,
    isDeleting: deleteUserMutation.isPending,
  };
}
```

### Form Hook
```typescript
import { useState } from 'react';
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';
import { User, CreateUserRequest, UpdateUserRequest } from '../types';

const userSchema = z.object({
  name: z.string().min(2, 'Name must be at least 2 characters'),
  email: z.string().email('Invalid email address')
});

type UserFormData = z.infer<typeof userSchema>;

export function useUserForm(user?: User) {
  const [isSubmitting, setIsSubmitting] = useState(false);

  const form = useForm<UserFormData>({
    resolver: zodResolver(userSchema),
    defaultValues: user ? {
      name: user.name,
      email: user.email
    } : {
      name: '',
      email: ''
    }
  });

  const reset = () => {
    form.reset(user ? {
      name: user.name,
      email: user.email
    } : {
      name: '',
      email: ''
    });
  };

  const prepareSubmitData = (data: UserFormData): CreateUserRequest | UpdateUserRequest => {
    if (user) {
      return {
        id: user.id,
        data
      };
    }
    return data;
  };

  return {
    form,
    isSubmitting,
    setIsSubmitting,
    reset,
    prepareSubmitData
  };
}
```

## State Management (Zustand)

### User Store
```typescript
import { create } from 'zustand';
import { devtools } from 'zustand/middleware';
import { User } from '../types';

interface UserState {
  users: User[];
  currentUser: User | null;
  isLoading: boolean;
  error: string | null;
  
  // Actions
  setUsers: (users: User[]) => void;
  setCurrentUser: (user: User | null) => void;
  addUser: (user: User) => void;
  updateUser: (user: User) => void;
  removeUser: (userId: number) => void;
  setLoading: (loading: boolean) => void;
  setError: (error: string | null) => void;
  clearError: () => void;
}

export const useUserStore = create<UserState>()(
  devtools(
    (set, get) => ({
      // Initial state
      users: [],
      currentUser: null,
      isLoading: false,
      error: null,

      // Actions
      setUsers: (users) => set({ users }),
      
      setCurrentUser: (currentUser) => set({ currentUser }),
      
      addUser: (user) => set((state) => ({
        users: [...state.users, user]
      })),
      
      updateUser: (updatedUser) => set((state) => ({
        users: state.users.map(user => 
          user.id === updatedUser.id ? updatedUser : user
        ),
        currentUser: state.currentUser?.id === updatedUser.id 
          ? updatedUser 
          : state.currentUser
      })),
      
      removeUser: (userId) => set((state) => ({
        users: state.users.filter(user => user.id !== userId),
        currentUser: state.currentUser?.id === userId 
          ? null 
          : state.currentUser
      })),
      
      setLoading: (isLoading) => set({ isLoading }),
      
      setError: (error) => set({ error }),
      
      clearError: () => set({ error: null })
    }),
    {
      name: 'user-store'
    }
  )
);
```

## API Integration (openapi-react-query)

### Generated API Client
```typescript
// This would be generated from your OpenAPI spec
import { createClient } from '@openapi-react-query/client';
import type { paths } from './api';

export const apiClient = createClient<paths>({
  baseUrl: process.env.NEXT_PUBLIC_API_URL || 'http://localhost:3000/api/v1',
  headers: {
    'Content-Type': 'application/json'
  }
});

// Service functions
export const userService = {
  getUsers: () => apiClient.GET('/users'),
  getUser: (id: number) => apiClient.GET('/users/{id}', { params: { path: { id } } }),
  createUser: (data: CreateUserRequest) => apiClient.POST('/users', { body: data }),
  updateUser: (data: UpdateUserRequest) => apiClient.PUT('/users/{id}', { 
    params: { path: { id: data.id }, body: data.data } 
  }),
  deleteUser: (id: number) => apiClient.DELETE('/users/{id}', { params: { path: { id } } })
};
```

## Error Handling

### Error Boundary
```typescript
import { Component, ErrorInfo, ReactNode } from 'react';
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';
import { Button } from '@/components/ui/button';

interface Props {
  children: ReactNode;
}

interface State {
  hasError: boolean;
  error?: Error;
}

export class ErrorBoundary extends Component<Props, State> {
  public state: State = {
    hasError: false
  };

  public static getDerivedStateFromError(error: Error): State {
    return { hasError: true, error };
  }

  public componentDidCatch(error: Error, errorInfo: ErrorInfo) {
    console.error('Uncaught error:', error, errorInfo);
  }

  private handleRetry = () => {
    this.setState({ hasError: false, error: undefined });
  };

  public render() {
    if (this.state.hasError) {
      return (
        <Card className="w-full max-w-md mx-auto mt-8">
          <CardHeader>
            <CardTitle className="text-red-600">Something went wrong</CardTitle>
          </CardHeader>
          <CardContent className="space-y-4">
            <p className="text-gray-600">
              {this.state.error?.message || 'An unexpected error occurred'}
            </p>
            <Button onClick={this.handleRetry} className="w-full">
              Try Again
            </Button>
          </CardContent>
        </Card>
      );
    }

    return this.props.children;
  }
}
```

## Testing with MSW + Vitest

### Component Test Example
```typescript
import { render, screen, waitFor } from '@testing-library/react';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { UserPage } from '../pages/UserPage';
import { server } from '../mocks/server';
import { rest } from 'msw';
import { User } from '../types';

// Test utilities
const createQueryClient = () => new QueryClient({
  defaultOptions: {
    queries: { retry: false },
    mutations: { retry: false }
  }
});

const renderWithQueryClient = (component: React.ReactElement) => {
  const queryClient = createQueryClient();
  return render(
    <QueryClientProvider client={queryClient}>
      {component}
    </QueryClientProvider>
  );
};

describe('UserPage', () => {
  beforeAll(() => server.listen());
  afterEach(() => server.resetHandlers());
  afterAll(() => server.close());

  it('should display user data', async () => {
    // Arrange
    const mockUser: User = {
      id: 1,
      name: 'Test User',
      email: 'test@example.com',
      createdAt: '2023-01-01T00:00:00Z'
    };

    server.use(
      rest.get('/api/v1/users/1', (req, res, ctx) => {
        return res(ctx.json(mockUser));
      })
    );

    // Act
    renderWithQueryClient(<UserPage />);

    // Assert
    expect(screen.getByText('Loading...')).toBeInTheDocument();
    
    await waitFor(() => {
      expect(screen.getByText('Test User')).toBeInTheDocument();
      expect(screen.getByText('test@example.com')).toBeInTheDocument();
    });
  });

  it('should display error message', async () => {
    // Arrange
    server.use(
      rest.get('/api/v1/users/1', (req, res, ctx) => {
        return res(ctx.status(404), ctx.json({ error: 'User not found' }));
      })
    );

    // Act
    renderWithQueryClient(<UserPage />);

    // Assert
    await waitFor(() => {
      expect(screen.getByText('User not found')).toBeInTheDocument();
    });
  });
});
```

### MSW Setup
```typescript
import { setupServer } from 'msw/node';
import { rest } from 'msw';
import { User } from '../types';

// Mock data
export const mockUsers: User[] = [
  {
    id: 1,
    name: 'Test User 1',
    email: 'test1@example.com',
    createdAt: '2023-01-01T00:00:00Z'
  },
  {
    id: 2,
    name: 'Test User 2',
    email: 'test2@example.com',
    createdAt: '2023-01-02T00:00:00Z'
  }
];

// API handlers
export const handlers = [
  // GET /users
  rest.get('/api/v1/users', (req, res, ctx) => {
    return res(ctx.json(mockUsers));
  }),

  // GET /users/:id
  rest.get('/api/v1/users/:id', (req, res, ctx) => {
    const { id } = req.params;
    const user = mockUsers.find(u => u.id === Number(id));
    
    if (!user) {
      return res(ctx.status(404), ctx.json({ error: 'User not found' }));
    }
    
    return res(ctx.json(user));
  }),

  // POST /users
  rest.post('/api/v1/users', async (req, res, ctx) => {
    const userData = await req.json();
    const newUser: User = {
      id: Math.max(...mockUsers.map(u => u.id)) + 1,
      ...userData,
      createdAt: new Date().toISOString()
    };
    
    mockUsers.push(newUser);
    return res(ctx.status(201), ctx.json(newUser));
  }),

  // PUT /users/:id
  rest.put('/api/v1/users/:id', async (req, res, ctx) => {
    const { id } = req.params;
    const updateData = await req.json();
    const userIndex = mockUsers.findIndex(u => u.id === Number(id));
    
    if (userIndex === -1) {
      return res(ctx.status(404), ctx.json({ error: 'User not found' }));
    }
    
    mockUsers[userIndex] = { ...mockUsers[userIndex], ...updateData };
    return res(ctx.json(mockUsers[userIndex]));
  }),

  // DELETE /users/:id
  rest.delete('/api/v1/users/:id', (req, res, ctx) => {
    const { id } = req.params;
    const userIndex = mockUsers.findIndex(u => u.id === Number(id));
    
    if (userIndex === -1) {
      return res(ctx.status(404), ctx.json({ error: 'User not found' }));
    }
    
    mockUsers.splice(userIndex, 1);
    return res(ctx.status(204));
  })
];

// Server setup
export const server = setupServer(...handlers);
```

---

**Version**: {{version}}
**Last Updated**: {{updated_date}}