# Security Review Example

This example shows Lynceus (Security Engineer) reviewing authentication code and finding a critical SQL injection vulnerability.

## Input Code

```typescript
// src/auth/login.ts
export async function login(username: string, password: string) {
  // ❌ VULNERABLE CODE - DO NOT USE
  const query = `
    SELECT * FROM users
    WHERE username = '${username}'
    AND password = '${password}'
  `;

  const user = await db.query(query);

  if (user) {
    const token = generateToken(user.id);
    return { success: true, token };
  }

  return { success: false, error: 'Invalid credentials' };
}
```

## Command

```
/team-argonauts:security-review src/auth/login.ts
```

## Lynceus' Review Output

```markdown
# 👁️ Lynceus' Security Review

**Scope:** src/auth/login.ts
**Date:** 2024-01-15

## Summary
Critical security vulnerabilities found. This authentication implementation is exploitable and must be fixed before any deployment. SQL injection allows complete database compromise, and passwords are compared in plaintext.

## Threat Model
- **Assets:** User credentials, entire database, application access
- **Attackers:** Unauthenticated external attackers, low skill required
- **Attack Surface:** Login endpoint (public-facing)

## Findings

### P0: SQL Injection Vulnerability
**Category:** Injection
**Location:** `src/auth/login.ts:4-7`
**Issue:** User input (`username` and `password`) directly concatenated into SQL query without parameterization. An attacker can inject SQL code to bypass authentication or dump the entire database.

**Impact:**
- Authentication bypass (login as any user)
- Database dump (exfiltrate all user data)
- Data modification or deletion
- Potential remote code execution (depending on DB permissions)

**Exploit Example:**
```
Username: admin' --
Password: anything

Generated query:
SELECT * FROM users WHERE username = 'admin' --' AND password = 'anything'
                                           ^^^ comment out rest
Result: Logs in as admin without knowing password
```

**Fix:**
```typescript
// Use parameterized queries
export async function login(username: string, password: string) {
  const query = `
    SELECT * FROM users
    WHERE username = $1
  `;

  const user = await db.query(query, [username]);

  if (!user) {
    return { success: false, error: 'Invalid credentials' };
  }

  // Compare password hash, not plaintext
  const isValid = await bcrypt.compare(password, user.password_hash);

  if (isValid) {
    const token = generateToken(user.id);
    return { success: true, token };
  }

  return { success: false, error: 'Invalid credentials' };
}
```

---

### P0: Plaintext Password Comparison
**Category:** Cryptographic Failures
**Location:** `src/auth/login.ts:7`
**Issue:** Passwords are being compared directly in the SQL query, meaning they must be stored in plaintext in the database. This is a catastrophic security failure.

**Impact:**
- If database is compromised, all passwords are exposed
- Users who reuse passwords across sites are vulnerable everywhere
- Regulatory violations (GDPR, CCPA, etc.)
- Reputation damage

**Fix:**
```typescript
// In registration
import bcrypt from 'bcrypt';

export async function register(username: string, password: string) {
  // Hash password with bcrypt (cost factor 12)
  const password_hash = await bcrypt.hash(password, 12);

  await db.query(
    'INSERT INTO users (username, password_hash) VALUES ($1, $2)',
    [username, password_hash]
  );
}

// In login (see above fix)
const isValid = await bcrypt.compare(password, user.password_hash);
```

---

### P1: No Rate Limiting
**Category:** Authentication Failures
**Location:** `src/auth/login.ts` (entire function)
**Issue:** No rate limiting or account lockout mechanism. Attackers can attempt unlimited login attempts for brute-force attacks.

**Impact:**
- Credential stuffing attacks
- Brute force password attacks
- Account enumeration

**Fix:**
```typescript
import rateLimit from 'express-rate-limit';

// Add rate limiting middleware
export const loginRateLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 5, // 5 attempts
  message: 'Too many login attempts, please try again later',
  standardHeaders: true,
  legacyHeaders: false,
});

