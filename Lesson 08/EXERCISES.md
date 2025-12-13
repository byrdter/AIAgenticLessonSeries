# Lesson 8 — Exercises

## Building Complete Features: Practice the Full PIV Loop

These exercises have you build your own features from scratch, using everything you learned in Module 2.

---

## The Pattern You'll Follow

Every exercise follows the same pattern:

1. **Plan** — Write a comprehensive prompt
2. **Implement** — Claude Code builds the feature
3. **Validate** — Run all guardrails (pytest, Ruff, MyPy)
4. **Iterate** — Fix any failures
5. **Verify** — Test the API, check logs, query database

By the end of these exercises, this pattern should feel automatic.

---

## Exercise 1: Build a Comments Feature

Create a complete comments feature from scratch.

### The Prompt

Give this prompt to Claude Code:

```
Look at backend/app/examples/complete_feature/ as a reference.
Create a "comments" feature in backend/app/features/comments/ with:
- Comment model: id (int), content (str), author (str), created_at (datetime), updated_at (datetime)
- Full CRUD: models.py, schemas.py, routes.py, service.py
- Complete test suite in tests/test_comments.py
- Proper logging for all operations

Then:
1. Register the router in main.py
2. Create and run the migration
3. Run pytest, ruff, and mypy on the comments folder
4. Start the server on port 8123
5. Test with curl: create a comment, list comments
6. Show me the logs

Fix any guardrail failures before moving on.
```

### Verification Checklist

- [ ] Files created in backend/app/features/comments/
- [ ] pytest passes
- [ ] Ruff passes
- [ ] MyPy passes
- [ ] POST /comments creates a comment
- [ ] GET /comments returns the comment
- [ ] Logs show the operations

### Document Your Results

Take screenshots of:
1. The files in VS Code sidebar
2. All guardrails passing
3. The POST response JSON
4. The logs from the requests

---

## Exercise 2: Build a Tags Feature

Create a tags feature with a slightly different model.

### The Prompt

```
Using backend/app/examples/complete_feature/ as reference, create a "tags" feature in backend/app/features/tags/ with:
- Tag model: id (int), name (str), color (str - hex color code), created_at (datetime)
- Full CRUD operations
- Validation: name required, color must be valid hex (e.g., "#FF5733")
- Complete tests including validation tests
- Proper logging

Then run all guardrails, fix any issues, and test with curl.
```

### Additional Challenge

Add validation for the color field. It should:
- Start with #
- Be exactly 7 characters
- Contain only valid hex characters (0-9, A-F)

### Verification Checklist

- [ ] Tag model has name, color, created_at
- [ ] Color validation works (rejects invalid colors)
- [ ] All guardrails pass
- [ ] API works for CRUD operations
- [ ] Logs show operations

---

## Exercise 3: Build a Categories Feature with Self-Reference

Create a categories feature where categories can have parent categories.

### The Prompt

```
Create a "categories" feature in backend/app/features/categories/ with:
- Category model: id (int), name (str), description (str optional), parent_id (int optional - self-reference), created_at (datetime)
- Full CRUD operations
- A category with parent_id=null is a top-level category
- A category with parent_id=X is a child of category X
- Include tests for both top-level and nested categories
- Proper logging

Run all guardrails and test the API.
```

### Test Scenarios

1. Create a top-level category (no parent_id)
2. Create a child category (with parent_id pointing to the first category)
3. List all categories
4. Verify parent-child relationship in responses

### Verification Checklist

- [ ] Top-level categories work
- [ ] Child categories reference parent
- [ ] All guardrails pass
- [ ] API returns nested structure (or includes parent_id)

---

## Exercise 4: The Speed Run

Build a feature as fast as possible while maintaining quality.

### Choose Your Feature

Pick one:
- **Bookmarks** — id, url, title, description, created_at
- **Reminders** — id, message, due_date, completed, created_at
- **Contacts** — id, name, email, phone, created_at

### Time Yourself

1. Start timer when you begin writing the prompt
2. Stop timer when all guardrails pass and API works
3. Record your time

### Goals

| Time | Rating |
|------|--------|
| Under 10 minutes | Excellent |
| 10-15 minutes | Good |
| 15-20 minutes | Acceptable |
| Over 20 minutes | Needs practice |

### Document

- What was your total time?
- Did any guardrails fail? How many iterations to fix?
- What would you do differently next time?

---

## Exercise 5: Debug from Logs

