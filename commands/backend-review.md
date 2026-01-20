---
name: backend-review
description: Quick backend review with Heracles (Backend Engineer)
argument-hint: "[file or directory path] (optional - defaults to recent changes)"
allowed-tools: ["Read", "Grep", "Glob", "Task"]
---

# Backend Review with Heracles

Execute a focused backend review with Heracles, the Backend Engineer. Specialized in API design, scalability, fault tolerance, and production-ready backend systems.

## What Heracles Reviews

- **API Design:** RESTful conventions, error handling, versioning
- **Scalability:** Performance at scale, connection pooling, caching
- **Fault Tolerance:** Timeouts, retries, circuit breakers, graceful degradation
- **Data Access:** Query patterns, N+1 prevention, transaction boundaries
- **Observability:** Logging, metrics, health checks

## Instructions

1. **Determine Review Scope**
   - If a path argument is provided, review that specific file or directory
   - If no argument, identify recently modified files (use `git diff` or `git status`)
   - If no git changes, ask the user what to review

2. **Execute Backend Review**
   - Use the Task tool to invoke the `heracles` agent
   - Provide full code context for backend analysis
   - Collect findings in P0-P4 format with production scenarios

3. **Compile Backend Report**

## Output Format

```markdown
# 💪 Heracles' Backend Review

**Scope:** [Files/directories reviewed]
**Date:** [Current date]

## Summary
[2-3 sentences: Assessment and critical concerns]

## Findings

### P[0-4]: [Issue Title]
**Category:** API | Data Access | Fault Tolerance | Scalability
**Location:** `file:line`
**Issue:** [What's wrong, incident scenario]
**Fix:**
```[language]
// Concrete fix
```

## Production Readiness
- Error handling: ✅/❌ [Notes]
- Timeouts: ✅/❌ [Notes]
- Retry logic: ✅/❌ [Notes]
- Observability: ✅/❌ [Notes]
- Performance: ✅/❌ [Notes]

## Verdict: [SHIP | SHIP WITH NOTES | REVISE | BLOCK]
```

## Common Issues Heracles Catches

- Missing timeouts on external calls
- No retry logic with exponential backoff
- N+1 query problems
- Missing error handling
- Unbounded list queries (no pagination)
- No circuit breakers on critical paths
- Missing connection pooling
- Synchronous operations that should be async
- No graceful degradation

## Examples

**Review API endpoints:**
```
/team-argonauts:backend-review src/api
```

**Review database layer:**
```
/team-argonauts:backend-review src/repositories
```

**Review recent changes:**
```
/team-argonauts:backend-review
```

## When to Use

- Before deploying new API endpoints
- When implementing external service integrations
- After database query changes
- When performance is critical
- Before high-traffic events
- When resilience is a concern
