---
name: security-audit
description: |
  Production security audit checklist. Use this skill on ANY project that handles user
  authentication, processes user input, or is exposed to the internet. Trigger when the project
  has JWT, sessions, OAuth, bcrypt, helmet, cors, multer, or any auth middleware. Also trigger
  when the user says "security review", "check my auth", "JWT audit", "is my API secure",
  "CORS issue", "rate limiting", "SQL injection check", "can users access each other's data",
  or "IDOR vulnerability". This skill should run alongside every other audit skill —
  security applies to ALL stacks. When activated, immediately scan using the grep patterns below.
---

# Security Production Audit

## Output Format
Report findings as a severity table:
| # | Severity | File | Issue | Production Impact | Fix |
|---|----------|------|-------|-------------------|-----|

After the table, list CRITICAL fixes first with exact code changes.
Ask: "Want me to apply these fixes? I'll start with CRITICAL."
After applying CRITICAL fixes, re-scan before moving to HIGH severity.

> NOTE: This skill applies to ALL stacks. Always run in combination with the relevant
> stack skill (node-express, python-fastapi, react-frontend, etc).

## CRITICAL Checks

### 1. Authentication Bypass — Missing Auth on Routes
**What to grep:** Route files — check which routes DON'T have auth middleware
**Check:** Are there endpoints that should require auth but don't? (admin routes, data endpoints)
**Risk:** Unauthenticated access to sensitive data or admin functions.
**Fix:** Audit every route. Apply auth middleware at router level, whitelist public routes explicitly.

### 2. Authorization Bypass — Missing Ownership Checks
**What to grep:** `findFirst|findOne|findById` without `where: { user_id: req.user.id }`
**Check:** Can User A access User B's data by guessing/enumerating IDs?
**Risk:** Horizontal privilege escalation. Any user can read/modify any other user's data.
**Fix:** Always filter by authenticated user's ID: `where: { id: resourceId, teacher_id: req.teacher.teacher_id }`.

### 3. SQL/NoSQL Injection
**What to grep:** `$queryRawUnsafe|$executeRawUnsafe|raw(|exec(` with string interpolation
**Check:** Is user input ever interpolated into raw queries without parameterization?
**Risk:** Full database compromise. Data exfiltration, modification, deletion.
**Fix:** Use parameterized queries (`$1`, `$2`). Never interpolate user input into SQL strings.

### 4. JWT — No Expiry or Very Long Expiry
**What to grep:** `jwt.sign(` — check `expiresIn` value
**Check:** What's the access token lifetime? Is there a refresh token flow?
**Risk:** Stolen token valid forever (or for days). No way to revoke access.
**Fix:** Access token: 15-30 min. Refresh token: 7 days with rotation. Revocation list for compromised tokens.

### 5. Rate Limiting Gaps
**What to grep:** Rate limit middleware — which routes are covered?
**Check:** Are login, registration, password reset, and expensive operations rate-limited?
**Risk:** Brute force attacks on login. API abuse. Cost amplification on AI endpoints.
**Fix:** Per-IP limit on login (5/min). Per-user limit on expensive ops. Global baseline limit.

## HIGH Checks

### 6. Rate Limiter Bypass — Redis Fallback
**What to grep:** Rate limiter store configuration — what happens when Redis is down?
**Check:** Does the rate limiter fall back to in-memory store in cluster mode?
**Risk:** In cluster mode (4 processes), each has own memory store = 4× the rate limit.
**Fix:** Fail closed on auth routes (503 if Redis unavailable). Or use sticky sessions.

### 7. CSRF on Cookie-Based Auth
**What to grep:** `cookie|httpOnly|sameSite` in auth/token code
**Check:** If refresh tokens are in cookies, is there CSRF protection?
**Risk:** Malicious site can trigger authenticated actions via cookie-bearing requests.
**Fix:** `SameSite=Strict` on cookies. Or double-submit cookie pattern. Or token in header only.

### 8. Sensitive Data in Responses
**What to grep:** API responses returning `password|hash|secret|token|private_key`
**Check:** Do user/admin endpoints return password hashes, tokens, or internal IDs?
**Risk:** Information disclosure. Leaked hashes can be cracked offline.
**Fix:** Explicit `select` in queries. Never return password fields. Strip sensitive data in serializer.

### 9. File Upload — No Type Validation
**What to grep:** `multer|upload` config — check `fileFilter`
**Check:** Can users upload `.exe`, `.php`, `.sh` files? Is MIME type validated?
**Risk:** Malicious file upload → remote code execution if served/executed.
**Fix:** Whitelist allowed extensions AND validate MIME type. Store outside web root.

### 10. Missing Security Headers
**What to grep:** `helmet` or manual header setting
**Check:** Are `X-Content-Type-Options`, `X-Frame-Options`, `Strict-Transport-Security` set?
**Risk:** Clickjacking, MIME sniffing, protocol downgrade attacks.
**Fix:** Use `helmet()` middleware (Express) or set headers manually.

## MEDIUM Checks

### 11. Password Storage — Weak Hashing
**What to grep:** `bcrypt|argon2|scrypt|pbkdf2` — which algorithm? What cost factor?
**Check:** Is bcrypt rounds ≥ 10? Or is MD5/SHA256 used (bad)?
**Risk:** Weak hashing = passwords crackable in hours with GPU.
**Fix:** bcrypt with rounds=12, or argon2id. Never MD5/SHA for passwords.

### 12. No Account Lockout
**What to grep:** Failed login attempt tracking
**Check:** After 10 failed logins, is the account locked or delayed?
**Risk:** Unlimited brute force attempts on known usernames.
**Fix:** Lock account for 15 min after 5 failed attempts. Or exponential delay.

### 13. Verbose Error Messages in Production
**What to grep:** `stack|trace|err.message` in error responses
**Check:** Do 500 errors return stack traces or internal details?
**Risk:** Information disclosure — reveals file paths, library versions, DB schema.
**Fix:** Generic error in production. Full details only in logs.

### 14. Missing Input Length Limits
**What to grep:** `express.json({ limit` and text field length validation
**Check:** Can a user send a 100MB JSON body? A 1M character text field?
**Risk:** DoS via oversized payloads. DB storage exhaustion.
**Fix:** `express.json({ limit: '1mb' })`. Validate string lengths in schema.

### 15. Insecure Direct Object References (IDOR)
**What to grep:** Route params like `/:id` used directly in DB queries
**Check:** Is the ID validated as belonging to the authenticated user?
**Risk:** Enumerate IDs to access other users' resources.
**Fix:** Always include ownership check: `WHERE id = :id AND owner_id = :userId`.
