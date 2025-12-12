# Lesson 7 — Exercises

## Structured Logging: Watch Your Code Talk

These exercises help you understand and work with structured logging.

---

## Exercise 1: Observe Basic Logging

Start the server and make requests to observe logs.

### Step 1: Start the Server

```bash
cd backend
uv run uvicorn app.main:app --reload --port 8123
```

Keep this terminal open — you'll watch logs here.

### Step 2: Make Requests (in another terminal)

```bash
# Health check
curl http://localhost:8123/health

# List tasks
curl http://localhost:8123/tasks

# Create a task
curl -X POST http://localhost:8123/tasks \
  -H "Content-Type: application/json" \
  -d '{"title": "Exercise 1 task", "status": "todo"}'

# Get the task
curl http://localhost:8123/tasks/1
```

### Step 3: Document What You See

For each request, note:
- How many log entries appeared?
- What event names did you see?
- What was the log level (INFO, WARNING, ERROR)?

### Questions:
1. Which request generated the most log entries?
2. What fields appeared in every log entry?
3. Did you see the same correlation ID across related logs?

---

## Exercise 2: Trace with Correlation IDs

Practice using correlation IDs to trace requests.

### Step 1: Make Several Requests

```bash
# Request 1
curl http://localhost:8123/health

# Request 2
curl http://localhost:8123/tasks

# Request 3
curl -X POST http://localhost:8123/tasks \
  -H "Content-Type: application/json" \
  -d '{"title": "Trace me", "status": "in_progress"}'

# Request 4
curl http://localhost:8123/tasks
```

### Step 2: Find the Correlation IDs

For each request, find the correlation ID in the logs.

| Request | Correlation ID |
|---------|----------------|
| GET /health | |
| GET /tasks (first) | |
| POST /tasks | |
| GET /tasks (second) | |

### Step 3: Answer Questions

1. Are all four correlation IDs different?
2. For the POST request, how many log entries share the same correlation ID?
3. If you had 1000 requests per second, how would correlation IDs help you debug?

---

## Exercise 3: Trigger and Observe Errors

Practice seeing different log levels.

### Step 1: Trigger a 404

```bash
curl http://localhost:8123/tasks/99999
```

**Observe the logs:**
- What event name appears?
- What log level is used (INFO, WARNING, or ERROR)?
- Is 404 considered an error or expected behavior?

### Step 2: Trigger a Validation Error

```bash
curl -X POST http://localhost:8123/tasks \
  -H "Content-Type: application/json" \
  -d '{"title": "ab"}'  # Title too short
```

**Observe the logs:**
- What status code was returned?
- What log level was used?
- Was the invalid input logged?

### Step 3: Trigger a Server Error

Ask Claude Code:

```
In backend/app/features/tasks/service.py, temporarily add this line
at the start of get_task:

    raise Exception("Intentional error for logging exercise")

Then make a request to GET /tasks/1.
```

**Observe the logs:**
- What event name appears?
- What log level is used?
- What error details are included?

### Step 4: Fix and Observe Recovery

Ask Claude Code to remove the exception, then make the same request again.

**Compare:**
- What's different between the error logs and success logs?

---

## Exercise 4: Debug with Logs

Practice the AI debugging workflow.

### Step 1: Create an Error Scenario

Ask Claude Code:

```
In the tasks service, temporarily break something that will cause 
an error when listing tasks. Don't tell me what you broke.
```

### Step 2: Make a Request and Capture Logs

```bash
curl http://localhost:8123/tasks
```

Copy the error logs that appear.

### Step 3: Ask Claude Code to Diagnose

```
Here are the error logs from my API:

[paste your logs]

Based only on these logs:
1. What operation failed?
2. What was the error?
3. What do you think caused it?
4. How would you fix it?
```

### Step 4: Compare Diagnosis to Actual Problem

Did Claude Code correctly identify the issue from logs alone?

### Step 5: Fix and Verify

Ask Claude Code to fix the issue, then verify with a successful request.

---

## Exercise 5: Explore Log Configuration

Understand how logging is configured.

### Step 1: Find the Configuration

Ask Claude Code:

```
Show me where logging is configured in this codebase.
What library is used? What format are logs output in?
```

### Step 2: Document the Configuration

Create a file called `logging-notes.md`:

