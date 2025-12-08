# Expected Results for Lesson 5

## What Success Looks Like at Each Step

Use this document to verify Claude Code is working correctly. These are the expected outputs and results.

---

## 1. Files Created

### Expected Folder Structure

After Claude Code finishes creating files, you should see:

```
backend/app/features/
└── tasks/
    ├── __init__.py
    ├── models.py
    ├── schemas.py
    ├── routes.py
    ├── service.py
    └── tests/
        ├── __init__.py
        └── test_tasks.py
```

### Verification Command
```bash
ls -la backend/app/features/tasks/
```

### Expected Output
```
total 40
drwxr-xr-x  3 user user 4096 Jan 15 10:30 .
drwxr-xr-x  3 user user 4096 Jan 15 10:30 ..
-rw-r--r--  1 user user    0 Jan 15 10:30 __init__.py
-rw-r--r--  1 user user  856 Jan 15 10:30 models.py
-rw-r--r--  1 user user  724 Jan 15 10:30 routes.py
-rw-r--r--  1 user user  943 Jan 15 10:30 schemas.py
-rw-r--r--  1 user user  612 Jan 15 10:30 service.py
drwxr-xr-x  2 user user 4096 Jan 15 10:30 tests
```

---

## 2. Router Registration

### Expected Change in main.py

Claude Code should add something like:

```python
from app.features.tasks.routes import router as tasks_router

# ... in the app setup section:
app.include_router(tasks_router, prefix="/tasks", tags=["tasks"])
```

### Verification
Open `backend/app/main.py` and look for the tasks router import and registration.

---

## 3. Migration Created

### Expected Migration Output

```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.autogenerate.compare] Detected added table 'tasks'
  Generating /app/backend/alembic/versions/abc123_add_tasks_table.py ...  done
```

### Expected Migration File Content (approximate)

A new file in `backend/alembic/versions/` containing:

```python
def upgrade() -> None:
    op.create_table('tasks',
        sa.Column('id', sa.Integer(), nullable=False),
        sa.Column('title', sa.String(), nullable=False),
        sa.Column('description', sa.String(), nullable=True),
        sa.Column('status', sa.String(), nullable=True),
        sa.Column('created_at', sa.DateTime(), server_default=sa.text('now()'), nullable=True),
        sa.Column('updated_at', sa.DateTime(), server_default=sa.text('now()'), nullable=True),
        sa.PrimaryKeyConstraint('id')
    )
    op.create_index(op.f('ix_tasks_id'), 'tasks', ['id'], unique=False)
```

---

## 4. Migration Applied

### Expected Output

```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade abc122 -> abc123, add_tasks_table
```

### Verification Command
```bash
docker exec -it full-stack-foundations-db-1 psql -U postgres -d appdb -c "\dt"
```

### Expected Output (should include tasks table)
```
           List of relations
 Schema |      Name       | Type  |  Owner   
--------+-----------------+-------+----------
 public | alembic_version | table | postgres
 public | tasks           | table | postgres
 ...
```

---

## 5. Server Started

### Expected Output

```
INFO:     Will watch for changes in these directories: ['/app/backend']
INFO:     Uvicorn running on http://127.0.0.1:8123 (Press CTRL+C to quit)
INFO:     Started reloader process [12345] using WatchFiles
INFO:     Started server process [12346]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
```

### Verification
```bash
curl http://localhost:8123/health
```

### Expected Response
```json
{"status": "healthy", "service": "api"}
```

---

## 6. POST Creates Task

### Command Claude Code Should Run
```bash
curl -X POST http://localhost:8123/tasks \
  -H "Content-Type: application/json" \
  -d '{"title": "Learn Claude Code", "status": "in_progress"}'
```

### Expected Response
```json
{
  "id": 1,
  "title": "Learn Claude Code",
  "description": null,
  "status": "in_progress",
  "created_at": "2024-01-15T10:35:42.123456",
  "updated_at": "2024-01-15T10:35:42.123456"
}
```

### Key Things to Verify
- `id` is assigned (should be 1)
- `title` matches what was sent
- `status` matches what was sent
- `description` is null (not sent)
- Timestamps are present

---

## 7. GET Lists Tasks

### Command
```bash
curl http://localhost:8123/tasks
```

### Expected Response
```json
[
  {
    "id": 1,
    "title": "Learn Claude Code",
    "description": null,
    "status": "in_progress",
    "created_at": "2024-01-15T10:35:42.123456",
    "updated_at": "2024-01-15T10:35:42.123456"
  }
]
```

### Key Things to Verify
- Response is an array
- Contains the task we created
- All fields are present

---

## 8. Database Shows Data

### Command
```bash
docker exec -it full-stack-foundations-db-1 psql -U postgres -d appdb -c "SELECT * FROM tasks;"
```

### Expected Output
```
 id |      title       | description |   status    |         created_at         |         updated_at         
----+------------------+-------------+-------------+----------------------------+----------------------------
  1 | Learn Claude Code|             | in_progress | 2024-01-15 10:35:42.123456 | 2024-01-15 10:35:42.123456
(1 row)
```