Practice the debugging workflow from Lesson 7.

### Step 1: Build a Feature

Use any of the previous features, or create a new one.

### Step 2: Break Something

Ask Claude Code:

```
Introduce a subtle bug in the [feature] service layer that will 
cause an error when listing items. Don't tell me what you did.
```

### Step 3: Trigger the Error

```bash
curl http://localhost:8123/[feature]
```

### Step 4: Diagnose from Logs Only

Copy the error logs and give them to Claude Code:

```
Here are the error logs from my API:
[paste logs]

Based only on these logs, what went wrong?
```

### Step 5: Compare

Did Claude Code correctly identify the bug from logs alone?

### Step 6: Fix and Verify

Fix the bug and confirm the API works again.

---

## Exercise 6: Refactor an Existing Feature

Take an existing feature and improve it.

### Step 1: Analyze

Ask Claude Code:

```
Analyze the tasks feature in backend/app/features/tasks/.

What improvements could be made for:
- Code organization
- Test coverage
- Logging detail
- Error handling
- Type safety

Don't make changes yet, just list suggestions.
```

### Step 2: Pick Improvements

Choose 2-3 improvements to implement.

### Step 3: Implement

Ask Claude Code to implement the improvements one at a time.

### Step 4: Validate

After each change, run all guardrails:
- pytest
- ruff
- mypy

### Step 5: Document

What improvements did you make? Did they pass all guardrails?

---

## Exercise 7: Write Your Own Comprehensive Prompt

Design a prompt for a feature of your choice.

### Step 1: Choose a Domain

Pick something relevant to you:
- Blog posts
- Recipes
- Workouts
- Playlists
- Anything else

### Step 2: Write the Prompt

Include:
- Model definition with all fields and types
- File structure
- Reference to example feature
- Validation steps
- Testing commands
- Self-correction instruction

### Step 3: Test Your Prompt

Give it to Claude Code. Does it work first try?

### Step 4: Iterate on the Prompt

If it didn't work perfectly, improve the prompt. What was missing?

### Step 5: Share

(Optional) Share your final prompt with classmates. A good prompt is reusable.

---

## Exercise 8: The Full Stack Preview

Get ready for Module 3 by connecting backend to frontend.

### Step 1: Pick a Feature

Use one of your completed features.

### Step 2: Document the API

List all endpoints:

| Method | Path | Request Body | Response |
|--------|------|--------------|----------|
| POST | /feature | {...} | {...} |
| GET | /feature | - | [...] |
| ... | ... | ... | ... |

### Step 3: Think About Frontend

What React components would you need?
- List component (displays all items)
- Detail component (displays one item)
- Form component (creates/edits items)
- Delete confirmation?

### Step 4: Write a Frontend Prompt (Don't Run Yet)

Write what you would ask Claude Code to create for the frontend:

```
Create a React component for [feature] that:
- Displays a list of items from GET /[feature]
- Has a form to create new items via POST /[feature]
- Shows loading and error states
- Uses TypeScript
- Has proper styling
```

Save this prompt for Module 3.

---

## Submission Checklist

Before completing Module 2:

- [ ] Built at least one complete feature from scratch (Exercise 1, 2, or 3)
- [ ] All guardrails passed (pytest, Ruff, MyPy)
- [ ] API tested and working
- [ ] Logs verified
- [ ] Database contains data
- [ ] Debugged from logs at least once (Exercise 5)
- [ ] Wrote your own comprehensive prompt (Exercise 7)

### Portfolio Artifacts

Save these for your portfolio:
1. Screenshot of feature files in VS Code
2. Screenshot of all guardrails passing
3. Screenshot of API response
4. Your best comprehensive prompt
5. Notes on what you learned

---

## Reflection Questions

After completing the exercises:

1. **What's the hardest part of the PIV loop?** Planning, implementing, or validating?

2. **How did your prompts improve?** Compare your first prompt to your last.

3. **When did you need to intervene manually?** What couldn't Claude Code figure out?

4. **What patterns do you now recognize?** In code structure, in testing, in logging?

5. **How fast can you build a feature now?** Compared to traditional development?

---

## Looking Ahead: Module 3

In Module 3, you'll apply the same PIV loop to frontend development:

- React components
- TypeScript types
- Testing with Vitest
- Linting with ESLint
- Connecting to your backend APIs

The pattern is the same. The tools change.

See you in Module 3!
