# AI Agent Rules & Guidelines

> **📌 Single Source of Truth**: This document is the master rulebook for all AI code generation. Multiple projects and IDEs reference this file through their `.cursorrules` configuration. When updating rules, coordinate with your team as changes affect all projects using this document.

This document contains specific rules and guidelines for different AI agents and use cases. Use these when you need to generate code for specific scenarios or when invoking specialized agents.

**How to Use**:
- Projects reference this file through their `.cursorrules` configuration
- When generating code, Cursor reads `.cursorrules` which instructs it to follow rules from this document
- Each project can add project-specific overrides in their `.cursorrules` file

## Table of Contents

1. [Design Principles](#design-principles)
2. [API Development Agent](#api-development-agent)
3. [Frontend Component Agent](#frontend-component-agent)
4. [Database Schema Agent](#database-schema-agent)
5. [Testing Agent](#testing-agent)
6. [Security Agent](#security-agent)
7. [Performance Optimization Agent](#performance-optimization-agent)
8. [Documentation Agent](#documentation-agent)
9. [Refactoring Agent](#refactoring-agent)
10. [Migration Agent](#migration-agent)
11. [Code Review Agent](#code-review-agent)
12. [Commit Message Agent](#commit-message-agent)

---

## Design Principles

### Core Principles

These principles should guide all code generation and should be applied consistently across all agents.

#### 1. Single Responsibility Principle (SRP)
- Each function, component, or module should have one reason to change
- A component should do one thing and do it well
- Split large components into smaller, focused components
- Extract business logic into separate functions or hooks

**Example - Bad:**
```typescript
// Component doing too much
const UserDashboard = () => {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(false);
  
  useEffect(() => {
    // Fetching logic
    // Filtering logic
    // Sorting logic
    // Formatting logic
  }, []);
  
  // Rendering multiple concerns
  return (
    <div>
      {/* User list */}
      {/* Statistics */}
      {/* Filters */}
      {/* Actions */}
    </div>
  );
};
```

**Example - Good:**
```typescript
// Separated concerns
const useUsers = () => {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(false);
  // Fetching logic only
  return { users, loading };
};

const useUserFilters = (users) => {
  // Filtering logic only
  return { filteredUsers, filters, setFilters };
};

const UserDashboard = () => {
  const { users, loading } = useUsers();
  const { filteredUsers, filters, setFilters } = useUserFilters(users);
  
  return (
    <div>
      <UserFilters filters={filters} onFiltersChange={setFilters} />
      <UserList users={filteredUsers} loading={loading} />
      <UserStatistics users={filteredUsers} />
    </div>
  );
};
```

#### 2. Don't Repeat Yourself (DRY)
- Extract common logic into reusable functions, hooks, or utilities
- Create shared components for repeated UI patterns
- Use configuration objects for repeated patterns
- Avoid copy-pasting code; refactor instead

#### 3. Keep It Simple, Stupid (KISS)
- Prefer simple solutions over complex ones
- Avoid premature optimization
- Write code that is easy to understand and maintain
- Use clear, descriptive names

#### 4. Separation of Concerns
- Separate presentation from business logic
- Keep data fetching separate from rendering
- Isolate side effects (API calls, subscriptions) in hooks
- Keep UI components focused on rendering

#### 5. Composition Over Inheritance
- Build complex components by composing simpler ones
- Use React's composition patterns (children, render props, compound components)
- Prefer higher-order components or hooks over class inheritance
- Create flexible, reusable building blocks

#### 6. Open/Closed Principle
- Components should be open for extension but closed for modification
- Use props and composition to extend behavior
- Avoid modifying existing components; create new ones or use composition

### React-Specific Patterns

#### Custom Hooks for Code Division
Extract reusable logic into custom hooks to separate concerns and improve reusability.

**Pattern: Data Fetching Hook**
```typescript
// hooks/useUserData.ts
export const useUserData = (userId: string) => {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    let cancelled = false;
    
    const fetchUser = async () => {
      try {
        setLoading(true);
        setError(null);
        const data = await fetchUserById(userId);
        if (!cancelled) {
          setUser(data);
        }
      } catch (err) {
        if (!cancelled) {
          setError(err as Error);
        }
      } finally {
        if (!cancelled) {
          setLoading(false);
        }
      }
    };

    fetchUser();
    return () => { cancelled = true; };
  }, [userId]);

  return { user, loading, error };
};
```

**Pattern: Form State Hook**
```typescript
// hooks/useFormState.ts
export const useFormState = <T extends Record<string, any>>(initialValues: T) => {
  const [values, setValues] = useState<T>(initialValues);
  const [errors, setErrors] = useState<Partial<Record<keyof T, string>>>({});
  const [touched, setTouched] = useState<Partial<Record<keyof T, boolean>>>({});

  const setValue = useCallback((name: keyof T, value: any) => {
    setValues(prev => ({ ...prev, [name]: value }));
    // Clear error when user starts typing
    if (errors[name]) {
      setErrors(prev => {
        const next = { ...prev };
        delete next[name];
        return next;
      });
    }
  }, [errors]);

  const setFieldTouched = useCallback((name: keyof T) => {
    setTouched(prev => ({ ...prev, [name]: true }));
  }, []);

  const reset = useCallback(() => {
    setValues(initialValues);
    setErrors({});
    setTouched({});
  }, [initialValues]);

  return { values, errors, touched, setValue, setFieldTouched, reset };
};
```

#### React.createPortal for Modals and Overlays
Use `createPortal` to render components outside the normal DOM hierarchy (modals, tooltips, dropdowns).

**Pattern: Modal Component with Portal**
```typescript
import { createPortal } from 'react-dom';
import { useEffect, useRef } from 'react';

interface ModalProps {
  isOpen: boolean;
  onClose: () => void;
  children: React.ReactNode;
  title?: string;
}

export const Modal: React.FC<ModalProps> = ({ isOpen, onClose, children, title }) => {
  const modalRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    if (!isOpen) return;

    const handleEscape = (e: KeyboardEvent) => {
      if (e.key === 'Escape') onClose();
    };

    // Prevent body scroll when modal is open
    document.body.style.overflow = 'hidden';
    document.addEventListener('keydown', handleEscape);

    return () => {
      document.body.style.overflow = '';
      document.removeEventListener('keydown', handleEscape);
    };
  }, [isOpen, onClose]);

  useEffect(() => {
    if (isOpen && modalRef.current) {
      // Focus trap for accessibility
      const focusableElements = modalRef.current.querySelectorAll(
        'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
      );
      const firstElement = focusableElements[0] as HTMLElement;
      firstElement?.focus();
    }
  }, [isOpen]);

  if (!isOpen) return null;

  return createPortal(
    <div
      className="fixed inset-0 z-50 flex items-center justify-center bg-black bg-opacity-50"
      onClick={(e) => {
        if (e.target === e.currentTarget) onClose();
      }}
      role="dialog"
      aria-modal="true"
      aria-labelledby={title ? 'modal-title' : undefined}
    >
      <div
        ref={modalRef}
        className="bg-white rounded-lg shadow-xl max-w-md w-full mx-4"
      >
        {title && (
          <h2 id="modal-title" className="text-xl font-bold p-4 border-b">
            {title}
          </h2>
        )}
        <div className="p-4">{children}</div>
      </div>
    </div>,
    document.body
  );
};
```

#### Memoization for Performance
Use `React.memo`, `useMemo`, and `useCallback` strategically to prevent unnecessary re-renders.

**Pattern: Component Memoization**
```typescript
// Memoize expensive components
interface UserCardProps {
  user: User;
  onSelect: (userId: string) => void;
}

export const UserCard = React.memo<UserCardProps>(({ user, onSelect }) => {
  return (
    <div onClick={() => onSelect(user.id)}>
      <h3>{user.name}</h3>
      <p>{user.email}</p>
    </div>
  );
}, (prevProps, nextProps) => {
  // Custom comparison function
  return prevProps.user.id === nextProps.user.id &&
         prevProps.user.name === nextProps.user.name &&
         prevProps.user.email === nextProps.user.email;
});
```

**Pattern: useMemo for Expensive Calculations**
```typescript
const ProductList = ({ products, filters }) => {
  // Memoize filtered and sorted products
  const filteredProducts = useMemo(() => {
    return products
      .filter(product => {
        if (filters.category && product.category !== filters.category) return false;
        if (filters.priceMax && product.price > filters.priceMax) return false;
        return true;
      })
      .sort((a, b) => {
        if (filters.sortBy === 'price') return a.price - b.price;
        if (filters.sortBy === 'name') return a.name.localeCompare(b.name);
        return 0;
      });
  }, [products, filters]);

  // Memoize statistics calculation
  const stats = useMemo(() => {
    return {
      total: filteredProducts.length,
      averagePrice: filteredProducts.reduce((sum, p) => sum + p.price, 0) / filteredProducts.length,
      categories: [...new Set(filteredProducts.map(p => p.category))],
    };
  }, [filteredProducts]);

  return (
    <div>
      <ProductStats stats={stats} />
      {filteredProducts.map(product => (
        <ProductCard key={product.id} product={product} />
      ))}
    </div>
  );
};
```

**Pattern: useCallback for Stable Function References**
```typescript
const UserList = ({ users, onUserSelect }) => {
  // Memoize callback to prevent child re-renders
  const handleSelect = useCallback((userId: string) => {
    onUserSelect(userId);
  }, [onUserSelect]);

  // Memoize filtered users
  const activeUsers = useMemo(() => {
    return users.filter(user => user.isActive);
  }, [users]);

  return (
    <div>
      {activeUsers.map(user => (
        <UserCard
          key={user.id}
          user={user}
          onSelect={handleSelect} // Stable reference
        />
      ))}
    </div>
  );
};
```

#### Compound Components Pattern
Create flexible, composable components that work together while maintaining a clean API.

**Pattern: Compound Component Example**
```typescript
// Compound component: Tabs
interface TabsContextValue {
  activeTab: string;
  setActiveTab: (tab: string) => void;
}

const TabsContext = createContext<TabsContextValue | undefined>(undefined);

const useTabsContext = () => {
  const context = useContext(TabsContext);
  if (!context) {
    throw new Error('Tabs components must be used within Tabs');
  }
  return context;
};

interface TabsProps {
  defaultTab?: string;
  children: React.ReactNode;
}

const TabsRoot: React.FC<TabsProps> = ({ defaultTab, children }) => {
  const [activeTab, setActiveTab] = useState(defaultTab || '');

  return (
    <TabsContext.Provider value={{ activeTab, setActiveTab }}>
      <div className="tabs">{children}</div>
    </TabsContext.Provider>
  );
};

const TabsList: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  return <div className="tabs-list" role="tablist">{children}</div>;
};

interface TabsTriggerProps {
  value: string;
  children: React.ReactNode;
}

const TabsTrigger: React.FC<TabsTriggerProps> = ({ value, children }) => {
  const { activeTab, setActiveTab } = useTabsContext();
  const isActive = activeTab === value;

  return (
    <button
      role="tab"
      aria-selected={isActive}
      onClick={() => setActiveTab(value)}
      className={isActive ? 'active' : ''}
    >
      {children}
    </button>
  );
};

interface TabsContentProps {
  value: string;
  children: React.ReactNode;
}

const TabsContent: React.FC<TabsContentProps> = ({ value, children }) => {
  const { activeTab } = useTabsContext();
  
  if (activeTab !== value) return null;

  return (
    <div role="tabpanel" className="tabs-content">
      {children}
    </div>
  );
};

// Export as compound component
export const Tabs = Object.assign(TabsRoot, {
  List: TabsList,
  Trigger: TabsTrigger,
  Content: TabsContent,
});

// Usage:
<Tabs defaultTab="profile">
  <Tabs.List>
    <Tabs.Trigger value="profile">Profile</Tabs.Trigger>
    <Tabs.Trigger value="settings">Settings</Tabs.Trigger>
  </Tabs.List>
  <Tabs.Content value="profile">Profile content</Tabs.Content>
  <Tabs.Content value="settings">Settings content</Tabs.Content>
</Tabs>
```

### Design Principles Checklist
- [ ] Single Responsibility: Each component/function has one clear purpose
- [ ] DRY: No code duplication; logic extracted to reusable functions/hooks
- [ ] KISS: Solution is as simple as possible
- [ ] Separation of Concerns: Logic separated from presentation
- [ ] Composition: Complex components built from simpler ones
- [ ] Custom Hooks: Reusable logic extracted to hooks
- [ ] Portals: Modals/overlays use createPortal when appropriate
- [ ] Memoization: Used strategically for performance (not overused)
- [ ] Compound Components: Used for flexible, composable APIs

---

## API Development Agent

### Rules
- Always validate input using Zod or similar schema validation
- Return consistent response format: `{ success: boolean, data?: T, error?: string, message?: string }`
- Use proper HTTP status codes (200, 201, 400, 401, 403, 404, 500)
- Implement proper error handling with try-catch blocks
- Log all errors with context (userId, requestId, timestamp)
- Use middleware for authentication, authorization, and validation
- Implement rate limiting on public endpoints
- Include request/response logging for debugging
- Use transactions for multi-step database operations
- Never expose internal errors or stack traces to clients

### Example Pattern
```typescript
// POST /api/users
export const createUser = async (req: Request, res: Response) => {
  const schema = z.object({
    email: z.string().email(),
    name: z.string().min(1).max(100),
    role: z.enum(['user', 'admin']).default('user'),
  });

  try {
    const validated = schema.parse(req.body);
    
    // Check if user exists
    const existing = await User.findOne({ email: validated.email });
    if (existing) {
      return res.status(409).json({
        success: false,
        error: 'User with this email already exists',
      });
    }

    // Create user in transaction
    const user = await db.transaction(async (tx) => {
      const newUser = await User.create(validated, { transaction: tx });
      await AuditLog.create({
        action: 'user_created',
        userId: newUser.id,
      }, { transaction: tx });
      return newUser;
    });

    logger.info('User created', { userId: user.id, email: user.email });
    
    return res.status(201).json({
      success: true,
      data: user,
      message: 'User created successfully',
    });
  } catch (error) {
    if (error instanceof z.ZodError) {
      return res.status(400).json({
        success: false,
        error: 'Validation failed',
        details: error.errors,
      });
    }
    
    logger.error('Failed to create user', { error, body: req.body });
    return res.status(500).json({
      success: false,
      error: 'Internal server error',
    });
  }
};
```

### Checklist
- [ ] Input validation schema defined
- [ ] Error handling implemented
- [ ] Proper HTTP status codes used
- [ ] Logging added for errors and important events
- [ ] Authentication/authorization checks
- [ ] Response format is consistent
- [ ] Database transactions for multi-step operations
- [ ] Rate limiting considered (if public endpoint)

---

## Frontend Component Agent

### Rules
- Use TypeScript with proper interfaces for props
- Prefer functional components with hooks
- **Follow Design Principles** (see [Design Principles](#design-principles) section)
- Extract reusable logic into custom hooks (see custom hooks patterns)
- Use React.memo, useMemo, and useCallback strategically (see memoization patterns)
- Use React.createPortal for modals, tooltips, and overlays
- Consider compound components pattern for flexible, composable APIs
- Implement proper loading and error states
- Use semantic HTML elements
- Ensure accessibility (ARIA labels, keyboard navigation, focus management)
- Follow mobile-first responsive design
- Use Tailwind CSS utility classes
- Implement proper form validation
- Handle edge cases (empty states, loading, errors)
- Apply Single Responsibility Principle - split large components

### Example Pattern
```typescript
import { useState, useEffect, useCallback } from 'react';
import { useQuery } from '@tanstack/react-query';

interface UserProfileProps {
  userId: string;
  onUpdate?: (user: User) => void;
}

export const UserProfile: React.FC<UserProfileProps> = ({ userId, onUpdate }) => {
  const [isEditing, setIsEditing] = useState(false);
  
  const { data: user, isLoading, error, refetch } = useQuery({
    queryKey: ['user', userId],
    queryFn: () => fetchUser(userId),
    enabled: !!userId,
  });

  const handleUpdate = useCallback(async (data: UpdateUserData) => {
    try {
      const updated = await updateUser(userId, data);
      onUpdate?.(updated);
      setIsEditing(false);
      await refetch();
    } catch (err) {
      // Error handling
    }
  }, [userId, onUpdate, refetch]);

  if (isLoading) {
    return <LoadingSpinner />;
  }

  if (error) {
    return <ErrorMessage message="Failed to load user profile" />;
  }

  if (!user) {
    return <EmptyState message="User not found" />;
  }

  return (
    <div className="user-profile" role="main">
      <h1 className="text-2xl font-bold mb-4">User Profile</h1>
      {/* Component content */}
    </div>
  );
};
```

### Checklist
- [ ] TypeScript interfaces defined for props
- [ ] Single Responsibility Principle applied (component has one clear purpose)
- [ ] Loading state handled
- [ ] Error state handled
- [ ] Empty state handled
- [ ] Accessibility attributes added (ARIA labels, keyboard navigation, focus management)
- [ ] Responsive design implemented (mobile-first)
- [ ] Custom hooks extracted for reusable logic (data fetching, form state, etc.)
- [ ] Memoization used strategically (React.memo, useMemo, useCallback)
- [ ] createPortal used for modals/overlays (if applicable)
- [ ] Compound components pattern considered (if flexible API needed)
- [ ] Form validation implemented (if applicable)
- [ ] Code follows Design Principles from [Design Principles](#design-principles) section

---

## Database Schema Agent

### Rules
- Use proper data types (avoid TEXT for everything)
- Add indexes for frequently queried fields
- Include timestamps (createdAt, updatedAt)
- Use foreign keys with proper constraints
- Add unique constraints where appropriate
- Include soft deletes (deletedAt) for important tables
- Normalize data to 3NF, denormalize only when justified
- Add check constraints for data validation
- Document schema decisions in migration comments
- Consider future scalability (partitioning, sharding)

### Example Pattern
```typescript
// Migration: create_users_table
export const up = async (knex: Knex) => {
  await knex.schema.createTable('users', (table) => {
    table.uuid('id').primary().defaultTo(knex.raw('gen_random_uuid()'));
    table.string('email', 255).notNullable().unique();
    table.string('name', 100).notNullable();
    table.enum('role', ['user', 'admin', 'moderator']).notNullable().defaultTo('user');
    table.string('password_hash', 255).notNullable();
    table.boolean('is_active').notNullable().defaultTo(true);
    table.timestamp('email_verified_at').nullable();
    table.timestamp('last_login_at').nullable();
    table.timestamps(true, true); // createdAt, updatedAt
    table.timestamp('deleted_at').nullable(); // Soft delete
    
    // Indexes
    table.index('email');
    table.index('role');
    table.index('is_active');
    table.index('created_at');
  });
};

// Model
export interface User {
  id: string;
  email: string;
  name: string;
  role: 'user' | 'admin' | 'moderator';
  passwordHash: string;
  isActive: boolean;
  emailVerifiedAt: Date | null;
  lastLoginAt: Date | null;
  createdAt: Date;
  updatedAt: Date;
  deletedAt: Date | null;
}
```

### Checklist
- [ ] Appropriate data types chosen
- [ ] Indexes added for query optimization
- [ ] Foreign key constraints defined
- [ ] Unique constraints where needed
- [ ] Timestamps included
- [ ] Soft delete support (if needed)
- [ ] Check constraints for data validation
- [ ] Migration is reversible (down function)

---

## Testing Agent

### Rules
- Write tests for all utility functions
- Test happy paths, edge cases, and error scenarios
- Use descriptive test names: `should [expected behavior] when [condition]`
- Mock external dependencies (APIs, databases, file system)
- Test user interactions, not implementation details
- Aim for >80% code coverage
- Use test fixtures for consistent test data
- Clean up test data after each test
- Test accessibility features
- Include integration tests for critical flows

### Example Pattern
```typescript
import { describe, it, expect, beforeEach, vi } from 'vitest';
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import { UserProfile } from './UserProfile';
import * as api from '@/api/users';

vi.mock('@/api/users');

describe('UserProfile', () => {
  const mockUser = {
    id: '1',
    email: 'test@example.com',
    name: 'Test User',
  };

  beforeEach(() => {
    vi.clearAllMocks();
  });

  it('should display user information when loaded', async () => {
    vi.mocked(api.fetchUser).mockResolvedValue(mockUser);
    
    render(<UserProfile userId="1" />);
    
    await waitFor(() => {
      expect(screen.getByText('Test User')).toBeInTheDocument();
      expect(screen.getByText('test@example.com')).toBeInTheDocument();
    });
  });

  it('should show loading state initially', () => {
    vi.mocked(api.fetchUser).mockImplementation(() => new Promise(() => {}));
    
    render(<UserProfile userId="1" />);
    
    expect(screen.getByRole('status')).toBeInTheDocument();
  });

  it('should display error message when fetch fails', async () => {
    vi.mocked(api.fetchUser).mockRejectedValue(new Error('Network error'));
    
    render(<UserProfile userId="1" />);
    
    await waitFor(() => {
      expect(screen.getByText(/failed to load/i)).toBeInTheDocument();
    });
  });

  it('should call onUpdate when user is updated', async () => {
    const onUpdate = vi.fn();
    vi.mocked(api.fetchUser).mockResolvedValue(mockUser);
    vi.mocked(api.updateUser).mockResolvedValue({ ...mockUser, name: 'Updated' });
    
    render(<UserProfile userId="1" onUpdate={onUpdate} />);
    
    // Simulate update action
    fireEvent.click(screen.getByRole('button', { name: /edit/i }));
    fireEvent.change(screen.getByLabelText(/name/i), { target: { value: 'Updated' } });
    fireEvent.click(screen.getByRole('button', { name: /save/i }));
    
    await waitFor(() => {
      expect(onUpdate).toHaveBeenCalledWith(expect.objectContaining({ name: 'Updated' }));
    });
  });
});
```

### Checklist
- [ ] Happy path tested
- [ ] Edge cases tested (empty, null, undefined)
- [ ] Error scenarios tested
- [ ] External dependencies mocked
- [ ] Test data cleaned up
- [ ] Descriptive test names used
- [ ] Integration tests for critical flows
- [ ] Accessibility tests included (if applicable)

---

## Security Agent

### Rules
- Always validate and sanitize user input
- Use parameterized queries (prevent SQL injection)
- Hash passwords with bcrypt (min 10 rounds)
- Implement proper authentication (JWT with refresh tokens)
- Check authorization before sensitive operations
- Use HTTPS in production
- Implement rate limiting on auth endpoints
- Sanitize output to prevent XSS
- Validate file uploads (type, size, content)
- Use environment variables for secrets
- Never log sensitive data (passwords, tokens, PII)
- Implement CSRF protection
- Use secure session management
- Follow OWASP Top 10 guidelines

### Example Pattern
```typescript
// Password hashing
import bcrypt from 'bcrypt';
const SALT_ROUNDS = 12;

export const hashPassword = async (password: string): Promise<string> => {
  return bcrypt.hash(password, SALT_ROUNDS);
};

export const verifyPassword = async (
  password: string,
  hash: string
): Promise<boolean> => {
  return bcrypt.compare(password, hash);
};

// Input validation
import { z } from 'zod';
import { sanitize } from 'dompurify';

const userInputSchema = z.object({
  email: z.string().email().max(255),
  name: z.string().min(1).max(100).transform(sanitize),
  bio: z.string().max(1000).transform(sanitize).optional(),
});

// Authorization middleware
export const requireRole = (allowedRoles: string[]) => {
  return async (req: Request, res: Response, next: NextFunction) => {
    const user = req.user;
    if (!user || !allowedRoles.includes(user.role)) {
      return res.status(403).json({
        success: false,
        error: 'Insufficient permissions',
      });
    }
    next();
  };
};

// Rate limiting
import rateLimit from 'express-rate-limit';

export const authRateLimit = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 5, // 5 requests per window
  message: 'Too many login attempts, please try again later',
});
```

### Checklist
- [ ] Input validation implemented
- [ ] Output sanitization for XSS prevention
- [ ] SQL injection prevention (parameterized queries)
- [ ] Password hashing with appropriate rounds
- [ ] Authentication implemented correctly
- [ ] Authorization checks in place
- [ ] Rate limiting on sensitive endpoints
- [ ] Secrets stored in environment variables
- [ ] No sensitive data in logs
- [ ] CSRF protection (if applicable)
- [ ] File upload validation

---

## Performance Optimization Agent

### Rules
- Implement code splitting and lazy loading
- Use React.memo, useMemo, useCallback appropriately
- Optimize database queries (add indexes, avoid N+1)
- Implement caching strategies (Redis, in-memory)
- Optimize images (WebP, lazy loading, responsive sizes)
- Debounce/throttle expensive operations
- Use pagination for large datasets
- Minimize bundle size (tree shaking, code splitting)
- Optimize re-renders (avoid unnecessary state updates)
- Use CDN for static assets
- Implement virtual scrolling for long lists
- Profile and measure before optimizing

### Example Pattern
```typescript
// Code splitting
import { lazy, Suspense } from 'react';

const HeavyComponent = lazy(() => import('./HeavyComponent'));

export const App = () => {
  return (
    <Suspense fallback={<LoadingSpinner />}>
      <HeavyComponent />
    </Suspense>
  );
};

// Memoization
import { useMemo, useCallback } from 'react';

export const ProductList = ({ products, filters }) => {
  const filteredProducts = useMemo(() => {
    return products.filter(p => 
      filters.category ? p.category === filters.category : true
    );
  }, [products, filters.category]);

  const handleSelect = useCallback((id: string) => {
    // Handler logic
  }, []);

  return (
    <div>
      {filteredProducts.map(product => (
        <ProductCard key={product.id} product={product} onSelect={handleSelect} />
      ))}
    </div>
  );
};

// Database query optimization
// Bad: N+1 query
const users = await User.findAll();
for (const user of users) {
  const posts = await Post.findAll({ where: { userId: user.id } });
}

// Good: Eager loading
const users = await User.findAll({
  include: [{ model: Post, as: 'posts' }],
});

// Caching
import Redis from 'ioredis';
const redis = new Redis(process.env.REDIS_URL);

export const getCachedUser = async (userId: string) => {
  const cacheKey = `user:${userId}`;
  const cached = await redis.get(cacheKey);
  
  if (cached) {
    return JSON.parse(cached);
  }
  
  const user = await User.findById(userId);
  await redis.setex(cacheKey, 3600, JSON.stringify(user)); // 1 hour TTL
  return user;
};
```

### Checklist
- [ ] Code splitting implemented
- [ ] Memoization used appropriately
- [ ] Database queries optimized (no N+1)
- [ ] Caching strategy implemented
- [ ] Images optimized
- [ ] Expensive operations debounced/throttled
- [ ] Pagination for large datasets
- [ ] Bundle size optimized
- [ ] Re-renders minimized

---

## Documentation Agent

### Rules
- Write JSDoc comments for all public functions
- Document function parameters and return types
- Explain "why" not "what" in comments
- Include usage examples in documentation
- Document complex algorithms and business logic
- Keep README files up to date
- Document API endpoints (OpenAPI/Swagger)
- Include setup and installation instructions
- Document environment variables
- Add inline comments for non-obvious code

### Example Pattern
```typescript
/**
 * Calculates the total price including tax and discounts
 * 
 * @param items - Array of items with price and quantity
 * @param taxRate - Tax rate as decimal (e.g., 0.1 for 10%)
 * @param discountCode - Optional discount code to apply
 * @returns Object containing subtotal, tax, discount, and total
 * 
 * @example
 * ```typescript
 * const items = [
 *   { price: 100, quantity: 2 },
 *   { price: 50, quantity: 1 }
 * ];
 * const result = calculateTotalWithTax(items, 0.1, 'SAVE10');
 * // Returns: { subtotal: 250, tax: 25, discount: 25, total: 250 }
 * ```
 */
export const calculateTotalWithTax = async (
  items: CartItem[],
  taxRate: number,
  discountCode?: string
): Promise<PriceBreakdown> => {
  // Implementation
};
```

### Checklist
- [ ] JSDoc comments for public functions
- [ ] Parameters and return types documented
- [ ] Usage examples included
- [ ] Complex logic explained
- [ ] README updated
- [ ] API documentation (if applicable)
- [ ] Environment variables documented

---

## Refactoring Agent

### Rules
- Maintain existing functionality (no breaking changes)
- Improve code readability and maintainability
- Extract reusable functions/components
- Remove code duplication (DRY principle)
- Simplify complex logic
- Improve naming for clarity
- Add type safety where missing
- Update tests after refactoring
- Document why refactoring was done
- Run tests before and after refactoring

### Example Pattern
```typescript
// Before: Duplicated logic
const calculateUserTotal = (user: User) => {
  let total = 0;
  for (const order of user.orders) {
    for (const item of order.items) {
      total += item.price * item.quantity;
    }
  }
  return total;
};

const calculateOrderTotal = (order: Order) => {
  let total = 0;
  for (const item of order.items) {
    total += item.price * item.quantity;
  }
  return total;
};

// After: Extracted reusable function
const calculateItemsTotal = (items: OrderItem[]): number => {
  return items.reduce(
    (sum, item) => sum + item.price * item.quantity,
    0
  );
};

const calculateOrderTotal = (order: Order): number => {
  return calculateItemsTotal(order.items);
};

const calculateUserTotal = (user: User): number => {
  return user.orders.reduce(
    (sum, order) => sum + calculateOrderTotal(order),
    0
  );
};
```

### Checklist
- [ ] Functionality preserved
- [ ] Code duplication removed
- [ ] Logic simplified
- [ ] Naming improved
- [ ] Type safety added
- [ ] Tests updated and passing
- [ ] Refactoring rationale documented

---

## Migration Agent

### Rules
- Create reversible migrations (up and down functions)
- Test migrations on staging before production
- Include data migrations when schema changes
- Add indexes in separate migration if large table
- Document breaking changes
- Backup database before running migrations
- Run migrations in transactions when possible
- Version control all migrations
- Never modify existing migrations (create new ones)

### Example Pattern
```typescript
// Migration: add_user_preferences_table
export const up = async (knex: Knex) => {
  await knex.schema.createTable('user_preferences', (table) => {
    table.uuid('id').primary().defaultTo(knex.raw('gen_random_uuid()'));
    table.uuid('user_id').notNullable().references('users.id').onDelete('CASCADE');
    table.string('theme', 20).notNullable().defaultTo('light');
    table.boolean('email_notifications').notNullable().defaultTo(true);
    table.jsonb('settings').notNullable().defaultTo('{}');
    table.timestamps(true, true);
    
    table.unique(['user_id']);
    table.index('user_id');
  });
  
  // Data migration: set default preferences for existing users
  await knex.raw(`
    INSERT INTO user_preferences (user_id, created_at, updated_at)
    SELECT id, NOW(), NOW() FROM users
    WHERE id NOT IN (SELECT user_id FROM user_preferences)
  `);
};

export const down = async (knex: Knex) => {
  await knex.schema.dropTableIfExists('user_preferences');
};
```

### Checklist
- [ ] Up and down functions implemented
- [ ] Data migrations included (if needed)
- [ ] Indexes added (separate migration if large table)
- [ ] Breaking changes documented
- [ ] Migration tested on staging
- [ ] Reversible (down function works)

---

## Code Review Agent

### Rules
- Check for security vulnerabilities
- Verify error handling is comprehensive
- Ensure tests are included and passing
- Check code follows project standards
- Verify performance considerations
- Check for accessibility issues
- Ensure proper logging is in place
- Verify documentation is adequate
- Check for code duplication
- Ensure proper type safety

### Review Checklist
- [ ] Security: No SQL injection, XSS, or other vulnerabilities
- [ ] Error handling: All error cases handled
- [ ] Tests: Unit and integration tests included
- [ ] Code style: Follows project conventions
- [ ] Performance: No obvious performance issues
- [ ] Accessibility: ARIA labels, keyboard navigation
- [ ] Logging: Important events logged
- [ ] Documentation: Code is well-documented
- [ ] DRY: No code duplication
- [ ] Type safety: Proper TypeScript types used

---

## Usage Tips

1. **Invoke Specific Agents**: When asking Cursor to generate code, reference the agent you need:
   - "Generate an API endpoint following the API Development Agent rules"
   - "Create a React component using Frontend Component Agent guidelines"
   - "Write tests following the Testing Agent patterns"
   - "Generate a commit message using Commit Message Agent format"

2. **Combine Agents**: For complex features, combine multiple agents:
   - "Create a user authentication API endpoint (API + Security agents)"
   - "Build a user profile component with tests (Frontend + Testing agents)"
   - "Refactor component following Design Principles and Frontend Component Agent rules"

3. **Design Principles**: Always apply Design Principles (Single Responsibility, DRY, KISS, etc.) when generating code. Reference the [Design Principles](#design-principles) section for patterns and examples.

4. **Update Rules**: As patterns evolve, update these rules to reflect current best practices.

5. **Team Alignment**: Ensure all team members are familiar with these rules for consistency.

---

## Commit Message Agent

### Rules
- Follow [Conventional Commits](https://www.conventionalcommits.org/) specification
- Use commitlint format for consistency
- Keep subject line under 72 characters
- Use imperative mood ("add feature" not "added feature" or "adds feature")
- Reference issues/PRs in footer when applicable
- Include breaking changes notation when needed
- Write clear, descriptive commit messages

### Commitlint Configuration Template

When generating commit messages, follow this commitlint format:

```
<type>(<scope>): <subject>

[optional body]

[optional footer(s)]
```

### Commit Types

Use these standard types (based on commitlint config):

- **feat**: A new feature
- **fix**: A bug fix
- **docs**: Documentation only changes
- **style**: Changes that do not affect the meaning of the code (white-space, formatting, missing semi-colons, etc.)
- **refactor**: A code change that neither fixes a bug nor adds a feature
- **perf**: A code change that improves performance
- **test**: Adding missing tests or correcting existing tests
- **build**: Changes that affect the build system or external dependencies (example scopes: gulp, broccoli, npm)
- **ci**: Changes to CI configuration files and scripts (example scopes: Travis, Circle, GitHub Actions)
- **chore**: Other changes that don't modify src or test files
- **revert**: Reverts a previous commit

### Scope

The scope should be the area of the codebase affected:
- Component name (e.g., `UserProfile`, `AuthForm`)
- Feature area (e.g., `auth`, `dashboard`, `api`)
- Module name (e.g., `database`, `utils`, `hooks`)

### Subject

- Use imperative, present tense: "change" not "changed" nor "changes"
- Don't capitalize the first letter
- No dot (.) at the end
- Maximum 72 characters

### Body (Optional)

- Use the imperative, present tense
- Explain **what** and **why** vs. **how**
- Wrap at 72 characters
- Can include multiple paragraphs

### Footer (Optional)

- Reference issues: `Closes #123`, `Fixes #456`
- Breaking changes: `BREAKING CHANGE: <description>`
- Co-authors: `Co-authored-by: Name <email>`

### Examples

#### Simple Feature
```
feat(auth): add JWT refresh token rotation

Implement automatic token refresh before expiration to improve
user experience and security. Tokens now refresh 5 minutes
before expiry.

Closes #123
```

#### Bug Fix
```
fix(api): prevent race condition in user creation

Add database transaction to ensure atomic user creation and
audit log entry. Prevents duplicate users when concurrent
requests occur.

Fixes #456
```

#### Breaking Change
```
feat(api)!: change user endpoint response format

BREAKING CHANGE: User endpoint now returns nested user object
instead of flat structure. Update clients to access user data
via response.data.user instead of response.user.

Migration guide: https://docs.example.com/migration
```

#### Refactor
```
refactor(components): extract UserCard into separate component

Split UserCard from UserList to improve reusability and
follow single responsibility principle. Component now accepts
user prop and onSelect callback.
```

#### Documentation
```
docs(readme): update setup instructions

Add Docker Compose setup steps and environment variable
configuration guide for new developers.
```

#### Performance
```
perf(database): add index on user email field

Improve query performance for user lookups by email. Reduces
query time from ~200ms to ~5ms for email-based searches.
```

#### Test
```
test(auth): add unit tests for JWT token validation

Cover edge cases including expired tokens, malformed tokens,
and missing tokens. Achieve 95% code coverage for auth module.
```

#### Chore
```
chore(deps): update React to v18.2.0

Update React and related dependencies to latest stable version.
No breaking changes for this project.
```

#### Multiple Changes
```
feat(dashboard): add user statistics widget

- Display total users count
- Show active users percentage
- Add last 30 days trend chart

Closes #789
```

### Commit Message Generation Template

When generating commit messages, use this template:

```typescript
interface CommitMessage {
  type: 'feat' | 'fix' | 'docs' | 'style' | 'refactor' | 'perf' | 'test' | 'build' | 'ci' | 'chore' | 'revert';
  scope?: string;
  subject: string; // imperative, present tense, max 72 chars
  body?: string; // optional, explain what and why
  breaking?: boolean;
  breakingDescription?: string;
  closes?: string[]; // issue numbers
  fixes?: string[]; // issue numbers
}

// Example generation:
const generateCommitMessage = (changes: Change[]): string => {
  // Analyze changes to determine type and scope
  // Generate appropriate commit message
  // Follow commitlint format
};
```

### Commitlint Checklist
- [ ] Type is one of the standard types (feat, fix, docs, etc.)
- [ ] Scope is appropriate and concise
- [ ] Subject is imperative, present tense, under 72 characters
- [ ] Body explains what and why (if needed)
- [ ] Breaking changes are noted with `BREAKING CHANGE:`
- [ ] Issues are referenced in footer (Closes #123, Fixes #456)
- [ ] Message follows commitlint format exactly

### Automated Commit Message Generation

When using Cursor to generate commit messages:

1. **Analyze Changes**: Review staged changes to determine type and scope
2. **Generate Subject**: Create concise, imperative subject line
3. **Add Body**: If changes are complex, add body explaining what and why
4. **Reference Issues**: Include issue numbers if applicable
5. **Check Breaking Changes**: Note any breaking changes
6. **Validate Format**: Ensure message follows commitlint specification

### Example Prompt for Cursor

When asking Cursor to generate a commit message:

```
Generate a commit message following commitlint format for these changes:
- Added JWT refresh token rotation feature
- Updated auth middleware to handle token refresh
- Added tests for token refresh logic
- Closes issue #123

Use the Commit Message Agent rules from Agents.md
```

---

**Remember**: These rules are guidelines. Adapt them to your specific project needs and team preferences.

