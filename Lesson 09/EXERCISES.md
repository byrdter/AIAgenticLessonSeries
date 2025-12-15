# Lesson 9 — Exercises

## Frontend Foundations Tour & Guardrails

These exercises help you explore the existing frontend and understand how the guardrails work.

---

## Exercise 1: Explore the Folder Structure

Understand the frontend architecture by mapping it to the backend.

### Step 1: List the Frontend Structure

```bash
cd frontend
find src -type d | head -20
```

### Step 2: Create a Structure Map

Create a file called `FRONTEND_STRUCTURE.md`:

```markdown
# Frontend Structure

## src/app/
[What's in this folder? How does it compare to backend/app/core/?]

## src/shared/
[What's in this folder? What's reusable?]

## src/features/
[What features exist? What's in each feature folder?]
```

### Step 3: Create Comparison Table

```markdown
| Backend | Frontend | Purpose |
|---------|----------|---------|
| core/ | app/ | |
| shared/ | shared/ | |
| features/ | features/ | |
```

---

## Exercise 2: Understand TypeScript Strict Mode

Explore the TypeScript configuration and test it.

### Step 1: Examine tsconfig.json

```bash
cat frontend/tsconfig.json
```

Document all the strict-related settings you find.

### Step 2: Intentionally Break TypeScript

Create `frontend/src/test-type-error.ts`:

```typescript
// Test file - will be deleted

function greet(name: string): string {
  return "Hello, " + name;
}

// Error 1: Wrong argument type
greet(123);

// Error 2: Possibly undefined with noUncheckedIndexedAccess
const items = ["a", "b", "c"];
const first = items[0];
console.log(first.toUpperCase());  // first could be undefined
```

### Step 3: Run TypeScript Check

```bash
npx tsc --noEmit
```

### Step 4: Document the Errors

What errors did TypeScript catch? List each one.

### Step 5: Clean Up

```bash
rm frontend/src/test-type-error.ts
```

---

## Exercise 3: Test ESLint

Understand what ESLint catches.

### Step 1: Run ESLint

```bash
cd frontend
npm run lint
```

Are there any existing issues?

### Step 2: Create a File with Lint Errors

Create `frontend/src/test-lint-error.tsx`:

```tsx
import { useState, useEffect } from 'react';

function BrokenComponent() {
  const [count, setCount] = useState(0);
  const unusedVariable = "I'm never used";
  
  useEffect(() => {
    console.log(count);
  }, []); // Missing count in dependencies
  
  return <div>{count}</div>;
}

export default BrokenComponent;
```

### Step 3: Run ESLint Again

```bash
npm run lint
```

### Step 4: Document What ESLint Catches

List each error/warning and what rule triggered it.

### Step 5: Clean Up

```bash
rm frontend/src/test-lint-error.tsx
```

---

## Exercise 4: Tour a Feature Slice

Understand how features are organized.

### Step 1: Pick a Feature

Look in `frontend/src/features/`. Pick one feature folder.

### Step 2: Document the Structure

For your chosen feature, document:

```markdown
# Feature: [name]

## Files
- [list all files]

## Components
- [what React components exist?]

## Hooks
- [what custom hooks exist?]

## Types
- [what TypeScript types are defined?]

## API
- [how does it fetch data?]
```

### Step 3: Compare to Backend

Find the matching backend feature (if one exists) and note the similarities.

---

## Exercise 5: Run All Guardrails

Practice running the full validation suite.

### Step 1: Create a Script

Create `frontend/validate.sh`:

```bash
#!/bin/bash
echo "=== Running ESLint ==="
npm run lint

echo ""
echo "=== Running TypeScript Check ==="
npx tsc --noEmit

echo ""
echo "=== Running Tests ==="
npm run test -- --run 2>/dev/null || echo "No tests configured yet"

echo ""
echo "=== All guardrails complete ==="
```

### Step 2: Make it Executable

```bash
chmod +x frontend/validate.sh
```

### Step 3: Run It

```bash
./frontend/validate.sh
```

### Step 4: Document the Output

What does each guardrail report?

---

## Exercise 6: Explore the Shared Folder

Understand what's available for reuse.

### Step 1: List Shared Contents

```bash
ls -la frontend/src/shared/
```

### Step 2: Document Available Components

Look in `shared/components/` (if it exists):

```markdown
# Shared Components

## [Component Name]
- Props: [what props does it accept?]
- Purpose: [what does it do?]
```

### Step 3: Document Available Hooks

Look in `shared/hooks/` (if it exists):

```markdown
# Shared Hooks

## [Hook Name]
- Parameters: [what does it accept?]
- Returns: [what does it return?]
- Purpose: [what does it do?]
```

---

## Exercise 7: Compare Guardrails

Create a comprehensive comparison between backend and frontend guardrails.

### Create Comparison Document

`GUARDRAILS_COMPARISON.md`:

```markdown
# Backend vs Frontend Guardrails

## Testing
| Aspect | Backend | Frontend |
|--------|---------|----------|
| Tool | pytest | Vitest |
| Command | | |
| Config file | | |
| Test location | | |

## Linting
| Aspect | Backend | Frontend |
|--------|---------|----------|
| Tool | Ruff | ESLint |
| Command | | |
| Config file | | |
| What it catches | | |

## Type Checking
| Aspect | Backend | Frontend |
|--------|---------|----------|
| Tool | MyPy | TypeScript |
| Command | | |
| Config file | | |
| Strict settings | | |

## The PIV Loop
How does Plan → Implement → Validate work the same on frontend?
```

---

## Exercise 8: Prepare for Testing (Lesson 10)

Get ready for the next lesson on frontend testing.

### Step 1: Check if Tests Exist

```bash
ls frontend/src/**/*.test.* 2>/dev/null || echo "No test files found"
```

### Step 2: Check Test Configuration

Look for:
- `vitest.config.ts`
- `jest.config.js`
- Test setup files

### Step 3: Try Running Tests

```bash
cd frontend
npm run test -- --run
```

What happens? Document the output.

### Step 4: Questions for Lesson 10

Write down questions you have about frontend testing:
1. How do you test React components?
2. How do you mock API calls?
3. How do you test hooks?

---

## Submission Checklist

Before moving to Lesson 10:

- [ ] Explored folder structure (Exercise 1)
- [ ] Tested TypeScript strict mode (Exercise 2)
- [ ] Tested ESLint (Exercise 3)
- [ ] Toured a feature slice (Exercise 4)
- [ ] Ran all guardrails (Exercise 5)
- [ ] Explored shared folder (Exercise 6)
- [ ] Created guardrails comparison (Exercise 7)
- [ ] Prepared for testing lesson (Exercise 8)

---

## Looking Ahead: Lesson 10

In Lesson 10, you'll run the test suite and write component tests. You'll see:
- Vitest running tests (like pytest)
- React Testing Library for component tests
- The RED → GREEN cycle

Same pattern as backend testing — tests as guardrails for AI-generated code.
