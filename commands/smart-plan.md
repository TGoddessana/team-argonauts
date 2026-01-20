---
name: smart-plan
description: Parse smart-review report and split into independent, actionable plan files for each critical issue
argument-hint: "<review-report-file> (e.g., 'BACKEND_REVIEW_2026-01-20.md')"
allowed-tools: ["Read", "Write", "Glob", "AskUserQuestion"]
---

# Smart Plan Splitter

Parse a smart-review report and create **independent, executable plan files** for each P0/P1 issue.

## Problem Statement

Smart reviews often discover 10+ critical issues in legacy codebases. Fixing all issues in a single Claude Code session:
- ❌ Reduces code quality (context overload)
- ❌ Makes it hard to track progress
- ❌ Prevents parallel work by multiple developers
- ❌ Risks incomplete fixes

## Solution

Split the review report into **isolated plan files**, one per issue:

```
Input:  BACKEND_REVIEW_2026-01-20.md (5 P0s, 9 P1s, 4 P2s)
Output: plans/
        ├── p0-1-sql-injection.plan.md
        ├── p0-2-db-connection-pool.plan.md
        ├── p0-3-shared-secret.plan.md
        ├── p0-4-sync-file-parsing.plan.md
        ├── p0-5-transaction-atomicity.plan.md
        ├── p1-1-gemini-timeout.plan.md
        ├── p1-2-n-plus-one-query.plan.md
        └── summary.md (tracking file)
```

## How It Works

### Phase 1: Parse Review Report

Read the specified review report file and extract:
- **P0 Issues** (Critical - blocks production)
- **P1 Issues** (High priority - needed before scale)
- **P2 Issues** (Medium priority - technical debt)

For each issue, extract:
- Title
- Severity
- Category
- File locations
- Impact description
- Suggested fix
- Estimated effort

### Phase 2: Generate Plan Files

For each P0 and P1 issue, create a **standalone plan file**:

**File naming convention:**
```
plans/[priority]-[number]-[slug].plan.md

Examples:
- p0-1-sql-injection.plan.md
- p0-2-db-connection-pool.plan.md
- p1-1-gemini-timeout.plan.md
```

**Plan file structure:**
```markdown
# [Issue Title]

**Priority:** P0/P1/P2
**Category:** [Security/Performance/Architecture/etc]
**Estimated Effort:** [hours/days]
**Status:** 🔴 Not Started

## Problem

[Clear description of the issue from review report]

## Impact

[What happens if not fixed]

## Files Affected

- `path/to/file.py:line`
- `path/to/another.py:line`

## Solution

[Detailed fix from review report]

## Implementation Steps

1. [ ] Read affected files
2. [ ] Implement fix
3. [ ] Add tests for the fix
4. [ ] Verify no regressions
5. [ ] Update documentation if needed

## Verification Criteria

- [ ] [Specific check 1]
- [ ] [Specific check 2]
- [ ] Tests pass
- [ ] No new issues introduced

## Dependencies

- None (or list other issues that must be fixed first)

## Notes

[Any additional context from review]
```

### Phase 3: Create Summary Tracking File

Generate `plans/summary.md` to track all issues:

