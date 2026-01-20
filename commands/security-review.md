---
name: security-review
description: Quick security review with Lynceus (Security Engineer)
argument-hint: "[file or directory path] (optional - defaults to recent changes)"
allowed-tools: ["Read", "Grep", "Glob", "Task"]
---

# Security Review with Lynceus

Execute a focused security review with Lynceus, the Security Engineer. Specialized in finding vulnerabilities, auth issues, injection attacks, and data protection problems.

## What Lynceus Reviews

- **Authentication/Authorization:** Session handling, token security, password storage
- **Injection Vulnerabilities:** SQL injection, XSS, command injection, SSRF
- **Access Control:** IDOR, privilege escalation, broken access control
- **Data Protection:** Encryption, PII handling, secrets management
- **OWASP Top 10:** Comprehensive security vulnerability check

## Instructions

1. **Determine Review Scope**
   - If a path argument is provided, review that specific file or directory
   - If no argument, identify recently modified files (use `git diff` or `git status`)
   - If no git changes, ask the user what to review

2. **Execute Security Review**
   - Use the Task tool to invoke the `lynceus` agent
   - Provide full code context for security analysis
   - Collect findings in P0-P4 format with exploit scenarios

3. **Compile Security Report**

## Output Format

```markdown
# 👁️ Lynceus' Security Review

**Scope:** [Files/directories reviewed]
**Date:** [Current date]

## Summary
[2-3 sentences: Security posture and critical findings]

## Threat Model
- **Assets:** [What are we protecting?]
- **Attackers:** [Who would target this?]
- **Attack Surface:** [Entry points]

## Findings

### P[0-4]: [Vulnerability Title]
**Category:** Auth | Injection | Data Exposure | Access Control | Config
**Location:** `file:line`
**Issue:** [Vulnerability and exploit scenario]
**Impact:** [What attacker gains]
**Fix:**
```[language]
// Secure implementation
```

## Security Checklist
- Auth: ✅/❌ [Notes]
- Authz: ✅/❌ [Notes]
- Input validation: ✅/❌ [Notes]
- Secrets: ✅/❌ [Notes]
- Dependencies: ✅/❌ [Notes]

## Verdict: [SECURE | CONDITIONAL | INSECURE | CRITICAL]
```

## Common Issues Lynceus Catches

- Hardcoded credentials or API keys
- SQL injection from string concatenation
- Missing authentication checks
- IDOR vulnerabilities (accessing other users' data)
- XSS from unescaped output
- Insecure password storage (plaintext, MD5, SHA1)
- Missing CSRF protection
- Overly permissive CORS

## Examples

**Review authentication code:**
```
/team-argonauts:security-review src/auth
```

**Review API endpoints:**
```
/team-argonauts:security-review src/api
```

**Review recent changes:**
```
/team-argonauts:security-review
```

## When to Use

- Before deploying code that handles user data
- When implementing authentication or authorization
- After adding new API endpoints
- When accepting user input
- Before code review for sensitive features
- When security is a primary concern
