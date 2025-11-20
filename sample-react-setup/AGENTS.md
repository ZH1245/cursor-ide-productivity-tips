# Agent Rules for React / Next.js Projects

Professional development guidelines for React and Next.js projects, emphasizing code quality, maintainability, performance, and best practices.

---

## 🛠️ Linting & Formatting

- **ESLint**: Enabled for JS, TS, React, and Next files with strict rules.
- **Prettier**: Enforced as the formatter for all JS/TS/React files.
- **Auto Fix On Save**: Organizes imports and auto-fixes any lint issues.
- **Consistent Style**: Follows Prettier and ESLint configurations strictly.
- **Whitespace**: Trims trailing whitespace and ensures a final newline.

### Editor Configuration

```json
{
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.organizeImports": true,
    "source.fixAll.eslint": true
  },
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "files.trimTrailingWhitespace": true,
  "files.insertFinalNewline": true,
  "files.exclude": {
    "**/.git": true,
    "**/.DS_Store": true,
    "**/node_modules": true,
    "**/.next": true
  },
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact"
  ],
  "typescript.validate.enable": true,
  "javascript.validate.enable": true,
  "editor.tabSize": 2,
  "editor.detectIndentation": false
}
```

---

## 📝 Documentation Standards

### Code Documentation

- **JSDoc Comments**: All exported functions, classes, components, types, and interfaces MUST have JSDoc comments.
- **Type Annotations**: Use explicit TypeScript types; avoid `any` unless absolutely necessary.
- **Type Documentation**: All exported types, interfaces, and type aliases MUST be documented with JSDoc comments explaining their purpose, properties, and usage.
- **Inline Comments**: Explain "why" not "what" - code should be self-documenting.
- **Complex Logic**: Document non-obvious algorithms, business rules, and edge cases.

#### JSDoc Format

```typescript
/**
 * Calculates the total price including tax and discounts.
 *
 * @param basePrice - The base price before tax and discounts
 * @param taxRate - Tax rate as a decimal (e.g., 0.08 for 8%)
 * @param discount - Discount amount to apply
 * @returns The final price after tax and discount
 * @throws {Error} If basePrice is negative or taxRate is invalid
 *
 * @example
 * ```ts
 * const total = calculateTotalPrice(100, 0.08, 10);
 * // Returns: 98
 * ```
 */
export function calculateTotalPrice(
  basePrice: number,
  taxRate: number,
  discount: number
): number {
  // Implementation
}
```

#### Component Documentation

```typescript
/**
 * Button component with multiple variants and sizes.
 *
 * @component
 * @param {ButtonProps} props - Component props
 * @param {React.ReactNode} props.children - Button content
 * @param {'primary' | 'secondary'} props.variant - Visual style variant
 * @param {'sm' | 'md' | 'lg'} props.size - Button size
 * @param {boolean} props.disabled - Whether button is disabled
 * @param {() => void} props.onClick - Click handler
 *
 * @example
 * ```tsx
 * <Button variant="primary" size="md" onClick={handleClick}>
 *   Click Me
 * </Button>
 * ```
 */
export function Button({ children, variant, size, disabled, onClick }: ButtonProps) {
  // Implementation
}
```

#### Type and Interface Documentation

**All exported types, interfaces, and type aliases MUST have JSDoc comments.**

```typescript
/**
 * User data structure representing a user in the system.
 *
 * @typedef {Object} User
 * @property {string} id - Unique user identifier (UUID)
 * @property {string} email - User's email address (must be valid email format)
 * @property {string} name - User's full name
 * @property {Date} createdAt - Account creation timestamp
 * @property {UserRole} role - User's role in the system
 * @property {boolean} [isActive] - Whether the user account is active (optional, defaults to true)
 *
 * @example
 * ```ts
 * const user: User = {
 *   id: '123e4567-e89b-12d3-a456-426614174000',
 *   email: 'user@example.com',
 *   name: 'John Doe',
 *   createdAt: new Date('2024-01-01'),
 *   role: 'admin',
 *   isActive: true
 * };
 * ```
 */
export type User = {
  id: string;
  email: string;
  name: string;
  createdAt: Date;
  role: UserRole;
  isActive?: boolean;
};

/**
 * Component props for the Button component.
 *
 * @interface ButtonProps
 * @property {React.ReactNode} children - Button content (text or React elements)
 * @property {'primary' | 'secondary' | 'danger'} [variant='primary'] - Visual style variant
 * @property {'sm' | 'md' | 'lg'} [size='md'] - Button size
 * @property {boolean} [disabled=false] - Whether button is disabled
 * @property {() => void} [onClick] - Click event handler
 * @property {string} [className] - Additional CSS classes
 *
 * @example
 * ```tsx
 * const props: ButtonProps = {
 *   children: 'Click me',
 *   variant: 'primary',
 *   size: 'md',
 *   onClick: () => console.log('clicked')
 * };
 * ```
 */
export interface ButtonProps {
  children: React.ReactNode;
  variant?: 'primary' | 'secondary' | 'danger';
  size?: 'sm' | 'md' | 'lg';
  disabled?: boolean;
  onClick?: () => void;
  className?: string;
}

/**
 * API response wrapper with status and data.
 *
 * @template T - Type of the data payload
 *
 * @typedef {Object} ApiResponse
 * @property {'success' | 'error'} status - Response status
 * @property {T} [data] - Response data (only present when status is 'success')
 * @property {string} [error] - Error message (only present when status is 'error')
 *
 * @example
 * ```ts
 * // Success response
 * const successResponse: ApiResponse<User> = {
 *   status: 'success',
 *   data: { id: '1', name: 'John' }
 * };
 *
 * // Error response
 * const errorResponse: ApiResponse<User> = {
 *   status: 'error',
 *   error: 'User not found'
 * };
 * ```
 */
export type ApiResponse<T> =
  | { status: 'success'; data: T }
  | { status: 'error'; error: string };

/**
 * Configuration options for the slideshow component.
 *
 * @typedef {Object} SlideshowProps
 * @property {number} activeIndex - Currently active slide index (0-based)
 * @property {SlideshowContent[]} slideshowContent - Array of slideshow content items
 * @property {boolean} hasUserInteracted - Whether user has interacted with the slideshow
 * @property {(index: number) => void} [onActiveIndexChange] - Callback when active index changes
 * @property {() => void} [onUserInteraction] - Callback when user interacts with slideshow
 */
export type SlideshowProps = {
  activeIndex: number;
  slideshowContent: SlideshowContent[];
  hasUserInteracted: boolean;
  onActiveIndexChange?: (index: number) => void;
  onUserInteraction?: () => void;
};
```

### File-Level Documentation

- **File Headers**: Include a brief description at the top of complex files.
- **Module Purpose**: Document the purpose and responsibility of each module.
- **Exports**: Document all public exports.

```typescript
/**
 * @fileoverview Authentication utilities and JWT token management.
 * Provides functions for token generation, validation, and refresh.
 */

// Module code...
```

### README Documentation

- **Project README**: Must include setup, installation, and usage instructions.
- **Component Documentation**: Document complex components in their own README if needed.
- **API Documentation**: Document all API endpoints, request/response schemas.
- **Architecture Decisions**: Document significant architectural choices.

### Documentation Maintenance Guidelines

**When AI agents make code changes, follow these documentation update rules:**

#### README.md Updates

**✅ Update README.md when:**
- Adding new major features or capabilities
- Changing project structure (new folders, significant reorganization)
- Updating tech stack or dependencies
- Adding new setup/installation steps
- Changing build or deployment process
- Adding new scripts or commands

**❌ Don't update README.md for:**
- Small bug fixes
- Internal refactoring that doesn't change user-facing behavior
- Adding utility functions or helpers
- Minor component additions
- Code style or formatting changes

**Rule of thumb**: README.md should reflect what developers need to know to get started and understand the project at a high level. Keep it stable and manually curated.

#### Code-Level Documentation (JSDoc)

**✅ Always update when:**
- Adding new exported functions, components, or classes
- Adding new exported types, interfaces, or type aliases
- Changing function signatures or component props
- Modifying type definitions or interfaces
- Modifying public APIs

**This is automatic**: JSDoc comments are part of the code and should be updated alongside code changes.

