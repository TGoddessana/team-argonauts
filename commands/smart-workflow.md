---
name: smart-workflow
description: End-to-end workflow - Review → Plan → Sequential Implementation with clean context per issue
argument-hint: "<natural language scope> (e.g., 'backend security issues', 'all P0 fixes')"
allowed-tools: ["Read", "Grep", "Glob", "Task", "AskUserQuestion", "Write", "TodoWrite", "Skill"]
---

# Smart Workflow: Simple Sequential Execution

Execute complete workflow from code review to implementation, one issue at a time with clean context.

**🤖 AUTOMATED WORKFLOW:** Steps 1-3 (Review → Plan → Summary) run automatically without user input. User approval is requested only at Step 4 before implementation begins.

## Philosophy

**Simple > Complex**
- ✅ Sequential execution (safe, predictable)
- ✅ Clean context per issue (no contamination)
- ✅ User control at each step
- ❌ No parallel execution (avoid complexity)
- ❌ No batching (avoid conflicts)

## What This Does

```
User Input: "backend security issues"
  ↓ (AUTOMATIC - no user input needed)
1. Execute /smart-review "backend security issues"
   → BACKEND_REVIEW_2026-01-20.md
  ↓ (AUTOMATIC - no user input needed)
2. Execute /smart-plan BACKEND_REVIEW_2026-01-20.md
   → plans/p0-1-*.md, p0-2-*.md, ... (13 files)
  ↓ (AUTOMATIC - no user input needed)
3. Show summary to user
   → "Found 5 P0 and 8 P1 issues"
  ↓ (FIRST USER INTERACTION)
4. Ask: "Execute issues one by one?"
   → [Yes] [Customize] [No]
  ↓
5. For each issue (in priority order):
   ┌─────────────────────────────────────┐
   │ Clean Context (New Agent)           │
   ├─────────────────────────────────────┤
   │ 1. Load ONLY this plan file         │
   │ 2. Implement fix                    │
   │ 3. Run tests                        │
   │ 4. Return result                    │
   └─────────────────────────────────────┘
   ↓
   Ask: "Commit this fix?"
   → [Yes] [Skip] [Stop Workflow]
   ↓
   Update summary.md
   ↓
   Ask: "Continue to next issue?"
   → [Yes] [Skip This] [Stop]
```

## How It Works

### Phase 1: Review

Execute smart-review skill:

```markdown
Skill: smart-review
Args: <user's natural language input>
Output: REVIEW_REPORT_DATE.md
```

**Key Point:** This happens in the current context.

### Phase 2: Planning

Execute smart-plan skill:

```markdown
Skill: smart-plan
Args: <review report filename>
Output: plans/ directory with individual plan files
```

**Key Point:** This also happens in the current context.

### Phase 3: Show Summary

Read all plan files and show user:

```markdown
✅ Workflow Preparation Complete!

📊 Summary:
  - Review: BACKEND_REVIEW_2026-01-20.md
  - Plans: 13 issues (5 P0, 8 P1)
  - Total estimated time: ~47.5 hours

📋 Issues by Priority:
  P0 (Critical - ~18.5h):
    1. p0-1-sql-injection.plan.md (2h)
    2. p0-2-db-connection-pool.plan.md (4h)
    3. p0-3-shared-secret.plan.md (30min)
    4. p0-4-sync-file-parsing.plan.md (4h)
    5. p0-5-transaction-atomicity.plan.md (8h)

  P1 (High - ~29h):
    6. p1-1-gemini-timeout.plan.md (2h)
    7. p1-2-n-plus-one-query.plan.md (4h)
    ... (6 more)

🎯 Execution Plan:
  - Execute issues sequentially (one at a time)
  - Each issue runs in CLEAN CONTEXT (fresh agent)
  - User approval before each implementation
  - User approval before each commit
```

