---
name: infra-devops-audit
description: |
  Production audit checklist for infrastructure and deployment. Use this skill whenever the
  project has ecosystem.config.js, Dockerfile, docker-compose.yml, .github/workflows/, Procfile,
  nginx.conf, or terraform/pulumi configs. Also trigger when the user says "review my deployment",
  "check my Docker setup", "PM2 config audit", "why does my server restart", "health check not
  working", "server runs out of memory", or "CI/CD pipeline review". When activated, immediately
  scan using the grep patterns below — do NOT wait to be asked.
---

# Infrastructure & DevOps Production Audit

## Output Format
Report findings as a severity table:
| # | Severity | File | Issue | Production Impact | Fix |
|---|----------|------|-------|-------------------|-----|

After the table, list CRITICAL fixes first with exact code changes.
Ask: "Want me to apply these fixes? I'll start with CRITICAL."
After applying CRITICAL fixes, re-scan before moving to HIGH severity.

## CRITICAL Checks

### 1. No Graceful Shutdown (Repeated — Cross-Stack)
**What to grep:** `SIGTERM|SIGINT|beforeExit|shutdown` in main entry file
**Check:** When the process manager restarts the app, are in-flight requests completed?
**Risk:** Data corruption, partial writes, stalled queue jobs, broken WebSocket sessions.
**Fix:** Handle SIGTERM: stop accepting new connections, drain existing, close DB/Redis, exit.

### 2. Memory Limit Too Low for Workload
**What to grep:** `max_memory_restart` in ecosystem.config.js, or `--max-old-space-size` in node args
**Check:** What's the memory limit vs actual peak usage? Does the workload have memory spikes?
**Risk:** PM2 kills process mid-request during memory spike. Repeated OOM restarts = downtime.
**Fix:** Set limit to 2x average usage. Monitor actual peak. For file processing: 1GB+.

### 3. Single Instance (No Redundancy)
**What to grep:** `instances: 1` or no cluster mode in PM2/Docker config
**Check:** If the single instance crashes, is there zero-downtime recovery?
**Risk:** Single point of failure. Crash = downtime until restart (2-10s).
**Fix:** `instances: 'max'` or at least 2 in PM2 cluster mode. Or Docker with replicas.

### 4. No Health Check (or Shallow Health Check)
**What to grep:** `/health|/healthz|/readyz` endpoint
**Check:** Does it verify DB + Redis connectivity, or just return 200?
**Risk:** Load balancer routes traffic to instance with dead DB connection → all requests fail.
**Fix:** Health check should: `SELECT 1` on DB, `PING` Redis, return 503 if either fails.

## HIGH Checks

### 5. Secrets in Code / Config Files
**What to grep:** `API_KEY|SECRET|PASSWORD|TOKEN` with actual values (not `process.env`)
**Check:** Are `.env` files committed? Are secrets in ecosystem.config.js?
**Risk:** Secrets in git history = compromised forever even after removal.
**Fix:** Use environment variables. Add `.env` to `.gitignore`. Use secrets manager for production.

### 6. No Log Rotation / Structured Logging
**What to grep:** `console.log|console.error` — is there a logging library?
**Check:** Where do logs go? Is there rotation? Can you search/filter logs?
**Risk:** Logs fill disk → server crashes. Unstructured logs = impossible to debug in production.
**Fix:** Use `pino` or `winston` with JSON output. PM2 log rotation or ship to CloudWatch/Datadog.

### 7. No Deployment Rollback Strategy
**What to grep:** Deployment scripts, CI/CD config — is there a rollback step?
**Check:** If a deploy breaks production, how fast can you rollback? Is it automated?
**Risk:** Bad deploy + no rollback = extended downtime while you debug.
**Fix:** Keep previous version available. `pm2 deploy revert` or Docker image tags.

### 8. Missing Environment Validation on Startup
**What to grep:** `process.env.` usage — are required vars checked at startup?
**Check:** If `DATABASE_URL` is missing, does the app crash immediately or fail silently later?
**Risk:** App starts, passes health check, then fails on first real request.
**Fix:** Validate all required env vars at startup. Exit with clear error if missing.

### 9. No Backup Strategy for Data
**What to grep:** Database backup config, cron jobs for dumps
**Check:** Is the database backed up? How often? Is restore tested?
**Risk:** Data loss from accidental deletion, corruption, or provider outage.
**Fix:** Automated daily backups. Test restore quarterly. Point-in-time recovery if available.

### 10. CORS/Proxy Misconfiguration
**What to grep:** `cors` config, nginx `proxy_pass`, `X-Forwarded-For` handling
**Check:** Is `trust proxy` set in Express? Does the app see real client IPs behind proxy?
**Risk:** Rate limiting by IP fails (all requests show proxy IP). CORS blocks legitimate frontend.
**Fix:** `app.set('trust proxy', 1)` behind reverse proxy. Whitelist exact frontend origins.

## MEDIUM Checks

### 11. No Monitoring / Alerting
**What to grep:** APM integration, error tracking (Sentry, Datadog, New Relic)
**Check:** If the app starts throwing 500s at 3am, does anyone get notified?
**Fix:** Add error tracking (Sentry free tier). Set up uptime monitoring (UptimeRobot).

### 12. No CI/CD Pipeline
**What to grep:** `.github/workflows/`, `Jenkinsfile`, `gitlab-ci.yml`
**Check:** Is deployment manual? Are tests run before deploy?
**Risk:** Human error in manual deploys. Untested code reaches production.

### 13. Development Dependencies in Production
**What to grep:** `devDependencies` installed in production, `nodemon` in start script
**Check:** Is `NODE_ENV=production`? Is `npm install --production` used?
**Fix:** Set `NODE_ENV=production`. Use `npm ci --omit=dev` in production builds.

### 14. No Request ID / Tracing
**What to grep:** Request ID middleware, correlation IDs in logs
**Check:** Can you trace a single request across services (API → queue → worker → AI)?
**Fix:** Generate UUID per request, pass through headers, include in all logs.

### 15. Temp Files Not Cleaned Up
**What to grep:** `os.tmpdir()|/tmp/` usage — is there cleanup logic?
**Check:** Do temp files from failed operations accumulate on disk?
**Risk:** Disk fills up → server crashes. Especially with file upload workloads.
**Fix:** Scheduled cleanup job (cron/setInterval) that deletes files older than N hours.
