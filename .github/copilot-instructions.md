# React Starter Copilot Instructions

## Architecture Overview

This is a modern React SPA using a **feature-based architecture** with declarative routing, React Query for server state, and shadcn/ui components.

### Provider Hierarchy (src/main.tsx)
```
BrowserRouter > QueryClientProvider > ErrorBoundary > ThemeProvider > AccountProvider > App
```

### Routing Structure
- **Declarative routing** using `routes.ts` with lazy-loaded components
- **Layout-based routing** with `MainLayout` (header/footer) and `AccountLayout` (centered auth forms)
- Routes defined as nested `RouteObject[]` with layout components as parents
- Path constants in `paths` object for type-safe navigation

## Feature Organization

### Feature Structure Pattern
Each feature follows this structure:
```
src/features/{feature}/
├── index.ts           # Exports hooks, context, types
├── {Feature}Layout.tsx # Layout wrapper if needed
├── {Feature}Provider.tsx # Context provider
├── formschema.ts      # Zod validation schemas
├── hooks.ts           # Custom hooks for data fetching
├── types.ts           # TypeScript interfaces
└── components/        # Feature-specific components
```

### Key Patterns

**Context Creation** (`src/lib/context.ts`):
- Use `ContextProviderFactory<T>` type for provider components
- Export context and custom hook from feature `index.ts`
- Throw error if context used outside provider

**Data Fetching** (`src/features/quote/index.ts`):
- Use `queryOptions` + `useSuspenseQuery` pattern
- Export query objects for reusability
- Wrap components using suspense queries in `<Suspense>` with skeleton fallbacks

**Form Handling**:
- Zod schemas in `formschema.ts` with `.required()` for better error messages
- `useForm` with `standardSchemaResolver(formSchema)`
- Custom hooks for form logic and mutation handling
- Server error handling with `ValidationError` class and `replaceFormErrors` utility

**API Client** (`src/lib/api.ts`):
- Centralized `APIClient` class with typed response patterns
- `ValidationError` class for 422 responses with field-level errors
- Base URL pattern: `http://localhost:8888`

## Component Conventions

**Component Organization**:
- UI components in `src/components/ui/` (shadcn/ui)
- Layout components in `src/components/layout/`
- Feature components in `src/features/{feature}/components/`

**Import Aliases**:
- Always use `@/` alias for absolute imports
- Never use relative imports for better maintainability

**Loading States**:
- Use `Suspense` + skeleton components for data fetching
- Use `isPending` from mutations for button loading states
- Include `Loader2Icon` with `animate-spin` for loading indicators

## Development Workflow

**Commands** (pnpm-based):
```bash
pnpm run dev          # Development server (port 5173)
pnpm run build        # Production build
pnpm run test         # Unit tests (Vitest + Playwright)
pnpm run test:e2e     # E2E tests
pnpm run storybook    # Component development
```

**Adding shadcn/ui Components**:
```bash
pnpm dlx shadcn@latest add [component-name]
```

**Testing Setup**:
- Vitest with Playwright browser testing
- Tests require `QueryClientProvider` wrapper for data fetching components
- Use `vitest-browser-react` for rendering in tests

## Key Files

- `src/routes.ts` - Route definitions and path constants
- `src/main.tsx` - Provider hierarchy and app initialization  
- `src/lib/api.ts` - HTTP client and error handling
- `src/lib/utils.ts` - Shared utilities (`cn`, `replaceFormErrors`)
- `vite.config.ts` - React Compiler enabled, `@` alias configured

## Critical Dependencies

- **React Router** (declarative mode) - Client-side routing only
- **React Query** - Server state management, not client state
- **Zod** - Schema validation for forms and API responses
- **React Hook Form** - Form handling with validation
- **shadcn/ui** - Component library built on Radix + Tailwind
- **Sonner** - Toast notifications
- **React Compiler** - Automatic optimization (enabled in Vite config)

## Anti-Patterns to Avoid

- Don't use React Router for data fetching (use React Query)
- Don't mix client state in React Query (use zustand if needed)
- Don't use relative imports (always use `@/` alias)
- Don't create components without proper TypeScript typing
- Don't skip error boundaries for components using suspense queries