**Type Documentation Requirements:**
- **All exported types MUST have JSDoc comments** explaining their purpose
- **All exported interfaces MUST have JSDoc comments** with `@interface` tag
- **All exported type aliases MUST have JSDoc comments** with `@typedef` tag
- **Document all properties** using `@property` tags for object types
- **Document generic type parameters** using `@template` tags
- **Include examples** for complex types to show usage patterns
- **Explain optional properties** and default values
- **Document union types** and discriminated unions clearly

#### Best Practices

1. **Code changes → JSDoc updates**: Always (automatic)
2. **Major changes → README.md updates**: When structure/features change significantly
3. **Keep README.md stable**: Don't update it for every small change
4. **Inline documentation**: Use JSDoc for code-level documentation, README.md for project-level

**Example workflow:**
- Agent adds a new authentication hook → Updates JSDoc only
- Agent adds a new auth system with setup steps → Updates README.md (setup section) + adds JSDoc
- Agent creates complex feature → Updates README.md (features list) + adds comprehensive JSDoc

---

## 🎯 Single Responsibility Principle (SRP)

### Core Principles

- **One Responsibility Per Function**: Each function should do one thing and do it well.
- **One Responsibility Per Component**: Components should have a single, clear purpose.
- **One Responsibility Per Module**: Each file/module should have a single, well-defined responsibility.
- **Separation of Concerns**: Separate business logic, UI, data fetching, and utilities.

### Function-Level SRP

```typescript
// ❌ BAD: Multiple responsibilities
function processUserData(user: User) {
  // Validates user
  if (!user.email) throw new Error('Invalid email');

  // Formats user data
  const formatted = `${user.name} <${user.email}>`;

  // Saves to database
  await db.users.save(user);

  // Sends email
  await emailService.send(user.email, 'Welcome!');

  // Logs activity
  logger.info('User processed', { userId: user.id });

  return formatted;
}

// ✅ GOOD: Single responsibility per function
function validateUser(user: User): void {
  if (!user.email) throw new Error('Invalid email');
}

function formatUserDisplay(user: User): string {
  return `${user.name} <${user.email}>`;
}

async function saveUser(user: User): Promise<void> {
  await db.users.save(user);
}

async function sendWelcomeEmail(email: string): Promise<void> {
  await emailService.send(email, 'Welcome!');
}

function logUserActivity(userId: string): void {
  logger.info('User processed', { userId });
}

// Composed function
async function processUserData(user: User): Promise<string> {
  validateUser(user);
  const formatted = formatUserDisplay(user);
  await saveUser(user);
  await sendWelcomeEmail(user.email);
  logUserActivity(user.id);
  return formatted;
}
```

### Component-Level SRP

```typescript
// ❌ BAD: Component handles data fetching, formatting, and rendering
function UserProfile({ userId }: { userId: string }) {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(data => {
        const formatted = {
          name: data.name.toUpperCase(),
          email: data.email.toLowerCase(),
          // ... formatting logic
        };
        setUser(formatted);
        setLoading(false);
      });
  }, [userId]);

  if (loading) return <Spinner />;
  if (!user) return <Error />;

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
      {/* Complex rendering logic */}
    </div>
  );
}

// ✅ GOOD: Separated concerns
// hooks/useUser.ts
function useUser(userId: string) {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    userService.getUser(userId)
      .then(setUser)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [userId]);

  return { user, loading, error };
}

// utils/userFormatter.ts
export function formatUserDisplay(user: User): FormattedUser {
  return {
    name: user.name.toUpperCase(),
    email: user.email.toLowerCase(),
  };
}

// components/UserProfile.tsx
function UserProfile({ userId }: { userId: string }) {
  const { user, loading, error } = useUser(userId);

  if (loading) return <Spinner />;
  if (error) return <Error error={error} />;
  if (!user) return null;

  const formatted = formatUserDisplay(user);

  return <UserProfileView user={formatted} />;
}
```

### Module-Level SRP

- **Separate by Domain**: Group related functionality (e.g., `auth/`, `users/`, `payments/`).
- **Separate by Layer**: Keep API routes, services, utilities, and components separate.
- **Single Export Focus**: Each module should export related functionality, not mixed concerns.

```
src/
├── components/          # UI components only
├── hooks/              # Custom React hooks only
├── services/           # Business logic and API calls
├── utils/              # Pure utility functions
├── types/              # TypeScript type definitions
├── constants/          # Constants and configuration
└── lib/                # Third-party library wrappers
```

---

## 🚀 Next.js Server-Side Rendering (SSR) & SEO Priority

### Core Philosophy

**This project prioritizes Server-Side Rendering (SSR) and SEO optimization above all else.**

- **Server Components First**: Default to Server Components; use Client Components only when absolutely necessary
- **SEO-First Thinking**: Every decision should consider SEO impact and server-side rendering benefits
- **Client Components as Last Resort**: Only use `'use client'` when interactivity, browser APIs, or CSS animations require it
- **Maximum SSR**: Strive for maximum server-side rendering to improve performance, SEO, and initial page load

### Decision Tree: Server vs Client Components

```
┌─────────────────────────────────────┐
│ Need interactivity or browser APIs? │
└──────────────┬──────────────────────┘
               │
       ┌───────┴────────┐
       │                │
      YES              NO
       │                │
       ▼                ▼
┌──────────────┐  ┌──────────────┐
│ Use Client   │  │ Use Server   │
│ Component    │  │ Component    │
│ ('use client')│  │ (default)    │
└──────────────┘  └──────────────┘
```

### When to Use Server Components (Default)

**✅ ALWAYS use Server Components for:**
- Data fetching (database queries, API calls)
- Accessing backend resources (file system, environment variables)
- Keeping sensitive information on server (API keys, tokens)
- Large dependencies that should be kept on server
- Static content and layouts
- SEO-critical content (headings, meta tags, structured data)
- Content that doesn't require interactivity

```typescript
// ✅ GOOD: Server Component (default)
import { getTranslations } from 'next-intl/server';

export default async function HomePage({
  params,
}: {
  params: Promise<{ locale: string }>;
}) {
  const { locale } = await params;
  const t = await getTranslations({ locale, namespace: 'home' });
  const data = await fetchData(); // Server-side data fetching

  return (
    <div>
      <h1>{t('title')}</h1>
      <p>{t('description')}</p>
      {/* SEO-optimized content rendered on server */}
    </div>
  );
}
```

### When to Use Client Components (Last Resort)

**⚠️ ONLY use Client Components when:**
- You need interactivity (onClick, onChange, useState, useEffect)
- You need browser APIs (window, document, localStorage, etc.)
- You need React hooks that require client-side (useState, useEffect, useContext)
- You need CSS animations that require JavaScript triggers
- You need event listeners (scroll, resize, mouse events)
- You need third-party libraries that require browser APIs

```typescript
// ⚠️ ONLY when interactivity is required
'use client';

import { useState } from 'react';

export function InteractiveButton() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      Clicked {count} times
    </button>
  );
}
```

### Hybrid Approach: Server + Client Pattern

**✅ BEST PRACTICE**: Split components into Server (data/logic) + Client (interactivity)

```typescript
// ✅ GOOD: Server Component fetches data
// app/components/ProductList.tsx (Server Component)
import { getProducts } from '@/lib/products';
import ProductCard from './ProductCard'; // Client Component for interactivity

export default async function ProductList() {
  const products = await getProducts(); // Server-side data fetching

  return (
    <div>
      {products.map((product) => (
        <ProductCard key={product.id} product={product} />
      ))}
    </div>
  );
}

// ✅ GOOD: Client Component handles interactivity only
// app/components/ProductCard.tsx (Client Component)
'use client';

import { useState } from 'react';

export default function ProductCard({ product }: { product: Product }) {
  const [isExpanded, setIsExpanded] = useState(false);

  return (
    <div>
      <h3>{product.name}</h3>
      <button onClick={() => setIsExpanded(!isExpanded)}>
        {isExpanded ? 'Collapse' : 'Expand'}
      </button>
    </div>
  );
}
```

### CSS Animations vs Client Components

**✅ PREFER CSS animations over JavaScript when possible:**