Use AskUserQuestion:
```markdown
question: "Begin sequential execution?"
options:
  - "Execute all P0 issues" (recommended)
  - "Execute all P0 + P1 issues"
  - "Execute specific issues (I'll choose)"
  - "No, I'll do it manually"
```

### Phase 4: Sequential Execution (Clean Context Per Issue)

**CRITICAL: Each issue runs in a NEW AGENT with CLEAN CONTEXT**

For each selected issue:

#### Step 4.1: Confirm Issue Execution

```markdown
Use AskUserQuestion:
  question: "Execute P0-1: SQL Injection (2h estimated)?"
  options:
    - "Yes - Execute now"
    - "Skip this issue"
    - "Stop workflow"
```

#### Step 4.2: Select Expert Agent & Launch

**IMPORTANT: Choose the RIGHT expert agent for each issue type**

```markdown
Step A: Read plan file and extract category
  Read plans/p0-1-sql-injection.plan.md
  Extract: **Category:** Security - SQL Injection

Step B: Map category to expert agent

Agent Selection Rules:
  - Security, Authentication, Injection, CSRF, XSS → "lynceus"
  - Performance, N+1 Query, Scalability, Backend → "heracles"
  - Architecture, Design, Refactoring → "jason"
  - Infrastructure, DevOps, Docker, K8s → "argus"
  - Testing, QA, Race Conditions → "atalanta"
  - Frontend, UI, Components → "orpheus"
  - Unknown/General → "general-purpose"

Category Matching Examples:
  "Security - SQL Injection" → lynceus
  "Performance" → heracles
  "Architecture" → jason
  "Scalability" → heracles
  "Security - CSRF" → lynceus
  "Concurrency" → atalanta

Step C: Launch expert agent

<Task>
  subagent_type: <selected-expert-agent>  # e.g., "lynceus" for security
  description: "Fix P0-1 SQL Injection"
  prompt: "You are implementing a single fix from a plan file.

  CRITICAL INSTRUCTIONS:
  1. Read the plan file: plans/p0-1-sql-injection.plan.md
  2. Read ALL files mentioned in 'Files Affected' section
  3. Implement the fix EXACTLY as described in 'Solution' section
  4. Follow 'Implementation Steps' checklist
  5. Add tests from 'Verification Criteria' section
  6. Run tests to verify the fix works
  7. DO NOT commit - just implement and test

  IMPORTANT RULES:
  - You have CLEAN CONTEXT - no prior conversation history
  - Focus ONLY on this single issue
  - Do NOT modify files not mentioned in the plan
  - If the plan references other issues, ignore them
  - Apply your domain expertise (security/performance/architecture)
  - Return a summary when done

  Return format:
  ✅ Implementation complete
  📁 Files modified: [list]
  🧪 Tests added: [list]
  ✅/❌ Tests result: [pass/fail]
  📝 Notes: [any issues or deviations from plan]"
</Task>
```

**Why Expert Agents:**
- ✅ Domain expertise (Lynceus knows security best practices)
- ✅ Better implementation quality (specialists > generalists)
- ✅ Appropriate test coverage (experts know what to test)
- ✅ Clean context per issue (still maintained)
- ✅ Predictable behavior (same expert, same baseline)

#### Step 4.3: Review Agent Result

After agent completes:

```markdown
Agent Result:
  ✅ Implementation complete

  📁 Files modified:
    - packages/web/seedup_web/services/recommendation_service.py

  🧪 Tests added:
    - tests/services/test_recommendation_service.py::test_sql_injection_blocked
    - tests/services/test_recommendation_service.py::test_embedding_param_binding

  ✅ Tests result: All tests pass (2/2)

  📝 Notes: No issues. Used SQLAlchemy ORM as planned.

Show user:
  - Full result summary
  - Ask: "Review changes? [Show Diff] [Skip]"
```

#### Step 4.4: Commit Approval

```markdown
Use AskUserQuestion:
  question: "Create commit for P0-1 fix?"
  options:
    - "Yes - Create commit"
    - "No - Skip commit (I'll do it later)"
    - "Stop workflow"
```

