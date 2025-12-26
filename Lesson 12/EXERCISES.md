# Lesson 12 — Exercises

## Error Handling & UX Guardrails

These exercises help you implement and understand UX guardrails that ensure users always see meaningful UI.

---

## Exercise 1: Create State Components

Build a complete set of state components.

### Step 1: Create LoadingState

```tsx
// shared/components/LoadingState.tsx
interface LoadingStateProps {
  message?: string;
}

export function LoadingState({ message = 'Loading...' }: LoadingStateProps) {
  // TODO: Implement
  // - Show a spinner (can be CSS or emoji for now)
  // - Show the message
  // - Add role="status" for accessibility
}
```

### Step 2: Create ErrorState

```tsx
// shared/components/ErrorState.tsx
interface ErrorStateProps {
  message: string;
  onRetry?: () => void;
}

export function ErrorState({ message, onRetry }: ErrorStateProps) {
  // TODO: Implement
  // - Show error message
  // - Show retry button if onRetry provided
  // - Add role="alert" for accessibility
}
```

### Step 3: Create EmptyState

```tsx
// shared/components/EmptyState.tsx
interface EmptyStateProps {
  message: string;
  action?: string;
  onAction?: () => void;
}

export function EmptyState({ message, action, onAction }: EmptyStateProps) {
  // TODO: Implement
  // - Show message
  // - Show action button if action and onAction provided
}
```

### Step 4: Export All

```tsx
// shared/components/index.ts
export { LoadingState } from './LoadingState';
export { ErrorState } from './ErrorState';
export { EmptyState } from './EmptyState';
```

---

## Exercise 2: Create Error Boundary

Build the safety net for render errors.

### Step 1: Create ErrorFallback

```tsx
// shared/components/ErrorFallback.tsx
interface ErrorFallbackProps {
  error: Error | null;
  onReset?: () => void;
}

export function ErrorFallback({ error, onReset }: ErrorFallbackProps) {
  return (
    <div className="error-fallback">
      <h2>Something went wrong</h2>
      <p>{error?.message || 'Unknown error'}</p>
      {onReset && (
        <button onClick={onReset}>Try Again</button>
      )}
    </div>
  );
}
```

### Step 2: Create ErrorBoundary

```tsx
// shared/components/ErrorBoundary.tsx
import React from 'react';
import { ErrorFallback } from './ErrorFallback';

interface Props {
  children: React.ReactNode;
  fallback?: React.ReactNode;
}

interface State {
  hasError: boolean;
  error: Error | null;
}

export class ErrorBoundary extends React.Component<Props, State> {
  state: State = { hasError: false, error: null };

  static getDerivedStateFromError(error: Error): State {
    // TODO: Return new state with error
  }

  componentDidCatch(error: Error, errorInfo: React.ErrorInfo) {
    // TODO: Log error
  }

  handleReset = () => {
    // TODO: Reset state
  };

  render() {
    // TODO: Return fallback if error, children otherwise
  }
}
```

### Step 3: Test It

Create a broken component:

```tsx
function BrokenComponent() {
  throw new Error('Test error');
  return null;
}
```

Use it with and without boundary:

```tsx
// Without boundary - should crash
<BrokenComponent />

// With boundary - should show fallback
<ErrorBoundary>
  <BrokenComponent />
</ErrorBoundary>
```

---

## Exercise 3: Apply Four States Pattern

Refactor a component to handle all states.

### Step 1: Find a Data Component

Look for a component that fetches data but doesn't handle all states.

### Step 2: Apply the Pattern

```tsx
function MyDataComponent() {
  const { data, loading, error, refetch } = useMyData();
  
  // 1. Loading (check first!)
  if (loading) {
    return <LoadingState message="Loading data..." />;
  }
  
  // 2. Error (check second)
  if (error) {
    return <ErrorState message={error} onRetry={refetch} />;
  }
  
  // 3. Empty (check third)
  if (data.length === 0) {
    return <EmptyState message="No data yet" />;
  }
  
  // 4. Success (default)
  return (
    <ul>
      {data.map(item => <li key={item.id}>{item.name}</li>)}
    </ul>
  );
}
```

### Step 3: Verify Each State

Test that each state appears correctly:
- Stop backend → error state
- Empty database → empty state
- Throttle network → loading state visible
- Normal operation → success state

---

## Exercise 4: Add Refetch Capability

Make sure users can recover from errors.

### Step 1: Update Hook

```tsx
export function useData() {
  const [data, setData] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  const fetchData = useCallback(async () => {
    setLoading(true);
    setError(null);
    try {
      const result = await api.getData();
      setData(result);
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Failed to load');
    } finally {
      setLoading(false);
    }
  }, []);

  useEffect(() => {
    fetchData();
  }, [fetchData]);

  return { data, loading, error, refetch: fetchData };
}
```

### Step 2: Use in Component

```tsx
const { data, loading, error, refetch } = useData();

if (error) {
  return <ErrorState message={error} onRetry={refetch} />;
}
```

### Step 3: Test Retry

1. Stop the backend (cause error)
2. See error state with retry button
3. Start the backend
4. Click retry
5. See loading state
6. See data appear

---

## Exercise 5: Test State Components

Write tests for your state components.