```typescript
// ✅ GOOD: CSS animations (no client component needed)
// app/components/AnimatedCard.tsx (Server Component)
export default function AnimatedCard({ title }: { title: string }) {
  return (
    <div className="animate-fade-in">
      <h2>{title}</h2>
    </div>
  );
}

// app/globals.css
@keyframes fade-in {
  from { opacity: 0; transform: translateY(20px); }
  to { opacity: 1; transform: translateY(0); }
}

.animate-fade-in {
  animation: fade-in 0.6s ease-out;
}

// ⚠️ ONLY use client component if animation requires JavaScript triggers
'use client';

import { useState, useEffect } from 'react';

export function ScrollTriggeredAnimation() {
  const [isVisible, setIsVisible] = useState(false);

  useEffect(() => {
    // Intersection Observer requires browser API
    const observer = new IntersectionObserver((entries) => {
      setIsVisible(entries[0].isIntersecting);
    });
    // ...
  }, []);

  return <div className={isVisible ? 'animate-fade-in' : ''}>Content</div>;
}
```

### SEO Optimization Strategy

**Every component should consider SEO:**

1. **Server-Side Rendering**: All SEO-critical content must be server-rendered
2. **Semantic HTML**: Use proper HTML5 semantic elements
3. **Metadata**: Generate metadata on server (never client-side)
4. **Structured Data**: Add JSON-LD on server
5. **Performance**: Minimize client-side JavaScript for faster initial load

```typescript
// ✅ GOOD: SEO-optimized Server Component
export default async function BlogPost({ params }: { params: Promise<{ slug: string }> }) {
  const { slug } = await params;
  const post = await getPost(slug); // Server-side fetch

  return (
    <article>
      <h1>{post.title}</h1>
      <time dateTime={post.date}>{post.date}</time>
      <div dangerouslySetInnerHTML={{ __html: post.content }} />
      {/* All content server-rendered for SEO */}
    </article>
  );
}
```

### Performance Benefits of Server Components

- **Reduced JavaScript Bundle**: Less code sent to client
- **Faster Initial Load**: Content rendered on server
- **Better SEO**: Search engines can crawl server-rendered content
- **Improved Core Web Vitals**: Better LCP, FID, CLS scores
- **Lower Server Costs**: Less client-side processing

### Migration Checklist

When reviewing code, ask:
1. ✅ Can this be a Server Component?
2. ✅ Can interactivity be isolated to a small Client Component?
3. ✅ Can CSS handle the animation instead of JavaScript?
4. ✅ Is the data fetching happening on the server?
5. ✅ Is SEO-critical content server-rendered?

---

## ⚡ Code Optimization

### Performance Best Practices

#### React Optimization

- **Memoization**: Use `React.memo()` for expensive components, `useMemo()` for expensive calculations, `useCallback()` for stable function references.
- **Code Splitting**: Use dynamic imports (`next/dynamic`) for heavy components.
- **Lazy Loading**: Lazy load routes and components that aren't immediately needed.
- **Avoid Unnecessary Re-renders**: Optimize dependencies in hooks, avoid creating objects/arrays in render.

```typescript
// ✅ GOOD: Memoized expensive component
const ExpensiveComponent = React.memo(function ExpensiveComponent({ data }: Props) {
  const processed = useMemo(() => {
    return expensiveCalculation(data);
  }, [data]);

  return <div>{processed}</div>;
});

// ✅ GOOD: Stable callback reference
function Parent({ items }: { items: Item[] }) {
  const handleClick = useCallback((id: string) => {
    // Handle click
  }, []);

  return items.map(item => (
    <Child key={item.id} item={item} onClick={handleClick} />
  ));
}

// ✅ GOOD: Dynamic import for code splitting
const HeavyComponent = dynamic(() => import('./HeavyComponent'), {
  loading: () => <Spinner />,
  ssr: false,
});
```

#### Next.js Optimization

- **Server Components**: Prefer Server Components; use Client Components only when necessary.
- **Static Generation**: Use `generateStaticParams` and static generation where possible.
- **Image Optimization**: Always use `next/image` for images.
- **Font Optimization**: Use `next/font` for custom fonts.
- **API Route Optimization**: Implement proper caching headers, use edge runtime when appropriate.

```typescript
// ✅ GOOD: Server Component by default
export default async function Page() {
  const data = await fetchData(); // Runs on server

  return (
    <div>
      <h1>{data.title}</h1>
      <ClientInteractiveComponent data={data} />
    </div>
  );
}

// ✅ GOOD: Static generation with ISR
export async function generateStaticParams() {
  return [{ id: '1' }, { id: '2' }];
}

export const revalidate = 3600; // ISR: revalidate every hour
```

#### JavaScript/TypeScript Optimization

- **Avoid Premature Optimization**: Profile first, optimize bottlenecks.
- **Efficient Algorithms**: Use appropriate data structures (Map vs Object, Set vs Array).
- **Debounce/Throttle**: Use for expensive operations (search, scroll handlers).
- **Batch Operations**: Batch state updates and API calls when possible.

```typescript
// ✅ GOOD: Efficient data structure usage
const userMap = new Map(users.map(u => [u.id, u])); // O(1) lookup
const uniqueIds = new Set(ids); // O(1) membership check

// ✅ GOOD: Debounced search
const debouncedSearch = useMemo(
  () => debounce((query: string) => {
    searchAPI(query);
  }, 300),
  []
);
```

### Bundle Size Optimization

- **Tree Shaking**: Use ES modules, avoid default exports for utilities.
- **Remove Unused Code**: Regularly audit and remove dead code.
- **Analyze Bundle**: Use `@next/bundle-analyzer` to identify large dependencies.
- **Optimize Imports**: Import only what you need from libraries.

```typescript
// ❌ BAD: Imports entire library
import _ from 'lodash';
const result = _.debounce(fn, 300);

// ✅ GOOD: Imports only needed function
import debounce from 'lodash/debounce';
const result = debounce(fn, 300);
```

### Database & API Optimization

- **Pagination**: Always paginate large datasets.
- **Indexing**: Ensure proper database indexes for query patterns.
- **Caching**: Implement caching for expensive operations (Redis, React Query).
- **Request Batching**: Batch multiple requests when possible.
- **Connection Pooling**: Use connection pooling for database connections.

---

## 🔍 SEO (Search Engine Optimization)

### Core SEO Principles

- **Metadata**: Every page must have unique, descriptive metadata (title, description, keywords).
- **Structured Data**: Use JSON-LD structured data for rich snippets (Organization, WebSite, BreadcrumbList, etc.).
- **Semantic HTML**: Use proper HTML5 semantic elements (`<main>`, `<article>`, `<section>`, `<nav>`, `<header>`, `<footer>`).
- **Performance**: Fast page loads improve SEO rankings (Core Web Vitals).
- **Mobile-First**: Ensure responsive design and mobile optimization.
- **Accessibility**: Accessible sites rank better (ARIA labels, alt text, proper heading hierarchy).

### Next.js SEO Implementation

#### Metadata API

Use Next.js 13+ Metadata API for all pages:

```typescript
// ✅ GOOD: Page-specific metadata
import type { Metadata } from 'next';
import { generateMetadata } from '@/app/lib/seo';

export const metadata: Metadata = generateMetadata({
  title: 'About Us',
  description: 'Learn more about OneDrops and our mission.',
  keywords: ['about', 'company', 'onedrops'],
  path: '/about',
  ogImage: '/og-about.jpg',
});
```

#### Structured Data (JSON-LD)

Add structured data for better search engine understanding:

```typescript
// ✅ GOOD: Structured data for Organization
import { generateStructuredData } from '@/app/lib/seo';
import Script from 'next/script';

const organizationSchema = generateStructuredData('Organization', {
  name: 'OneDrops',
  url: 'https://onedrops.com',
  logo: 'https://onedrops.com/logo.png',
  contactPoint: {
    '@type': 'ContactPoint',
    telephone: '+1-555-123-4567',
    contactType: 'customer service',
  },
});

<Script
  id="organization-schema"
  type="application/ld+json"
  dangerouslySetInnerHTML={{ __html: organizationSchema }}
/>
```

### SEO Best Practices

#### Title Tags

- **Length**: 50-60 characters (optimal for search results)
- **Uniqueness**: Each page must have a unique title
- **Format**: `Page Title | Site Name`
- **Keywords**: Include primary keyword near the beginning

