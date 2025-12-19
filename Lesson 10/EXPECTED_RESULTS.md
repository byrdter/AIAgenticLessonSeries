# Expected Results for Lesson 10

## What Success Looks Like at Each Step

Use this to verify the frontend testing setup is working correctly.

---

## 1. Dependencies Installed

### Command

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom @testing-library/user-event jsdom
```

### Expected Output

```
added 50+ packages in 5s
```

### Verify in package.json

```json
{
  "devDependencies": {
    "@testing-library/jest-dom": "^6.x.x",
    "@testing-library/react": "^14.x.x",
    "@testing-library/user-event": "^14.x.x",
    "jsdom": "^24.x.x",
    "vitest": "^1.x.x"
  }
}
```

---

## 2. Vitest Configuration

### Expected vitest.config.ts

```typescript
import { defineConfig } from 'vitest/config';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'jsdom',
    globals: true,
    setupFiles: './src/test/setup.ts',
  },
});
```

### Expected src/test/setup.ts

```typescript
import '@testing-library/jest-dom';
```

### Expected package.json Scripts

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "lint": "eslint .",
    "test": "vitest",
    "test:run": "vitest run"
  }
}
```

---

## 3. First Test Passing

### Test File: src/shared/components/Button.test.tsx

```tsx
import { render, screen } from '@testing-library/react';
import { Button } from './Button';

describe('Button', () => {
  it('renders children correctly', () => {
    render(<Button onClick={() => {}}>Click Me</Button>);
    expect(screen.getByText('Click Me')).toBeInTheDocument();
  });
});
```

### Command

```bash
npm run test:run
```

### Expected Output

```
 ✓ src/shared/components/Button.test.tsx (1)
   ✓ Button (1)
     ✓ renders children correctly

 Test Files  1 passed (1)
      Tests  1 passed (1)
   Start at  10:30:45
   Duration  1.23s
```

**Key indicators:**
- Green checkmark (✓)
- "1 passed"
- No errors

---

## 4. Multiple Tests Passing

### Updated Test File

```tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { Button } from './Button';

describe('Button', () => {
  it('renders children correctly', () => {
    render(<Button onClick={() => {}}>Click Me</Button>);
    expect(screen.getByText('Click Me')).toBeInTheDocument();
  });

  it('calls onClick when clicked', () => {
    const handleClick = vi.fn();
    render(<Button onClick={handleClick}>Click Me</Button>);
    
    fireEvent.click(screen.getByText('Click Me'));
    
    expect(handleClick).toHaveBeenCalledTimes(1);
  });

  it('does not call onClick when disabled', () => {
    const handleClick = vi.fn();
    render(<Button onClick={handleClick} disabled>Click Me</Button>);
    
    fireEvent.click(screen.getByText('Click Me'));
    
    expect(handleClick).not.toHaveBeenCalled();
  });
});
```

### Expected Output

```
 ✓ src/shared/components/Button.test.tsx (3)
   ✓ Button (3)
     ✓ renders children correctly
     ✓ calls onClick when clicked
     ✓ does not call onClick when disabled

 Test Files  1 passed (1)
      Tests  3 passed (3)
   Start at  10:32:15
   Duration  1.45s
```

**Key indicators:**
- Three green checkmarks
- "3 passed"
- All test names visible

---

## 5. Test Failure (RED)

### After Breaking Button (removing disabled prop)

```tsx
// Broken Button.tsx
export function Button({ children, onClick, disabled }: ButtonProps) {
  return (
    <button onClick={onClick}>  {/* disabled prop missing */}
      {children}
    </button>
  );
}
```

### Expected Output

```
 ✓ src/shared/components/Button.test.tsx (3)
   ✓ Button (3)
     ✓ renders children correctly
     ✓ calls onClick when clicked
     ✗ does not call onClick when disabled

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯ Failed Tests 1 ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯

 FAIL  src/shared/components/Button.test.tsx > Button > does not call onClick when disabled
AssertionError: expected "spy" to not be called at all, but actually been called 1 times

 ❯ src/shared/components/Button.test.tsx:21:26
     19|     fireEvent.click(screen.getByText('Click Me'));
     20|
     21|     expect(handleClick).not.toHaveBeenCalled();
       |                          ^
     22|   });
     23| });

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯

 Test Files  1 failed (1)
      Tests  1 failed | 2 passed (3)
```

**Key indicators:**
- Red X (✗) on failing test
- "FAIL" with file path
- Error message explains the problem
- Line number shown
- "1 failed | 2 passed"

---

## 6. Test Fixed (GREEN Again)

### After Fixing Button

```tsx
// Fixed Button.tsx
export function Button({ children, onClick, disabled }: ButtonProps) {
  return (
    <button onClick={onClick} disabled={disabled}>
      {children}
    </button>
  );
}
```

### Expected Output