// Apply to login route
app.post('/login', loginRateLimiter, loginHandler);
```

---

### P1: Generic Error Messages Leak Information
**Category:** Information Disclosure
**Location:** `src/auth/login.ts:16`
**Issue:** Using generic "Invalid credentials" is good, but code structure may still leak whether username exists via timing differences.

**Fix:**
```typescript
// Constant-time comparison to prevent timing attacks
export async function login(username: string, password: string) {
  const user = await db.query(
    'SELECT * FROM users WHERE username = $1',
    [username]
  );

  // Always hash a password even if user doesn't exist (timing attack prevention)
  const password_hash = user?.password_hash || '$2b$12$fakehashtopreventtiming';
  const isValid = await bcrypt.compare(password, password_hash);

  if (user && isValid) {
    const token = generateToken(user.id);
    return { success: true, token };
  }

  // Same error whether user doesn't exist or password wrong
  return { success: false, error: 'Invalid credentials' };
}
```

---

### P2: Missing Audit Logging
**Category:** Security Logging
**Location:** `src/auth/login.ts` (entire function)
**Issue:** No logging of authentication attempts, making it impossible to detect attacks or investigate breaches.

**Fix:**
```typescript
import logger from './logger';

export async function login(username: string, password: string) {
  // ... authentication logic ...

  if (user && isValid) {
    logger.info('Login successful', {
      userId: user.id,
      username: user.username,
      timestamp: new Date().toISOString(),
      ip: req.ip // from request context
    });
    return { success: true, token };
  }

  logger.warn('Login failed', {
    username, // OK to log username on failure for security monitoring
    timestamp: new Date().toISOString(),
    ip: req.ip
  });

  return { success: false, error: 'Invalid credentials' };
}
```

---

## Security Checklist

- Auth: ❌ **CRITICAL ISSUES**
  - SQL injection vulnerability
  - Plaintext passwords
  - No rate limiting

- Authz: ⚠️ **NOT REVIEWED** (no authorization logic in this file)

- Input validation: ❌ **MISSING**
  - No validation on username/password format
  - No length limits

- Secrets: ❌ **PLAINTEXT PASSWORDS**

- Dependencies: ⚠️ **NEEDS REVIEW**
  - Need to add bcrypt dependency
  - Need to review database library for SQL injection protection

---

## Verdict: **CRITICAL - BLOCK DEPLOYMENT**

**This code must NOT be deployed to production under any circumstances.**

### Required Fixes Before Deployment:
1. ✅ Use parameterized queries (fixes SQL injection)
2. ✅ Hash passwords with bcrypt (fixes plaintext storage)
3. ✅ Add rate limiting (prevents brute force)
4. ✅ Add audit logging (enables detection)
5. ✅ Add constant-time comparison (prevents timing attacks)

### Estimated Fix Time:
- 2-3 hours for secure implementation
- Additional time for testing and security review

### Next Steps:
1. Implement all P0 fixes immediately
2. Add comprehensive tests including security test cases
3. Re-run security review after fixes
4. Consider penetration testing before production deployment

---

## Team Handoffs

- **Heracles (Backend):** Needs to implement rate limiting middleware and audit logging infrastructure
- **Atalanta (QA):** Needs security test cases for SQL injection, brute force, timing attacks
- **Jason (Architect):** Should review overall authentication strategy and session management approach
```

## Key Takeaways

1. **SQL Injection is still common** - Always use parameterized queries
2. **Never store plaintext passwords** - Use bcrypt, argon2, or scrypt
3. **Defense in depth** - Rate limiting, logging, and proper error handling all matter
4. **Security is not optional** - P0 security issues block deployment

## After Fixing

After implementing the fixes, run the security review again:

```
/team-argonauts:security-review src/auth/login.ts
```

Expected result: **SECURE** verdict with no P0/P1 issues.
