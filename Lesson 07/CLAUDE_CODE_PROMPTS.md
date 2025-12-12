# Claude Code Prompts for Lesson 7

## The Prompts You'll Use in This Lesson

These prompts help demonstrate logging, trigger errors, and show AI debugging from logs.

---

## Prompt 1: Start Server and Explain Logging

```
Start the backend server and explain where logs are configured in this codebase.

Show me:
1. Where logging is set up (which files)
2. What format logs are in (JSON?)
3. How correlation IDs are generated

Start the server on port 8123.
```

**Expected:** Claude Code explains logging config and starts server.

---

## Prompt 2: Make Request and Show Logs

If you want Claude Code to make requests and show logs:

```
Make a GET request to http://localhost:8123/health 
and show me the logs that were generated.

Point out the key fields in the log output.
```

**Expected:** Claude Code makes request and explains log structure.

---

## Prompt 3: Create Task with Logging

```
Make a POST request to create a task:
{
  "title": "Learn structured logging",
  "status": "in_progress"
}

Show me all the log events that were generated for this single request.
Point out how the correlation ID is the same across all of them.
```

**Expected:** Multiple log events with same correlation ID.

---

## Prompt 4: Trigger an Error (404)

```
Make a GET request to /tasks/99999 (a task that doesn't exist).

Show me the logs and explain what level and event appear for a not-found error.
```

**Expected:** WARNING level log with not_found event.

---

## Prompt 5: Trigger a Real Error

```
In backend/app/features/tasks/service.py, temporarily add this line 
at the start of the list_tasks function:

    raise Exception("Simulated database connection error")

Then make a GET request to /tasks and show me the error logs.

Don't fix it yet — I want to see the error logs first.
```

**Expected:** ERROR level log with stack trace or error details.

---

## Prompt 6: Debug from Logs

After the error is triggered, give Claude Code the logs:

```
Here are the error logs from my API:

[paste the error logs]

Based only on these logs:
1. What operation failed?
2. What was the error?
3. Where in the code is the problem?
4. How do I fix it?
```

**Expected:** Claude Code diagnoses the issue from logs.

---

## Prompt 7: Fix and Verify

```
Fix the error you diagnosed and make the same request again.
Show me the logs to confirm it's working.
```

**Expected:** Clean INFO level logs, no errors.

---

## Prompt 8: Explain Correlation ID Tracing

```
Make 3 different API requests in sequence:
1. GET /health
2. POST /tasks with {"title": "First task"}
3. GET /tasks

For each request, show me the correlation ID.
Explain how I would use these IDs to trace requests in a production system.
```

**Expected:** Three different correlation IDs, explanation of tracing.

---

## Troubleshooting Prompts

### If Logs Aren't Appearing

```
I'm not seeing any logs in the terminal when I make requests.

Check:
1. Is logging configured in backend/app/core/logging.py?
2. Is the log level set correctly?
3. Is structlog installed?

Fix any issues and show me working logs.
```

### If Logs Aren't JSON

```
My logs are appearing as plain text, not JSON.

Check the logging configuration and update it to output structured JSON logs.
Show me the before and after.
```

### If No Correlation ID

```
The logs don't have a correlation_id field.

Check if there's middleware that adds correlation IDs to requests.
If not, add it. Show me logs with correlation IDs.
```

---

## Alternative Demo Flow (Shorter)

If you need a quicker version:

```
Let's demonstrate structured logging in 5 minutes:

1. Start the server on port 8123
2. Make a GET request to /health — show me the logs
3. Make a POST to /tasks with {"title": "Quick demo"} — show the correlation ID
4. Make a request to /tasks/99999 — show the error/warning log
5. Explain how I could use these logs to debug in production

Walk through each step.
```

---

## Advanced: Security Logging

For discussing what NOT to log:

```
Review the logging in the tasks feature.

Check if any of these sensitive items could be logged:
- Passwords
- API keys
- Personal information (PII)
- Authentication tokens

Are there any security concerns with the current logging?
If so, how should they be fixed?
```

---

## Student Homework Prompt

Students can use this:

```
I want to understand the logging in this codebase.

1. Make 5 different API requests (mix of GET, POST, etc.)
2. For each request, capture the logs
3. Find the correlation ID for each
4. Trigger one error and capture the error log
5. Explain the started/completed pattern you see

Document your findings.
```

---

## Prompt for Checking Log Configuration

```
Show me how logging is configured in this codebase:

1. What library is used (structlog? logging? other?)
2. What format are logs output in?
3. What fields are included in every log?
4. How are correlation IDs generated and added?
5. What log level is set for production vs development?

Point me to the relevant files.
```

---

## Prompt for Adding Logging to a New Feature

For future lessons:

```
I'm adding a new feature and want proper logging.

Show me the logging pattern used in backend/app/features/tasks/service.py.

Then add similar logging to [new feature]:
- Log when operations start
- Log when they complete (with relevant data)
- Log when they fail (with error details)
- Include appropriate context in each log

Make sure the correlation ID is included automatically.
```

---

## Tips for Log Demo

1. **Use curl for requests** — Easier to show than a GUI tool

2. **Keep logs visible** — The terminal with streaming logs is the star

3. **Highlight correlation IDs** — This concept is new to many developers

4. **Show the AI debugging workflow** — This is the "aha" moment

5. **Real errors > staged errors** — If Claude Code's simulated error doesn't work perfectly, that's still a teaching moment
