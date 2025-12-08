# Lesson 5 — Exercises

## Your First Backend Feature with Claude Code

These exercises have you practice what you saw in the lesson — prompting Claude Code to create features and verifying the results.

---

## Exercise 1: Verify the Tasks Feature

Before creating your own feature, verify the tasks feature from the lesson works in your environment.

### Steps:

1. **Check the files exist:**
   ```bash
   ls backend/app/features/tasks/
   ```
   You should see: models.py, schemas.py, routes.py, service.py, tests/

2. **Check the server is running:**
   ```bash
   curl http://localhost:8123/health
   ```
   
3. **Create a task:**
   ```bash
   curl -X POST http://localhost:8123/tasks \
     -H "Content-Type: application/json" \
     -d '{"title": "My first task", "status": "todo"}'
   ```

4. **List tasks:**
   ```bash
   curl http://localhost:8123/tasks
   ```

5. **Run tests:**
   ```bash
   cd backend
   uv run pytest app/features/tasks/tests/ -v
   ```

### Questions:
- Did all tests pass?
- What fields are in the task response?
- What happens if you try to create a task with a 2-character title?

---

## Exercise 2: Create a Notes Feature

Now create your own feature using Claude Code.

### The Prompt:

Open Claude Code and paste this:

```
Using backend/app/examples/complete_feature/ as the pattern, create a "notes" feature in backend/app/features/notes/ with:

Note model:
- id (integer, primary key)
- content (text, required, min 1 character)
- created_at, updated_at (timestamps)

Create:
- models.py
- schemas.py (NoteCreate, NoteUpdate, NoteResponse)
- routes.py (list, get, create, update, delete)
- service.py (with logging)
- tests/test_notes.py

Then:
1. Register router in main.py
2. Create and run migration
3. Start server
4. Create a note via curl: {"content": "This is my first note"}
5. List notes to verify
6. Run tests

Show results at each step.
```

### Verification Checklist:

- [ ] `backend/app/features/notes/` folder exists
- [ ] Contains models.py, schemas.py, routes.py, service.py
- [ ] Contains tests/test_notes.py
- [ ] Migration ran successfully
- [ ] Server started
- [ ] POST `/notes` returns created note with ID
- [ ] GET `/notes` returns list with the note
- [ ] All tests pass

### Questions to Answer:
1. How many files did Claude Code create?
2. How many tests were generated?
3. Did anything fail? How did you fix it?

---

## Exercise 3: Create a Tags Feature

Create another feature to practice the pattern.

### The Prompt:

```
Using backend/app/examples/complete_feature/ as the pattern, create a "tags" feature in backend/app/features/tags/ with:

Tag model:
- id (integer, primary key)
- name (string, required, unique, max 50 chars)
- color (string, optional, default "#3B82F6")
- created_at (timestamp)

Create full CRUD with tests.

Then:
1. Register router
2. Create and run migration
3. Start server
4. Create two tags: {"name": "urgent", "color": "#EF4444"} and {"name": "feature"}
5. List tags to verify both exist
6. Run tests

Show results.
```

### Verification Checklist:

- [ ] Tags feature folder exists with all files
- [ ] Migration created and applied
- [ ] Can create tag with custom color
- [ ] Can create tag with default color
- [ ] Both tags appear in list
- [ ] Tests pass

### Bonus Challenge:
What happens if you try to create a tag with the same name twice? (Hint: the name is unique)

---

## Exercise 4: Modify an Existing Feature

Practice modifying a feature after it's created.

### The Prompt:

```
Add a "priority" field to the tasks feature:

- priority: integer, 1-5, default 3

Update:
1. models.py - add the field
2. schemas.py - add to TaskCreate, TaskUpdate, and TaskResponse
3. Create a new migration for this change
4. Run the migration
5. Create a task with priority 5: {"title": "High priority task", "priority": 5}
6. Create a task without priority (should default to 3)
7. List tasks to see both priorities
8. Update any tests if needed

Show results at each step.
```

### Verification:

- [ ] New migration file exists
- [ ] Migration applied successfully
- [ ] Task with priority 5 has priority: 5
- [ ] Task without priority has priority: 3
- [ ] Tests still pass

---

## Exercise 5: Write Your Own Prompt

Now design your own feature and write the prompt from scratch.

### Choose a Feature:
Pick one:
- **Comments** (id, text, author_name, created_at)
- **Categories** (id, name, description, parent_id)
- **Bookmarks** (id, url, title, notes, created_at)
- **Or your own idea**

### Write the Prompt:

Follow this structure:
```
Using backend/app/examples/complete_feature/ as the pattern, create a "[NAME]" feature in backend/app/features/[name]/ with:

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
4. Test with curl
5. Run tests

Show results at each step.
```

### Deliverables:
1. Your prompt (saved in a markdown file)
2. Screenshot or copy of Claude Code's output
3. Verification that all tests pass

---

## Exercise 6: Handle Failures

Practice the "fix" loop when things go wrong.

### Intentionally Break Something:

1. Delete a file Claude Code created:
   ```bash
   rm backend/app/features/tasks/service.py
   ```

2. Try to start the server:
   ```bash
   cd backend
   uv run uvicorn app.main:app --port 8123
   ```
   (It should fail with an import error)

3. Ask Claude Code to fix it:
   ```
   The server won't start. I'm getting this error:
   [paste the error]
   
   Please fix it.
   ```

4. Verify the fix worked.

### Questions:
- What error did you see?
- How did Claude Code fix it?
- Did it recreate the exact same file or something different?

---

## Exercise 7: Document Your Learning

Create a markdown file documenting what you learned.

### Template:

```markdown
# Lesson 5 Learning Notes

## What I Did
- Created [list features you created]
- Total prompts sent to Claude Code: [number]
- Total features working: [number]

## What Worked Well
- [list things that went smoothly]

## What Went Wrong
- [list issues you encountered]

## How I Fixed Issues
- [describe how you resolved problems]

## Key Insights
- [what did you learn about prompting Claude Code?]

## Questions I Still Have
- [anything unclear?]
```

---

## Submission Checklist

Before moving to Lesson 6:

- [ ] Tasks feature works (from lesson)
- [ ] Notes feature works (Exercise 2)
- [ ] Tags feature works (Exercise 3)
- [ ] Successfully modified tasks feature (Exercise 4)
- [ ] Created one feature from your own prompt (Exercise 5)
- [ ] Practiced fixing a broken feature (Exercise 6)
- [ ] Completed learning notes (Exercise 7)

---

## Common Issues and Solutions

### "Claude Code doesn't create all the files"
**Solution:** Prompt it to continue:
```
You stopped early. Please also create:
- service.py
- tests/test_[feature].py
```

### "Migration fails with table already exists"
**Solution:** Drop the table and retry:
```bash
docker exec -it full-stack-foundations-db-1 psql -U postgres -d appdb -c "DROP TABLE IF EXISTS [tablename] CASCADE;"
```
Then ask Claude Code to create and run the migration again.

### "Tests fail"
**Solution:** This is normal! Ask Claude Code:
```
These tests failed:
[paste output]
Please fix the code.
```

### "Server won't start"
**Solution:** Check the error message. Usually it's:
- Import error → missing file or typo
- Router not registered → check main.py
- Port in use → stop other server first

---

## Looking Ahead

In Lesson 6, you'll learn about:
- **pytest** — Writing and running tests
- **Ruff** — Linting and formatting
- **MyPy** — Type checking

These are the guardrails that validate every PIV loop. Claude Code runs these automatically when you ask it to "run tests" — now you'll understand what's happening under the hood.
