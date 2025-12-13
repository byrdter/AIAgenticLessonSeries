# Expected Results for Lesson 8

## What Success Looks Like at Each Step

Use this to verify the complete feature build is working correctly.

---

## 1. Files Created

### Expected Directory Structure

```
backend/app/features/notes/
├── __init__.py
├── models.py
├── schemas.py
├── routes.py
├── service.py
└── tests/
    ├── __init__.py
    └── test_notes.py
```

### Verify Command

```bash
ls -la backend/app/features/notes/
ls -la backend/app/features/notes/tests/
```

### Key Files Content Check

**models.py** should contain:
- SQLAlchemy model class `Note`
- Fields: id, title, content, created_at, updated_at
- Table name defined

**schemas.py** should contain:
- `NoteCreate` — for POST requests
- `NoteUpdate` — for PATCH/PUT requests
- `NoteResponse` — for API responses
- Pydantic models with validation

**routes.py** should contain:
- Router definition
- POST /notes endpoint
- GET /notes endpoint (list)
- GET /notes/{id} endpoint (single)
- PUT or PATCH /notes/{id} endpoint
- DELETE /notes/{id} endpoint

**service.py** should contain:
- `create_note()` function
- `get_note()` function
- `get_notes()` or `list_notes()` function
- `update_note()` function
- `delete_note()` function
- Logging for each operation

**tests/test_notes.py** should contain:
- Test for create
- Test for read one
- Test for read all
- Test for update
- Test for delete
- Test for not found

---

## 2. Router Registered

### Expected in main.py

Look for something like:

```python
from app.features.notes.routes import router as notes_router

app.include_router(notes_router, prefix="/notes", tags=["notes"])
```

### Verify

When server starts, the /notes endpoints should be available.

---

## 3. Guardrails Pass

### pytest Output

```bash
pytest backend/app/features/notes/tests/ -v
```

**Expected output:**

```
========================= test session starts =========================
collected 6 items

backend/app/features/notes/tests/test_notes.py::test_create_note PASSED
backend/app/features/notes/tests/test_notes.py::test_get_note PASSED
backend/app/features/notes/tests/test_notes.py::test_list_notes PASSED
backend/app/features/notes/tests/test_notes.py::test_update_note PASSED
backend/app/features/notes/tests/test_notes.py::test_delete_note PASSED
backend/app/features/notes/tests/test_notes.py::test_get_note_not_found PASSED

========================= 6 passed in 0.45s =========================
```

**Key indicators:**
- All tests show PASSED
- No failures or errors
- Exit code 0

---

### Ruff Output

```bash
ruff check backend/app/features/notes/
```

**Expected output:**

```
All checks passed!
```

Or simply no output (which means no issues).

**Key indicators:**
- No error lines
- No warnings about security issues
- Exit code 0

---

### MyPy Output

```bash
mypy backend/app/features/notes/
```

**Expected output:**

```
Success: no issues found in 6 source files
```

**Key indicators:**
- "Success" message
- No type errors
- No missing annotations warnings

---

## 4. Migration Created and Run

### Create Migration

```bash
cd backend
alembic revision --autogenerate -m "Add notes table"
```

**Expected output:**

```
INFO  [alembic.autogenerate.compare] Detected added table 'notes'
  Generating /path/to/backend/alembic/versions/abc123_add_notes_table.py ...  done
```

**Key indicators:**
- "Detected added table 'notes'"
- New file created in alembic/versions/

---

### Run Migration

```bash
alembic upgrade head
```

**Expected output:**

```
INFO  [alembic.runtime.migration] Context impl SQLiteImpl.
INFO  [alembic.runtime.migration] Will assume non-transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade abc123 -> def456, Add notes table
```

**Key indicators:**
- "Running upgrade" message
- No errors

---

## 5. Server Starts

### Command

```bash
cd backend
uv run uvicorn app.main:app --reload --port 8123
```

**Expected output:**

```
INFO:     Will watch for changes in these directories: ['/path/to/backend']
INFO:     Uvicorn running on http://127.0.0.1:8123 (Press CTRL+C to quit)
INFO:     Started reloader process [12345] using WatchFiles
INFO:     Started server process [12346]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
```

**Key indicators:**
- "Uvicorn running on http://127.0.0.1:8123"
- "Application startup complete"
- No import errors
- No syntax errors

---

## 6. API Requests Work

### Create a Note (POST)

```bash
curl -X POST http://localhost:8123/notes \
  -H "Content-Type: application/json" \
  -d '{"title": "First Note", "content": "This is my first note built with all guardrails."}'
```

**Expected response:**

```json
{
  "id": 1,
  "title": "First Note",
  "content": "This is my first note built with all guardrails.",
  "created_at": "2024-01-15T10:30:45.123456",
  "updated_at": "2024-01-15T10:30:45.123456"
}
```

**Key indicators:**
- HTTP 201 Created (or 200 OK)
- JSON response with id
- Timestamps present

---

### List Notes (GET)

```bash
curl http://localhost:8123/notes
```

**Expected response:**

