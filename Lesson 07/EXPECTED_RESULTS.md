# Expected Results for Lesson 7

## What Success Looks Like at Each Step

Use this to verify logging is working correctly during the demo.

---

## 1. Server Started with Logging

### Command

```bash
cd backend
uv run uvicorn app.main:app --reload --port 8123
```

### Expected Output

```
INFO:     Will watch for changes in these directories: ['/path/to/backend']
INFO:     Uvicorn running on http://127.0.0.1:8123 (Press CTRL+C to quit)
INFO:     Started reloader process [12345] using WatchFiles
INFO:     Started server process [12346]
INFO:     Waiting for application startup.
{"timestamp": "2024-01-15T10:30:00.000Z", "level": "INFO", "event": "application.startup", ...}
INFO:     Application startup complete.
```

**Key indicators:**
- Server running on port 8123
- May see initial JSON log for application startup

---

## 2. Health Check Request

### Command

```bash
curl http://localhost:8123/health
```

### Expected Response

```json
{"status": "healthy", "service": "api"}
```

### Expected Log Output

```json
{"timestamp": "2024-01-15T10:30:15.123Z", "level": "INFO", "event": "http.request.started", "correlation_id": "abc123", "method": "GET", "path": "/health"}
{"timestamp": "2024-01-15T10:30:15.135Z", "level": "INFO", "event": "http.request.completed", "correlation_id": "abc123", "method": "GET", "path": "/health", "status_code": 200, "duration_ms": 12}
```

**Key indicators:**
- JSON format
- Same correlation_id in both logs
- Started and completed events
- Status code 200

---

## 3. Create Task Request

### Command

```bash
curl -X POST http://localhost:8123/tasks \
  -H "Content-Type: application/json" \
  -d '{"title": "Learn structured logging", "status": "in_progress"}'
```

### Expected Response

```json
{
  "id": 1,
  "title": "Learn structured logging",
  "status": "in_progress",
  "description": null,
  "created_at": "2024-01-15T10:30:45.123456",
  "updated_at": "2024-01-15T10:30:45.123456"
}
```

### Expected Log Output

```json
{"timestamp": "2024-01-15T10:30:45.100Z", "level": "INFO", "event": "http.request.started", "correlation_id": "def456", "method": "POST", "path": "/tasks"}
{"timestamp": "2024-01-15T10:30:45.110Z", "level": "INFO", "event": "task.create.started", "correlation_id": "def456", "title": "Learn structured logging"}
{"timestamp": "2024-01-15T10:30:45.120Z", "level": "INFO", "event": "database.query", "correlation_id": "def456", "operation": "insert", "table": "tasks"}
{"timestamp": "2024-01-15T10:30:45.130Z", "level": "INFO", "event": "task.create.completed", "correlation_id": "def456", "task_id": 1}
{"timestamp": "2024-01-15T10:30:45.135Z", "level": "INFO", "event": "http.request.completed", "correlation_id": "def456", "method": "POST", "path": "/tasks", "status_code": 201, "duration_ms": 35}
```

**Key indicators:**
- Multiple log events for single request
- Same correlation_id across all
- Started → database → completed pattern
- Task ID in completed log

---

## 4. List Tasks Request

### Command

```bash
curl http://localhost:8123/tasks
```

### Expected Response

```json
[
  {
    "id": 1,
    "title": "Learn structured logging",
    "status": "in_progress",
    ...
  }
]
```

### Expected Log Output

```json
{"timestamp": "2024-01-15T10:31:00.100Z", "level": "INFO", "event": "http.request.started", "correlation_id": "ghi789", "method": "GET", "path": "/tasks"}
{"timestamp": "2024-01-15T10:31:00.110Z", "level": "INFO", "event": "task.list.started", "correlation_id": "ghi789"}
{"timestamp": "2024-01-15T10:31:00.120Z", "level": "INFO", "event": "task.list.completed", "correlation_id": "ghi789", "count": 1}
{"timestamp": "2024-01-15T10:31:00.125Z", "level": "INFO", "event": "http.request.completed", "correlation_id": "ghi789", "method": "GET", "path": "/tasks", "status_code": 200, "duration_ms": 25}
```

**Key indicators:**
- Different correlation_id from previous request
- Count of tasks returned in completed log

---

## 5. Not Found Request (404)

### Command

```bash
curl http://localhost:8123/tasks/99999
```

### Expected Response

```json
{"detail": "Task not found"}
```

### Expected Log Output

```json
{"timestamp": "2024-01-15T10:31:30.100Z", "level": "INFO", "event": "http.request.started", "correlation_id": "jkl012", "method": "GET", "path": "/tasks/99999"}
{"timestamp": "2024-01-15T10:31:30.110Z", "level": "INFO", "event": "task.get.started", "correlation_id": "jkl012", "task_id": 99999}
{"timestamp": "2024-01-15T10:31:30.120Z", "level": "WARNING", "event": "task.get.not_found", "correlation_id": "jkl012", "task_id": 99999}
{"timestamp": "2024-01-15T10:31:30.125Z", "level": "INFO", "event": "http.request.completed", "correlation_id": "jkl012", "method": "GET", "path": "/tasks/99999", "status_code": 404, "duration_ms": 25}
```

