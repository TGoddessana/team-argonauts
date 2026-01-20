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
```

### 5. Sequential Execution

For each selected issue:

```
A. Confirm: "Execute [issue]?" → Yes/Skip/Stop

B. Read plan file, extract category

C. Map category to agent:
   - Security/Auth/Injection → lynceus
   - Performance/Scalability → heracles
   - Architecture/Design → jason
   - Infrastructure/DevOps → argus
   - Testing/QA → atalanta
   - Frontend/UI → orpheus
   - Unknown → general-purpose

D. Launch agent:
   Task tool with:
     - subagent_type: <selected agent>
     - prompt: "Read <plan_file>, implement fix, add tests, verify"

E. Review result, ask: "Create commit?" → Yes/No

F. Update progress, ask: "Continue?" → Yes/Pause/Stop
```

## Key Rules

**Steps 1-3: SILENT execution**
- No text output
- No user questions
- Just execute tools

**Step 4+: User interaction**
- Show summary
- Get approval for each step