```markdown
# Smart Review Action Plan

**Review Date:** 2026-01-20
**Source Report:** BACKEND_REVIEW_2026-01-20.md
**Total Issues:** 18 (5 P0, 9 P1, 4 P2)

## Progress Overview

- 🔴 Not Started: 14
- 🟡 In Progress: 0
- 🟢 Completed: 0

## P0 Issues (Critical - Block Production)

| # | Issue | File | Status | Effort |
|---|-------|------|--------|--------|
| 1 | SQL Injection in Recommendation Engine | `recommendation_service.py:747` | 🔴 | 2h |
| 2 | Database Connection Pool Misconfiguration | `db_config.py:14` | 🔴 | 4h |
| 3 | JWT and Session Share Same Secret | `app.py:65`, `security.py:189` | 🔴 | 30min |
| 4 | Sync File Parsing Blocks Event Loop | `business_plan_service.py:112` | 🔴 | 4h |
| 5 | Transaction Management Breaks Atomicity | `db_config.py:30` | 🔴 | 8h |

**Total P0 Effort:** ~18.5 hours

## P1 Issues (High Priority - Before Scale)

| # | Issue | File | Status | Effort |
|---|-------|------|--------|--------|
| 1 | Gemini API Timeout Missing | `ai_analyzer.py:405` | 🔴 | 2h |
| 2 | N+1 Query in Recommendation | `recommendation_service.py:632` | 🔴 | 4h |
| ... | ... | ... | ... | ... |

**Total P1 Effort:** ~22 hours

## P2 Issues (Technical Debt)

[Summary only - not split into individual plans unless user requests]

## Usage

### Fix a single issue:
```bash
# Start a new Claude Code session
claude-code

# In Claude Code, reference the plan:
"Please read plans/p0-1-sql-injection.plan.md and implement the fix"
```

### Track progress:
Update the Status column in this file as you complete issues:
- 🔴 Not Started
- 🟡 In Progress
- 🟢 Completed

### Prioritization:
1. Fix all P0 issues before production deployment (~2.5 days)
2. Fix P1 issues before scaling to 10K+ users (~3 days)
3. Address P2 issues as technical debt

## Dependencies Graph

```
P0-2 (DB Pool) ──┐
P0-5 (Transaction) ─┤
                    ├──→ P1-2 (N+1 Query)
                    │
P0-1 (SQL Injection) ┘

(Other issues are independent)
```
```

### Phase 4: Ask User for Confirmation

Use AskUserQuestion to show:
- Total issues found: X P0s, Y P1s, Z P2s
- Estimated effort breakdown
- Ask which priorities to split into plans:
  - Option 1: P0 only (recommended for immediate fixes)
  - Option 2: P0 + P1 (recommended for pre-scale)
  - Option 3: P0 + P1 + P2 (full technical debt)

## Instructions

### Step 1: Validate Input

Check that the review report file exists:
- Use Read tool to load the report
- Verify it's a smart-review output (has "Team Argonauts Smart Review" header)
- If file not found, ask user for correct path

### Step 2: Parse Issues

Extract all P0, P1, and P2 issues from the report:

**Parsing logic:**
- Look for sections like "### 🔴 P0: [Title]"
- Extract:
  - Issue title (after emoji and priority)
  - Severity (P0/P1/P2)
  - Category (look for "**Category:**")
  - File locations (look for "**Location:**" or "**Where:**")
  - Impact (look for "**Impact:**" or "**Why:**")
  - Fix (look for "**Fix:**" or "**Solution:**")
  - Effort (look for "**Effort:**" or estimate based on complexity)

**Handle variations:**
- Korean and English headers
- Different emoji markers
- Various section formats

### Step 3: Ask User for Scope

Use AskUserQuestion:

```
questions:
  - question: "Found 5 P0 issues, 9 P1 issues, and 4 P2 issues. Which should I create plan files for?"
    header: "Plan Scope"
    multiSelect: false
    options:
      - label: "P0 only (critical fixes, ~18 hours)"
        description: "Issues that block production deployment. Fix these first."
      - label: "P0 + P1 (pre-scale fixes, ~40 hours) (Recommended)"
        description: "Issues needed before reaching 10K+ users. Recommended for most teams."
      - label: "P0 + P1 + P2 (all issues, ~50+ hours)"
        description: "Full technical debt cleanup. Only if you have dedicated time."
```

### Step 4: Generate Plan Files

For each issue in selected scope:

1. Create `plans/` directory if not exists
2. Generate plan file with naming: `p[priority]-[number]-[slug].plan.md`
   - Slug: lowercase, hyphenated, max 40 chars
   - Example: "SQL Injection in Recommendation Engine" → `sql-injection-recommendation`
