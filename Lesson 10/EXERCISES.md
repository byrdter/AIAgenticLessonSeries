# Lesson 10 — Exercises

## Component & Unit Testing with Vitest

These exercises help you practice writing frontend tests and understanding the testing guardrail.

---

## Exercise 1: Verify Your Setup

Make sure testing is properly configured.

### Step 1: Check Configuration Files

Verify these files exist:
- `vitest.config.ts`
- `src/test/setup.ts`

### Step 2: Run Tests

```bash
npm run test:run
```

### Step 3: Document

Create a file `TESTING_SETUP.md`:

```markdown
# Testing Setup

## Configuration
- vitest.config.ts: [what does each option do?]
- src/test/setup.ts: [what does this file do?]

## Commands
- npm run test: [what does this do?]
- npm run test:run: [what does this do?]

## Testing Libraries
- vitest: [purpose]
- @testing-library/react: [purpose]
- @testing-library/jest-dom: [purpose]
- jsdom: [purpose]
```

---

## Exercise 2: Test the LoadingState Component

Write comprehensive tests for LoadingState.

### Step 1: Review the Component

```tsx
// src/shared/components/LoadingState.tsx
export function LoadingState({ message = 'Loading...' }: { message?: string }) {
  return <div className="loading">{message}</div>;
}
```

### Step 2: Write Tests

Create `src/shared/components/LoadingState.test.tsx`:

```tsx
import { render, screen } from '@testing-library/react';
import { LoadingState } from './LoadingState';

describe('LoadingState', () => {
  it('renders default loading message', () => {
    // TODO: render LoadingState without props
    // TODO: expect "Loading..." to be in document
  });

  it('renders custom message', () => {
    // TODO: render LoadingState with message="Please wait..."
    // TODO: expect "Please wait..." to be in document
  });
});
```

### Step 3: Run and Verify

```bash
npm run test:run -- LoadingState
```

Both tests should pass.

---

## Exercise 3: Test the ErrorState Component

Write tests including interaction testing.

### Step 1: Review the Component

```tsx
// src/shared/components/ErrorState.tsx
interface ErrorStateProps {
  message: string;
  onRetry?: () => void;
}

export function ErrorState({ message, onRetry }: ErrorStateProps) {
  return (
    <div className="error" role="alert">
      <p>Error: {message}</p>
      {onRetry && <button onClick={onRetry}>Retry</button>}
    </div>
  );
}
```

### Step 2: Write Tests

Create `src/shared/components/ErrorState.test.tsx`:

```tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { ErrorState } from './ErrorState';

describe('ErrorState', () => {
  it('renders error message', () => {
    render(<ErrorState message="Something went wrong" />);
    expect(screen.getByText(/Something went wrong/)).toBeInTheDocument();
  });

  it('has alert role for accessibility', () => {
    render(<ErrorState message="Error occurred" />);
    expect(screen.getByRole('alert')).toBeInTheDocument();
  });

  it('shows retry button when onRetry provided', () => {
    render(<ErrorState message="Error" onRetry={() => {}} />);
    // TODO: expect retry button to exist
  });

  it('does not show retry button when onRetry not provided', () => {
    render(<ErrorState message="Error" />);
    // TODO: expect retry button NOT to exist
    // Hint: use queryByRole instead of getByRole
  });

  it('calls onRetry when retry button clicked', () => {
    const handleRetry = vi.fn();
    render(<ErrorState message="Error" onRetry={handleRetry} />);
    
    // TODO: click the retry button
    // TODO: expect handleRetry to have been called
  });
});
```

### Step 3: Run and Verify

All 5 tests should pass.

---

## Exercise 4: Test the Card Component

Practice testing children rendering.

### Step 1: Review the Component

```tsx
// src/shared/components/Card.tsx
interface CardProps {
  title: string;
  children: React.ReactNode;
}

export function Card({ title, children }: CardProps) {
  return (
    <div className="card">
      <h2>{title}</h2>
      <div className="card-content">{children}</div>
    </div>
  );
}
```

### Step 2: Write Tests

Create `src/shared/components/Card.test.tsx`:

```tsx
import { render, screen } from '@testing-library/react';
import { Card } from './Card';

describe('Card', () => {
  it('renders title as heading', () => {
    render(<Card title="My Card"><p>Content</p></Card>);
    
    // Use getByRole for semantic queries
    expect(screen.getByRole('heading', { name: 'My Card' })).toBeInTheDocument();
  });

  it('renders children', () => {
    render(
      <Card title="Test Card">
        <p>This is the card content</p>
      </Card>
    );
    
    expect(screen.getByText('This is the card content')).toBeInTheDocument();
  });

  it('renders complex children', () => {
    render(
      <Card title="Complex Card">
        <ul>
          <li>Item 1</li>
          <li>Item 2</li>
        </ul>
      </Card>
    );
    
    // TODO: expect both list items to be in document
  });
});
```

---

## Exercise 5: Break and Fix

Practice the RED → GREEN cycle.

### Step 1: Break LoadingState

Modify `LoadingState.tsx` to always show "Loading..." regardless of the `message` prop:

```tsx
export function LoadingState({ message = 'Loading...' }: { message?: string }) {
  return <div className="loading">Loading...</div>;  // Bug: ignores message prop
}
```

### Step 2: Run Tests

```bash
npm run test:run -- LoadingState
```

Which test fails? What's the error message?

### Step 3: Fix It

Restore the correct implementation and verify tests pass.

### Step 4: Break ErrorState

Remove the `onRetry` functionality:

```tsx
export function ErrorState({ message, onRetry }: ErrorStateProps) {
  return (
    <div className="error" role="alert">
      <p>Error: {message}</p>
      {/* Removed retry button */}
    </div>
  );
}
```