### Key Things to Verify
- Row exists in database
- Data matches what was sent via API
- Timestamps are populated

---

## 9. Tests Pass

### Command
```bash
pytest backend/app/features/tasks/tests/ -v
```

### Expected Output
```
========================= test session starts ==========================
platform linux -- Python 3.12.0, pytest-7.4.0
collected 4 items

backend/app/features/tasks/tests/test_tasks.py::test_create_task PASSED  [ 25%]
backend/app/features/tasks/tests/test_tasks.py::test_get_task PASSED     [ 50%]
backend/app/features/tasks/tests/test_tasks.py::test_list_tasks PASSED   [ 75%]
backend/app/features/tasks/tests/test_tasks.py::test_update_task PASSED  [100%]

========================== 4 passed in 0.53s ===========================
```

### Acceptable Variations
- May have 3-6 tests (Claude Code might add more)
- May include `test_delete_task`
- Timing will vary

### Key Things to Verify
- All tests pass (no failures)
- Tests are actually running (not skipped)

---

## Common Variations (All Acceptable)

### Different File Names
Claude Code might create:
- `crud.py` instead of `routes.py`
- `api.py` instead of `routes.py`
- `repository.py` in addition to `service.py`

**As long as it works, it's fine.**

### Different Test Count
- Minimum: 3 tests (create, get, list)
- Expected: 4-5 tests (add update, delete)
- Maximum: 8+ tests (validation tests, edge cases)

**More tests is better.**

### Different Schema Names
- `TaskCreate` / `TaskCreateSchema` / `CreateTask`
- `TaskResponse` / `TaskRead` / `TaskOut`

**Naming conventions vary; functionality is what matters.**

---

## Quick Verification Checklist

Use this during recording:

- [ ] Folder `backend/app/features/tasks/` exists
- [ ] Contains at least 5 Python files
- [ ] Migration ran without errors
- [ ] Server started on port 8123
- [ ] POST returned JSON with `id: 1`
- [ ] GET returned array with the task
- [ ] Database query showed the row
- [ ] All pytest tests passed

If all 8 checkboxes are true, the lesson is successful.

---

## Troubleshooting Quick Reference

| Symptom | Likely Cause | Fix |
|---------|--------------|-----|
| No files created | Claude Code stopped early | Prompt to continue |
| Migration error | Table already exists | Drop table, retry |
| Import error on server start | Missing router registration | Check main.py |
| 404 on /tasks | Router not registered | Add to main.py |
| Empty response from GET | Task not created | Check POST worked |
| Tests fail | Code bug | Ask Claude Code to fix |

---

## Sample Complete Claude Code Session

Here's what a successful session might look like in the terminal:

```
$ claude

> [paste prompt]

I'll create the tasks feature following the pattern from complete_feature.

First, let me read the reference files...
[Reading backend/app/examples/complete_feature/models.py]
[Reading backend/app/examples/complete_feature/schemas.py]
...

Now I'll create the tasks feature:

[Creating backend/app/features/tasks/__init__.py]
[Creating backend/app/features/tasks/models.py]
[Creating backend/app/features/tasks/schemas.py]
[Creating backend/app/features/tasks/routes.py]
[Creating backend/app/features/tasks/service.py]
[Creating backend/app/features/tasks/tests/__init__.py]
[Creating backend/app/features/tasks/tests/test_tasks.py]

Registering router in main.py...
[Editing backend/app/main.py]

Creating migration...
$ alembic revision --autogenerate -m "add_tasks_table"
INFO  [alembic.autogenerate.compare] Detected added table 'tasks'
  Generating .../abc123_add_tasks_table.py ...  done

Running migration...
$ alembic upgrade head
INFO  [alembic.runtime.migration] Running upgrade -> abc123, add_tasks_table

Starting server...
$ uvicorn app.main:app --reload --port 8123
INFO:     Uvicorn running on http://127.0.0.1:8123

Testing API...
$ curl -X POST http://localhost:8123/tasks -H "Content-Type: application/json" -d '{"title": "Learn Claude Code", "status": "in_progress"}'
{"id":1,"title":"Learn Claude Code","description":null,"status":"in_progress","created_at":"2024-01-15T10:35:42","updated_at":"2024-01-15T10:35:42"}

$ curl http://localhost:8123/tasks
[{"id":1,"title":"Learn Claude Code","description":null,"status":"in_progress","created_at":"2024-01-15T10:35:42","updated_at":"2024-01-15T10:35:42"}]

Checking database...
$ docker exec -it ... psql -U postgres -d appdb -c "SELECT * FROM tasks;"
 id |      title       | description |   status    |     created_at      |     updated_at      
----+------------------+-------------+-------------+---------------------+---------------------
  1 | Learn Claude Code|             | in_progress | 2024-01-15 10:35:42 | 2024-01-15 10:35:42

Running tests...
$ pytest backend/app/features/tasks/tests/ -v
...
4 passed in 0.53s

✓ Tasks feature created successfully!
```

This is what success looks like.