3. Fill in all sections from parsed data
4. Set initial status: 🔴 Not Started

**Critical: Each plan must be INDEPENDENT**
- Include full context (don't reference other plans)
- List all affected files with line numbers
- Provide complete fix code, not just hints
- Add verification steps specific to this issue
- Note dependencies if issue requires another fix first

### Step 5: Generate Summary File

Create `plans/summary.md` with:
- Overview table of all issues
- Progress tracking
- Effort estimates
- Dependencies graph (if any)
- Usage instructions

### Step 6: Output Confirmation

Print summary:
```
✅ Created 14 plan files in plans/

P0 Issues (5):
  - plans/p0-1-sql-injection.plan.md
  - plans/p0-2-db-connection-pool.plan.md
  - plans/p0-3-shared-secret.plan.md
  - plans/p0-4-sync-file-parsing.plan.md
  - plans/p0-5-transaction-atomicity.plan.md

P1 Issues (9):
  - plans/p1-1-gemini-timeout.plan.md
  - plans/p1-2-n-plus-one-query.plan.md
  - ... (7 more)

📊 Summary: plans/summary.md

Next Steps:
1. Review plans/summary.md for prioritization
2. Pick a plan file to start with (recommend P0-1)
3. In a new Claude Code session, run:
   "Read plans/p0-1-sql-injection.plan.md and implement the fix"
```

## Example Workflow

### User runs smart-review:
```bash
/smart-review "backend code"
# Output: BACKEND_REVIEW_2026-01-20.md
```

### User splits into plans:
```bash
/smart-plan BACKEND_REVIEW_2026-01-20.md
# Output: plans/ directory with 14 plan files
```

### User fixes issues one by one:
```bash
# New Claude Code session
"Read plans/p0-1-sql-injection.plan.md and implement the fix"

# After completion, update summary
"Mark P0-1 as completed in plans/summary.md"

# Next issue
"Read plans/p0-2-db-connection-pool.plan.md and implement the fix"
```

## Benefits

### ✅ Focused Sessions
Each Claude Code session tackles ONE issue with full context

### ✅ Parallel Work
Multiple developers can fix different issues simultaneously

### ✅ Progress Tracking
summary.md shows what's done, what's next

### ✅ Reusable Plans
Plan files can be revisited if fix needs revision

### ✅ Better Quality
No context overload = higher quality fixes

### ✅ Testable Units
Each fix is independently verifiable

## Edge Cases

| Situation | Action |
|-----------|--------|
| **Review file not found** | Ask user for correct path or run /smart-review first |
| **No P0/P1 issues** | Congratulate user, offer to create P2 plans anyway |
| **Issues have dependencies** | Note in both plan file and summary.md |
| **Unclear effort estimate** | Use conservative estimate, note uncertainty |
| **Files already exist** | Ask user if they want to overwrite or merge |

## Output Structure

```
plans/
├── p0-1-sql-injection.plan.md
├── p0-2-db-connection-pool.plan.md
├── p0-3-shared-secret.plan.md
├── p0-4-sync-file-parsing.plan.md
├── p0-5-transaction-atomicity.plan.md
├── p1-1-gemini-timeout.plan.md
├── p1-2-n-plus-one-query.plan.md
├── p1-3-oauth-csrf.plan.md
├── p1-4-jwt-in-url.plan.md
├── p1-5-circuit-breaker.plan.md
├── p1-6-path-traversal.plan.md
├── p1-7-race-condition.plan.md
├── p1-8-recommendation-scale.plan.md
├── p1-9-another-issue.plan.md
└── summary.md
```

## Integration with /smart-fix (Future)

This command creates the groundwork for a future `/smart-fix` command:

```bash
/smart-fix p0-1
# Reads plans/p0-1-sql-injection.plan.md
# Implements the fix automatically
# Updates summary.md
```

For now, users manually read plan files and work with Claude Code normally.