```markdown
# Logging Configuration

## Library Used
[what library?]

## Log Format
[JSON? plain text?]

## Fields Included
- timestamp
- level
- event
- [what else?]

## Correlation ID
Where is it generated? [file/function]
How is it added to logs? [middleware? context?]

## Log Level
Current level: [DEBUG/INFO/WARNING/ERROR]
```

### Step 3: Experiment (Optional)

If you're comfortable, try changing the log level:

```
Change the log level to DEBUG and make a request.
What additional logs appear?
Then change it back to INFO.
```

---

## Exercise 6: Log Event Patterns

Document the event naming patterns in the codebase.

### Step 1: Collect Event Names

Make several different requests and collect all the unique event names you see:

```bash
curl http://localhost:8123/health
curl http://localhost:8123/tasks
curl -X POST http://localhost:8123/tasks -H "Content-Type: application/json" -d '{"title": "Test"}'
curl http://localhost:8123/tasks/1
curl -X PUT http://localhost:8123/tasks/1 -H "Content-Type: application/json" -d '{"status": "done"}'
curl -X DELETE http://localhost:8123/tasks/1
curl http://localhost:8123/tasks/99999  # 404
```

### Step 2: Categorize Events

| Event Name | Category | Level |
|------------|----------|-------|
| http.request.started | HTTP | INFO |
| http.request.completed | HTTP | INFO |
| task.create.started | Business | INFO |
| task.create.completed | Business | INFO |
| task.get.not_found | Business | WARNING |
| ... | ... | ... |

### Step 3: Identify Patterns

Answer these questions:
1. What's the naming pattern for events? (noun.verb? verb.noun?)
2. What suffix indicates success? (completed? success?)
3. What suffix indicates failure? (failed? error?)
4. Are there consistent prefixes for different areas? (http.*, task.*, db.*)

---

## Exercise 7: Production Logging Scenario

Think about logging in a production context.

### Scenario

You're on call. At 3 AM, you get an alert: "500 errors increasing on /tasks endpoint."

You have access to logs but NOT the source code (it's deployed on a server you can't access directly).

### Step 1: What Would You Look For?

Write down:
1. What log level would you filter for?
2. What event names would indicate the problem?
3. What fields would help you identify affected requests?
4. How would you find all logs for one failing request?

### Step 2: Simulate the Scenario

Ask Claude Code to break something, then use ONLY the logs to:
1. Identify that there's a problem
2. Determine what operation is failing
3. Find the error message
4. Propose a likely cause

### Step 3: Reflect

Was this easier or harder than reading through source code?

---

## Exercise 8: Create Your Logging Reference

Create a reference document for yourself.

### Create `my-logging-reference.md`:

```markdown
# My Logging Reference

## Key Concepts

### Structured Logging
[What is it? Why use it?]

### Correlation IDs
[What are they? How do they help?]

### Log Levels
- DEBUG: [when to use]
- INFO: [when to use]
- WARNING: [when to use]
- ERROR: [when to use]

## Event Patterns in This Codebase

### HTTP Events
- http.request.started
- http.request.completed

### Task Events
- task.create.started
- task.create.completed
- task.create.failed
- [others I discovered]

## Debugging Workflow

1. [step 1]
2. [step 2]
3. [step 3]

## Commands I Use

```bash
# Start server with logs
[command]

# Make test request
[command]

# Filter logs (if applicable)
[command]
```

## What I Learned
[personal notes]
```

---

## Submission Checklist

Before moving to Lesson 8:

- [ ] Made requests and observed logs (Exercise 1)
- [ ] Traced requests using correlation IDs (Exercise 2)
- [ ] Triggered and observed different error types (Exercise 3)
- [ ] Debugged an issue using only logs (Exercise 4)
- [ ] Explored logging configuration (Exercise 5)
- [ ] Documented event patterns (Exercise 6)
- [ ] Thought through production scenario (Exercise 7)
- [ ] Created personal logging reference (Exercise 8)

---

## Looking Ahead

In Lesson 8, you'll put all four guardrails together:
- **pytest** — Tests
- **Ruff** — Linting
- **MyPy** — Types
- **Logging** — Runtime visibility

You'll build a complete feature from scratch, using all guardrails in the PIV loop.
