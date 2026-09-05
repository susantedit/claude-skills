---
name: python-fastapi-audit
description: |
  Production audit checklist for Python backends. Use this skill whenever the project has
  fastapi, flask, django, celery, sqlalchemy, alembic, httpx, aiohttp, or pydantic in
  requirements.txt or pyproject.toml. Also trigger when the user says "review my Python API",
  "check my FastAPI app", "Django performance audit", "why is my Celery task hanging",
  "SQLAlchemy session error", "async blocking issue", or "Python backend crashes".
  When activated, immediately scan using the grep patterns below — do NOT wait to be asked.
---

# Python Backend Production Audit

## Output Format
Report findings as a severity table:
| # | Severity | File | Issue | Production Impact | Fix |
|---|----------|------|-------|-------------------|-----|

After the table, list CRITICAL fixes first with exact code changes.
Ask: "Want me to apply these fixes? I'll start with CRITICAL."
After applying CRITICAL fixes, re-scan before moving to HIGH severity.

## CRITICAL Checks

### 1. Sync Blocking in Async Context
**What to grep:** `def ` (sync) functions called inside `async def` routes without `run_in_executor`
**Check:** Are CPU-heavy or I/O-blocking operations (file reads, subprocess, `requests.get`) called directly in async handlers?
**Risk:** Blocks the entire event loop. One slow sync call blocks ALL concurrent requests.
**Fix:** Use `asyncio.to_thread()` or `loop.run_in_executor()` for blocking ops. Or use `httpx` instead of `requests`.

### 2. No Exception Handler / Unhandled Exceptions
**What to grep:** `@app.exception_handler` or global error middleware
**Check:** What happens when an unhandled exception occurs? Does it return a 500 with stack trace?
**Risk:** Stack traces leaked to client (info disclosure). Unhandled errors may crash workers.
**Fix:** Add global exception handler that logs full error, returns generic 500 to client.

### 3. SQLAlchemy Session Leaks
**What to grep:** `Session()` or `sessionmaker` — check if sessions are always closed
**Check:** Is `session.close()` in a `finally` block? Or is `get_db()` dependency using `yield`?
**Risk:** Leaked sessions exhaust connection pool → DB connection timeout → all requests fail.
**Fix:** Use FastAPI `Depends(get_db)` with `yield` pattern, or context manager `with Session() as session:`.

### 4. No Request Timeout / Worker Timeout
**What to grep:** `uvicorn` or `gunicorn` config — check for `timeout` setting
**Check:** What happens if an endpoint hangs forever (waiting on external API)?
**Risk:** Worker stuck forever → eventually all workers exhausted → service unresponsive.
**Fix:** Gunicorn: `timeout = 120`. Uvicorn: use `asyncio.wait_for()` on external calls. Add `httpx` timeout.

## HIGH Checks

### 5. Missing Dependency Injection for DB
**What to grep:** Global `db = Session()` or module-level DB connections
**Check:** Is the DB session created per-request or shared globally?
**Risk:** Shared session across requests = race conditions, stale data, connection pool exhaustion.
**Fix:** Use FastAPI `Depends()` with per-request session lifecycle.

### 6. Celery Task Without Timeout/Retry Limits
**What to grep:** `@celery.task` or `@shared_task` — check for `time_limit`, `max_retries`
**Check:** Can a task run forever? Can it retry infinitely?
**Risk:** Hung task blocks worker slot. Infinite retries on permanent failure = queue flooding.
**Fix:** Add `time_limit=300`, `soft_time_limit=240`, `max_retries=3`, `default_retry_delay=60`.

### 7. N+1 Queries
**What to grep:** Loops containing `session.query` or `await db.execute` inside `for` loops
**Check:** Are related objects loaded eagerly or lazily?
**Risk:** 100 items × 1 query each = 100 DB round trips instead of 1 JOIN.
**Fix:** Use `joinedload()`, `selectinload()`, or batch queries with `IN` clause.

### 8. No Rate Limiting
**What to grep:** `slowapi` or `flask-limiter` or custom rate limit middleware
**Check:** Are expensive endpoints (AI calls, file uploads) rate-limited?
**Risk:** Single user can exhaust API quotas, overload workers, run up costs.
**Fix:** Add `slowapi` (FastAPI) or `flask-limiter` (Flask) with per-user limits.

### 9. Large File Upload in Memory
**What to grep:** `await file.read()` or `request.body()` for file uploads
**Check:** Is the entire file loaded into memory before processing?
**Risk:** Large files (50MB+) × concurrent uploads = OOM.
**Fix:** Use `SpooledTemporaryFile` or stream to disk with `shutil.copyfileobj()`.

### 10. Missing CORS Configuration
**What to grep:** `CORSMiddleware` or `flask-cors` — check `allow_origins`
**Check:** Is it `allow_origins=["*"]` in production?
**Risk:** Any website can make authenticated requests to your API.
**Fix:** Whitelist specific frontend origins. Never use `*` with `allow_credentials=True`.

## MEDIUM Checks

### 11. Pydantic Validation Gaps
**What to grep:** Route handlers accepting `dict` or `Any` instead of Pydantic models
**Check:** Are request bodies validated with Pydantic models or raw dicts?
**Risk:** Type confusion, missing fields, oversized payloads.

### 12. Missing Alembic Migrations
**What to grep:** `alembic/versions/` — are there migrations? Or is `create_all()` used?
**Check:** Is schema managed by migrations or auto-created?
**Risk:** `create_all()` doesn't handle schema changes. Production DB diverges from code.

### 13. Secrets in Code
**What to grep:** `SECRET_KEY`, `API_KEY`, `PASSWORD` hardcoded (not from env)
**Check:** Are secrets loaded from environment variables or hardcoded?

### 14. No Health Check Endpoint
**What to grep:** `/health` or `/healthz` route
**Check:** Does it verify DB and Redis connectivity, or just return 200?
**Risk:** Load balancer routes traffic to unhealthy instance.

### 15. Async Context Variable Leaks
**What to grep:** `contextvars.ContextVar` or thread-local storage in async code
**Check:** Are context variables properly scoped per-request?
**Risk:** Request A's data leaks into Request B in async context.
