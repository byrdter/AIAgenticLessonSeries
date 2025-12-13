# Claude Code Prompts for Lesson 8

## The Master Prompt and Variants

This lesson uses one comprehensive prompt that builds everything. Variants are provided for troubleshooting.

---

## THE MASTER PROMPT

This is the primary prompt for the lesson. It creates the entire feature and runs all validation.

```
Look at backend/app/examples/complete_feature/ as a reference.
Create a "notes" feature in backend/app/features/notes/ with:
- Note model: id (int), title (str), content (str), created_at (datetime), updated_at (datetime)
- Full CRUD endpoints: create, read one, read all, update, delete
- Files needed: models.py, schemas.py, routes.py, service.py
- Complete test suite in tests/test_notes.py
- Proper logging for all operations (started, completed, failed events)

Then:
1. Register the notes router in main.py
2. Create an Alembic migration for the notes table
3. Run the migration
4. Run pytest on backend/app/features/notes/tests/ -v
5. Run ruff check on backend/app/features/notes/
6. Run mypy on backend/app/features/notes/
7. Start the server on port 8123
8. Test with curl:
   - POST /notes with {"title": "First Note", "content": "Testing the notes feature"}
   - GET /notes to list all notes
9. Show the logs from these requests
10. Query the database to show the note exists

Show results at each step. If any guardrail fails, fix the issues and re-run.
```

---

## SHORTER VERSION (If Time Constrained)

```
Using backend/app/examples/complete_feature/ as reference, create a notes feature in backend/app/features/notes/ with:
- Note model: id, title, content, created_at, updated_at
- Full CRUD: models.py, schemas.py, routes.py, service.py, tests/

Then: register router, run migration, run all guardrails (pytest, ruff, mypy), start server on 8123, test with curl POST and GET /notes.

Show results at each step.
```

---

## STEP-BY-STEP VERSION (If Issues Occur)

If the master prompt doesn't work, break it into steps:

### Step 1: Create the Feature Structure

```
Look at backend/app/examples/complete_feature/ as a reference.

Create a "notes" feature in backend/app/features/notes/ with these files:
- __init__.py
- models.py (Note model with id, title, content, created_at, updated_at)
- schemas.py (NoteCreate, NoteUpdate, NoteResponse schemas)
- routes.py (CRUD endpoints)
- service.py (database operations)
- tests/__init__.py
- tests/test_notes.py (complete test suite)

Include proper logging for all operations.
```

### Step 2: Register the Router

```
Register the notes router in backend/app/main.py.
Follow the same pattern used for the tasks router.
```

### Step 3: Create and Run Migration

```
Create an Alembic migration for the notes table:
alembic revision --autogenerate -m "Add notes table"

Then run it:
alembic upgrade head
```

### Step 4: Run Guardrails

```
Run all three guardrails on the notes feature:
1. pytest backend/app/features/notes/tests/ -v
2. ruff check backend/app/features/notes/
3. mypy backend/app/features/notes/

If any fail, fix the issues and re-run.
```

### Step 5: Start Server and Test

```
Start the server:
uv run uvicorn app.main:app --reload --port 8123

Then test with:
curl -X POST http://localhost:8123/notes -H "Content-Type: application/json" -d '{"title": "First Note", "content": "Testing"}'
curl http://localhost:8123/notes
```

### Step 6: Verify Database

```
Query the database to show the notes table has data:
[SQLite] sqlite3 backend/app.db "SELECT * FROM notes;"
[PostgreSQL] psql -c "SELECT * FROM notes;"
```

---

## TROUBLESHOOTING PROMPTS

### If Files Are Incomplete

```
The notes feature files look incomplete. Please complete all files:

models.py - Full Note model with SQLAlchemy
schemas.py - NoteCreate, NoteUpdate, NoteResponse with Pydantic
routes.py - Full CRUD endpoints using the service layer
service.py - All database operations with proper logging
tests/test_notes.py - Tests for all CRUD operations

Reference backend/app/examples/complete_feature/ for the complete patterns.
```

### If pytest Fails

```
pytest failed with these errors:
[paste pytest output]

Fix all test failures and run pytest again.
```

### If Ruff Fails

```
Ruff found these issues in the notes feature:
[paste ruff output]

Fix all linting issues.
```

Or use auto-fix:

```
Run: ruff check backend/app/features/notes/ --fix

Then show me any remaining issues.
```

### If MyPy Fails

```
MyPy found these type errors:
[paste mypy output]

Fix all type annotations and errors.
```

### If Migration Fails

```
The Alembic migration failed:
[paste error]

Diagnose the issue and fix it. Common problems:
- Note model not imported in alembic env.py
- Table already exists from previous testing
- Syntax error in model
```

### If Server Won't Start

```
The server failed to start:
[paste error]

Fix the issue and start the server again.
```

### If API Returns Errors

```
The notes API is returning errors:
[paste curl output and/or logs]

Diagnose from the logs and fix the issue.
```

---

## CLEANUP PROMPT (Before Recording)

Use this to reset state before recording:

```
I need to prepare for a fresh demo of creating a notes feature.

1. Check if backend/app/features/notes/ exists - if so, delete it
2. Check if there's a notes table in the database - if so, tell me how to remove it
3. Check if notes router is registered in main.py - if so, remove it
4. Verify the example feature exists at backend/app/examples/complete_feature/

Report what you found and what needs to be cleaned up.
```

---

## VERIFICATION PROMPT (After Demo)

To verify everything worked:

```
Verify the notes feature is complete:

1. List all files in backend/app/features/notes/
2. Run pytest backend/app/features/notes/tests/ -v
3. Run ruff check backend/app/features/notes/
4. Run mypy backend/app/features/notes/
5. Make a GET request to http://localhost:8123/notes
6. Query the database for all notes

Report the status of each check.
```

---

## ALTERNATIVE FEATURES (For Student Homework)

### Comments Feature

```
Using the same pattern as notes, create a "comments" feature in backend/app/features/comments/ with:
- Comment model: id, content, author, created_at, updated_at
- Full CRUD
- Complete tests
- Proper logging

Then run all guardrails and test with curl.
```

### Tags Feature

```
Create a "tags" feature in backend/app/features/tags/ with:
- Tag model: id, name, color (hex string), created_at
- Full CRUD
- Complete tests
- Proper logging

Then run all guardrails and test with curl.
```

### Categories Feature

```
Create a "categories" feature in backend/app/features/categories/ with:
- Category model: id, name, description, parent_id (optional self-reference), created_at
- Full CRUD
- Complete tests
- Proper logging

Then run all guardrails and test with curl.
```

---

## PROMPT ENGINEERING NOTES

### Why the Prompt Works

1. **Reference first** — Points to existing code to copy patterns
2. **Specific structure** — Lists exactly what files and fields
3. **Complete workflow** — Includes validation and testing steps
4. **Show results** — Asks for output at each step (visibility)
5. **Self-correction** — Includes "if fails, fix and re-run"

### Prompt Patterns to Teach Students

**Reference pattern:**
```
Look at [existing code] as a reference.
Create [new thing] following the same patterns.
```

**Validation pattern:**
```
Then run [guardrail]. If it fails, fix and re-run.
```

**Verification pattern:**
```
Show results at each step.
Test with [command].
Query [database/api] to verify.
```

### What Makes a Good Feature Prompt

- Clear model definition (fields and types)
- Explicit file list
- Reference to existing patterns
- Validation steps included
- Verification commands included
- Permission to self-correct
