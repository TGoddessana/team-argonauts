---
name: review
description: Run a comprehensive production-ready review with the entire Argonauts team (8 senior engineers)
argument-hint: "[file or directory path] (optional - defaults to recent changes)"
allowed-tools: ["Read", "Grep", "Glob", "Task"]
---

# Team Argonauts Full Review

Execute a comprehensive code review using all 8 Argonauts team members. Each expert reviews from their domain perspective, providing FAANG-level production-ready feedback.

## The Team

| Agent | Role | Focus |
|-------|------|-------|
| Jason | Tech Lead | Architecture, trade-offs, design direction |
| Heracles | Backend | API design, scalability, fault tolerance |
| Hephaestus | DevOps/SRE | Deployment, monitoring, infrastructure |
| Asclepius | QA | Test coverage, edge cases, quality |
| Orpheus | Frontend | UX, performance, accessibility |
| Argus | Security | Vulnerabilities, auth, data protection |
| Athena | DBA | Query optimization, schema, indexing |
| Calliope | Tech Writer | Documentation, naming, clarity |

## Instructions

1. **Determine Review Scope**
   - If a path argument is provided, review that specific file or directory
   - If no argument, identify recently modified files (use `git diff` or `git status`)
   - If no git changes, ask the user what to review

2. **Execute Reviews in Order**
   For each relevant agent (skip agents not applicable to the code type):

   a. **Jason (Tech Lead)** - Always first, sets context
   b. **Argus (Security)** - Security issues are critical
   c. **Heracles (Backend)** - If backend/API code exists
   d. **Athena (DBA)** - If database/query code exists
   e. **Orpheus (Frontend)** - If frontend code exists
   f. **Hephaestus (DevOps)** - If infrastructure/config exists
   g. **Asclepius (QA)** - Test coverage for all code
   h. **Calliope (Tech Writer)** - Documentation last

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

## 👁️ Argus (Security)
[Argus's review output]

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

- Skip agents that aren't relevant (e.g., skip Orpheus for pure backend code)
- Always include Jason (architecture) and Asclepius (testing)
- Consolidate duplicate findings across agents
- Prioritize by severity, not by agent order