```
 ✓ src/shared/components/Button.test.tsx (3)
   ✓ Button (3)
     ✓ renders children correctly
     ✓ calls onClick when clicked
     ✓ does not call onClick when disabled

 Test Files  1 passed (1)
      Tests  3 passed (3)
   Start at  10:35:00
   Duration  1.32s
```

**Key indicators:**
- All green checkmarks again
- "3 passed"
- No failures

---

## 7. Hook Test Passing

### Hook File: src/shared/hooks/useCounter.ts

```typescript
import { useState } from 'react';

export function useCounter(initial = 0) {
  const [count, setCount] = useState(initial);
  
  const increment = () => setCount(c => c + 1);
  const decrement = () => setCount(c => c - 1);
  const reset = () => setCount(initial);
  
  return { count, increment, decrement, reset };
}
```

### Test File: src/shared/hooks/useCounter.test.ts

```typescript
import { renderHook, act } from '@testing-library/react';
import { useCounter } from './useCounter';

describe('useCounter', () => {
  it('starts with initial value', () => {
    const { result } = renderHook(() => useCounter(5));
    expect(result.current.count).toBe(5);
  });

  it('increments the count', () => {
    const { result } = renderHook(() => useCounter(0));
    
    act(() => {
      result.current.increment();
    });
    
    expect(result.current.count).toBe(1);
  });

  it('decrements the count', () => {
    const { result } = renderHook(() => useCounter(5));
    
    act(() => {
      result.current.decrement();
    });
    
    expect(result.current.count).toBe(4);
  });

  it('resets to initial value', () => {
    const { result } = renderHook(() => useCounter(10));
    
    act(() => {
      result.current.increment();
      result.current.increment();
      result.current.reset();
    });
    
    expect(result.current.count).toBe(10);
  });
});
```

### Expected Output

```
 ✓ src/shared/hooks/useCounter.test.ts (4)
   ✓ useCounter (4)
     ✓ starts with initial value
     ✓ increments the count
     ✓ decrements the count
     ✓ resets to initial value

 Test Files  1 passed (1)
      Tests  4 passed (4)
```

---

## Complete Verification Checklist

### Before Moving On

- [ ] Testing dependencies installed
- [ ] vitest.config.ts exists with jsdom environment
- [ ] src/test/setup.ts exists with jest-dom import
- [ ] Test scripts in package.json
- [ ] At least one component test written
- [ ] Tests pass (green output)
- [ ] Intentionally broke something
- [ ] Tests failed (red output)
- [ ] Fixed the issue
- [ ] Tests pass again (green output)

### Success State

When all checkboxes are complete, the frontend testing guardrail is operational.

---

## Common Output Variations

### Watch Mode Output

When running `npm run test` (watch mode):

```
 RERUN  src/shared/components/Button.test.tsx x1

 ✓ src/shared/components/Button.test.tsx (3)

 Test Files  1 passed (1)
      Tests  3 passed (3)

 Duration  234ms

 PASS  Waiting for file changes...
       press h to show help, press q to quit
```

### With Coverage

```bash
npm run test:run -- --coverage
```

```
 ✓ src/shared/components/Button.test.tsx (3)

 Test Files  1 passed (1)
      Tests  3 passed (3)

---------------------|---------|----------|---------|---------|
File                 | % Stmts | % Branch | % Funcs | % Lines |
---------------------|---------|----------|---------|---------|
All files            |   85.71 |      100 |     100 |   85.71 |
 Button.tsx          |     100 |      100 |     100 |     100 |
---------------------|---------|----------|---------|---------|
```

---

## Quick Reference

### Commands

```bash
# Run tests once
npm run test:run

# Run tests in watch mode
npm run test

# Run specific test file
npm run test:run -- Button.test.tsx

# Run tests matching pattern
npm run test:run -- -t "renders"

# Run with coverage
npm run test:run -- --coverage
```

### Common Matchers

```typescript
// Presence
expect(element).toBeInTheDocument();
expect(element).not.toBeInTheDocument();

// Visibility
expect(element).toBeVisible();
expect(element).not.toBeVisible();

// Enabled/Disabled
expect(element).toBeEnabled();
expect(element).toBeDisabled();

// Text content
expect(element).toHaveTextContent('Hello');

// Function calls
expect(mockFn).toHaveBeenCalled();
expect(mockFn).toHaveBeenCalledTimes(1);
expect(mockFn).toHaveBeenCalledWith('arg');
expect(mockFn).not.toHaveBeenCalled();
```

---

## Troubleshooting Quick Reference

| Problem | Likely Cause | Solution |
|---------|-------------|----------|
| "vitest: not found" | Not installed | `npm install -D vitest` |
| "document not defined" | No jsdom | Add `environment: 'jsdom'` to config |
| "toBeInTheDocument not a function" | No jest-dom | Check setup file imports |
| "vi is not defined" | No globals | Add `globals: true` to config |
| Test passes but shouldn't | Wrong assertion | Use `screen.debug()` to see DOM |
| Can't find element | Wrong query | Try different query method |
