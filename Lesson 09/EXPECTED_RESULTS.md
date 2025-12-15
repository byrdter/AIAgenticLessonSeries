# Expected Results for Lesson 9

## What Success Looks Like When Touring the Frontend

Use this to verify the frontend guardrails are working correctly.

---

## 1. Folder Structure

### Expected Structure

```
frontend/
├── src/
│   ├── app/            # App shell, routing (like backend core/)
│   │   └── ...
│   ├── shared/         # Reusable components, hooks
│   │   ├── components/
│   │   ├── hooks/
│   │   └── api/
│   └── features/       # Vertical slices
│       └── ...
├── e2e/                # Playwright tests
├── tsconfig.json
├── eslint.config.js    # or .eslintrc
├── vite.config.ts
└── package.json
```

### Verify Command

```bash
ls -la frontend/src/
```

**Expected output:**
```
app/
features/
shared/
main.tsx
...
```

---

## 2. TypeScript Strict Mode

### Expected tsconfig.json (Key Settings)

```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    ...
  }
}
```

**Key indicators:**
- `"strict": true` is present
- May have additional strict options

### View Command

```bash
cat frontend/tsconfig.json | grep -A 20 "compilerOptions"
```

---

## 3. Development Server Running

### Command

```bash
cd frontend
npm run dev
```

### Expected Output

```
  VITE v5.x.x  ready in 283 ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
  ➜  press h + enter to show help
```

**Key indicators:**
- "ready in X ms" message
- URL shown (default: localhost:5173)
- No errors

---

## 4. App Visible in Browser

### URL

```
http://localhost:5173
```

### Expected View

The React app should display. Content depends on what's already built — could be:
- A simple layout with header
- The Agent Workspace UI
- Default app shell

**Key indicators:**
- Page loads without errors
- No console errors in browser dev tools
- React is rendering

---

## 5. ESLint Running

### Command

```bash
cd frontend
npm run lint
```

### Expected Output (Clean)

```
> frontend@0.0.0 lint
> eslint .

```
(Empty output = no issues)

Or with explicit success:
```
✔ No ESLint warnings or errors
```

### Expected Output (With Issues)

If there are issues to fix:
```
/path/to/file.tsx
  1:1  warning  'unused' is defined but never used  @typescript-eslint/no-unused-vars

✖ 1 problem (0 errors, 1 warning)
```

**Key indicators:**
- Command runs without crashing
- Shows file paths and line numbers for issues
- Summary of problems at the end

---

## 6. TypeScript Check Running

### Command

```bash
cd frontend
npm run typecheck
```

Or if no script exists:
```bash
npx tsc --noEmit
```

### Expected Output (Clean)

```
(no output - everything compiles)
```

### Expected Output (With Errors)

```
src/shared/components/Button.tsx:5:3 - error TS2345: Argument of type 'number' is not assignable to parameter of type 'string'.

5   greet(123);
      ~~~

Found 1 error in src/shared/components/Button.tsx:5
```

**Key indicators:**
- Shows file path and line number
- Explains the type mismatch
- Counts total errors

---

## 7. Package.json Scripts

### Expected Scripts

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "lint": "eslint .",
    "typecheck": "tsc --noEmit",
    "test": "vitest"
  }
}
```

Scripts may vary, but should include:
- `dev` — starts development server
- `lint` — runs ESLint
- `typecheck` or similar — runs TypeScript
- `test` — runs Vitest (covered in Lesson 10)

---

## 8. Intentional Type Error (Demo)

### Create Test File

```typescript
// frontend/src/test-type-error.ts
function greet(name: string) {
  return "Hello, " + name;
}

greet(123); // Wrong type!
```

### Run TypeScript Check

```bash
npx tsc --noEmit
```

### Expected Error

```
src/test-type-error.ts:5:7 - error TS2345: Argument of type 'number' is not assignable to parameter of type 'string'.

5 greet(123);
        ~~~

Found 1 error.
```

**This demonstrates:** TypeScript strict mode catches type errors.

### Cleanup

```bash
rm frontend/src/test-type-error.ts
```

---

## 9. Intentional Lint Error (Demo)

### Create Test File

```tsx
// frontend/src/test-lint-error.tsx
import { useState, useEffect } from 'react';

function BrokenComponent() {
  const [count, setCount] = useState(0);
  const unusedVar = "never used";  // ESLint: no-unused-vars
  
  useEffect(() => {
    console.log(count);
  }, []); // ESLint: missing dependency
  
  return <div>{count}</div>;
}

export default BrokenComponent;
```

### Run ESLint

```bash
npm run lint
```

### Expected Errors

```
src/test-lint-error.tsx
  5:9   error  'unusedVar' is defined but never used     @typescript-eslint/no-unused-vars
  8:6   warning  React Hook useEffect has a missing dependency: 'count'  react-hooks/exhaustive-deps

✖ 2 problems (1 error, 1 warning)
```

**This demonstrates:** ESLint catches unused variables and hook issues.

### Cleanup

```bash
rm frontend/src/test-lint-error.tsx
```

---

## Complete Verification Checklist

### Before Moving On

- [ ] Folder structure visible (app/, shared/, features/)
- [ ] tsconfig.json has strict mode
- [ ] Dev server starts without errors
- [ ] App visible in browser
- [ ] npm run lint works
- [ ] TypeScript check works (tsc --noEmit or npm run typecheck)
- [ ] Understand how structure mirrors backend

### Success State

When all checkboxes are complete, students understand the frontend foundation and guardrails.

---

## Quick Reference Commands

```bash
# Navigate to frontend
cd frontend

# Start dev server
npm run dev

# Run ESLint
npm run lint

# Run TypeScript check
npm run typecheck
# or
npx tsc --noEmit

# Build for production
npm run build

# Run tests (Lesson 10)
npm run test
```

---

## Comparison Table

| Backend Command | Frontend Command |
|-----------------|------------------|
| `cd backend` | `cd frontend` |
| `pytest` | `npm run test` |
| `ruff check .` | `npm run lint` |
| `mypy .` | `npm run typecheck` |
| `uvicorn app.main:app` | `npm run dev` |
