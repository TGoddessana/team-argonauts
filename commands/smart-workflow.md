---
name: smart-workflow
description: Automated workflow - Review → Plan → Implementation
argument-hint: "<scope> (e.g., 'backend security')"
allowed-tools: ["Skill", "Glob", "Read", "AskUserQuestion", "Task", "TodoWrite"]
---

# Smart Workflow

Fully automated: Review → Plan → Summary → Sequential Implementation

## Instructions

Execute these steps **without any output between steps**:

### 1. Review
```
Skill: smart-review
Args: <user input>
Output: Extract report filename
```

### 2. Plan
```
Skill: smart-plan
Args: <report filename from step 1>
Output: Verify plans/ created
```

### 3. Summary
```
Glob: plans/*.plan.md
Read each file: priority, effort, title
Sort: P0 first, then P1, then P2
```

### 4. User Decision (FIRST interaction)
```
Show summary:
  - X P0 issues (~Yh)
  - Z P1 issues (~Wh)

AskUserQuestion:
  "Which issues to execute?"
  Options:
    - "All P0 (recommended)"
    - "All P0 + P1"
    - "Custom selection"
    - "None (manual)"

After user selects:
  - If "None": Stop workflow
  - Otherwise: IMMEDIATELY proceed to Step 5 with selected issues
```

### 5. Sequential Execution

**IMPORTANT: Execute this step IMMEDIATELY after user makes selection in Step 4.**

For each selected issue (in priority order: P0-1, P0-2, ..., P1-1, P1-2, ...):

```
A. Ask user confirmation:
   AskUserQuestion:
     "Execute [issue-title] (~Xh estimated)?"
     Options: "Execute now" / "Skip this" / "Stop workflow"

   If "Skip": continue to next issue
   If "Stop": end workflow
   If "Execute": proceed to B

B. Read plan file:
   Read plans/pX-Y-slug.plan.md
   Extract: **Category:** field

C. Map category to expert agent:
   Category pattern → Agent:
     - "Security", "Auth", "Injection" → lynceus
     - "Performance", "Scalability", "N+1" → heracles
     - "Architecture", "Design" → jason
     - "Infrastructure", "DevOps", "Docker" → argus
     - "Testing", "QA", "Race" → atalanta
     - "Frontend", "UI", "Component" → orpheus
     - Unknown/other → general-purpose

D. Launch expert agent (Task tool):
   Task:
     subagent_type: <agent from step C>
     description: "Implement [issue-title]"
     prompt: "You are implementing a fix from a plan file.

             1. Read the plan: plans/pX-Y-slug.plan.md
             2. Read all files in 'Files Affected' section
             3. Implement the fix in 'Solution' section
             4. Follow 'Implementation Steps' checklist
             5. Add tests from 'Verification Criteria'
             6. Run tests to verify
             7. DO NOT commit (just implement and test)

             Return summary: files modified, tests added, results"

   Wait for agent to complete.

E. Review agent result and ask about commit:
   Show agent's output to user

   AskUserQuestion:
     "Create git commit for this fix?"
     Options: "Yes, commit" / "No, skip commit" / "Stop workflow"

   If "Yes": Create commit with descriptive message
   If "No": skip commit, continue
   If "Stop": end workflow

F. Ask to continue:
   AskUserQuestion:
     "Continue to next issue?"
     Options: "Yes" / "Pause" / "Stop"

   If "Yes": loop to next issue (back to step A)
   If "Pause" or "Stop": end workflow
```

## Key Rules

**Steps 1-3: SILENT execution**
- No text output
- No user questions
- Just execute tools

**Step 4+: User interaction**
- Show summary
- Get approval for each step
