# Claude Code Prompts for Lesson 12

## Prompts for Error Handling & UX Guardrails

---

## EXPLORATION PROMPTS

### Find Existing Error Handling

```
Look at the frontend and find:

1. Any ErrorBoundary components
2. LoadingState or Loading components
3. ErrorState or Error components
4. EmptyState or Empty components

Show me where they are and how they're used.
```

---

### Analyze a Data Component

```
Find a component that fetches data and analyze how it handles:

1. Loading state (while fetching)
2. Error state (if fetch fails)
3. Empty state (no data returned)
4. Success state (data to render)

Which states are handled? Which are missing?
```

---

## CREATION PROMPTS

### Create Error Boundary

```
Create an ErrorBoundary component in shared/components/ErrorBoundary.tsx:

Requirements:
- Class component (required for getDerivedStateFromError)
- Catches render errors in children
- Displays a fallback UI when error occurs
- Logs errors to console
- Has a "Try Again" button that resets the boundary
- TypeScript typed

Include a simple ErrorFallback component.
```

---

### Create LoadingState Component

```
Create a LoadingState component in shared/components/LoadingState.tsx:

Requirements:
- Accepts optional message prop (default: "Loading...")
- Shows a spinner or loading indicator
- Has role="status" for accessibility
- TypeScript typed
- Clean, minimal styling

Export from shared/components/index.ts
```

---

### Create ErrorState Component

```
Create an ErrorState component in shared/components/ErrorState.tsx:

Requirements:
- Accepts message prop (required)
- Accepts optional onRetry callback
- Shows error message
- Shows retry button if onRetry provided
- Has role="alert" for accessibility
- TypeScript typed

Export from shared/components/index.ts
```

---

### Create EmptyState Component

```
Create an EmptyState component in shared/components/EmptyState.tsx:

Requirements:
- Accepts message prop (required)
- Accepts optional action text and onAction callback
- Shows message
- Shows action button if both action and onAction provided
- Friendly, encouraging tone
- TypeScript typed

Export from shared/components/index.ts
```

---

### Create All State Components

```
Create a complete set of state components for the shared/components folder:

1. LoadingState.tsx
   - message prop (optional, default "Loading...")
   - Spinner + message
   - role="status"

2. ErrorState.tsx
   - message prop (required)
   - onRetry prop (optional)
   - Error icon + message + retry button
   - role="alert"

3. EmptyState.tsx
   - message prop (required)
   - action prop (optional)
   - onAction prop (optional)
   - Friendly message + action button

4. ErrorBoundary.tsx
   - Class component
   - Catches render errors
   - Fallback UI with reset

5. Update index.ts to export all

All components should be TypeScript and accessible.
```

---

## IMPLEMENTATION PROMPTS

### Apply Four States Pattern

```
Refactor this component to handle all four states:

[paste component code]

Add:
1. if (loading) return <LoadingState message="..." />
2. if (error) return <ErrorState message={error} onRetry={refetch} />
3. if (data.length === 0) return <EmptyState message="..." />
4. Success: render the data

Make sure loading is checked first, then error, then empty, then success.
```

---

### Wrap Page with Error Boundary

```
Add an error boundary to this page component:

[paste page code]

Wrap the main content with ErrorBoundary.
Create an appropriate fallback for this page context.
```

---

### Add Retry Functionality

```
This component shows errors but doesn't let users retry:

[paste component code]

Add:
1. A refetch function to the hook (or create one)
2. Pass refetch to ErrorState's onRetry prop
3. Make sure loading state shows during retry
```

---

## TESTING PROMPTS

### Test Error Boundary

```
Write tests for the ErrorBoundary component:

1. Test that it renders children normally
2. Test that it catches errors and shows fallback
3. Test that the reset button works

Use a component that throws to test error catching.
```

---

### Test Loading State

```
Write tests for LoadingState component:

1. Test default message renders
2. Test custom message renders
3. Test accessibility (role="status")
```

---

### Test ErrorState

```
Write tests for ErrorState component:

1. Test error message renders
2. Test retry button shows when onRetry provided
3. Test retry button hidden when no onRetry
4. Test clicking retry calls onRetry
5. Test accessibility (role="alert")
```

---

### Test EmptyState

```
Write tests for EmptyState component:

1. Test message renders
2. Test action button shows when action and onAction provided
3. Test clicking action button calls onAction
4. Test action button hidden when no onAction
```

---

## DEBUGGING PROMPTS

### Debug Error Not Caught

```
The error boundary isn't catching this error:

[paste code]

Check:
1. Is the error thrown during render? (boundaries only catch render errors)
2. Is the error in an event handler? (use try/catch instead)
3. Is the error in async code? (use .catch() instead)
4. Is the boundary wrapping the right component?

Explain why it's not caught and how to fix it.
```

---

### Debug Loading State Not Showing

```
The loading state never appears:

[paste code]

Check:
1. Is loading set to true initially?
2. Is loading checked before other states?
3. Is the fetch too fast to see loading?
4. Is there caching preventing the fetch?

Diagnose and fix.
```

---

### Debug Empty State Flashing

```
The empty state briefly appears before data loads:

[paste code]

The issue is likely checking empty before loading is complete.

Fix the order of state checks:
1. Loading should be first
2. Error should be second
3. Empty should be third
4. Success should be last
```

---

## DEMONSTRATION PROMPTS

### Create Broken Component for Demo

```
Create a component that intentionally throws an error during render:

function BrokenComponent() {
  throw new Error('Intentional error for demo');
  return null;
}

Then show:
1. What happens without error boundary (crash)
2. What happens with error boundary (fallback)
```

---

### Add Artificial Delay for Demo

```
Modify this hook to add a 2-second delay so loading state is visible:

[paste hook code]

Add:
await new Promise(resolve => setTimeout(resolve, 2000));

This is for demo purposes only.
```

---

## TIPS FOR USING THESE PROMPTS

1. **Create all state components together** — They work as a set

2. **Apply pattern consistently** — Every data component uses the same structure

3. **Test boundaries with intentional errors** — Make sure they actually catch

4. **Use Network throttling for loading demos** — More realistic than artificial delays

5. **Wrap at page level** — Error boundaries work best wrapping sections, not individual components