If "Yes":
```bash
git add <modified files>
git commit -m "fix(security): prevent SQL injection in recommendation engine

- Use SQLAlchemy ORM with pgvector for safe parameter binding
- Add security tests for injection attempts
- Tests: 2/2 passing

Resolves: P0-1 (plans/p0-1-sql-injection.plan.md)
Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

#### Step 4.5: Update Progress

Update summary.md:
```markdown
| # | Issue | Status | Effort |
|---|-------|--------|--------|
| 1 | SQL Injection | 🟢 Completed | 2h → 1.8h |
```

#### Step 4.6: Continue to Next Issue

```markdown
Use AskUserQuestion:
  question: "P0-1 complete! Continue to P0-2: DB Connection Pool (4h)?"
  options:
    - "Yes - Continue"
    - "Pause - I'll resume later"
    - "Stop workflow"
```

If "Pause":
```markdown
✅ Workflow paused

Progress:
  - Completed: 1/13 issues (P0-1)
  - Remaining: 12 issues
  - Time spent: 1.8h / Total: 47.5h

Resume with:
  /smart-workflow --resume plans/
```

#### Step 4.7: Repeat for Each Issue

Loop back to Step 4.1 for next issue.

## Key Features

### 1. Clean Context Per Issue

**Each issue gets a FRESH AGENT:**

```
Issue P0-1:
  Agent A (clean) → Read plan → Implement → Return result → DISPOSED

Issue P0-2:
  Agent B (clean) → Read plan → Implement → Return result → DISPOSED

Issue P0-3:
  Agent C (clean) → Read plan → Implement → Return result → DISPOSED
```

**NOT this (contaminated context):**
```
❌ Same agent for all:
  Main Agent → P0-1 → P0-2 → P0-3
  (Context keeps growing, token waste, potential contamination)
```

### 2. User Control Points

User approves at:
1. ✅ Before starting workflow
2. ✅ Before each issue execution
3. ✅ Before each commit
4. ✅ Before continuing to next issue

### 3. Resume Support

If workflow paused/interrupted:

```bash
/smart-workflow --resume plans/

Claude:
📊 Found existing workflow:
  - Total: 13 issues
  - Completed: 3 issues (P0-1, P0-2, P0-3)
  - Remaining: 10 issues

Resume from P0-4: Sync File Parsing?
[Yes] [Choose Different Issue] [Start New Workflow]
```

## Instructions

**CRITICAL: This is an AUTOMATED workflow. Execute ALL steps sequentially WITHOUT waiting for user input between steps.**

### Step 1: Execute Review

```markdown
Invoke Skill tool:
  skill: "smart-review"
  args: <user's natural language input>

Wait for completion.
Store result filename: REVIEW_REPORT_DATE.md

IMPORTANT: After smart-review completes, DO NOT stop or ask the user what to do next.
Immediately proceed to Step 2.
```

### Step 2: Execute Planning

```markdown
IMMEDIATELY after Step 1 completes, invoke Skill tool:
  skill: "smart-plan"
  args: <review report filename from Step 1>

Wait for completion.
Verify plans/ directory created.

IMPORTANT: After smart-plan completes, DO NOT stop.
Immediately proceed to Step 3.
```

### Step 3: Read All Plan Files

```markdown
IMMEDIATELY after Step 2 completes, use Glob to find all plan files:
  pattern: "plans/*.plan.md"

For each file:
  1. Read priority (P0/P1/P2)
  2. Read estimated effort
  3. Extract issue title

Sort by priority (P0 first, then P1, then P2)
Within priority, sort by number (p0-1 before p0-2)

IMPORTANT: After reading all plan files, immediately proceed to Step 4.
```

### Step 4: Show Summary and Get Approval

```markdown
NOW you can present a summary to the user and wait for their decision:

Present summary to user:
  - Total issues
  - Breakdown by priority
  - Total estimated time
  - Execution approach (sequential, clean context)