### Step 5: Run Tests and Observe

Which tests fail? How clear are the error messages?

### Step 6: Fix and Document

Fix the component. Document what you learned about the RED → GREEN cycle.

---

## Exercise 6: Test a Custom Hook

Practice testing hooks with `renderHook`.

### Step 1: Create useToggle Hook

```tsx
// src/shared/hooks/useToggle.ts
import { useState, useCallback } from 'react';

export function useToggle(initial = false) {
  const [value, setValue] = useState(initial);
  
  const toggle = useCallback(() => setValue(v => !v), []);
  const setTrue = useCallback(() => setValue(true), []);
  const setFalse = useCallback(() => setValue(false), []);
  
  return { value, toggle, setTrue, setFalse };
}
```

### Step 2: Write Tests

```tsx
// src/shared/hooks/useToggle.test.ts
import { renderHook, act } from '@testing-library/react';
import { useToggle } from './useToggle';

describe('useToggle', () => {
  it('starts with initial value (default false)', () => {
    const { result } = renderHook(() => useToggle());
    expect(result.current.value).toBe(false);
  });

  it('starts with provided initial value', () => {
    const { result } = renderHook(() => useToggle(true));
    expect(result.current.value).toBe(true);
  });

  it('toggles value', () => {
    const { result } = renderHook(() => useToggle(false));
    
    act(() => {
      result.current.toggle();
    });
    
    expect(result.current.value).toBe(true);
    
    act(() => {
      result.current.toggle();
    });
    
    expect(result.current.value).toBe(false);
  });

  it('setTrue sets value to true', () => {
    // TODO: implement this test
  });

  it('setFalse sets value to false', () => {
    // TODO: implement this test
  });
});
```

### Step 3: Complete and Run

Fill in the TODO tests and verify all pass.

---

## Exercise 7: Test User Interactions

Practice more complex interaction testing.

### Step 1: Create a Counter Component

```tsx
// src/shared/components/Counter.tsx
interface CounterProps {
  initial?: number;
  min?: number;
  max?: number;
}

export function Counter({ initial = 0, min, max }: CounterProps) {
  const [count, setCount] = useState(initial);
  
  const increment = () => {
    if (max === undefined || count < max) {
      setCount(c => c + 1);
    }
  };
  
  const decrement = () => {
    if (min === undefined || count > min) {
      setCount(c => c - 1);
    }
  };
  
  return (
    <div>
      <button onClick={decrement}>-</button>
      <span>{count}</span>
      <button onClick={increment}>+</button>
    </div>
  );
}
```

### Step 2: Write Comprehensive Tests

```tsx
// src/shared/components/Counter.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { Counter } from './Counter';

describe('Counter', () => {
  it('displays initial count', () => {
    render(<Counter initial={5} />);
    expect(screen.getByText('5')).toBeInTheDocument();
  });

  it('increments on + click', () => {
    render(<Counter initial={0} />);
    fireEvent.click(screen.getByText('+'));
    expect(screen.getByText('1')).toBeInTheDocument();
  });

  it('decrements on - click', () => {
    render(<Counter initial={5} />);
    fireEvent.click(screen.getByText('-'));
    expect(screen.getByText('4')).toBeInTheDocument();
  });

  it('respects max limit', () => {
    render(<Counter initial={5} max={5} />);
    fireEvent.click(screen.getByText('+'));
    expect(screen.getByText('5')).toBeInTheDocument();  // Still 5, not 6
  });

  it('respects min limit', () => {
    render(<Counter initial={0} min={0} />);
    fireEvent.click(screen.getByText('-'));
    expect(screen.getByText('0')).toBeInTheDocument();  // Still 0, not -1
  });
});
```

### Step 3: Run and Verify

All tests should pass. If any fail, fix the component or the tests.

---

## Exercise 8: Compare to Backend Testing

Connect frontend testing to backend patterns.

### Step 1: Review Backend Test

Look at a backend test from Module 2:

```python
# backend test
def test_create_task(client):
    response = client.post("/tasks", json={"title": "Test"})
    assert response.status_code == 201
    assert response.json()["title"] == "Test"
```

### Step 2: Review Frontend Test

Look at a frontend test:

```tsx
// frontend test
it('creates a task', () => {
  render(<TaskForm onSubmit={handleSubmit} />);
  fireEvent.change(screen.getByLabelText('Title'), { target: { value: 'Test' } });
  fireEvent.click(screen.getByText('Create'));
  expect(handleSubmit).toHaveBeenCalledWith({ title: 'Test' });
});
```

### Step 3: Create Comparison Table

```markdown
| Aspect | Backend (pytest) | Frontend (Vitest) |
|--------|-----------------|-------------------|
| Test runner | | |
| Assertion syntax | | |
| Mocking | | |
| Setup/teardown | | |
| Running tests | | |
```

### Step 4: Questions

1. What's similar between frontend and backend testing?
2. What's different?
3. How does the PIV loop apply to both?

---

## Submission Checklist

Before moving to Lesson 11:

- [ ] Testing setup verified (Exercise 1)
- [ ] LoadingState tests written and passing (Exercise 2)
- [ ] ErrorState tests written and passing (Exercise 3)
- [ ] Card tests written and passing (Exercise 4)
- [ ] Experienced RED → GREEN cycle (Exercise 5)
- [ ] Hook testing completed (Exercise 6)
- [ ] Counter interaction tests passing (Exercise 7)
- [ ] Compared to backend testing (Exercise 8)

---

## Looking Ahead: Lesson 11

In Lesson 11, you'll build the API layer and learn to test components that fetch data. You'll use:

- Mocking API calls
- Testing loading states
- Testing error states
- Integration between components and API

Same pattern, more complexity.
