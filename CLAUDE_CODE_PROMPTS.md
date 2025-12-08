# Claude Code Prompts for Lesson 5

## The Prompts You'll Use in This Lesson

This document contains the exact prompts for the lesson demo, plus troubleshooting prompts if things go wrong.

---

## Main Prompt: Create the Tasks Feature

Copy this exactly into Claude Code:

```
Look at backend/app/examples/complete_feature/ as a reference pattern.

Create a new feature called "tasks" in backend/app/features/tasks/ following the exact same structure:

Task model should have:
- id (integer, primary key)
- title (string, required, min 3 chars)
- description (string, optional)
- status (string: "todo", "in_progress", "done", default "todo")
- created_at, updated_at (timestamps)

Create these files:
- models.py (SQLAlchemy model)
- schemas.py (Pydantic schemas: TaskCreate, TaskUpdate, TaskResponse)
- routes.py (CRUD endpoints: list, get, create, update, delete)
- service.py (business logic with logging)
- tests/test_tasks.py (basic tests)

Then:
1. Register the router in backend/app/main.py
2. Create an Alembic migration
3. Run the migration
4. Start the server on port 8123
5. Create a sample task via curl: {"title": "Learn Claude Code", "status": "in_progress"}
6. List all tasks to verify
7. Show the task in the database using psql
8. Run pytest on the new tests

Show me results at each step.
```

---

## Troubleshooting Prompts

### If Claude Code Stops Early

```
Continue. You still need to:
- Start the server
- Test with curl
- Check the database
- Run pytest
```

### If Migration Fails

```
The migration failed with this error:
[paste the error message]

Please fix it and run the migration again.
```

### If Server Won't Start

```
The server failed to start with this error:
[paste the error message]

What's wrong and how do we fix it?
```

### If Tests Fail

```
These tests failed:
[paste pytest output]

Please fix the code to make all tests pass, then run pytest again.
```

### If Database Query Fails

```
The database query failed:
[paste error]

Please check if the tasks table exists and show me its contents.
```

---

## Alternative Prompt: Shorter Version

If time is limited, use this condensed prompt:

```
Using backend/app/examples/complete_feature/ as the pattern, create a "tasks" feature in backend/app/features/tasks/ with:

- Task model: id, title (required), description (optional), status (todo/in_progress/done), timestamps
- Full CRUD endpoints
- Tests

After creating files:
1. Register router in main.py
2. Create and run migration
3. Start server and test with curl
4. Run pytest

Show results at each step.
```

---

## Alternative Prompt: More Detailed Version

If you want Claude Code to be very explicit:

```
I need you to create a complete backend feature following our established patterns.

FIRST, read and understand these files:
- backend/app/examples/complete_feature/models.py
- backend/app/examples/complete_feature/schemas.py
- backend/app/examples/complete_feature/routes.py
- backend/app/examples/complete_feature/service.py
- backend/app/examples/complete_feature/tests/test_complete_feature.py

NOW, create a "tasks" feature at backend/app/features/tasks/ with identical structure.

TASK MODEL SPECIFICATION:
- id: Integer, primary key, auto-increment
- title: String, required, minimum 3 characters
- description: String, optional, nullable
- status: String, one of ["todo", "in_progress", "done"], default "todo"
- created_at: DateTime, set on creation
- updated_at: DateTime, updated on modification

FILES TO CREATE:
1. backend/app/features/tasks/__init__.py
2. backend/app/features/tasks/models.py
3. backend/app/features/tasks/schemas.py (TaskCreate, TaskUpdate, TaskResponse)
4. backend/app/features/tasks/routes.py (GET /, GET /{id}, POST /, PUT /{id}, DELETE /{id})
5. backend/app/features/tasks/service.py (with structured logging)
6. backend/app/features/tasks/tests/__init__.py
7. backend/app/features/tasks/tests/test_tasks.py

AFTER CREATING FILES:
1. Add import and router registration to backend/app/main.py
2. Generate migration: alembic revision --autogenerate -m "add_tasks_table"
3. Apply migration: alembic upgrade head
4. Start server: uvicorn app.main:app --reload --port 8123
5. Test POST: curl -X POST http://localhost:8123/tasks -H "Content-Type: application/json" -d '{"title": "Learn Claude Code", "status": "in_progress"}'
6. Test GET: curl http://localhost:8123/tasks
7. Query database: docker exec -it [container] psql -U postgres -d appdb -c "SELECT * FROM tasks;"
8. Run tests: pytest backend/app/features/tasks/tests/ -v

SHOW OUTPUT at each step.
```

---

## Prompt for Student Homework: Notes Feature

Students can use this for their homework:

```
Using backend/app/examples/complete_feature/ as the pattern, create a "notes" feature in backend/app/features/notes/ with:

Note model:
- id (integer, primary key)
- content (text, required)
- created_at, updated_at (timestamps)

Create:
- models.py, schemas.py, routes.py, service.py, tests/

Then:
1. Register router
2. Create and run migration
3. Start server
4. Create a note via curl
5. List notes to verify
6. Run tests

Show results at each step.
```

---

## Prompt for Student Homework: Tags Feature

Alternative homework option:

```
Using backend/app/examples/complete_feature/ as the pattern, create a "tags" feature in backend/app/features/tags/ with:

Tag model:
- id (integer, primary key)
- name (string, required, unique)
- color (string, optional, default "#000000")
- created_at (timestamp)

Create:
- models.py, schemas.py, routes.py, service.py, tests/

Then:
1. Register router
2. Create and run migration
3. Start server
4. Create two tags via curl
5. List tags to verify
6. Run tests

Show results at each step.
```

---

## Prompt for Adding to Existing Feature

After the main lesson, you might show this:

```
Add a "priority" field to the tasks feature:

- priority: integer, 1-5, default 3

Update:
1. models.py - add the field
2. schemas.py - add to Create, Update, and Response
3. Create a new migration for the change
4. Run the migration
5. Update an existing task to set priority
6. Verify the change

Show results at each step.
```

---

## Tips for Effective Prompts

### DO:
- Reference existing patterns ("look at complete_feature")
- Be explicit about file locations
- Specify field types and constraints
- Include verification steps
- Ask for output at each step

### DON'T:
- Be vague ("create a feature")
- Assume Claude Code knows your conventions
- Skip the verification steps
- Forget to mention the router registration

### Structure:
1. **Context** — What to reference
2. **What to create** — Explicit structure
3. **What to do after** — Verification steps
4. **Show results** — Always end with this

---

## Adapting Prompts for Different Features

The pattern is:

```
Using [reference pattern] as the template, create a "[feature name]" feature in [location] with:

[Model name] model:
- [field]: [type], [constraints]
- [field]: [type], [constraints]
- ...

Create:
- [file list]

Then:
1. Register router
2. Create and run migration
3. Start server
4. Test with curl (create, list)
5. Verify in database
6. Run tests

Show results at each step.
```

This structure works for any CRUD feature. Students should internalize this pattern.
