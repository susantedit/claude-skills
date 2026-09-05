---
name: node-express-audit
description: |
  Production audit checklist for Node.js/Express backends. Use this skill whenever the project
  has express, fastify, koa, hapi, bull, bullmq, ioredis, prisma, sequelize, mongoose, multer,
  or socket.io in package.json. Also trigger when the user says things like "review my Node
  backend", "check my Express API", "audit my queue worker", "is my Bull job setup correct",
  or "why does my server crash". When activated, immediately scan the codebase using the
  grep patterns below — do NOT wait to be asked.
---

# Node.js / Express Production Audit

## Output Format
Report findings as a severity table:
| # | Severity | File | Issue | Production Impact | Fix |
|---|----------|------|-------|-------------------|-----|

After the table, list CRITICAL fixes first with exact code changes.
Ask: "Want me to apply these fixes? I'll start with CRITICAL."
After applying CRITICAL fixes, re-scan before moving to HIGH severity.

## CRITICAL Checks

### 1. Unhandled Async Errors (Process Crash)
**What to grep:** `router.get|router.post|app.get|app.post` with `async` but no `try/catch`
**Check:** Does the project use `express-async-errors` or wrap every async handler?
**Without it:** Any rejected promise in a route handler crashes the process in Node 15+.
**Fix:** `require('express-async-errors')` at top of server entry point.

### 2. Memory Storage for File Uploads (OOM)
**What to grep:** `multer.memoryStorage()` or `multer({ storage: multer.memoryStorage`
**Check:** Are uploaded files held in RAM? What's the file size limit × concurrent uploads?
**Risk:** 50 files × 50MB = 2.5GB RAM → OOM crash with PM2 memory limits.
**Fix:** Switch to `multer.diskStorage()`, stream files to temp dir, read lazily.

### 3. No Graceful Shutdown
**What to grep:** `SIGTERM|SIGINT|graceful` in server entry file
**Check:** Does the server handle SIGTERM? Does it drain connections before exit?
**Without it:** Every deploy kills in-flight requests, corrupts partial writes, leaves Bull jobs stalled.
**Fix:** Add SIGTERM/SIGINT handler: `server.close()` → `io.close()` → `process.exit(0)` with timeout.

### 4. Stream/FD Leaks
**What to grep:** `fs.createReadStream` without corresponding `.destroy()` in finally/catch
**Check:** If axios/fetch throws mid-transfer, is the read stream destroyed?
**Risk:** File descriptor leak → EMFILE after ~1000 leaked streams → server can't open files.
**Fix:** Assign stream to variable, wrap in try/finally, call `stream.destroy()` in finally.

## HIGH Checks

### 5. Rate Limiter Ordering (Bypass)
**What to grep:** Rate limit middleware applied BEFORE auth middleware in route mounting
**Check:** Does the rate limiter key use `req.user?.id` or `req.ip`? If `req.ip` — is auth already parsed?
**Risk:** If rate limiter runs before auth, key falls back to IP. Behind NAT = shared limit. Rotating IPs = bypass.
**Fix:** Apply rate limiter AFTER auth middleware, or inside route file after auth runs.

### 6. Missing Job Timeouts (Bull/BullMQ)
**What to grep:** `new Bull(` or `new Queue(` — check `defaultJobOptions` for `timeout`
**Check:** What happens if the external API (OCR, LLM, etc.) hangs forever?
**Risk:** Job occupies concurrency slot indefinitely. After N hung jobs, queue is fully blocked.
**Fix:** Add `timeout` to each queue's `defaultJobOptions` (e.g., 5-6 min for API calls).

### 7. Missing Pagination on List Endpoints
**What to grep:** `findMany|find(` without `take|limit|skip|offset`
**Check:** Can a teacher/user with 500+ records get them all in one response?
**Risk:** 500 records × 50KB each = 25MB response. Kills bandwidth, client memory, DB.
**Fix:** Add `page`/`limit` query params, cap at 100, return `{ data, pagination: { page, limit, total, pages } }`.

### 8. Large Payloads in List Responses
**What to grep:** `include:` or `select:` in Prisma queries — look for heavy fields like `raw_response`, `extracted_text`
**Check:** Are large text blobs included in list endpoints?
**Fix:** Remove heavy fields from list selects. Only include them in detail endpoints.

### 9. Redis Connection Proliferation
**What to grep:** `new Redis(` or `new IORedis(` — count instances across all files
**Check:** How many Redis connections per process? In cluster mode (4 processes) = 4× connections.
**Risk:** Free-tier Redis (Upstash, Redis Cloud) has connection limits (30-100). 7 connections × 4 processes = 28.
**Fix:** Create shared `redisClient.js` exporting 2-3 connections (commands, pub, sub). All modules import from there.

### 10. No Backpressure on Queue
**What to grep:** `queue.add(` — is there any check on queue size before adding?
**Check:** Can a burst of requests add 500 jobs instantly? What's the Redis memory impact?
**Fix:** Add `limiter: { max: N, duration: ms }` to queue config, or check queue size before enqueuing.

## MEDIUM Checks

### 11. Cache Stampede
**What to grep:** `cache.get` → miss → DB query → `cache.set` pattern without locking
**Risk:** When cache expires, N concurrent requests all miss and hit DB simultaneously.
**Fix:** Implement lock-based cache-aside (single-flight pattern) or stale-while-revalidate.

### 12. Socket.io Listener Accumulation
**What to grep:** `socket.on(` inside `io.on('connection')` — are listeners added to global objects?
**Check:** Are event listeners added to `io` or shared objects inside the connection handler?
**Risk:** Each connection adds listeners → memory leak → MaxListenersExceededWarning.

### 13. Missing Input Validation
**What to grep:** `req.body.` or `req.query.` used directly without validation
**Check:** Are types, formats, and lengths validated? Is `express-validator` or `zod` used?
**Risk:** Type confusion, oversized payloads, injection vectors.

### 14. Error Messages Leaked to Client
**What to grep:** `err.message` or `error.message` in `res.json` or `res.status`
**Check:** Are internal error details (DB errors, stack traces) sent to the client?
**Fix:** Log full error server-side, return generic message to client.

### 15. Prisma/DB Calls Without Timeout
**What to grep:** `prisma.` calls without `$transaction` timeout or connection pool config
**Risk:** If DB is slow/unreachable, requests hang until Express timeout (default: none).
**Fix:** Set `connect_timeout` in DATABASE_URL, or use `prisma.$transaction` with timeout option.
