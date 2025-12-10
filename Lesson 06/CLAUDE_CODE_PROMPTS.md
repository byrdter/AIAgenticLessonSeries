# Claude Code Prompts for Lesson 6

## The Prompts You'll Use in This Lesson

These prompts run guardrails, break code intentionally, and have Claude Code fix issues.

---

## Prompt 1: Baseline Check (All Green)

Run this first to establish that everything works:

```
Run these three guardrails on the tasks feature:
1. pytest backend/app/features/tasks/tests/ -v
2. ruff check backend/app/features/tasks/
3. mypy backend/app/features/tasks/

Show me the results of each.
```

**Expected:** All three pass.

---

## Prompt 2: Break the Code (Test Failure)

This intentionally introduces a bug:

```
"In backend/app/features/tasks/schemas.py, change lines 48-49 from if 
  len(v) < 3: and raise ValueError("Title must be at least 3 
  characters") to if len(v) < 5: and raise ValueError("Title must be at 
  least 5 characters")
```

**Expected:** Claude Code edits service.py to return None.

---

## Prompt 3: Run pytest (See Failure)

```
Run pytest on the tasks feature tests.
```

**Expected:** RED — tests fail with assertion error.

---

## Prompt 4: Fix the Test Failure

```
The tests are failing. Look at the error message and fix the code.
Then run pytest again to verify the fix worked.
```

**Expected:** Claude Code reads error, fixes code, runs pytest, all green.

---

## Prompt 5: Break the Code Again (Type Error)

```
In backend/app/features/tasks/schemas.py, change the title field 
from str to int in the TaskCreate schema.

Then run mypy on the tasks feature.
```

**Expected:** MyPy shows type errors.

---

## Prompt 6: Fix the Type Error

```
MyPy found type errors. Fix them and run mypy again to verify.
```

**Expected:** Claude Code fixes types, mypy passes.

---

## Prompt 7: Final Verification

```
Run all three guardrails one more time to confirm everything passes:
1. pytest backend/app/features/tasks/tests/ -v
2. ruff check backend/app/features/tasks/
3. mypy backend/app/features/tasks/
```

**Expected:** All green.

---

## Troubleshooting Prompts

### If pytest Can't Find Tests

```
pytest says it collected 0 items. 
Check that backend/app/features/tasks/tests/ has test files 
and they're named test_*.py.
List the test files and show their contents.
```

### If MyPy Has Import Errors

```
MyPy is showing "Cannot find module" errors.
Run mypy with --ignore-missing-imports flag:
mypy backend/app/features/tasks/ --ignore-missing-imports
```

### If Claude Code's Fix Doesn't Work

```
The fix didn't work — the tests are still failing.
Here's the current error:
[paste error]

Look more carefully at what's wrong and try a different fix.
```

### If Multiple Errors Appear

```
Multiple guardrails are failing. Let's fix them one at a time.
Start with the pytest failures first — show me the error and fix it.
```

---

## Alternative Break Prompts

### Break with Unused Import (Ruff)

```
In backend/app/features/tasks/routes.py, add this import at the top:
import os

Don't use it anywhere — just add the import.
Then run ruff check on the tasks feature.
```

**Expected:** Ruff catches unused import (F401).

### Break with Multiple Issues

```
Make these changes to the tasks feature:
1. In service.py, change create_task to return None
2. In schemas.py, add an unused import: import json
3. In routes.py, change a type hint from Task to str

Then run all three guardrails and show me what fails.
```

**Expected:** All three guardrails fail with different errors.

### Break with Security Issue

```
In backend/app/features/tasks/service.py, add this line somewhere:
password = "admin123"

Then run ruff check.
```

**Expected:** Ruff catches hardcoded password (S105).

---

## Student Homework Prompts

### Exercise 1: Break and Fix

```
Break something in the tasks feature (your choice).
Run the guardrails to see the failure.
Then fix it and verify guardrails pass.

Document:
1. What you broke
2. What the error message said
3. How you fixed it
```

### Exercise 2: Security Check

```
Run ruff check on the entire backend with security rules:
ruff check backend/ --select S

Are there any security issues? If so, list them.
```

### Exercise 3: Type Coverage

```
Run mypy on the entire backend:
mypy backend/app/

How many type errors are there (if any)?
What files have the most issues?
```

---

## Combined Demo Prompt (If Short on Time)

This single prompt runs the whole cycle:

```
Let's test the guardrails on the tasks feature:

1. First, run all three guardrails (pytest, ruff, mypy) to show they pass

2. Then, break the code by changing create_task to return None in service.py

3. Run pytest to see it fail

4. Fix the bug and run pytest again

5. Show me the final green output

Walk me through each step.
```

---

## Prompt Structure Patterns

### Running Guardrails

```
Run [guardrail] on [path]:
[command]

Show me the results.
```

### Breaking Code

```
In [file path], change [what to change].
[Specific instruction]

Don't run anything yet — just make the change.
```

### Fixing Errors

```
[Guardrail] is failing with this error:
[paste error or describe]

Fix the code and run [guardrail] again to verify.
```

### Verification

```
Run all three guardrails to confirm everything passes:
1. pytest [path] -v
2. ruff check [path]
3. mypy [path]
```

---

## Tips for Live Demo

1. **Have prompts ready** — Copy/paste is faster than typing live

2. **Pause after errors** — Let viewers read the error before asking for a fix

3. **Show the code change** — When Claude Code breaks or fixes code, make sure the editor shows the change

4. **If things go wrong** — Just ask Claude Code to try again or diagnose the problem

5. **Keep it simple** — The basic break-then-fix cycle is enough. Don't over-complicate.

 1. pytest - Catches Runtime/Logic Errors

  File: backend/app/features/tasks/schemas.py

  Change: Modify the minimum title length validation:
> 
