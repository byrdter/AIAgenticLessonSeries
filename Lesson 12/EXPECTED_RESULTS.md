# Expected Results for Lesson 12

## What Success Looks Like for Error Handling & UX Guardrails

---

## 1. Error Boundary Component

### Expected Location

```
frontend/src/shared/components/ErrorBoundary.tsx
```

### Expected Code Structure

```tsx
import React from 'react';

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
    return { hasError: true, error };
  }

  componentDidCatch(error: Error, errorInfo: React.ErrorInfo) {
    console.error('Error caught by boundary:', error, errorInfo);
  }

  handleReset = () => {
    this.setState({ hasError: false, error: null });
  };

  render() {
    if (this.state.hasError) {
      return this.props.fallback || (
        <ErrorFallback 
          error={this.state.error} 
          onReset={this.handleReset} 
        />
      );
    }
    return this.props.children;
  }
}
```

**Key indicators:**
- Class component (required for lifecycle methods)
- TypeScript typed
- Catches errors and updates state
- Has reset functionality

---

## 2. LoadingState Component

### Expected Code

```tsx
interface LoadingStateProps {
  message?: string;
}

export function LoadingState({ message = 'Loading...' }: LoadingStateProps) {
  return (
    <div className="loading-state" role="status">
      <div className="spinner" aria-hidden="true" />
      <p>{message}</p>
    </div>
  );
}
```

**Key indicators:**
- Optional message prop with default
- role="status" for accessibility
- Spinner or loading indicator

---

## 3. ErrorState Component

### Expected Code

```tsx
interface ErrorStateProps {
  message: string;
  onRetry?: () => void;
}

export function ErrorState({ message, onRetry }: ErrorStateProps) {
  return (
    <div className="error-state" role="alert">
      <p>Something went wrong: {message}</p>
      {onRetry && (
        <button onClick={onRetry} type="button">
          Try Again
        </button>
      )}
    </div>
  );
}
```

**Key indicators:**
- Required message prop
- Optional onRetry callback
- role="alert" for accessibility
- Retry button only when onRetry provided

---

## 4. EmptyState Component

### Expected Code

```tsx
interface EmptyStateProps {
  message: string;
  action?: string;
  onAction?: () => void;
}

export function EmptyState({ message, action, onAction }: EmptyStateProps) {
  return (
    <div className="empty-state">
      <p>{message}</p>
      {action && onAction && (
        <button onClick={onAction} type="button">
          {action}
        </button>
      )}
    </div>
  );
}
```

**Key indicators:**
- Required message prop
- Optional action text and callback
- Action button only when both provided

---

## 5. Error Boundary Catching Error

### Without Boundary (Crash)

**Browser shows:**
- White screen (production)
- React error overlay (development)
- Console: Uncaught Error

### With Boundary (Caught)

**Browser shows:**
- Fallback component
- Error message
- Reset/retry button

**Console shows:**
```
Error caught by boundary: Error: [error message]
    at BrokenComponent (...)
    at renderWithHooks (...)
```

---

## 6. Loading State Visible

### Using Network Throttling

**DevTools → Network → Slow 3G**

**Expected behavior:**
1. User action triggers fetch
2. LoadingState appears immediately
3. Spinner visible for 2-5 seconds (on Slow 3G)
4. Data appears, loading disappears

**What user sees:**
```
[Spinner]
Loading tasks...
```

Then:
```
- Task 1
- Task 2
- Task 3
```

---

## 7. Empty State Displayed

### When No Data

**Expected behavior:**
1. Fetch completes successfully
2. Response is empty array: `[]`
3. EmptyState appears with message

**What user sees:**
```
No tasks yet.
[Create your first task]  ← button
```

---

## 8. Error State with Retry

### When Fetch Fails

**Expected behavior:**
1. Fetch fails (network error, 500, etc.)
2. ErrorState appears with message
3. Retry button visible
4. Clicking retry triggers new fetch
5. Loading state shows during retry

**What user sees:**
```
Something went wrong: Failed to fetch
[Try Again]  ← button
```

After clicking retry:
```
[Spinner]
Loading tasks...
```

---

## 9. Complete Four States Pattern

### Component Structure

```tsx
function TaskList() {
  const { tasks, loading, error, refetch } = useTasks();
  
  // 1. Loading
  if (loading) {
    return <LoadingState message="Loading tasks..." />;
  }
  
  // 2. Error
  if (error) {
    return <ErrorState message={error} onRetry={refetch} />;
  }
  
  // 3. Empty
  if (tasks.length === 0) {
    return <EmptyState 
      message="No tasks yet" 
      action="Create task"
      onAction={() => navigate('/tasks/new')}
    />;
  }
  
  // 4. Success
  return (
    <ul>
      {tasks.map(task => (
        <li key={task.id}>{task.title}</li>
      ))}
    </ul>
  );
}
```

**Key indicators:**
- Loading checked first
- Error checked second
- Empty checked third
- Success is default (last)

---

## Complete Verification Checklist

### Before Moving On

- [ ] ErrorBoundary component exists
- [ ] ErrorBoundary catches render errors
- [ ] LoadingState component exists
- [ ] Loading state visible during fetch
- [ ] ErrorState component exists
- [ ] ErrorState shows on fetch failure
- [ ] Retry button triggers refetch
- [ ] EmptyState component exists
- [ ] Empty state shows when no data
- [ ] Four states pattern applied to data components

### Success State

When all checkboxes complete, UX guardrails are in place.

---

## Quick Reference

### Components

```
shared/components/
├── ErrorBoundary.tsx
├── LoadingState.tsx
├── ErrorState.tsx
├── EmptyState.tsx
└── index.ts
```

### Pattern Order

```tsx
if (loading) return <LoadingState />;
if (error) return <ErrorState />;
if (data.length === 0) return <EmptyState />;
return <SuccessRender data={data} />;
```

### Accessibility

| Component | Role |
|-----------|------|
| LoadingState | role="status" |
| ErrorState | role="alert" |
| EmptyState | (none required) |

---

## Troubleshooting Quick Reference

| Problem | Likely Cause | Solution |
|---------|-------------|----------|
| Error not caught | Error in event handler | Use try/catch in handler |
| Error not caught | Error in async code | Use .catch() |
| Loading never shows | Fetch too fast | Use network throttling |
| Loading never shows | Data cached | Clear cache |
| Empty flashes briefly | Wrong check order | Check loading first |
| Retry doesn't work | No refetch function | Add refetch to hook |
