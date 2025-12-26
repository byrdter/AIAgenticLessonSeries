# Lesson 11 — Exercises

## API Layer & Data Fetching

These exercises help you understand how the frontend communicates with the backend through a clean API layer.

---

## Exercise 1: Trace the Data Flow

Understand how data moves from backend to browser.

### Step 1: Find a Component That Displays Data

Look in `frontend/src/features/` for a component that shows data (tasks, notes, workspaces, etc.).

### Step 2: Trace Backwards

Document the flow:

```markdown
# Data Flow: [Component Name]

## 1. Component
File: 
Hook used: 

## 2. Custom Hook
File: 
API function called: 
States managed: loading, error, data

## 3. Feature API
File: 
Endpoint called: 
HTTP method: 

## 4. API Client
File: 
Base URL: 

## 5. Backend
Endpoint: 
Response type: 
```

### Step 3: Verify with Network Tab

Open DevTools → Network → Fetch/XHR. Refresh the page and find the API call.

Document:
- Request URL
- Response status
- Response body

---

## Exercise 2: Understand the API Client

Deep dive into the API client configuration.

### Step 1: Find the API Client

```bash
find frontend/src -name "client.ts" -o -name "api.ts" | head -5
```

### Step 2: Answer These Questions

1. Where does the base URL come from?
2. What default headers are set?
3. How are errors handled?
4. What does the generic `<T>` do?

### Step 3: Document the Pattern

```markdown
# API Client Analysis

## Configuration
- Base URL source: 
- Default headers: 

## Error Handling
- What triggers an error: 
- Error format: 

## Type Safety
- How responses are typed: 
```

---

## Exercise 3: Add a New API Function

Practice extending the API layer.

### Step 1: Choose a Feature

Pick an existing feature (tasks, notes, etc.).

### Step 2: Add an Update Function

If the feature doesn't have an `update` function, add one:

```typescript
// In features/[feature]/api.ts
update: (id: number, data: Partial<FeatureType>) =>
  apiClient<FeatureType>(`/[feature]/${id}`, {
    method: 'PATCH',
    body: JSON.stringify(data),
  }),
```

### Step 3: Test It

Use the browser console or a test file:

```typescript
import { featureApi } from './api';

// Test the update function
const updated = await featureApi.update(1, { title: 'Updated Title' });
console.log(updated);
```

---

## Exercise 4: Create a Custom Hook

Build a hook for a single item.

### Step 1: Create useTask(id) Hook

```typescript
// features/tasks/hooks.ts
export function useTask(id: number) {
  const [task, setTask] = useState<Task | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    if (!id) return;
    
    setLoading(true);
    setError(null);
    
    tasksApi.getById(id)
      .then(setTask)
      .catch(err => setError(err.message))
      .finally(() => setLoading(false));
  }, [id]);

  return { task, loading, error };
}
```

### Step 2: Use It in a Component

```tsx
function TaskDetail({ taskId }: { taskId: number }) {
  const { task, loading, error } = useTask(taskId);
  
  if (loading) return <LoadingState />;
  if (error) return <ErrorState message={error} />;
  if (!task) return <div>Task not found</div>;
  
  return (
    <div>
      <h1>{task.title}</h1>
      <p>Status: {task.completed ? 'Done' : 'Pending'}</p>
    </div>
  );
}
```

### Step 3: Test Different Scenarios

- Valid ID → shows task
- Invalid ID → shows error
- Loading state → appears briefly

---

## Exercise 5: Create a Mutation Hook

Build a hook for creating/updating data.

### Step 1: Create useCreateTask Hook

```typescript
export function useCreateTask() {
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const createTask = async (data: CreateTaskInput): Promise<Task | null> => {
    setLoading(true);
    setError(null);
    
    try {
      const task = await tasksApi.create(data);
      return task;
    } catch (err) {
      const message = err instanceof Error ? err.message : 'Failed to create';
      setError(message);
      return null;
    } finally {
      setLoading(false);
    }
  };

  return { createTask, loading, error };
}
```

### Step 2: Use in a Form Component

```tsx
function CreateTaskForm() {
  const [title, setTitle] = useState('');
  const { createTask, loading, error } = useCreateTask();

  const handleSubmit = async (e: FormEvent) => {
    e.preventDefault();
    const task = await createTask({ title });
    if (task) {
      setTitle('');
      // Maybe redirect or show success
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        value={title}
        onChange={e => setTitle(e.target.value)}
        disabled={loading}
      />
      <button type="submit" disabled={loading}>
        {loading ? 'Creating...' : 'Create Task'}
      </button>
      {error && <p className="error">{error}</p>}
    </form>
  );
}
```

