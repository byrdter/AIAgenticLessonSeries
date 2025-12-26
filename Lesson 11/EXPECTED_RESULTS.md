# Expected Results for Lesson 11

## What Success Looks Like for API Layer & Data Fetching

---

## 1. API Client Structure

### Expected Location

```
frontend/src/shared/api/
├── client.ts     # Base API client
├── types.ts      # Shared API types (optional)
└── index.ts      # Exports
```

### Expected API Client Code

```typescript
// client.ts
const API_BASE_URL = import.meta.env.VITE_API_BASE_URL || 'http://localhost:8123';

export async function apiClient<T>(
  endpoint: string,
  options?: RequestInit
): Promise<T> {
  const url = `${API_BASE_URL}${endpoint}`;
  
  const response = await fetch(url, {
    headers: {
      'Content-Type': 'application/json',
      ...options?.headers,
    },
    ...options,
  });

  if (!response.ok) {
    throw new Error(`API Error: ${response.status}`);
  }

  return response.json();
}
```

**Key indicators:**
- Generic `<T>` for type safety
- Base URL from environment variable
- Default headers
- Error handling for non-OK responses

---

## 2. Feature API Functions

### Expected Location

```
frontend/src/features/tasks/
├── api.ts        # API functions
├── hooks.ts      # Custom hooks
├── types.ts      # TypeScript types
└── components/
```

### Expected API Functions

```typescript
// features/tasks/api.ts
import { apiClient } from '@/shared/api/client';
import { Task, CreateTaskInput } from './types';

export const tasksApi = {
  getAll: () => apiClient<Task[]>('/tasks'),
  getById: (id: number) => apiClient<Task>(`/tasks/${id}`),
  create: (data: CreateTaskInput) => 
    apiClient<Task>('/tasks', {
      method: 'POST',
      body: JSON.stringify(data),
    }),
  delete: (id: number) =>
    apiClient<void>(`/tasks/${id}`, { method: 'DELETE' }),
};
```

**Key indicators:**
- Typed return values
- Object with methods pattern
- Uses shared apiClient

---

## 3. Custom Hooks

### Expected Hook Pattern

```typescript
// features/tasks/hooks.ts
export function useTasks() {
  const [tasks, setTasks] = useState<Task[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    tasksApi.getAll()
      .then(setTasks)
      .catch(err => setError(err.message))
      .finally(() => setLoading(false));
  }, []);

  return { tasks, loading, error };
}
```

**Key indicators:**
- Three states: data, loading, error
- Fetches on mount (empty dependency array)
- Handles both success and failure
- Returns all three states

---

## 4. Both Servers Running

### Backend

```bash
cd backend
uvicorn app.main:app --port 8123
```

**Expected output:**
```
INFO:     Started server process [12345]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
INFO:     Uvicorn running on http://127.0.0.1:8123 (Press CTRL+C to quit)
```

### Frontend

```bash
cd frontend
npm run dev
```

**Expected output:**
```
  VITE v5.x.x  ready in 300 ms

  ➜  Local:   http://localhost:5173/
```

---

## 5. Data Successfully Fetched

### Browser View

When navigating to a page that uses the hook:
- Loading state briefly shows
- Data appears (tasks, notes, etc.)
- No errors in console

### Component Usage

```tsx
function TaskList() {
  const { tasks, loading, error } = useTasks();
  
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  
  return (
    <ul>
      {tasks.map(task => (
        <li key={task.id}>{task.title}</li>
      ))}
    </ul>
  );
}
```

**Expected behavior:**
1. "Loading..." appears briefly
2. List of tasks appears
3. No "Error:" message

---

## 6. Network Tab Evidence

### Open DevTools → Network → Fetch/XHR

**Expected request:**

| Field | Value |
|-------|-------|
| URL | http://localhost:8123/tasks |
| Method | GET |
| Status | 200 OK |
| Type | fetch |

### Request Headers

```
Content-Type: application/json
Accept: */*
Origin: http://localhost:5173
```

### Response

```json
[
  {
    "id": 1,
    "title": "Learn API patterns",
    "completed": false,
    "created_at": "2024-01-15T10:30:00Z"
  },
  {
    "id": 2,
    "title": "Build frontend features",
    "completed": false,
    "created_at": "2024-01-15T10:31:00Z"
  }
]
```

---

## 7. Type Safety Working

### Valid Code

```typescript
const { tasks } = useTasks();
const firstTask = tasks[0];
console.log(firstTask.title);      // ✅ OK - title exists
console.log(firstTask.completed);  // ✅ OK - completed exists
```

### Invalid Code (TypeScript Error)

```typescript
const { tasks } = useTasks();
const firstTask = tasks[0];
console.log(firstTask.name);  // ❌ Error: Property 'name' does not exist on type 'Task'
```

**Expected TypeScript error:**
```
Property 'name' does not exist on type 'Task'.
Did you mean 'title'?
```

---

## 8. Error State Working

### When Backend Is Down

```typescript
const { error } = useTasks();
// error = "API Error: 500" or "Failed to fetch"
```

### When 404 Returned

```typescript
const { task, error } = useTask(99999);
// error = "API Error: 404"
```

---

## Complete Verification Checklist

### Before Moving On

- [ ] API client exists in shared/api/
- [ ] Feature API functions exist
- [ ] Custom hooks manage loading/error/data
- [ ] Both servers running
- [ ] Data displays in browser
- [ ] Network tab shows real requests
- [ ] TypeScript catches type errors
- [ ] Error states work properly

### Success State

When all checkboxes complete, the API layer is working correctly.

---

## Quick Reference

### File Locations

```
shared/api/client.ts       - API client
features/*/api.ts          - Feature API functions
features/*/hooks.ts        - Custom hooks
features/*/types.ts        - TypeScript types
```

### Commands

```bash
# Start backend
cd backend && uvicorn app.main:app --port 8123

# Start frontend
cd frontend && npm run dev

# Test API directly
curl http://localhost:8123/tasks
```

### Hook Pattern

```typescript
function useData() {
  const [data, setData] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    api.getData()
      .then(setData)
      .catch(err => setError(err.message))
      .finally(() => setLoading(false));
  }, []);
  
  return { data, loading, error };
}
```

---

## Troubleshooting Quick Reference

| Problem | Likely Cause | Solution |
|---------|-------------|----------|
| CORS error | Backend not configured | Add CORSMiddleware |
| 404 error | Wrong endpoint | Check URL matches backend route |
| Type error | Schema mismatch | Update TypeScript types |
| Infinite loop | Wrong useEffect deps | Use empty array or useCallback |
| No data shows | Backend down | Start uvicorn |
| Loading forever | Promise not resolving | Check network tab for request |
