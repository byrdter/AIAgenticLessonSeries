# Claude Code Prompts for Lesson 11

## Prompts for Exploring and Building API Layer

---

## EXPLORATION PROMPTS

### Explore Existing API Layer

```
Look at the frontend API layer:

1. Find the API client in shared/api/ or similar
2. Find feature API functions (e.g., features/*/api.ts)
3. Find custom hooks that fetch data

Show me the structure and explain how data flows from component to backend.
```

---

### Understand the API Client

```
Show me the API client configuration:

1. Where is the base URL configured?
2. What default headers are set?
3. How are errors handled?
4. How is the response typed?

Explain how this compares to the backend's database configuration.
```

---

### Trace a Data Fetch

```
Trace how data flows from a component to the backend:

1. Find a component that displays data (like tasks or notes)
2. Find the hook it uses
3. Find the API function the hook calls
4. Find what endpoint the API function hits

Draw out the flow for me.
```

---

## DEMONSTRATION PROMPTS

### Start Both Servers

```
Start both the backend and frontend servers:

Backend:
cd backend
uvicorn app.main:app --port 8123

Frontend:
cd frontend
npm run dev

Confirm both are running.
```

---

### Create Test Data

```
Create some test data in the backend:

curl -X POST http://localhost:8123/tasks \
  -H "Content-Type: application/json" \
  -d '{"title": "Learn API patterns"}'

curl -X POST http://localhost:8123/tasks \
  -H "Content-Type: application/json" \
  -d '{"title": "Build frontend features"}'

curl http://localhost:8123/tasks
```

---

### Test API Client Directly

```
In the browser console or a test file, test the API client:

import { apiClient } from '@/shared/api/client';

// Try fetching tasks
const tasks = await apiClient('/tasks');
console.log(tasks);

Does it work? What type does it return?
```

---

## BUILDING PROMPTS

### Create API Function for New Endpoint

```
Add API functions for a new endpoint. For example, if we have notes:

Create frontend/src/features/notes/api.ts:

import { apiClient } from '@/shared/api/client';
import { Note, CreateNoteInput } from './types';

export const notesApi = {
  getAll: () => apiClient<Note[]>('/notes'),
  getById: (id: number) => apiClient<Note>(`/notes/${id}`),
  create: (data: CreateNoteInput) => 
    apiClient<Note>('/notes', {
      method: 'POST',
      body: JSON.stringify(data),
    }),
};
```

---

### Create Custom Data Fetching Hook

```
Create a custom hook for fetching notes:

// features/notes/hooks.ts
import { useState, useEffect, useCallback } from 'react';
import { notesApi } from './api';
import { Note } from './types';

export function useNotes() {
  const [notes, setNotes] = useState<Note[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  const refetch = useCallback(() => {
    setLoading(true);
    setError(null);
    notesApi.getAll()
      .then(setNotes)
      .catch(err => setError(err.message))
      .finally(() => setLoading(false));
  }, []);

  useEffect(() => {
    refetch();
  }, [refetch]);

  return { notes, loading, error, refetch };
}
```

---

### Create Mutation Hook

```
Create a hook for creating a new task:

// features/tasks/hooks.ts
export function useCreateTask() {
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const createTask = async (data: CreateTaskInput) => {
    setLoading(true);
    setError(null);
    try {
      const task = await tasksApi.create(data);
      return task;
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Failed to create');
      throw err;
    } finally {
      setLoading(false);
    }
  };

  return { createTask, loading, error };
}

Usage:
const { createTask, loading } = useCreateTask();
await createTask({ title: 'New task' });
```

---

### Create Types Matching Backend

```
Look at the backend Pydantic schemas and create matching TypeScript types:

Backend (schemas.py):
class TaskResponse(BaseModel):
    id: int
    title: str
    completed: bool
    created_at: datetime

Frontend (types.ts):
export interface Task {
  id: number;
  title: string;
  completed: boolean;
  created_at: string;  // ISO string
}

export interface CreateTaskInput {
  title: string;
}
```

---

## TESTING PROMPTS

### Test a Custom Hook

```
Write a test for the useTasks hook:

// features/tasks/hooks.test.ts
import { renderHook, waitFor } from '@testing-library/react';
import { useTasks } from './hooks';
import { tasksApi } from './api';

// Mock the API
vi.mock('./api', () => ({
  tasksApi: {
    getAll: vi.fn(),
  },
}));

describe('useTasks', () => {
  it('fetches tasks on mount', async () => {
    const mockTasks = [{ id: 1, title: 'Test', completed: false }];
    vi.mocked(tasksApi.getAll).mockResolvedValue(mockTasks);

    const { result } = renderHook(() => useTasks());

    expect(result.current.loading).toBe(true);

    await waitFor(() => {
      expect(result.current.loading).toBe(false);
    });

    expect(result.current.tasks).toEqual(mockTasks);
    expect(result.current.error).toBeNull();
  });

  it('handles errors', async () => {
    vi.mocked(tasksApi.getAll).mockRejectedValue(new Error('API Error'));

    const { result } = renderHook(() => useTasks());

    await waitFor(() => {
      expect(result.current.loading).toBe(false);
    });

    expect(result.current.error).toBe('API Error');
  });
});
```

---

### Test a Component That Fetches Data

```
Write a test for TaskList component:

// features/tasks/components/TaskList.test.tsx
import { render, screen, waitFor } from '@testing-library/react';
import { TaskList } from './TaskList';
import { useTasks } from '../hooks';

vi.mock('../hooks');

describe('TaskList', () => {
  it('shows loading state', () => {
    vi.mocked(useTasks).mockReturnValue({
      tasks: [],
      loading: true,
      error: null,
      refetch: vi.fn(),
    });

    render(<TaskList />);
    expect(screen.getByText(/loading/i)).toBeInTheDocument();
  });

  it('shows tasks when loaded', () => {
    vi.mocked(useTasks).mockReturnValue({
      tasks: [{ id: 1, title: 'Test Task', completed: false }],
      loading: false,
      error: null,
      refetch: vi.fn(),
    });

    render(<TaskList />);
    expect(screen.getByText('Test Task')).toBeInTheDocument();
  });

  it('shows error state', () => {
    vi.mocked(useTasks).mockReturnValue({
      tasks: [],
      loading: false,
      error: 'Failed to load',
      refetch: vi.fn(),
    });

    render(<TaskList />);
    expect(screen.getByText(/failed to load/i)).toBeInTheDocument();
  });
});
```

---

## DEBUGGING PROMPTS

### Debug CORS Error

```
The frontend is getting a CORS error when calling the API.

Check:
1. Is the backend CORS middleware configured?
2. Does it allow the frontend origin (http://localhost:5173)?
3. Does it allow the HTTP methods being used?

Show me the CORS configuration and fix any issues.
```

---

### Debug Type Mismatch

```
TypeScript is complaining about a type mismatch between the API response and my interface.

The backend returns:
[paste backend response]

My TypeScript interface is:
[paste interface]

Find the mismatch and fix the types.
```

---

### Debug Hook Not Updating

```
My custom hook fetches data but the component doesn't re-render.

Check:
1. Is useState being used correctly?
2. Is the setter being called?
3. Are there any dependency issues in useEffect?

Show me the hook code and diagnose the issue.
```

---

## TIPS FOR USING THESE PROMPTS

1. **Start with exploration** — Understand existing patterns first

2. **Build incrementally** — API function, then hook, then component

3. **Test each layer** — Mock the layer below when testing

4. **Match backend types** — Keep frontend types in sync with Pydantic

5. **Use Network tab** — Always verify real HTTP is happening
