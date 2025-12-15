# Claude Code Prompts for Lesson 9

## Prompts for Touring the Frontend & Running Guardrails

These prompts help explore the existing frontend and demonstrate the guardrails.

---

## EXPLORATION PROMPTS

### Explore the Frontend Structure

```
Look at the frontend folder structure.
Show me:
1. The contents of frontend/src/
2. What's in the app/ folder
3. What's in the shared/ folder
4. What's in the features/ folder

Explain how this structure compares to the backend structure.
```

---

### Show TypeScript Configuration

```
Open frontend/tsconfig.json and explain the strict TypeScript settings.

Which settings enforce type safety?
How does this compare to MyPy on the backend?
```

---

### Show Package Scripts

```
Look at frontend/package.json and list all the npm scripts.

Which scripts run the guardrails (lint, typecheck, test)?
```

---

## GUARDRAIL DEMONSTRATION PROMPTS

### Run All Frontend Guardrails

```
In the frontend folder, run all the guardrails:

1. npm run lint (ESLint)
2. npm run typecheck (TypeScript) - or npx tsc --noEmit if no script
3. npm run test (Vitest) - if configured

Show me the output of each.
```

---

### Start the Dev Server

```
Start the frontend development server:
cd frontend
npm run dev

Tell me the URL and confirm the app is running.
```

---

### Check TypeScript Strict Mode

```
Verify that TypeScript strict mode is enabled in the frontend:

1. Show me the relevant settings in tsconfig.json
2. Create a test file with a deliberate type error
3. Run the TypeScript check to show it catches the error
4. Delete the test file
```

---

### Check ESLint

```
Run ESLint on the frontend:
npm run lint

If there are any issues, explain what they are.
If it passes, confirm the code is clean.
```

---

## INTENTIONAL BREAKAGE PROMPTS (For Demonstration)

### Break TypeScript to Show Guardrail

```
In the frontend, create a temporary file src/test-type-error.ts with this content:

// Deliberate type error for demonstration
function greet(name: string) {
  return "Hello, " + name;
}

greet(123); // Wrong type!

const items: string[] = ["a", "b"];
console.log(items[5].toUpperCase()); // Possibly undefined

Then run: npx tsc --noEmit

Show me the errors TypeScript catches.
Then delete the file.
```

---

### Break ESLint to Show Guardrail

```
In the frontend, create a temporary file src/test-lint-error.tsx with content that violates ESLint rules:

import { useState, useEffect } from 'react';

function BrokenComponent() {
  const [count, setCount] = useState(0);
  const unusedVariable = "I'm never used";  // Unused variable
  
  useEffect(() => {
    console.log(count);
  }, []); // Missing dependency
  
  return <div>{count}</div>;
}

export default BrokenComponent;

Then run: npm run lint

Show me what ESLint catches.
Then delete the file.
```

---

## TOUR PROMPTS

### Tour a Feature Slice

```
Pick one feature folder in frontend/src/features/ and explain its structure:

1. What components does it have?
2. What hooks does it have?
3. What types are defined?
4. How does it connect to the API?

Compare this to a backend feature slice.
```

---

### Tour the Shared Folder

```
Look at frontend/src/shared/ and explain:

1. What reusable components exist?
2. What hooks are available?
3. Is there an API client?
4. What utilities are shared?
```

---

### Tour the App Shell

```
Look at frontend/src/app/ and explain:

1. What's the main entry point?
2. How is routing set up?
3. Are there any providers or context?
4. How does this compare to backend/app/core/?
```

---

## COMPARISON PROMPTS

### Compare Frontend to Backend Structure

```
Create a comparison showing how frontend and backend structures mirror each other:

Backend folder → Frontend equivalent
backend/app/core/ → ?
backend/app/shared/ → ?
backend/app/features/ → ?

Show specific examples from each.
```

---

### Compare Guardrails

```
List the frontend guardrails and their backend equivalents:

Frontend Tool → Backend Tool → What it catches

Include: linting, type checking, testing
```

---

## VERIFICATION PROMPTS

### Verify Frontend Setup

```
Verify the frontend is properly configured:

1. Show folder structure of frontend/src/
2. Confirm TypeScript strict mode in tsconfig.json
3. Run npm run lint and show output
4. Run npm run typecheck (or tsc --noEmit) and show output
5. Start npm run dev and confirm it runs

Report any issues found.
```

---

### Show Frontend-Backend Connection

```
Show how the frontend is configured to connect to the backend:

1. Find any .env or environment configuration
2. Look for API base URL settings
3. Show the API client configuration in shared/api/ or similar
```

---

## TIPS FOR USING THESE PROMPTS

1. **Start with exploration** — Understand what's there before running guardrails

2. **Run guardrails to demonstrate** — Show that TypeScript and ESLint catch issues

3. **Break things intentionally** — The best way to show guardrails is to trigger them

4. **Clean up after demos** — Delete any test files created for demonstration

5. **Connect to backend constantly** — The goal is showing the pattern is the same