### Test LoadingState

```tsx
import { render, screen } from '@testing-library/react';
import { LoadingState } from './LoadingState';

describe('LoadingState', () => {
  it('renders default message', () => {
    render(<LoadingState />);
    expect(screen.getByText('Loading...')).toBeInTheDocument();
  });

  it('renders custom message', () => {
    render(<LoadingState message="Please wait..." />);
    expect(screen.getByText('Please wait...')).toBeInTheDocument();
  });

  it('has status role for accessibility', () => {
    render(<LoadingState />);
    expect(screen.getByRole('status')).toBeInTheDocument();
  });
});
```

### Test ErrorState

```tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { ErrorState } from './ErrorState';

describe('ErrorState', () => {
  it('renders error message', () => {
    render(<ErrorState message="Something broke" />);
    expect(screen.getByText(/Something broke/)).toBeInTheDocument();
  });

  it('shows retry button when onRetry provided', () => {
    render(<ErrorState message="Error" onRetry={() => {}} />);
    expect(screen.getByRole('button')).toBeInTheDocument();
  });

  it('hides retry button when no onRetry', () => {
    render(<ErrorState message="Error" />);
    expect(screen.queryByRole('button')).not.toBeInTheDocument();
  });

  it('calls onRetry when clicked', () => {
    const handleRetry = vi.fn();
    render(<ErrorState message="Error" onRetry={handleRetry} />);
    fireEvent.click(screen.getByRole('button'));
    expect(handleRetry).toHaveBeenCalled();
  });
});
```

---

## Exercise 6: Wrap Pages with Boundaries

Add error boundaries to page-level components.

### Step 1: Identify Pages

Find your page/route components:
- TasksPage
- NotesPage
- SettingsPage
- etc.

### Step 2: Wrap Each Page

```tsx
// pages/TasksPage.tsx
export function TasksPage() {
  return (
    <ErrorBoundary fallback={<TasksErrorFallback />}>
      <TasksHeader />
      <TaskList />
      <CreateTaskButton />
    </ErrorBoundary>
  );
}

function TasksErrorFallback() {
  return (
    <div>
      <h1>Tasks Error</h1>
      <p>Something went wrong loading tasks.</p>
      <button onClick={() => window.location.reload()}>
        Refresh Page
      </button>
    </div>
  );
}
```

### Step 3: Create Page-Specific Fallbacks

Each page can have a customized error message relevant to that context.

---

## Exercise 7: Create a Style Guide

Document the patterns for your team (and AI).

### Create STATES_STYLE_GUIDE.md

```markdown
# UI State Patterns

## The Four States

Every component that fetches data must handle:

1. **Loading** - Show LoadingState component
2. **Error** - Show ErrorState with retry option
3. **Empty** - Show EmptyState with call-to-action
4. **Success** - Render the data

## Check Order

ALWAYS check in this order:
1. `if (loading)` - First!
2. `if (error)` - Second
3. `if (data.length === 0)` - Third
4. Default: render data

## Component Usage

### LoadingState
\`\`\`tsx
<LoadingState message="Loading tasks..." />
\`\`\`

### ErrorState
\`\`\`tsx
<ErrorState 
  message={error} 
  onRetry={refetch} 
/>
\`\`\`

### EmptyState
\`\`\`tsx
<EmptyState 
  message="No tasks yet"
  action="Create task"
  onAction={handleCreate}
/>
\`\`\`

## Error Boundaries

Wrap page-level components:
\`\`\`tsx
<ErrorBoundary fallback={<PageErrorFallback />}>
  <PageContent />
</ErrorBoundary>
\`\`\`
```

---

## Exercise 8: Comprehensive Review

Test everything together.

### Checklist

- [ ] All state components exist and are exported
- [ ] ErrorBoundary catches render errors
- [ ] LoadingState has role="status"
- [ ] ErrorState has role="alert"
- [ ] At least 3 data components use the four states pattern
- [ ] At least 2 pages have error boundaries
- [ ] Retry functionality works
- [ ] Tests pass for state components

### Final Test

1. **Network throttle test:** Slow 3G shows loading states
2. **Backend down test:** Error states appear, retry works
3. **Empty data test:** Empty states appear with actions
4. **Component crash test:** Error boundaries catch and show fallback

---

## Submission Checklist

Module 3 Complete:

- [ ] Created LoadingState (Exercise 1)
- [ ] Created ErrorState (Exercise 1)
- [ ] Created EmptyState (Exercise 1)
- [ ] Created ErrorBoundary (Exercise 2)
- [ ] Applied four states pattern (Exercise 3)
- [ ] Added refetch capability (Exercise 4)
- [ ] Tested state components (Exercise 5)
- [ ] Wrapped pages with boundaries (Exercise 6)
- [ ] Created style guide (Exercise 7)
- [ ] Passed comprehensive review (Exercise 8)

---

## Looking Ahead: Module 4

In Module 4, we'll put everything together — running complete PIV loops with Claude Code to build real features end-to-end.

You'll use:
- Backend guardrails (tests, linting, types, logging)
- Frontend guardrails (tests, linting, types, UX patterns)
- The PIV loop methodology

Full-stack features, AI-assisted, guardrail-validated.