Use AskUserQuestion:
  question: "Which issues to execute?"
  options:
    - "All P0 (recommended for production readiness)"
    - "All P0 + P1 (recommended before scaling)"
    - "Custom selection (I'll choose specific issues)"
    - "None (I'll execute manually)"
```

### Step 5: Sequential Execution Loop

```markdown
For each selected issue:

  A. Ask: "Execute [issue-title] ([time])?"
     → If No/Skip: continue to next
     → If Stop: exit workflow

  B. Select expert agent and launch:

     B1. Read plan file and extract category:
         plan_content = Read(plan_file)
         category = extract_category(plan_content)
         # Example: "**Category:** Security - SQL Injection"

     B2. Map category to expert agent:
         agent_mapping = {
             "security|authentication|injection|csrf|xss": "lynceus",
             "performance|scalability|backend|n\+1": "heracles",
             "architecture|design|refactoring": "jason",
             "infrastructure|devops|docker|k8s": "argus",
             "testing|qa|race|concurrency": "atalanta",
             "frontend|ui|component": "orpheus",
         }

         selected_agent = match_category(category, agent_mapping)
         if not selected_agent:
             selected_agent = "general-purpose"

     B3. Launch expert agent:
         Use Task tool with:
           - subagent_type: selected_agent (e.g., "lynceus")
           - Clean context (new agent)
           - Prompt: Read plan, implement, test, return result
           - DO NOT commit

  C. Receive result from agent
     - Parse modified files
     - Parse test results
     - Show to user

  D. Ask: "Create commit?"
     → If Yes: create commit with proper message
     → If No: skip commit

  E. Update summary.md:
     - Mark issue as completed
     - Update progress counters

  F. Ask: "Continue to next issue?"
     → If Yes: loop
     → If Pause: save state and exit
     → If Stop: exit workflow
```

### Step 6: Workflow Complete

```markdown
After all issues:

✅ Smart Workflow Complete!

📊 Final Summary:
  - Total issues: 13
  - Completed: 13 (5 P0, 8 P1)
  - Skipped: 0
  - Failed: 0
  - Time: ~35h (estimated: 47.5h)

📁 Files Modified: 23 files
🧪 Tests Added: 47 tests
✅ All Tests Passing: Yes

📝 Commits Created: 13
  - git log --oneline -13

🎯 Next Steps:
  1. Review all commits: git log -13
  2. Run full test suite: pytest
  3. Deploy to staging
  4. Create PR: gh pr create --fill
```

## Edge Cases

### Agent Fails

```markdown
If agent returns error:

⚠️ P0-2 Implementation Failed

Error: Tests failing (2/5 failed)
  - test_db_pool_size: FAILED
  - test_connection_limit: FAILED

Options:
  [Retry] - Try again with same plan
  [Skip] - Skip this issue
  [Manual] - I'll fix it manually
  [Stop] - Stop workflow
```

### Plan File Missing

```markdown
If plan file not found:

⚠️ Plan file not found: plans/p0-2-db-connection-pool.plan.md

This might happen if:
  - Plan generation failed
  - File was deleted
  - Wrong directory

Options:
  [Regenerate Plans] - Run /smart-plan again
  [Skip This Issue] - Continue to next
  [Stop Workflow]
```

### Merge Conflict

```markdown
If git commit fails due to conflict:

⚠️ Commit Failed: Merge conflict detected

Files in conflict:
  - packages/web/seedup_web/services/recommendation_service.py

Someone modified this file while the agent was working.

Options:
  [Resolve Manually] - I'll handle the conflict
  [Skip Commit] - Continue without committing
  [Stop Workflow]
