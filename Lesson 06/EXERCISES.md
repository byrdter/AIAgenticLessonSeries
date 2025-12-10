# Lesson 6 — Exercises

## The Guardrails: pytest, Ruff, and MyPy in Action



---

## Exercise 1: Run the Guardrails Yourself

Before breaking anything, establish your baseline.

### Step 1: Run pytest

```bash
cd backend
uv run pytest app/features/tasks/tests/ -v
```

**Questions:**
- How many tests ran?
- Did they all pass?
- How long did it take?

### Step 2: Run Ruff

```bash
uv run ruff check app/features/tasks/
```

**Questions:**
- Were there any issues?
- If yes, what rule codes were shown?

### Step 3: Run MyPy

```bash
uv run mypy app/features/tasks/
```

**Questions:**
- Did it say "Success"?
- Were there any type errors?

### Document Your Baseline

Create a file called `baseline.md`:

```markdown
# My Guardrail Baseline

## pytest
- Tests run: [number]
- All passed: [yes/no]
- Time: [seconds]

## Ruff
- Issues found: [number]
- Details: [list if any]

## MyPy
- Success: [yes/no]
- Errors: [list if any]
```

---

## Exercise 2: Break and Fix (Test Failure)

Practice the cycle you saw in the lesson.

### Step 1: Break Something

Ask Claude Code:

```
In backend/app/features/tasks/service.py, find the get_task function.
Change it to always return None, regardless of whether the task exists.
```

### Step 2: Run pytest

```bash
uv run pytest app/features/tasks/tests/ -v
```

**Questions:**
- Which test(s) failed?
- What was the error message?
- What line number was mentioned?

### Step 3: Ask Claude Code to Fix It

```
The tests are failing. Here's the error:
[paste error]

Fix the code and run pytest again.
```

### Step 4: Verify

Did pytest pass after the fix?

### Document Your Experience

```markdown
# Exercise 2: Test Failure

## What I Broke
[describe the change]

## Error Message
[paste the error]

## How Claude Code Fixed It
[describe the fix]

## Tests After Fix
[pass/fail]
```

---

## Exercise 3: Break and Fix (Type Error)

Now practice with type checking.

### Step 1: Break Types

Ask Claude Code:

```
In backend/app/features/tasks/schemas.py, change the status field 
from str to bool in the TaskCreate schema.
```

### Step 2: Run MyPy

```bash
uv run mypy app/features/tasks/
```

**Questions:**
- What type error did MyPy find?
- What files were affected?
- What was the expected type vs actual type?

### Step 3: Ask Claude Code to Fix It

```
MyPy found type errors:
[paste errors]

Fix the types and run mypy again.
```

### Step 4: Verify

Did MyPy pass after the fix?

---

## Exercise 4: Break and Fix (Style/Lint)

Practice with Ruff.

### Step 1: Break Style

Ask Claude Code:

```
In backend/app/features/tasks/routes.py, add these imports at the top:
import os
import sys
import json

Don't use any of them in the code.
```

### Step 2: Run Ruff

```bash
uv run ruff check app/features/tasks/
```

**Questions:**
- What rule code appeared? (F401?)
- How many unused imports were detected?

### Step 3: Fix with Ruff Auto-Fix

Ruff can fix some issues automatically:

```bash
uv run ruff check app/features/tasks/ --fix
```

### Step 4: Verify

```bash
uv run ruff check app/features/tasks/
```

Did it pass now?

---

## Exercise 5: Multiple Failures

Break the code in a way that fails multiple guardrails.

### Step 1: Create Multiple Problems

Ask Claude Code:

```
Make these changes to the tasks feature:
1. In service.py, add an unused import: import random
2. In schemas.py, change description from Optional[str] to int
3. In service.py, make delete_task return "deleted" instead of None

Don't run anything yet.
```

### Step 2: Run All Three Guardrails

```bash
uv run pytest app/features/tasks/tests/ -v
uv run ruff check app/features/tasks/
uv run mypy app/features/tasks/
```

### Step 3: Count the Failures

- pytest failures: ___
- Ruff issues: ___
- MyPy errors: ___

### Step 4: Fix Everything

Ask Claude Code:

```
All three guardrails are failing:

pytest:
[paste errors]

Ruff:
[paste errors]

MyPy:
[paste errors]

Fix all the issues and run all three guardrails to verify.
```

### Step 5: Verify All Green

Did all three pass?

---

## Exercise 6: Security Check

Explore Ruff's security rules.

### Step 1: Add a Security Issue

Ask Claude Code:

```
In backend/app/features/tasks/service.py, add this line at the top of the file:
API_KEY = "sk-1234567890abcdef"
```

### Step 2: Run Ruff with Security Rules

```bash
uv run ruff check app/features/tasks/ --select S
```

**Questions:**
- What security rule triggered? (S105?)
- What does the rule check for?

### Step 3: Fix the Security Issue

Ask Claude Code:

```
Ruff found a security issue:
[paste error]

Fix it by using an environment variable instead of a hardcoded value.
```

### Step 4: Explore More Security Rules

Run on the whole backend:

```bash
uv run ruff check app/ --select S
```

Are there any other security issues?

---

## Exercise 7: Write Your Own Test

Practice adding to the test suite.

### Step 1: Identify Missing Test

Ask Claude Code:

```
Look at backend/app/features/tasks/tests/test_tasks.py.
What functionality is NOT currently tested?
Suggest a test we should add.
```

### Step 2: Add the Test

Ask Claude Code to add a new test. For example:

```
Add a test that verifies:
- Creating a task with a title less than 3 characters should fail
- The error response should have a 422 status code

Add this to test_tasks.py.
```

### Step 3: Run the New Test

```bash
uv run pytest app/features/tasks/tests/ -v
```

Did the new test pass or fail?

If it failed, you found a gap in the validation! Ask Claude Code to add the validation.

---

## Exercise 8: Document the Guardrails

Create a reference guide for yourself.

### Create `guardrails-reference.md`:

```markdown
# Guardrails Reference

## pytest

**Command:** `uv run pytest [path] -v`

**What it catches:**
- [list what you learned]

**How to read errors:**
- [notes on understanding output]

**Common issues:**
- [issues you encountered]

## Ruff

**Command:** `uv run ruff check [path]`

**What it catches:**
- [list what you learned]

**Useful options:**
- `--fix` — auto-fix some issues
- `--select S` — only security rules

**Common rule codes:**
- F401: [description]
- S105: [description]

## MyPy

**Command:** `uv run mypy [path]`

**What it catches:**
- [list what you learned]

**How to read errors:**
- [notes on understanding output]

## The Self-Correction Loop

1. Run guardrails
2. If failure, paste error to Claude Code
3. Ask for fix
4. Run guardrails again
5. Repeat until green
```

---

## Submission Checklist

Before moving to Lesson 7:

- [ ] Ran all three guardrails on baseline (Exercise 1)
- [ ] Broke and fixed a test failure (Exercise 2)
- [ ] Broke and fixed a type error (Exercise 3)
- [ ] Broke and fixed a lint issue (Exercise 4)
- [ ] Broke multiple things and fixed them all (Exercise 5)
- [ ] Explored security rules (Exercise 6)
- [ ] Added a new test (Exercise 7)
- [ ] Created guardrails reference (Exercise 8)

---

## Looking Ahead

In Lesson 7, you'll learn about structured logging:
- See logs stream in real-time
- Understand log events and correlation IDs
- Debug issues using logs instead of reading code

Logging is the fourth guardrail — it helps you understand what happened when something goes wrong in production.