---

## Exercise 6: Test a Hook

Write tests for your custom hook.

### Step 1: Set Up Mock

```typescript
// features/tasks/hooks.test.ts
import { renderHook, waitFor } from '@testing-library/react';
import { useTasks } from './hooks';
import { tasksApi } from './api';

vi.mock('./api');
```

### Step 2: Test Success Case

```typescript
it('fetches tasks successfully', async () => {
  const mockTasks = [
    { id: 1, title: 'Test', completed: false }
  ];
  vi.mocked(tasksApi.getAll).mockResolvedValue(mockTasks);

  const { result } = renderHook(() => useTasks());

  // Initially loading
  expect(result.current.loading).toBe(true);

  // Wait for fetch to complete
  await waitFor(() => {
    expect(result.current.loading).toBe(false);
  });

  // Check results
  expect(result.current.tasks).toEqual(mockTasks);
  expect(result.current.error).toBeNull();
});
```

### Step 3: Test Error Case

```typescript
it('handles errors', async () => {
  vi.mocked(tasksApi.getAll).mockRejectedValue(new Error('Network error'));

  const { result } = renderHook(() => useTasks());

  await waitFor(() => {
    expect(result.current.loading).toBe(false);
  });

  expect(result.current.tasks).toEqual([]);
  expect(result.current.error).toBe('Network error');
});
```

### Step 4: Run Tests

```bash
npm run test -- hooks.test
```

---

## Exercise 7: Match Types to Backend

Ensure frontend types match backend schemas.

### Step 1: Find Backend Schema

Look at the Pydantic model:

```python
# backend/app/features/tasks/schemas.py
class TaskResponse(BaseModel):
    id: int
    title: str
    completed: bool
    created_at: datetime
    updated_at: datetime | None
```

### Step 2: Compare to Frontend Type

```typescript
// frontend/src/features/tasks/types.ts
interface Task {
  id: number;
  title: string;
  completed: boolean;
  created_at: string;
  updated_at: string | null;
}
```

### Step 3: Document Differences

Create `TYPE_MAPPING.md`:

```markdown
# Backend to Frontend Type Mapping

## Task

| Backend (Python) | Frontend (TypeScript) | Notes |
|------------------|----------------------|-------|
| id: int | id: number | |
| title: str | title: string | |
| completed: bool | completed: boolean | |
| created_at: datetime | created_at: string | ISO string |
| updated_at: datetime \| None | updated_at: string \| null | |
```

---

## Exercise 8: Handle Edge Cases

Make your hooks robust.

### Step 1: Handle Empty State

```typescript
function TaskList() {
  const { tasks, loading, error } = useTasks();
  
  if (loading) return <LoadingState />;
  if (error) return <ErrorState message={error} />;
  
  // Handle empty state!
  if (tasks.length === 0) {
    return <EmptyState message="No tasks yet. Create one!" />;
  }
  
  return (
    <ul>
      {tasks.map(task => <TaskItem key={task.id} task={task} />)}
    </ul>
  );
}
```

### Step 2: Handle Refetch

Add refetch capability to your hook:

```typescript
export function useTasks() {
  // ... existing state ...

  const refetch = useCallback(() => {
    setLoading(true);
    setError(null);
    tasksApi.getAll()
      .then(setTasks)
      .catch(err => setError(err.message))
      .finally(() => setLoading(false));
  }, []);

  useEffect(() => {
    refetch();
  }, [refetch]);

  return { tasks, loading, error, refetch };
}
```

### Step 3: Use Refetch

```tsx
function TaskList() {
  const { tasks, loading, error, refetch } = useTasks();
  
  // ... render logic ...
  
  return (
    <div>
      <button onClick={refetch} disabled={loading}>
        Refresh
      </button>
      {/* ... task list ... */}
    </div>
  );
}
```

---

## Submission Checklist

Before moving to Lesson 12:

- [ ] Traced data flow from component to backend (Exercise 1)
- [ ] Analyzed API client (Exercise 2)
- [ ] Added new API function (Exercise 3)
- [ ] Created single-item hook (Exercise 4)
- [ ] Created mutation hook (Exercise 5)
- [ ] Wrote hook tests (Exercise 6)
- [ ] Documented type mapping (Exercise 7)
- [ ] Handled edge cases (Exercise 8)

---

## Looking Ahead: Lesson 12

In Lesson 12, we'll add **error boundaries** and **loading states** — the UX guardrails that ensure users never see broken or empty screens.

You'll learn:
- React Error Boundaries for catching render errors
- Consistent loading/error/empty patterns
- How these patterns work with your API layer
