# Lesson 12 — HeyGen Script

## "Error Handling & UX Guardrails"



Welcome to Lesson 12

We've built frontend foundations: TypeScript for types, ESLint for linting, Vitest for testing, and a clean API layer for data fetching.

But there's one more guardrail: **UX safety**.

Users should never see a blank screen. They should never see a cryptic error message. They should never wonder if the app is frozen.

Today we add error boundaries, loading states, empty states, and error recovery patterns.

These are the UX guardrails that ensure your app handles failure gracefully.

---

### 2. TOUR ERROR BOUNDARY  

[ON SCREEN: Open ErrorBoundary component]

Let's start with error boundaries — React's try-catch for the UI.

[ON SCREEN: Show ErrorBoundary code]

```tsx
class ErrorBoundary extends React.Component {
  state = { hasError: false };

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  render() {
    if (this.state.hasError) {
      return <ErrorFallback />;
    }
    return this.props.children;
  }
}
```

When a component inside the boundary throws an error during rendering, React catches it here instead of crashing the whole app.

Without error boundaries, one broken component takes down everything. With them, the error is contained. The rest of the app keeps working.

[ON SCREEN: Show usage]

```tsx
<ErrorBoundary fallback={<ErrorFallback />}>
  <TaskList />
</ErrorBoundary>
```

If TaskList throws, users see the fallback instead of a white screen.

---

### 3. TOUR LOADING STATES  

[ON SCREEN: Open LoadingState component]

Next, loading states. Users need to know something is happening.

[ON SCREEN: Show LoadingState component]

```tsx
export function LoadingState({ message = 'Loading...' }) {
  return (
    <div className="loading-state" role="status">
      <Spinner />
      <p>{message}</p>
    </div>
  );
}
```

Simple, consistent, accessible. The `role="status"` tells screen readers this is a status update.

Every component that fetches data uses this same loading component. Consistent UX across the app.

[ON SCREEN: Show usage in component]

```tsx
function TaskList() {
  const { tasks, loading } = useTasks();
  
  if (loading) {
    return <LoadingState message="Loading tasks..." />;
  }
  
  // ... render tasks
}
```

---

### 4. TOUR EMPTY STATES  

[ON SCREEN: Open EmptyState component]

Empty states are often forgotten. No data isn't an error — it's a state.

[ON SCREEN: Show EmptyState component]

```tsx
export function EmptyState({ message, action, onAction }) {
  return (
    <div className="empty-state">
      <p>{message}</p>
      {action && <button onClick={onAction}>{action}</button>}
    </div>
  );
}
```

Instead of showing an empty list — which looks broken — show a helpful message with a call to action.

 

```tsx
if (tasks.length === 0) {
  return (
    <EmptyState 
      message="No tasks yet" 
      action="Create your first task"
      onAction={() => navigate('/tasks/new')}
    />
  );
}
```

The user knows there's nothing wrong. They know what to do next.

---

### 5. TOUR ERROR STATES  

[ON SCREEN: Open ErrorState component]

**SCRIPT:**

Error states with recovery. When something fails, tell the user and give them a way forward.

[ON SCREEN: Show ErrorState component]

```tsx
export function ErrorState({ message, onRetry }) {
  return (
    <div className="error-state" role="alert">
      <p>Something went wrong: {message}</p>
      {onRetry && <button onClick={onRetry}>Try Again</button>}
    </div>
  );
}
```

The `role="alert"` announces the error to screen readers. The retry button gives users agency.

[ON SCREEN: Show usage]

```tsx
function TaskList() {
  const { tasks, loading, error, refetch } = useTasks();
  
  if (error) {
    return <ErrorState message={error} onRetry={refetch} />;
  }
  
  // ... render tasks
}
```

Network glitch? Click retry. The user doesn't need to refresh the whole page.

---

### 6. BREAK A COMPONENT  

[ON SCREEN: Create a component with an error]

**SCRIPT:**

Let's see the error boundary in action.

[ON SCREEN: Create broken component]

```tsx
function BrokenComponent() {
  throw new Error('This component is broken!');
  return <div>You won't see this</div>;
}
```

[ON SCREEN: Use it without boundary]

First, without an error boundary:

```tsx
function App() {
  return <BrokenComponent />;
}
```

[ON SCREEN: Show white screen or React error overlay]

The whole app crashes. In production, users see nothing.

[ON SCREEN: Wrap with boundary]

Now with an error boundary:

```tsx
function App() {
  return (
    <ErrorBoundary fallback={<ErrorFallback />}>
      <BrokenComponent />
    </ErrorBoundary>
  );
}
```

[ON SCREEN: Show error fallback]

The error is caught. Users see a helpful message. The app doesn't crash.

This is the safety net.

### 8. THE COMPLETE PATTERN  

[ON SCREEN: Code showing all four states]

 Let's put it all together.

[ON SCREEN: Show complete component]

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
    return <EmptyState message="No tasks yet" />;
  }
  
  // 4. Success
  return (
    <ul>
      {tasks.map(task => <TaskItem key={task.id} task={task} />)}
    </ul>
  );
}
```

Four states, one component. Every path has a UI. Users are never lost.

This is the pattern. When Claude Code builds a data-fetching component, it follows this structure.

---