```typescript
// ✅ GOOD: Descriptive, keyword-rich title
title: 'Home'
// Renders as: "Home | OneDrops"

// ❌ BAD: Too generic
title: 'Page'
```

#### Meta Descriptions

- **Length**: 150-160 characters (optimal for search snippets)
- **Uniqueness**: Each page must have a unique description
- **Call-to-Action**: Include a clear CTA when appropriate
- **Keywords**: Naturally include relevant keywords

```typescript
// ✅ GOOD: Compelling, keyword-rich description
description: 'Welcome to OneDrops - A modern, performant landing page built with Next.js. Discover our features and get started today.'

// ❌ BAD: Too short or generic
description: 'OneDrops website'
```

#### Open Graph & Twitter Cards

Always include Open Graph and Twitter Card metadata for social sharing:

```typescript
// ✅ GOOD: Complete OG and Twitter metadata
openGraph: {
  type: 'website',
  locale: 'en_US',
  url: 'https://onedrops.com',
  siteName: 'OneDrops',
  title: 'OneDrops - Landing Page',
  description: 'A modern landing page...',
  images: [
    {
      url: '/og-image.jpg',
      width: 1200,
      height: 630,
      alt: 'OneDrops Landing Page',
    },
  ],
},
twitter: {
  card: 'summary_large_image',
  title: 'OneDrops - Landing Page',
  description: 'A modern landing page...',
  images: ['/og-image.jpg'],
  creator: '@onedrops',
},
```

#### Canonical URLs

Always set canonical URLs to prevent duplicate content issues:

```typescript
// ✅ GOOD: Canonical URL for each page
alternates: {
  canonical: 'https://onedrops.com/about',
},
```

#### Robots Meta

Control search engine crawling:

```typescript
// ✅ GOOD: Allow indexing (default)
robots: {
  index: true,
  follow: true,
  googleBot: {
    index: true,
    follow: true,
    'max-video-preview': -1,
    'max-image-preview': 'large',
    'max-snippet': -1,
  },
},

// For private/admin pages
robots: {
  index: false,
  follow: false,
},
```

### SEO Utilities

Use the provided SEO utilities for consistency:

```typescript
// app/lib/seo.ts
import { generateMetadata, generateStructuredData } from '@/app/lib/seo';

// Generate page metadata
export const metadata = generateMetadata({
  title: 'Page Name',
  description: 'Page description',
  keywords: ['keyword1', 'keyword2'],
  path: '/page-path',
});

// Generate structured data
const schema = generateStructuredData('Article', {
  headline: 'Article Title',
  author: { '@type': 'Person', name: 'Author Name' },
});
```

### Image SEO

- **Alt Text**: Always include descriptive alt text for images
- **File Names**: Use descriptive, keyword-rich file names
- **Optimization**: Use Next.js Image component for automatic optimization
- **Lazy Loading**: Use lazy loading for below-the-fold images

```typescript
// ✅ GOOD: Optimized image with alt text
<Image
  src="/hero-image.jpg"
  alt="OneDrops landing page hero section showing modern design"
  width={1200}
  height={630}
  priority // For above-the-fold images
/>

// ❌ BAD: Missing alt text
<Image src="/image.jpg" width={1200} height={630} />
```

### Performance & Core Web Vitals

- **LCP (Largest Contentful Paint)**: < 2.5 seconds
- **FID (First Input Delay)**: < 100 milliseconds
- **CLS (Cumulative Layout Shift)**: < 0.1
- **TTFB (Time to First Byte)**: < 600 milliseconds

Optimize for Core Web Vitals:
- Use Next.js Image optimization
- Implement code splitting
- Minimize JavaScript bundle size
- Use font optimization (`next/font`)
- Implement proper caching strategies

### SEO Checklist

Before deploying, ensure:

- [ ] Unique title and description for each page
- [ ] Open Graph and Twitter Card metadata included
- [ ] Canonical URLs set for all pages
- [ ] Structured data (JSON-LD) added where appropriate
- [ ] All images have descriptive alt text
- [ ] Semantic HTML structure used
- [ ] Mobile-responsive design
- [ ] Fast page load times (Core Web Vitals)
- [ ] Proper heading hierarchy (h1 → h2 → h3)
- [ ] Internal linking structure in place
- [ ] robots.txt configured (if needed)
- [ ] sitemap.xml generated (if needed)

---

## 🏗️ Architecture & Code Organization

### File Structure

```
app/                    # Next.js App Router
├── (routes)/          # Route groups
├── api/               # API routes
├── components/        # Shared components
│   ├── ui/           # Base UI components
│   └── features/     # Feature-specific components
├── hooks/             # Custom React hooks
├── lib/               # Utility libraries
├── services/          # Business logic services
├── types/             # TypeScript types
├── constants/         # Constants
└── styles/            # Global styles
```

### Naming Conventions

- **Components**: PascalCase (`UserProfile.tsx`)
- **Hooks**: camelCase starting with `use` (`useAuth.ts`)
- **Utilities**: camelCase (`formatDate.ts`)
- **Constants**: UPPER_SNAKE_CASE (`API_BASE_URL.ts`)
- **Types/Interfaces**: PascalCase (`User.ts`, `ApiResponse.ts`)
- **Files**: Match export name (default export should match filename)

### Component Organization: When to Split into Folders

**Guideline**: When a component becomes large (typically >300 lines) and consists of multiple related sub-components or sections, consider splitting it into a folder structure with multiple smaller component files.

#### Decision Criteria

**✅ Split into folder when:**
- Component exceeds 300-500 lines
- Component has multiple distinct sub-components (e.g., Header, Body, Footer sections)
- Component has complex internal state management that can be isolated
- Component has multiple related utility functions or types
- Component has multiple variants or configurations that warrant separate files
- Component is reused across multiple contexts with different configurations

**❌ Keep as single file when:**
- Component is simple and under 400 lines
- Component is a single, cohesive unit without distinct sub-sections
- Splitting would create unnecessary complexity
- Component is rarely used or unlikely to grow
- Related files would be fewer than 3-4 files

#### Folder Structure Pattern

When splitting a component, use this structure:

```
components/
├── features/
│   └── navigation/
│       ├── Footer/
│       │   ├── index.tsx          # Main component (exports default)
│       │   ├── FooterHeader.tsx   # Sub-component
│       │   ├── FooterNewsletter.tsx
│       │   ├── FooterNavigation.tsx
│       │   ├── FooterLegal.tsx
│       │   ├── types.ts           # Component-specific types
│       │   └── constants.ts       # Component-specific constants
│       └── Navbar.tsx              # Simple component (single file)
```

#### Implementation Pattern

```typescript
// ✅ GOOD: Large component split into folder
// components/features/navigation/Footer/index.tsx
import FooterHeader from './FooterHeader';
import FooterNewsletter from './FooterNewsletter';
import FooterNavigation from './FooterNavigation';
import FooterLegal from './FooterLegal';
import type { FooterProps } from './types';

export default function Footer({ locale }: FooterProps) {
  return (
    <footer>
      <FooterHeader />
      <FooterNewsletter />
      <FooterNavigation />
      <FooterLegal />
    </footer>
  );
}

// components/features/navigation/Footer/FooterHeader.tsx
export default function FooterHeader() {
  return (
    <div>
      {/* Header content */}
    </div>
  );
}

// components/features/navigation/Footer/types.ts
export interface FooterProps {
  locale: string;
}

// ❌ BAD: Simple component unnecessarily split
// components/ui/Button/Button.tsx
// components/ui/Button/index.tsx
// components/ui/Button/types.ts
// (Button is simple enough to be a single file)
```

#### Best Practices

1. **Check Existing Structure First**: Before splitting, review the codebase to see if similar components are split or kept as single files
2. **Consistent Organization**: Follow existing patterns in the codebase
3. **Logical Grouping**: Group related sub-components, types, and utilities together
4. **Clear Naming**: Use descriptive names for sub-components (e.g., `FooterHeader` not `Header`)
5. **Index File**: Always use `index.tsx` as the main entry point for the component
6. **Avoid Over-Splitting**: Don't create folders for components that are simple and cohesive
7. **Shared Types/Utils**: If types or utilities are shared across multiple components, keep them at a higher level (e.g., `types/` or `lib/`)

