---
name: review
description: Run a comprehensive production-ready review with the entire Argonauts team (7 senior engineers)
argument-hint: "[file or directory path] (optional - defaults to recent changes)"
allowed-tools: ["Read", "Grep", "Glob", "Task"]
---

# Team Argonauts Full Review

Execute a comprehensive code review using all 7 Argonauts team members. Each expert reviews from their domain perspective, providing FAANG-level production-ready feedback.

## The Team

| Agent | Role | Focus |
|-------|------|-------|
| Jason | Tech Lead/Architect | Architecture, trade-offs, design direction, orchestration |
| Heracles | Backend Engineer | API design, scalability, fault tolerance, data modeling |
| Orpheus | Frontend Engineer | UX, performance, accessibility, state management |
| Argus | DevOps/SRE | Deployment, monitoring, infrastructure, CI/CD |
| Lynceus | Security Engineer | Vulnerabilities, auth, data protection, threat modeling |
| Atalanta | QA Engineer | Test coverage, edge cases, quality, bug hunting |
| Calliope | Technical Writer | Documentation, naming, clarity, API docs |

## Instructions

1. **Determine Review Scope**
   - If a path argument is provided, review that specific file or directory
   - If no argument, identify recently modified files (use `git diff` or `git status`)
   - If no git changes, ask the user what to review

2. **Execute Reviews in Order**
   For each relevant agent (skip agents not applicable to the code type):

   a. **Jason (Tech Lead/Architect)** - Always first, sets context and architecture direction
   b. **Lynceus (Security Engineer)** - Security issues are critical
   c. **Heracles (Backend Engineer)** - If backend/API code exists
   d. **Orpheus (Frontend Engineer)** - If frontend code exists
   e. **Argus (DevOps/SRE)** - If infrastructure/config/deployment exists
   f. **Atalanta (QA Engineer)** - Test coverage for all code
   g. **Calliope (Technical Writer)** - Documentation and clarity last

3. **For Each Agent Review**
   - Use the Task tool to invoke the agent
   - Provide the agent with the code context
   - Collect findings in P0-P4 format

4. **Compile Final Report**

## Output Format

```markdown
# 🚀 Team Argonauts Review Report

**Scope:** [Files/directories reviewed]
**Date:** [Current date]

## Summary
- **Critical (P0):** [count]
- **High (P1):** [count]
- **Medium (P2):** [count]
- **Low (P3):** [count]
- **Nitpick (P4):** [count]

## Overall Verdict: [SHIP | SHIP WITH NOTES | REVISE | BLOCK]

---

## 🏛️ Jason (Tech Lead)
[Jason's review output]

---

## 👁️ Lynceus (Security Engineer)
[Lynceus's review output]

---

[Continue for each agent...]

---

## Action Items (Priority Order)

### Must Fix Before Ship (P0-P1)
1. [Item from findings]
2. [Item from findings]

### Should Fix Soon (P2)
1. [Item from findings]

### Consider Later (P3-P4)
1. [Item from findings]
```

## Notes

- Skip agents that aren't relevant (e.g., skip Orpheus for pure backend code, skip Argus if no infra changes)
- Always include Jason (architecture) and Atalanta (testing) - they review cross-cutting concerns
- Lynceus (security) should review any auth, input validation, or data handling code
- Consolidate duplicate findings across agents
- Prioritize by severity, not by agent order