**Key indicators:**
- WARNING level for not_found event
- Status code 404 in completed log
- Task ID included in logs

---

## 6. Error Request (Simulated)

### After Breaking Code

If you added `raise Exception("Simulated database error")` to service.py:

### Expected Response

```json
{"detail": "Internal Server Error"}
```
or
```
HTTP 500 Internal Server Error
```

### Expected Log Output

```json
{"timestamp": "2024-01-15T10:32:00.100Z", "level": "INFO", "event": "http.request.started", "correlation_id": "mno345", "method": "GET", "path": "/tasks"}
{"timestamp": "2024-01-15T10:32:00.110Z", "level": "INFO", "event": "task.list.started", "correlation_id": "mno345"}
{"timestamp": "2024-01-15T10:32:00.115Z", "level": "ERROR", "event": "task.list.failed", "correlation_id": "mno345", "error": "Simulated database error", "error_type": "Exception"}
{"timestamp": "2024-01-15T10:32:00.120Z", "level": "INFO", "event": "http.request.completed", "correlation_id": "mno345", "method": "GET", "path": "/tasks", "status_code": 500, "duration_ms": 20}
```

**Key indicators:**
- ERROR level for failed event
- Error message in log
- Status code 500

---

## 7. After Fix

### After Removing the Exception

### Expected Response

```json
[
  {"id": 1, "title": "Learn structured logging", ...}
]
```

### Expected Log Output

```json
{"timestamp": "2024-01-15T10:33:00.100Z", "level": "INFO", "event": "http.request.started", "correlation_id": "pqr678", "method": "GET", "path": "/tasks"}
{"timestamp": "2024-01-15T10:33:00.110Z", "level": "INFO", "event": "task.list.started", "correlation_id": "pqr678"}
{"timestamp": "2024-01-15T10:33:00.120Z", "level": "INFO", "event": "task.list.completed", "correlation_id": "pqr678", "count": 1}
{"timestamp": "2024-01-15T10:33:00.125Z", "level": "INFO", "event": "http.request.completed", "correlation_id": "pqr678", "method": "GET", "path": "/tasks", "status_code": 200, "duration_ms": 25}
```

**Key indicators:**
- Back to INFO level
- Completed event instead of failed
- Status code 200

---

## Log Format Variations

### Acceptable Variations

The exact log format may vary based on configuration:

**Timestamps:**
- ISO format: `"2024-01-15T10:30:45.123Z"`
- Unix timestamp: `1705319445.123`

**Correlation ID format:**
- UUID: `"abc12345-6789-..."` 
- Short ID: `"abc123"`
- Request ID: `"req_abc123"`

**Field names:**
- `correlation_id` or `request_id` or `trace_id`
- `event` or `message` or `msg`
- `level` or `severity` or `log_level`

**Additional fields:**
- May include `service`, `environment`, `host`
- May include `user_id` if authenticated
- May include `request_body` or `response_size`

---

## Verification Checklist

Use during recording:

### Basic Logging
- [ ] Server starts with visible logs
- [ ] Logs are JSON format
- [ ] Logs appear when requests are made

### Log Structure
- [ ] Timestamp is present
- [ ] Level (INFO/WARNING/ERROR) is present
- [ ] Event name is present
- [ ] Correlation ID is present

### Correlation ID
- [ ] Same ID across all logs for one request
- [ ] Different ID for different requests

### Event Patterns
- [ ] Started event visible
- [ ] Completed event visible
- [ ] Count or details in completed

### Error Handling
- [ ] 404 shows WARNING level
- [ ] 500 shows ERROR level
- [ ] Error message is in the log

### AI Debugging
- [ ] Logs copied to Claude Code
- [ ] Claude Code diagnoses correctly
- [ ] Fix applied successfully
- [ ] Clean logs after fix

---

## Common Issues

### Logs in Wrong Format

**Issue:** Logs appear as plain text, not JSON

**Example:**
```
INFO: 2024-01-15 10:30:45 - Task created
```

**Cause:** structlog not configured for JSON output

**Expected:** JSON format as shown above

---

### Missing Correlation ID

**Issue:** No `correlation_id` field in logs

**Cause:** Middleware not adding correlation ID

**Check:** Look for correlation ID middleware in app setup

---

### No Business Logic Logs

**Issue:** Only HTTP logs, no `task.create.started` etc.

**Cause:** Service layer not logging events

**Check:** Look at `service.py` for log statements

---

### Logs Not Appearing at All

**Issue:** Terminal shows nothing when requests made

**Possible causes:**
- Logging not configured
- Output going to file, not stdout
- Log level set too high (ERROR only)

**Check:** Logging configuration in `core/logging.py`
