# Claude Code Prompts for Lesson 10

## Prompts for Setting Up and Running Frontend Tests

---

## MASTER PROMPT: Complete Testing Setup

Use this for comprehensive setup in one prompt:

```
Set up Vitest with React Testing Library for the frontend project:

1. Install testing dependencies:
   npm install -D vitest @testing-library/react @testing-library/jest-dom @testing-library/user-event jsdom

2. Create vitest.config.ts:
   - Use jsdom environment
   - Enable globals (describe, it, expect without imports)
   - Set setupFiles to ./src/test/setup.ts

3. Create src/test/setup.ts that imports @testing-library/jest-dom

4. Add npm scripts to package.json:
   - "test": "vitest"
   - "test:run": "vitest run"

5. Write a test for src/shared/components/Button.tsx:
   - Test that it renders children
   - Test that onClick is called when clicked
   - Test that onClick is NOT called when disabled

6. Run the tests and show me the output.
```

---

## STEP-BY-STEP PROMPTS

### Step 1: Install Dependencies

```
Install testing dependencies for the frontend:

npm install -D vitest @testing-library/react @testing-library/jest-dom @testing-library/user-event jsdom

Show me the updated package.json devDependencies.
```

---

### Step 2: Create Vitest Config

```
Create vitest.config.ts in the frontend root with:

- React plugin from @vitejs/plugin-react
- jsdom test environment
- globals: true (so we don't need to import describe, it, expect)
- setupFiles pointing to ./src/test/setup.ts
```

---

### Step 3: Create Test Setup

```
Create src/test/setup.ts that:
- Imports @testing-library/jest-dom to add custom matchers

This file runs before each test file.
```

---

### Step 4: Add Test Scripts

```
Add test scripts to package.json:

"test": "vitest"         - runs in watch mode
"test:run": "vitest run" - runs once and exits
```

---

### Step 5: Write Button Tests

```
Write tests for src/shared/components/Button.tsx in Button.test.tsx:

1. Test 'renders children correctly':
   - Render Button with "Click Me" text
   - Expect "Click Me" to be in the document

2. Test 'calls onClick when clicked':
   - Create mock function with vi.fn()
   - Render Button with mock as onClick
   - Fire click event
   - Expect mock to have been called once

3. Test 'does not call onClick when disabled':
   - Create mock function
   - Render Button with disabled={true}
   - Fire click event
   - Expect mock NOT to have been called

Use React Testing Library patterns.
```

---

### Step 6: Run Tests

```
Run the tests:
npm run test:run

Show me the full output.
```

---

## BREAKING AND FIXING PROMPTS

### Intentionally Break the Button

```
Modify src/shared/components/Button.tsx to introduce a bug:
Remove the disabled prop from the button element.

Don't fix it - I want to see the test fail.
Then run: npm run test:run
```

---

### Fix from Test Error

```
The Button tests are failing with this error:
[paste error]

Fix the Button component so all tests pass.
Then run npm run test:run to verify.
```

---

### Generic Fix Prompt

```
Tests are failing:
[paste test output]

Read the error messages and fix the issues.
Run tests again to verify.
```

---

## ADDITIONAL COMPONENT TESTS

### Test LoadingState

```
Write tests for src/shared/components/LoadingState.tsx:

1. Test 'renders default loading message':
   - Render LoadingState without props
   - Expect "Loading..." to be in the document

2. Test 'renders custom message':
   - Render LoadingState with message="Please wait..."
   - Expect "Please wait..." to be in the document

Save as LoadingState.test.tsx and run tests.
```

---

### Test ErrorState

```
Write tests for src/shared/components/ErrorState.tsx:

1. Test 'renders error message':
   - Render ErrorState with message="Something went wrong"
   - Expect error message to be visible

2. Test 'shows retry button when onRetry provided':
   - Render ErrorState with onRetry function
   - Expect button with "Retry" text to exist

3. Test 'calls onRetry when retry clicked':
   - Create mock onRetry
   - Click the retry button
   - Expect mock to have been called

4. Test 'does not show retry button when onRetry not provided':
   - Render ErrorState without onRetry
   - Expect no retry button

Save as ErrorState.test.tsx and run tests.
```

---

### Test Card

```
Write tests for src/shared/components/Card.tsx:

1. Test 'renders title':
   - Render Card with title="My Card"
   - Expect heading with "My Card" to be in document

2. Test 'renders children':
   - Render Card with <p>Card content</p> as children
   - Expect "Card content" to be in document

Save as Card.test.tsx and run tests.
```

---

## HOOK TESTING PROMPTS

### Create and Test useCounter

```
Create a useCounter hook in src/shared/hooks/useCounter.ts:
- Takes optional initial value (default 0)
- Returns { count, increment, decrement, reset }

Then write tests in useCounter.test.ts:
1. Test 'starts with initial value'
2. Test 'increments count'
3. Test 'decrements count'
4. Test 'resets to initial value'

Use renderHook and act from @testing-library/react.
```

---

### Test Async Hook

```
Create a simple async hook and test it:

// useAsync.ts
Takes an async function, returns { data, loading, error }

// useAsync.test.ts
Test loading state, success state, and error state
Use waitFor from @testing-library/react for async assertions
```

---

## TROUBLESHOOTING PROMPTS

### If vitest command not found

```
Running npm run test gives "vitest: command not found"

Check:
1. Is vitest in devDependencies?
2. Run npm install
3. Try npx vitest run

Fix the issue.
```

---

### If document is not defined

```
Tests fail with: ReferenceError: document is not defined

This means jsdom isn't configured. Check vitest.config.ts has:
test: {
  environment: 'jsdom'
}

Fix and run tests again.
```

---

### If matchers aren't working

```
Tests fail with: expect(...).toBeInTheDocument is not a function

The jest-dom matchers aren't loaded. Check:
1. src/test/setup.ts exists and imports '@testing-library/jest-dom'
2. vitest.config.ts has setupFiles: './src/test/setup.ts'

Fix and run tests again.
```

---

### If tests pass when they should fail

```
I broke the component but tests still pass.

Check:
1. Is the test file actually running? (check output)
2. Is the test testing the right thing?
3. Add screen.debug() to see what's rendered

Diagnose why the test isn't catching the bug.
```

---

## COVERAGE PROMPT

```
Run tests with coverage:

1. Add coverage script to package.json:
   "test:coverage": "vitest run --coverage"

2. Install coverage provider if needed:
   npm install -D @vitest/coverage-v8

3. Run: npm run test:coverage

Show me the coverage report.
```

---

## WATCH MODE DEMO

```
Start tests in watch mode:
npm run test

Then modify the Button component slightly.
Describe what happens when a file changes.
```

---

## TIPS FOR USING THESE PROMPTS

1. **Start with the master prompt** — Gets everything set up at once

2. **Use step-by-step if issues** — Easier to debug in pieces

3. **Break things on purpose** — Shows the guardrail working

4. **Test more components** — Practice makes perfect

5. **Hook testing is optional but valuable** — Shows the pattern extends beyond components