#### Example: When to Split

```typescript
// ✅ GOOD: Split complex component
// Before: Footer.tsx (500+ lines with multiple sections)
// After:
// Footer/
//   ├── index.tsx (main component, ~50 lines)
//   ├── FooterHeader.tsx (~100 lines)
//   ├── FooterNewsletter.tsx (~150 lines)
//   ├── FooterNavigation.tsx (~100 lines)
//   └── FooterLegal.tsx (~100 lines)

// ✅ GOOD: Keep simple component as single file
// Button.tsx (150 lines - simple, cohesive component)
// No need to split into Button/Button.tsx, Button/types.ts, etc.

// ✅ GOOD: Split component with multiple variants
// ProductCard/
//   ├── index.tsx
//   ├── ProductCardStandard.tsx
//   ├── ProductCardFeatured.tsx
//   ├── ProductCardCompact.tsx
//   └── types.ts
```

#### Migration Checklist

When considering splitting a component:

1. ✅ Component exceeds 300-400 lines
2. ✅ Component has distinct, reusable sub-sections
3. ✅ Similar components in codebase are already split
4. ✅ Splitting improves maintainability and readability
5. ✅ Sub-components can be clearly named and organized
6. ✅ Types/utilities are component-specific (not shared widely)

**Remember**: The goal is improved maintainability and organization, not just file count. If splitting doesn't improve code clarity, keep it as a single file.

### Compound Component Pattern: When to Use

**Guideline**: The compound component pattern should only be used when sub-components need to be exposed to parent components for flexible composition. If sub-components are only used internally, use regular components instead.

#### Decision Criteria

**✅ Use Compound Components when:**
- Sub-components are used by **parent components** (external composition)
- You need flexible composition where parents can rearrange or customize sub-components
- The component is a reusable library component that needs maximum flexibility
- Sub-components are exported and used in different contexts

**❌ Don't use Compound Components when:**
- Sub-components are **only used internally** within the same file
- The component structure is fixed and never changes
- You're just organizing code internally (use regular helper components instead)
- The pattern adds complexity without providing flexibility

#### Examples

```typescript
// ✅ GOOD: Compound component with external usage
// app/components/home/PlansAndPricing.tsx
function PricingCard({ children, isStandard }: PricingCardProps) {
  return <div className={...}>{children}</div>;
}

function Tag({ tag }: { tag: string }) { ... }
function Title({ children }: { children: ReactNode }) { ... }
function Features({ features }: { features: string[] }) { ... }

PricingCard.Tag = Tag;
PricingCard.Title = Title;
PricingCard.Features = Features;

// Used externally by parent:
<PricingCard isStandard={isStandard}>
  <PricingCard.Tag tag={tag} />
  <PricingCard.Title>{title}</PricingCard.Title>
  <PricingCard.Features features={features} />
</PricingCard>

// ❌ BAD: Compound component with only internal usage
// Sub-components never used outside the file
function ProjectCard({ title, image }: ProjectCardProps) {
  return (
    <div>
      <ProjectCard.Image image={image} title={title} />
      <ProjectCard.Overlay />
      <ProjectCard.Content>
        <ProjectCard.Title>{title}</ProjectCard.Title>
      </ProjectCard.Content>
    </div>
  );
}

// ✅ BETTER: Regular internal components
function ProjectCard({ title, image }: ProjectCardProps) {
  return (
    <div>
      <ImageComponent image={image} title={title} />
      <Overlay />
      <Content>
        <Title>{title}</Title>
      </Content>
    </div>
  );
}

function ImageComponent({ image, title }: ImageProps) { ... }
function Overlay() { ... }
function Content({ children }: ContentProps) { ... }
function Title({ children }: TitleProps) { ... }
```

#### Benefits of Avoiding Unnecessary Compound Components

1. **Simpler Code**: No need for type definitions like `Component.SubComponent = SubComponent`
2. **Easier to Understand**: Direct function calls instead of property access
3. **Less Type Complexity**: No need for compound component type definitions
4. **Better Performance**: Slightly faster (no property lookups)
5. **Clearer Intent**: Regular components clearly indicate internal-only usage

#### Migration Checklist

When reviewing compound components:

1. ✅ Check if sub-components are used outside the file
2. ✅ If only used internally, convert to regular components
3. ✅ Remove compound component type definitions
4. ✅ Update imports if sub-components were exported
5. ✅ Simplify component usage (remove `.SubComponent` syntax)

**Remember**: Compound components are a powerful pattern for flexibility, but only use them when that flexibility is actually needed. For internal organization, regular components are simpler and clearer.

### Type Organization

**Guideline**: Types should be organized based on their scope and usage, following a clear hierarchy.

#### Type File Organization Rules

**✅ Types Folder (`app/types/`):**
- **Shared/Compound Types**: Compound component types, shared data types, and types used across multiple components
- **Domain Types**: Types that represent domain entities (e.g., `User`, `Product`, `Service`)
- **API Types**: Request/response types for API endpoints
- **Global Types**: Types used throughout the application

**✅ Component Files (at the end of file):**
- **Component Prop Types**: All prop types for sub-components should be defined at the end of the component file
- **Component-Specific Types**: Types that are only used within a single component or its sub-components
- **Local Types**: Types that are tightly coupled to a specific component implementation

#### Type Naming and Location Rules

1. **Component-Specific Types**: Types related to a component (including Client components) should be in the main type file for that feature, NOT in separate `*-client.ts` files
   - ✅ `grow-your-business.ts` - Contains all types for GrowYourBusiness (including `GrowYourBusinessClientProps`)
   - ❌ `grow-your-business-client.ts` - Separate file for client types

2. **Prop Types Location**:
   - Main component props → Types folder (`app/types/home/grow-your-business.ts`)
   - Sub-component prop types → Component file (at the end of `AdditionalServicesRow.tsx`)

3. **Compound Component Types** (only when needed):
   - Compound component type definition → Types folder (only if component uses compound pattern)
   - References prop types from component file (imports them)
   - **Note**: Most components don't need compound component types - only use when sub-components are exposed externally

#### Example Structure

```typescript
// ✅ GOOD: All types in main type file
// app/types/home/grow-your-business.ts
export type GrowYourBusinessProps = { locale: string };
export type GrowYourBusinessClientProps = { /* ... */ };
export type SlideshowContent = { /* ... */ };
export type SlideshowProps = { /* ... */ };

// ✅ GOOD: Sub-component prop types in component file
// app/components/home/AdditionalServices/AdditionalServicesRow.tsx
export type AdditionalServicesRowTextContentProps = { /* ... */ };
export type AdditionalServicesRowBadgeProps = { /* ... */ };

// ✅ GOOD: Compound component type in types folder (only when needed)
// app/types/home/plans-and-pricing.ts
import type { PricingCardTagProps, PricingCardTitleProps } from '@/app/components/...';
export type PricingCardComponent = (/* ... */) & {
  Tag: (props: PricingCardTagProps) => ReactElement;
  Title: (props: PricingCardTitleProps) => ReactElement;
  // Only define if sub-components are used externally
};
```

#### Type Documentation Requirements

**All exported types MUST have JSDoc documentation:**

```typescript
// ✅ GOOD: Fully documented type
/**
 * Props for the GrowYourBusiness component.
 *
 * @typedef {Object} GrowYourBusinessProps
 * @property {string} locale - Current locale code ('en' | 'ja')
 */
export type GrowYourBusinessProps = {
  locale: string;
};

/**
 * Content data for a single slideshow slide.
 *
 * @typedef {Object} SlideshowContent
 * @property {string} title - Slide title text
 * @property {React.ReactNode} description - Slide description (supports rich text)
 * @property {string} image - Image URL or path for the slide
 */
export type SlideshowContent = {
  title: string;
  description: React.ReactNode;
  image: string;
};

// ❌ BAD: Missing JSDoc documentation
export type GrowYourBusinessProps = {
  locale: string;
};
```

**Documentation Checklist for Types:**
- ✅ JSDoc comment with description
- ✅ `@typedef` tag for type aliases
- ✅ `@interface` tag for interfaces
- ✅ `@property` tags for all properties
- ✅ `@template` tags for generic types
- ✅ `@example` for complex types (optional but recommended)
- ✅ Document optional properties with `[propertyName]` notation
- ✅ Explain default values when applicable

