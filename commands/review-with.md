---
name: review-with
description: Run a targeted review with specific Argonauts team members
argument-hint: "<agent-names> [path] (e.g., 'heracles,argus src/api' or 'jason')"
allowed-tools: ["Read", "Grep", "Glob", "Task"]
---

# Targeted Argonauts Review

Execute a focused code review with selected Argonauts team members. Use this when you need specific expertise rather than a full team review.

## Available Agents

| Name | Role | Best For |
|------|------|----------|
| `jason` | Tech Lead | Architecture decisions, design reviews, trade-off analysis |
| `heracles` | Backend | API design, scalability, fault tolerance, data access |
| `hephaestus` | DevOps/SRE | Deployment, CI/CD, monitoring, infrastructure |
| `asclepius` | QA | Test coverage, edge cases, quality standards |
| `orpheus` | Frontend | UX, performance, accessibility, state management |
| `argus` | Security | Vulnerabilities, auth, injection, data protection |
| `athena` | DBA | Query optimization, schema design, indexing |
| `calliope` | Tech Writer | Documentation, naming, code clarity |

## Instructions

1. **Parse Arguments**
   - Extract agent names (comma-separated or space-separated)
   - Extract optional file/directory path
   - Valid formats:
     - `heracles` - Single agent, review recent changes
     - `heracles,argus` - Multiple agents, recent changes
     - `heracles src/api` - Single agent, specific path
     - `jason,athena,heracles src/db` - Multiple agents, specific path

2. **Validate Agent Names**
   - If invalid name provided, list available agents and ask for correction
   - Accept partial matches (e.g., `hera` → `heracles`)

3. **Determine Review Scope**
   - If path provided, review that file/directory
   - If no path, identify recent changes or ask user

4. **Execute Reviews**
   - Invoke each selected agent using the Task tool
   - Provide full code context to each agent
   - Collect findings in P0-P4 format

5. **Compile Report**

## Output Format

```markdown
# 🎯 Targeted Argonauts Review

**Reviewers:** [Agent names and roles]
**Scope:** [Files/directories reviewed]

## Summary
| Severity | Count |
|----------|-------|
| P0 Critical | [n] |
| P1 High | [n] |
| P2 Medium | [n] |
| P3 Low | [n] |
| P4 Nitpick | [n] |

## Verdict: [SHIP | SHIP WITH NOTES | REVISE | BLOCK]

---

## [Agent Emoji] [Agent Name] ([Role])
[Agent's full review output]

---

[Repeat for each selected agent...]

---

## Consolidated Action Items

### Critical (P0-P1)
- [ ] [Item]

### Important (P2)
- [ ] [Item]

### Optional (P3-P4)
- [ ] [Item]
```

## Examples

**Quick security check:**
```
/team-argonauts:review-with argus src/auth
```

**Backend + Database review:**
```
/team-argonauts:review-with heracles,athena src/api/users.ts
```

**Architecture decision review:**
```
/team-argonauts:review-with jason
```

**Full backend stack:**
```
/team-argonauts:review-with jason,heracles,athena,argus,asclepius src/
```