```

## Resume Support Implementation

### Saving State

After each issue completion, save state:

```json
// .claude/smart-workflow-state.json
{
  "workflow_id": "smart-workflow-2026-01-20-20:45",
  "review_file": "BACKEND_REVIEW_2026-01-20.md",
  "plans_dir": "plans/",
  "total_issues": 13,
  "completed": ["p0-1", "p0-2", "p0-3"],
  "skipped": [],
  "failed": [],
  "current": "p0-4",
  "started_at": "2026-01-20T20:45:00Z",
  "last_updated": "2026-01-20T22:30:00Z"
}
```

### Resuming

```markdown
If --resume flag or state file exists:

Read state file
Show progress summary
Ask: "Resume from [current-issue]?"

If Yes:
  Continue from current issue
Else:
  Start new workflow (archive old state)
```

## Performance Comparison

| Approach | Total Time | Context Size | User Control | Safety |
|----------|-----------|--------------|--------------|--------|
| **Manual (13 sessions)** | ~47.5h | Small | High | Highest |
| **This Workflow** | ~35h | Small | High | High |
| **Single Agent** | ~47.5h | Very Large | Low | Medium |
| **Parallel (risky)** | ~25h | Large | Low | Low |

**This workflow saves ~25% time vs manual while maintaining safety.**

## Example Execution

```markdown
User: /smart-workflow "backend security"

Claude:
🔍 Starting Smart Workflow...

[1/3] Review
  → Executing /smart-review "backend security"
  → Analyzing backend files...
  ✅ Review complete: BACKEND_REVIEW_2026-01-20.md
  → Found 5 P0, 8 P1 issues

[2/3] Planning
  → Executing /smart-plan BACKEND_REVIEW_2026-01-20.md
  → Generating plan files...
  ✅ Plans created: 13 files in plans/

[3/3] Summary
  📊 Workflow Ready

  Issues to fix:
    P0 (Critical): 5 issues (~18.5h)
    P1 (High): 8 issues (~29h)
    Total: 13 issues (~47.5h estimated)

Which issues should I execute?
  [All P0 issues (recommended)] [All P0 + P1] [Custom] [None]

User: All P0 issues

Claude:
✅ Will execute 5 P0 issues sequentially

Each issue will run in CLEAN CONTEXT (fresh agent)
You'll approve before each implementation and commit.

Ready to begin with P0-1?

📋 P0-1: SQL Injection in Recommendation Engine
  - Files: recommendation_service.py
  - Effort: 2h estimated
  - Impact: Critical security vulnerability

[Execute P0-1] [Skip] [Stop]

User: Execute P0-1

Claude:
🚀 Launching clean agent for P0-1...

[Agent working in isolated context...]

✅ Agent Complete (1.8h)

📁 Modified:
  - packages/web/seedup_web/services/recommendation_service.py

🧪 Tests Added (2):
  - test_sql_injection_blocked
  - test_embedding_param_binding

✅ Tests: 2/2 passing

[Show Diff] [Create Commit] [Skip Commit]

User: Create Commit

Claude:
✅ Commit created:
  "fix(security): prevent SQL injection in recommendation engine"

📊 Progress: 1/5 P0 issues complete (20%)

Continue to P0-2: DB Connection Pool (4h)?
[Yes] [Pause] [Stop]

User: Yes

[Repeats for remaining issues...]
```

## Notes

- Each agent is DISPOSED after completion (context cleaned)
- No context leakage between issues
- Token efficient (no accumulated conversation history)
- Predictable (each issue starts from same baseline)
- Safe (one issue at a time, fully tested before moving on)
- Resumable (can pause and continue later)
- User controlled (approval at every step)

## Comparison with Old Version

| Feature | Old (Complex) | New (Simple) |
|---------|--------------|--------------|
| **Execution** | Parallel batching | Sequential |
| **Context** | Shared | Clean per issue |
| **Complexity** | High | Low |
| **Safety** | Medium (conflicts) | High |
| **Speed** | Fast (~25h) | Medium (~35h) |
| **Predictability** | Low | High |
| **Debugging** | Hard | Easy |
| **User Control** | Medium | High |

**Trade-off:** We sacrifice ~30% speed for 2x safety and simplicity.
