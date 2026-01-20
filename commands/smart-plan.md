---
name: smart-plan
description: Split review report into actionable plan files per issue
argument-hint: "<review-file> (e.g., 'BACKEND_REVIEW_2026-01-20.md')"
allowed-tools: ["Read", "Write", "Glob", "AskUserQuestion"]
---

# Smart Plan

Splits review reports into independent, executable plan files - one per issue.

## Output Structure

```
Input:  BACKEND_REVIEW_2026-01-20.md (5 P0, 9 P1, 4 P2)
Output: plans/
        ├── p0-1-sql-injection.plan.md
        ├── p0-2-db-pool.plan.md
        ├── p1-1-timeout.plan.md
        └── summary.md
```

**Benefits:** Focused sessions, parallel work, progress tracking, no context overload.

## Plan File Template

```markdown
# [Issue Title]

**Priority:** P0/P1/P2
**Category:** [Security/Performance/Architecture/etc]
**Recommended Agent:** [lynceus/heracles/jason/argus/atalanta/orpheus]
**Effort:** [hours/days]
**Status:** 🔴 Not Started

## Problem
[Clear description from review]

## Impact
[What breaks if not fixed]

## Files Affected
- `path/to/file.py:123`
- `path/to/another.ts:45`

## Solution
[Detailed fix from review]

## Implementation Steps
1. [ ] Read affected files
2. [ ] Implement fix
3. [ ] Add tests
4. [ ] Verify no regressions
5. [ ] Update docs if needed

## Verification Criteria
- [ ] [Specific check 1]
- [ ] [Specific check 2]
- [ ] Tests pass
- [ ] No new issues

## Dependencies
- None (or list required fixes)

## Notes
[Additional context]
```

## Agent Recommendations

Map issue category to expert agent:

| Category | Agent | Expertise |
|----------|-------|-----------|
| Security, Auth, Injection, CSRF, XSS | **lynceus** | OWASP Top 10, threat modeling |
| Performance, N+1 Query, Scalability | **heracles** | Backend optimization, query tuning |
| Architecture, Design, Refactoring | **jason** | System design, tech lead decisions |
| Infrastructure, DevOps, K8s, CI/CD | **argus** | Deployment, monitoring, SRE |
| Testing, QA, Race Conditions | **atalanta** | Test coverage, edge cases |
| Frontend, UI, Components, UX | **orpheus** | Frontend architecture, accessibility |
| Documentation, API Docs | **calliope** | Technical writing, clarity |
| Unknown/General | **general-purpose** | Fallback |

**Examples:**
- "Security - SQL Injection" → lynceus
- "Performance - N+1 Query" → heracles
- "Architecture Flaw" → jason
- "Race Condition" → atalanta

## Instructions

### Step 1: Validate Input

```
Read <review-file>
Verify: Contains review header (e.g., "Team Argonauts", "Smart Review")
If not found: Ask user for correct path or suggest running /smart-review
```

### Step 2: Parse Issues

Extract P0, P1, P2 issues from report:

**Parsing patterns:**
- Look for: `### 🔴 P0:`, `### 🟡 P1:`, `### 🟢 P2:`
- Extract for each issue:
  - Title (after emoji/priority)
  - Severity (P0/P1/P2)
  - Category (`**Category:**` field)
  - Location (`**Location:**`, `**Where:**`, `**File:**`)
  - Impact (`**Impact:**`, `**Why:**`)
  - Fix (`**Fix:**`, `**Solution:**`)
  - Effort (`**Effort:**` or estimate from complexity)

**Handle variations:**
- Korean/English headers
- Different emoji markers
- Various section formats

### Step 3: Ask User for Scope

```markdown
AskUserQuestion:
  question: "Found X P0, Y P1, Z P2 issues. Create plan files for which priorities?"
  header: "Plan Scope"
  multiSelect: false
  options:
    - label: "P0 only (critical fixes, ~Xh)"
      description: "Production blockers. Fix before deployment."
    - label: "P0 + P1 (pre-scale, ~Yh) (Recommended)"
      description: "Issues needed before 10K+ users."
    - label: "P0 + P1 + P2 (all issues, ~Zh)"
      description: "Full technical debt cleanup."
```

### Step 4: Generate Plan Files

For each issue in selected scope:

1. **Create plans/ directory** (if not exists)

2. **Generate filename:**
   - Format: `p[priority]-[num]-[slug].plan.md`
   - Slug: lowercase, hyphenated, max 40 chars
   - Example: "SQL Injection in Recommendation" → `p0-1-sql-injection-recommendation.plan.md`

3. **Fill template:**
   - Use parsed data from Step 2
   - Map category to recommended agent (see table above)
   - Set status: 🔴 Not Started

4. **Make plans INDEPENDENT:**
   - Include full context (no references to other plans)
   - List all affected files with line numbers
   - Provide complete fix details
   - Add verification steps specific to this issue
   - Note dependencies if issue requires another fix first

### Step 5: Generate Summary File

Create `plans/summary.md`:

```markdown
# Smart Review Action Plan

**Review Date:** [date]
**Source Report:** [filename]
**Total Issues:** X (Y P0, Z P1, W P2)

## Progress Overview
- 🔴 Not Started: X
- 🟡 In Progress: 0
- 🟢 Completed: 0

## P0 Issues (Critical)
| # | Issue | File | Status | Effort |
|---|-------|------|--------|--------|
| 1 | SQL Injection | `file.py:123` | 🔴 | 2h |
...

**Total P0 Effort:** ~Xh

## P1 Issues (High)
[Similar table]

## Usage
1. Pick a plan: `plans/p0-1-*.plan.md`
2. New Claude session: "Read plans/p0-1-*.plan.md and implement"
3. Update status in this file
```

Include dependencies graph if any exist.

### Step 6: Output Confirmation

Show summary:

```
✅ Created X plan files in plans/

P0 Issues (Y):
  - plans/p0-1-sql-injection.plan.md
  - plans/p0-2-db-pool.plan.md
  ...

P1 Issues (Z):
  - plans/p1-1-timeout.plan.md
  ...

📊 Summary: plans/summary.md

Next Steps:
1. Review plans/summary.md for prioritization
2. Start with P0-1 (highest priority)
3. In new session: "Read plans/p0-1-*.plan.md and implement"
```

## Edge Cases

| Situation | Action |
|-----------|--------|
| Review file not found | Ask for path or suggest /smart-review |
| No P0/P1 issues | Congratulate, offer P2 plans |
| Plans exist | Ask to overwrite or merge |
| Unclear effort | Use conservative estimate + note |
| Issues have dependencies | Note in plan file and summary |