#### Migration Checklist

When organizing types:

1. ✅ All types for a feature are in the main type file (not split into `*-client.ts`)
2. ✅ Sub-component prop types are at the end of the component file
3. ✅ Compound component types are in the types folder
4. ✅ **All exported types have JSDoc documentation** (REQUIRED)
5. ✅ Types are properly exported
6. ✅ No circular dependencies between type files

**Remember**: Keep related types together. If types are related to the same feature/component, they belong in the same file, regardless of whether they're used by client or server components. **Always document exported types with JSDoc comments.**

### Tailwind CSS Styling Best Practices

**Guideline**: Always prefer Tailwind utility classes over inline styles. Before using inline styles, check the [Tailwind CSS documentation](https://tailwindcss.com/docs) to see if there's a utility class or a way to extend Tailwind to support your needs.

#### Rule: Check Tailwind Docs Before Using Inline Styles

**✅ ALWAYS do this:**
1. **Check Tailwind Documentation First**: Before using inline `style={{}}` props, check the [Tailwind CSS documentation](https://tailwindcss.com/docs) to see if:
   - A utility class already exists for what you need
   - You can use arbitrary values (e.g., `font-[var(--font-exo)]`)
   - You can add a custom utility using `@utility` directive
   - You can customize the theme using `@theme` directive

2. **Use Tailwind Utilities**: Prefer Tailwind utility classes over inline styles
   ```tsx
   // ✅ GOOD: Using Tailwind utility class
   <h3 className="font-exo text-2xl">Title</h3>

   // ❌ BAD: Using inline styles
   <h3 style={{ fontFamily: 'var(--font-exo), sans-serif' }}>Title</h3>
   ```

3. **Add Custom Utilities When Needed**: If you need a custom style that's used multiple times, add it as a custom utility in `globals.css`:
   ```css
   /* ✅ GOOD: Custom utility for reusable font family */
   @utility font-exo {
     font-family: var(--font-exo), sans-serif;
   }
   ```

4. **Use Arbitrary Values for One-Off Cases**: For one-time use cases, use Tailwind's arbitrary value syntax:
   ```tsx
   // ✅ GOOD: Arbitrary value for one-off case
   <div className="top-[117px]">Content</div>

   // ❌ BAD: Inline style for one-off case
   <div style={{ top: '117px' }}>Content</div>
   ```

#### Resources

- **Tailwind CSS Documentation**: [https://tailwindcss.com/docs](https://tailwindcss.com/docs)
- **Adding Custom Styles**: [https://tailwindcss.com/docs/adding-custom-styles](https://tailwindcss.com/docs/adding-custom-styles)
- **Arbitrary Values**: Use square bracket notation `[value]` for arbitrary values
- **Custom Utilities**: Use `@utility` directive in `globals.css` for reusable custom styles
- **Theme Customization**: Use `@theme` directive to customize design tokens

#### Common Patterns

**Font Families:**
```tsx
// ✅ GOOD: Custom utility class
<div className="font-exo">Text</div>

// ✅ GOOD: Arbitrary value (if utility doesn't exist)
<div className="font-[var(--font-exo)]">Text</div>

// ❌ BAD: Inline style
<div style={{ fontFamily: 'var(--font-exo)' }}>Text</div>
```

**CSS Variables:**
```tsx
// ✅ GOOD: Arbitrary value with CSS variable
<div className="bg-[var(--my-color)]">Content</div>

// ✅ GOOD: Custom property syntax (Tailwind v4)
<div className="fill-(--my-brand-color)">Content</div>

// ❌ BAD: Inline style
<div style={{ backgroundColor: 'var(--my-color)' }}>Content</div>
```

**Arbitrary Properties:**
```tsx
// ✅ GOOD: Arbitrary property
<div className="[mask-type:luminance]">Content</div>

// ❌ BAD: Inline style
<div style={{ maskType: 'luminance' }}>Content</div>
```

#### When Inline Styles Are Acceptable

**⚠️ ONLY use inline styles when:**
- The style is dynamically calculated and cannot be expressed as a Tailwind class
- The style is a one-time override that doesn't warrant a custom utility
- The style is for a third-party library that requires inline styles
- The style is for CSS-in-JS libraries that require inline styles

**Example of acceptable inline style:**
```tsx
// ✅ ACCEPTABLE: Dynamically calculated style
<div style={{ transform: `translateX(${scrollPosition}px)` }}>
  Content
</div>
```

#### Migration Checklist

When refactoring inline styles:

1. ✅ Check Tailwind docs for existing utility class
2. ✅ Check if arbitrary value syntax can be used
3. ✅ If used multiple times, create custom utility in `globals.css`
4. ✅ Update all instances to use the new utility class
5. ✅ Remove inline `style` props
6. ✅ Test to ensure styling still works correctly

**Remember**: Tailwind is designed to be extensible. If you need something that doesn't exist, extend Tailwind rather than breaking out to inline styles.

### Import Organization

```typescript
// 1. External dependencies
import React from 'react';
import { NextRequest } from 'next/server';
import { z } from 'zod';

// 2. Internal absolute imports (@/)
import { Button } from '@/components/ui/button';
import { useAuth } from '@/hooks/useAuth';
import { formatDate } from '@/lib/utils';

// 3. Relative imports
import { UserCard } from './UserCard';
import { userService } from '../services/userService';

// 4. Type imports (separate)
import type { User } from '@/types/user';
```

---

## 🌐 Internationalization (i18n) Best Practices

This project uses [next-intl](https://next-intl-docs.vercel.app/) for internationalization with support for Japanese and English.

### Core Principles

- **Route-based i18n**: Locales are part of the URL structure (`/en`, `/ja`)
- **Server Components First**: Use `getTranslations` in Server Components for optimal performance
- **Client Components**: Use `useTranslations` hook only when necessary (interactivity)
- **SEO Optimization**: Always include hreflang tags and alternate language links
- **Type Safety**: Use TypeScript types for translation keys when possible

### Translation File Structure

Translation files are located in `messages/` directory:

```json
// messages/en.json
{
  "metadata": {
    "title": "Home",
    "description": "Welcome to OneDrops..."
  },
  "common": {
    "welcome": "Welcome",
    "home": "Home"
  },
  "features": {
    "title": "Features",
    "description": "Our amazing features"
  }
}
```

### Using Translations in Server Components

```typescript
// ✅ GOOD: Server Component with translations
import { getTranslations } from 'next-intl/server';

export default async function HomePage({
  params,
}: {
  params: Promise<{ locale: string }>;
}) {
  const { locale } = await params;
  const t = await getTranslations({ locale, namespace: 'common' });

  return (
    <div>
      <h1>{t('welcome')}</h1>
      <p>{t('description')}</p>
    </div>
  );
}
```

### Using Translations in Client Components

```typescript
// ✅ GOOD: Client Component with translations
'use client';

import { useTranslations } from 'next-intl';

export function Button() {
  const t = useTranslations('common');

  return <button>{t('submit')}</button>;
}
```

### SEO for Internationalized Pages

Always include locale-specific metadata with hreflang tags:

```typescript
// ✅ GOOD: Locale-aware metadata with hreflang
export async function generateMetadata({
  params,
}: {
  params: Promise<{ locale: string }>;
}): Promise<Metadata> {
  const { locale } = await params;
  const t = await getTranslations({ locale, namespace: 'metadata' });
  const baseUrl = process.env.NEXT_PUBLIC_SITE_URL || 'https://onedrops.com';
  const localePath = locale === 'en' ? '' : `/${locale}`;

  return {
    title: t('title'),
    description: t('description'),
    alternates: {
      canonical: `${baseUrl}${localePath}`,
      languages: {
        en: `${baseUrl}`,
        ja: `${baseUrl}/ja`,
        'x-default': `${baseUrl}`, // Default to English
      },
    },
    openGraph: {
      locale: locale === 'en' ? 'en_US' : 'ja_JP',
      alternateLocale: locale === 'en' ? 'ja_JP' : 'en_US',
      // ... other OG properties
    },
  };
}
```

### Translation Key Naming Conventions

- **Namespaces**: Group related translations (e.g., `common`, `metadata`, `features`)
- **Nested Keys**: Use dot notation for hierarchical organization
- **Descriptive Names**: Use clear, descriptive keys (e.g., `welcomeMessage` not `msg1`)

```json
// ✅ GOOD: Clear, organized structure
{
  "common": {
    "buttons": {
      "submit": "Submit",
      "cancel": "Cancel"
    },
    "messages": {
      "success": "Operation successful",
      "error": "An error occurred"
    }
  }
}
```

### Adding New Locales

1. **Update `i18n.ts`**:
```typescript
export const locales = ['en', 'ja', 'ko'] as const; // Add new locale
```

2. **Create translation file**: `messages/ko.json`
3. **Update middleware matcher** if needed
4. **Update SEO alternate languages** in all page metadata

### Best Practices

- **Always provide translations**: Never leave translation keys empty
- **Consistent structure**: Keep the same structure across all locale files
- **Context-aware translations**: Consider cultural context, not just literal translation
- **Pluralization**: Use next-intl's pluralization features for proper grammar
- **Date/Number formatting**: Use locale-aware formatting functions
- **RTL support**: Consider right-to-left languages if needed in the future

```typescript
// ✅ GOOD: Locale-aware date formatting
import { useFormatter } from 'next-intl';

export function DateDisplay({ date }: { date: Date }) {
  const format = useFormatter();
  return <span>{format.dateTime(date, 'short')}</span>;
}
```

### Common Pitfalls

- **❌ BAD**: Hardcoding text instead of using translations
- **❌ BAD**: Missing hreflang tags in metadata
- **❌ BAD**: Inconsistent translation key structure across locales
- **❌ BAD**: Using `useTranslations` in Server Components (use `getTranslations` instead)
- **❌ BAD**: Not providing `x-default` hreflang tag

---

## 🎨 Icons & Assets

### Icon Management

All icons should be stored in `app/assets/icons/` as React components. When an icon is needed but doesn't exist in the project, use high-quality SVG icons from [SVG Repo](https://www.svgrepo.com/).

### Icon Component Structure

Icons should be created as React components with the following structure:

```typescript
import React, { SVGProps } from 'react';
import type { ReactElement } from 'react';

/**
 * Icon name component.
 *
 * @param {SVGProps<SVGSVGElement>} props - SVG element props
 * @returns {ReactElement} Icon component
 */
export default function IconName({
  className,
  width = '16',
  height = '16',
  ...props
}: SVGProps<SVGSVGElement>): ReactElement {
  return (
    <svg
      width={width}
      height={height}
      viewBox="0 0 24 24"
      fill="none"
      xmlns="http://www.w3.org/2000/svg"
      className={className}
      {...props}
    >
      {/* SVG paths from SVG Repo */}
    </svg>
  );
}
```

### Finding Icons on SVG Repo

**When an icon is not found in the project:**

1. **Search SVG Repo**: Visit [https://www.svgrepo.com/](https://www.svgrepo.com/) and search for the icon you need
2. **Select High-Quality Icon**: Choose icons that are:
   - Clean and simple
   - Well-designed
   - Consistent with the project's style
   - Open-licensed (check license on SVG Repo)
3. **Copy SVG Code**: Copy the SVG path data from the icon page
4. **Create Icon Component**: Create a new file in `app/assets/icons/` following the structure above
5. **Use Standard ViewBox**: Prefer `viewBox="0 0 24 24"` for consistency and scalability
6. **Use CurrentColor**: Use `stroke="currentColor"` or `fill="currentColor"` to allow color customization via CSS

### Icon Naming Conventions

- **File Names**: PascalCase matching the icon name (e.g., `Mail.tsx`, `ArrowRight.tsx`, `ChevronDown.tsx`)
- **Component Names**: Match the file name (e.g., `Mail`, `ArrowRight`, `ChevronDown`)
- **Descriptive Names**: Use clear, descriptive names (e.g., `Handshake` not `Icon1`)

### Using Icons in Components

```typescript
// ✅ GOOD: Import and use icon component
import MailIcon from '@/app/assets/icons/Mail';
import ArrowRightIcon from '@/app/assets/icons/ArrowRight';

export default function Footer() {
  return (
    <div>
      <MailIcon className="w-4 h-4" />
      <ArrowRightIcon width="20" height="20" />
    </div>
  );
}
```

### Icon Best Practices

- **Consistent Style**: Use icons from the same icon set or similar style when possible
- **Appropriate Size**: Set appropriate default width/height (typically 16-24px)
- **Accessibility**: Icons used for interactive elements should have proper `aria-label` attributes
- **Color Flexibility**: Use `currentColor` to allow icons to inherit text color
- **Scalability**: Use vector SVG format for crisp rendering at any size

### Common Icon Sources

- **SVG Repo**: [https://www.svgrepo.com/](https://www.svgrepo.com/) - Primary source for icons
- **Existing Icons**: Check `app/assets/icons/` first before creating new ones
- **Reusability**: Reuse existing icons when possible rather than creating duplicates

### Example: Adding a New Icon

```typescript
// 1. Search SVG Repo for "handshake" icon
// 2. Copy the SVG path data
// 3. Create app/assets/icons/Handshake.tsx

import React, { SVGProps } from 'react';
import type { ReactElement } from 'react';

export default function Handshake({
  className,
  width = '16',
  height = '16',
  ...props
}: SVGProps<SVGSVGElement>): ReactElement {
  return (
    <svg
      width={width}
      height={height}
      viewBox="0 0 24 24"
      fill="none"
      xmlns="http://www.w3.org/2000/svg"
      className={className}
      {...props}
    >
      <path
        d="M11.5 2C10.5 2 9.5 2.5 9 3.5L7.5 6.5C7.2 7.1 6.5 7.5 5.8 7.5H3C2.4 7.5 2 7.9 2 8.5V12C2 12.6 2.4 13 3 13H5.8C6.5 13 7.2 13.4 7.5 14L9 17C9.5 18 10.5 18.5 11.5 18.5C12.1 18.5 12.7 18.3 13.2 17.8L16.2 14.8C16.7 14.3 17.3 14 18 14H21C21.6 14 22 13.6 22 13V9.5C22 8.9 21.6 8.5 21 8.5H18C17.3 8.5 16.7 8.2 16.2 7.7L13.2 4.7C12.7 4.2 12.1 4 11.5 4V2Z"
        stroke="currentColor"
        strokeWidth="1.5"
        strokeLinecap="round"
        strokeLinejoin="round"
      />
    </svg>
  );
}

// 4. Import and use in components
import HandshakeIcon from '@/app/assets/icons/Handshake';
```

---

## 🔒 TypeScript Best Practices

- **Strict Mode**: Always use TypeScript strict mode.
- **Explicit Types**: Prefer explicit types over inference for function parameters and returns.
- **Avoid `any`**: Use `unknown` and type guards instead of `any`.
- **Type Guards**: Use type guards for runtime type checking.
- **Discriminated Unions**: Use discriminated unions for state management.

```typescript
// ✅ GOOD: Explicit types and type guards
function isUser(value: unknown): value is User {
  return (
    typeof value === 'object' &&
    value !== null &&
    'id' in value &&
    'email' in value
  );
}

function processUser(data: unknown): User {
  if (!isUser(data)) {
    throw new Error('Invalid user data');
  }
  return data;
}

// ✅ GOOD: Discriminated union
type ApiResponse<T> =
  | { status: 'success'; data: T }
  | { status: 'error'; error: string };
```

---

## 🧪 Testing Guidelines

- **Unit Tests**: Test pure functions and utilities.
- **Component Tests**: Test component behavior, not implementation.
- **Integration Tests**: Test feature workflows.
- **Test Coverage**: Aim for >80% coverage on critical paths.
- **Test Organization**: Mirror source structure in test files.

```typescript
// ✅ GOOD: Test structure
describe('formatDate', () => {
  it('should format date correctly', () => {
    expect(formatDate(new Date('2024-01-01'))).toBe('Jan 1, 2024');
  });

  it('should handle invalid dates', () => {
    expect(() => formatDate(null as any)).toThrow();
  });
});
```

---

## 🚨 Error Handling

- **Error Boundaries**: Use React Error Boundaries for component errors.
- **API Errors**: Handle API errors gracefully with proper error types.
- **Validation**: Validate inputs at boundaries (API routes, form submissions).
- **Logging**: Log errors with context for debugging.

```typescript
// ✅ GOOD: Proper error handling
async function fetchUser(id: string): Promise<User> {
  try {
    const response = await fetch(`/api/users/${id}`);

    if (!response.ok) {
      throw new ApiError(`Failed to fetch user: ${response.statusText}`, response.status);
    }

    const data = await response.json();
    return validateUser(data);
  } catch (error) {
    if (error instanceof ApiError) {
      throw error;
    }
    logger.error('Unexpected error fetching user', { id, error });
    throw new Error('Failed to fetch user');
  }
}
```

---

## 📁 File Handling

- **Ignored Folders**: Excludes `.next`, `node_modules`, `.git`, and `.DS_Store` from workspace.
- **File Size**: Keep files under 300 lines when possible; split large files.
- **File Naming**: Use kebab-case for non-component files, PascalCase for components.

---

## ✅ Code Review Checklist

Before submitting code, ensure:

- [ ] Code follows SRP (single responsibility per function/component/module)
- [ ] All exports have JSDoc documentation (functions, components, classes, types, interfaces)
- [ ] **All exported types, interfaces, and type aliases have JSDoc comments**
- [ ] TypeScript types are explicit and correct
- [ ] No `any` types (use `unknown` with type guards)
- [ ] Performance optimizations applied where needed
- [ ] Error handling implemented
- [ ] Tests written for new functionality
- [ ] Linting passes (`pnpm lint`)
- [ ] Prettier formatting applied
- [ ] No console.logs in production code (use logger)
- [ ] Imports are organized correctly
- [ ] No unused imports or variables

---

## 📝 Commit Message Standards (Conventional Commits)

Follow the [Conventional Commits](https://www.conventionalcommits.org/) specification for all commit messages. This ensures consistent, readable commit history and enables automated tooling.

**Why this matters**: Good commit messages make debugging easier, enable better changelog generation, and help teammates understand what changed. Bad commits like "fix stuff" or "WIP" are useless when you're trying to find when a bug was introduced. Stick to the format, keep it simple, and your future self will thank you.

### Commit Message Format

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Commit Types

Use one of the following types:

- **`feat`**: A new feature
- **`fix`**: A bug fix
- **`docs`**: Documentation only changes
- **`style`**: Changes that do not affect the meaning of the code (white-space, formatting, missing semi-colons, etc.)
- **`refactor`**: A code change that neither fixes a bug nor adds a feature
- **`perf`**: A code change that improves performance
- **`test`**: Adding missing tests or correcting existing tests
- **`build`**: Changes that affect the build system or external dependencies (example scopes: gulp, broccoli, npm)
- **`ci`**: Changes to CI configuration files and scripts
- **`chore`**: Other changes that don't modify src or test files
- **`revert`**: Reverts a previous commit

### Scope (Optional)

The scope should be the name of the package/area affected. Examples:

- `components` - UI components
- `hooks` - React hooks
- `api` - API routes
- `auth` - Authentication
- `types` - TypeScript types
- `utils` - Utility functions
- `config` - Configuration files

### Subject

- Use imperative, present tense: "change" not "changed" nor "changes"
- Don't capitalize the first letter
- No dot (.) at the end
- Maximum 72 characters
- Describe what the commit does, not why or how

### Body (Optional)

- Use imperative, present tense
- Include motivation for the change and contrast with previous behavior
- Wrap at 72 characters
- Separate from subject with a blank line
- Use bullet points (`-`) for multiple related changes
- Each bullet point should start with a capital letter and end without punctuation

### Footer (Optional)

- Reference issue numbers (e.g., `Closes #123`, `Fixes #456`)
- Breaking changes should be indicated with `BREAKING CHANGE:` followed by a description

### Examples

```bash
# Simple commit
feat: add user authentication

# With scope
feat(auth): add JWT token refresh

# With body
fix(api): handle null user responses

Previously, the API would throw an error when receiving null user data.
Now it returns a 404 status code with a proper error message.

# With bullet points for multiple items
chore: initialize project with essential configuration files and structure

- Add ESLint and Prettier configuration
- Set up TypeScript with strict mode
- Configure Next.js with App Router
- Add Tailwind CSS setup
- Create base component structure

# With breaking change
feat(api): change user endpoint response format

BREAKING CHANGE: The user endpoint now returns a nested object structure
instead of a flat structure. Update your API clients accordingly.

Closes #123

# Multi-item feature commit
feat(components): add form components and validation

- Add Input component with error states
- Add Select component with search
- Add Form wrapper with validation
- Add error message display component
```

### Good Commit Messages

```bash
✅ feat(components): add Button component with variants
✅ fix(hooks): prevent memory leak in useAuth hook
✅ docs(readme): update installation instructions
✅ refactor(utils): extract date formatting logic
✅ perf(api): optimize user list query with pagination
✅ test(components): add unit tests for Button component
✅ chore(deps): update Next.js to version 16.0.2
```

### Bad Commit Messages

```bash
❌ "Fixed bug" - Too vague, missing type
❌ "update code" - Missing type, vague subject
❌ "feat: Added new feature" - Past tense, capitalized
❌ "feat: add new feature." - Has period at end
❌ "feat: add new feature that does something really cool and amazing" - Too long
❌ "WIP" - Not descriptive
❌ "fix stuff" - Missing type, vague
```

### Commit Message Best Practices

1. **One Logical Change Per Commit**: Each commit should represent a single logical change.
2. **Write Clear Subjects**: The subject should clearly describe what changed.
3. **Use Body for Context**: If the commit needs explanation, use the body.
4. **Reference Issues**: Link commits to issues/tickets when applicable.
5. **Keep Commits Atomic**: Make small, focused commits rather than large ones.
6. **Review Before Committing**: Review your changes with `git diff` before committing.

### Commitlint Configuration Example

If using commitlint, here's a recommended `.commitlintrc.json`:

```json
{
  "extends": ["@commitlint/config-conventional"],
  "rules": {
    "type-enum": [
      2,
      "always",
      [
        "feat",
        "fix",
        "docs",
        "style",
        "refactor",
        "perf",
        "test",
        "build",
        "ci",
        "chore",
        "revert"
      ]
    ],
    "type-case": [2, "always", "lower-case"],
    "type-empty": [2, "never"],
    "scope-case": [2, "always", "lower-case"],
    "subject-empty": [2, "never"],
    "subject-full-stop": [2, "never", "."],
    "header-max-length": [2, "always", 72],
    "body-leading-blank": [2, "always"],
    "footer-leading-blank": [2, "always"]
  }
}
```

### Pre-commit Hooks

Consider using tools like:
- **Husky** - Git hooks made easy
- **lint-staged** - Run linters on staged files
- **commitlint** - Lint commit messages

Example `package.json` scripts:

```json
{
  "scripts": {
    "prepare": "husky install",
    "commitlint": "commitlint --edit"
  },
  "devDependencies": {
    "@commitlint/cli": "^18.0.0",
    "@commitlint/config-conventional": "^18.0.0",
    "husky": "^8.0.0"
  }
}
```

---

## 📚 Project Documentation

Additional project-specific documentation is available in the `docs/` folder:

- **[Rich Text Usage Guide](./docs/RICH_TEXT_USAGE.md)** - Guide for using rich text formatting in translations with `next-intl`
- **[SEO & Static Generation](./docs/SEO_AND_STATIC_GENERATION.md)** - SEO implementation and static site generation setup

**Note**: All documentation files (except `Agents.md` and `README.md`) are located in the `docs/` folder for better organization.

---

## 🔗 Resources

- [Next.js Documentation](https://nextjs.org/docs)
- [React Documentation](https://react.dev)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [ESLint Rules](https://eslint.org/docs/rules/)
- [Prettier Options](https://prettier.io/docs/en/options.html)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [Commitlint](https://commitlint.js.org/)
- [Tailwind CSS Documentation](https://tailwindcss.com/docs)
- [Tailwind CSS - Adding Custom Styles](https://tailwindcss.com/docs/adding-custom-styles)

---

**Remember**: Write code that is readable, maintainable, and performant. When in doubt, prioritize clarity over cleverness.