```json
[
  {
    "id": 1,
    "title": "First Note",
    "content": "This is my first note built with all guardrails.",
    "created_at": "2024-01-15T10:30:45.123456",
    "updated_at": "2024-01-15T10:30:45.123456"
  }
]
```

**Key indicators:**
- HTTP 200 OK
- JSON array
- Contains the note we created

---

### Get Single Note (GET)

```bash
curl http://localhost:8123/notes/1
```

**Expected response:**

```json
{
  "id": 1,
  "title": "First Note",
  "content": "This is my first note built with all guardrails.",
  "created_at": "2024-01-15T10:30:45.123456",
  "updated_at": "2024-01-15T10:30:45.123456"
}
```

---

### Update Note (PUT or PATCH)

```bash
curl -X PUT http://localhost:8123/notes/1 \
  -H "Content-Type: application/json" \
  -d '{"title": "Updated Note", "content": "Content has been updated."}'
```

**Expected response:**

```json
{
  "id": 1,
  "title": "Updated Note",
  "content": "Content has been updated.",
  "created_at": "2024-01-15T10:30:45.123456",
  "updated_at": "2024-01-15T10:35:00.654321"
}
```

**Key indicators:**
- Title and content changed
- updated_at timestamp is newer

---

### Delete Note (DELETE)

```bash
curl -X DELETE http://localhost:8123/notes/1
```

**Expected response:**

```json
{"message": "Note deleted"}
```

Or HTTP 204 No Content (empty response).

---

## 7. Logs Streaming

### Expected Log Output (in server terminal)

After POST /notes:

```json
{"timestamp": "2024-01-15T10:30:45.100Z", "level": "INFO", "event": "http.request.started", "correlation_id": "abc123", "method": "POST", "path": "/notes"}
{"timestamp": "2024-01-15T10:30:45.110Z", "level": "INFO", "event": "note.create.started", "correlation_id": "abc123", "title": "First Note"}
{"timestamp": "2024-01-15T10:30:45.120Z", "level": "INFO", "event": "note.create.completed", "correlation_id": "abc123", "note_id": 1}
{"timestamp": "2024-01-15T10:30:45.125Z", "level": "INFO", "event": "http.request.completed", "correlation_id": "abc123", "method": "POST", "path": "/notes", "status_code": 201}
```

**Key indicators:**
- JSON format
- Correlation ID consistent
- Event names follow pattern (started, completed)
- Note ID in completed log

---

After GET /notes:

```json
{"timestamp": "2024-01-15T10:31:00.100Z", "level": "INFO", "event": "http.request.started", "correlation_id": "def456", "method": "GET", "path": "/notes"}
{"timestamp": "2024-01-15T10:31:00.110Z", "level": "INFO", "event": "note.list.started", "correlation_id": "def456"}
{"timestamp": "2024-01-15T10:31:00.120Z", "level": "INFO", "event": "note.list.completed", "correlation_id": "def456", "count": 1}
{"timestamp": "2024-01-15T10:31:00.125Z", "level": "INFO", "event": "http.request.completed", "correlation_id": "def456", "method": "GET", "path": "/notes", "status_code": 200}
```

**Key indicators:**
- Different correlation ID from POST
- Count in list completed

---

## 8. Database Verification

### SQLite Query

```bash
sqlite3 backend/app.db "SELECT * FROM notes;"
```

**Expected output:**

```
1|First Note|This is my first note built with all guardrails.|2024-01-15 10:30:45|2024-01-15 10:30:45
```

### PostgreSQL Query

```bash
psql -d yourdb -c "SELECT * FROM notes;"
```

**Expected output:**

```
 id |    title    |                       content                        |         created_at         |         updated_at         
----+-------------+------------------------------------------------------+----------------------------+----------------------------
  1 | First Note  | This is my first note built with all guardrails.    | 2024-01-15 10:30:45.123456 | 2024-01-15 10:30:45.123456
(1 row)
```

**Key indicators:**
- Row exists
- Data matches what we POSTed
- Timestamps present

---

## Complete Verification Checklist

### Before Wrap-up

- [ ] Files created in correct structure
- [ ] Router registered in main.py
- [ ] pytest passes (all green)
- [ ] Ruff passes (no issues)
- [ ] MyPy passes (no errors)
- [ ] Migration created successfully
- [ ] Migration ran successfully
- [ ] Server started without errors
- [ ] POST /notes returns created note
- [ ] GET /notes returns list with note
- [ ] Logs show operations with correlation IDs
- [ ] Database contains the note row

### Success State

When all checkboxes are complete, the feature is fully built and validated. This is the PIV loop in action.

---

## Troubleshooting Quick Reference

| Problem | Likely Cause | Solution |
|---------|-------------|----------|
| Files not created | Prompt unclear | Use step-by-step prompts |
| pytest fails | Logic error | Let Claude Code fix from error output |
| Ruff fails | Style issues | Run with --fix or let Claude Code fix |
| MyPy fails | Type annotations | Let Claude Code fix type errors |
| Migration fails | Model issue | Check Note model is correct |
| Server won't start | Import error | Check for syntax/import issues |
| API returns 500 | Runtime error | Check logs for error details |
| No logs appearing | Logging not configured | Check service.py has logging |
| Database empty | Migration not run | Run alembic upgrade head |
