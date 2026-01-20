---
name: architecture-review
description: Quick architecture review with Jason (Tech Lead/Architect)
argument-hint: "[file or directory path] (optional - defaults to recent changes)"
allowed-tools: ["Read", "Grep", "Glob", "Task"]
---

# Architecture Review with Jason

Execute a focused architecture review with Jason, the Tech Lead and Architect. Specialized in system design, trade-off analysis, and strategic technical decisions.

## What Jason Reviews

- **Architecture Decisions:** Technology choices, patterns, system boundaries
- **Design Quality:** Coupling, cohesion, abstraction levels
- **Scalability:** Growth trajectory, performance bottlenecks
- **Trade-offs:** Explicit analysis of choices and their costs
- **Team Fit:** Can the team build, operate, and maintain this?

## Instructions

1. **Determine Review Scope**
   - If a path argument is provided, review that specific file or directory
   - If no argument, identify recently modified files or ask for design docs
   - Architecture reviews often benefit from design documents

2. **Execute Architecture Review**
   - Use the Task tool to invoke the `jason` agent
   - Provide code context and any design documentation
   - Collect findings in P0-P4 format with architectural implications

3. **Compile Architecture Report**

## Output Format

```markdown
# 🏛️ Jason's Architecture Review

**Scope:** [Files/directories/design reviewed]
**Date:** [Current date]

## Summary
[2-3 sentences: Assessment and top concern]

## What's Strong
[Genuine positives in the design]

## Findings

### P[0-4]: [Architecture Issue]
**Category:** Architecture | Coupling | Scalability | Operability
**Issue:** [What's wrong, why it matters]
**Recommendation:** [Specific fix]
**Trade-off:** [What this costs]

## Hidden Assumptions
1. [Assumption] — [Risk if wrong]

## Scale Considerations
- Current scale: [X]
- This design works until: [Y]
- At 10x, you'll need: [Z]

## Team Handoffs
- **Heracles:** [Backend implementation needs]
- **Lynceus:** [Security considerations]
- **Argus:** [Infrastructure requirements]

## Verdict: [APPROVED | NEEDS REVISION | BLOCKED]
```

## Common Issues Jason Catches

- Over-engineering for current scale
- Under-engineering for known growth
- Technology choices mismatched to team skills
- Hidden coupling between components
- Unclear failure modes
- Missing observability strategy
- Premature optimization
- Premature abstraction
- Unclear service boundaries
- Missing rollback strategy

## Examples

**Review system design:**
```
/team-argonauts:architecture-review docs/design.md
```

**Review new service architecture:**
```
/team-argonauts:architecture-review src/services/new-feature
```

**Strategic technology decision:**
```
/team-argonauts:architecture-review
# Then describe the decision you're making
```

## When to Use

- Before starting major new features
- When choosing between technology options
- After initial implementation of new service
- When refactoring system architecture
- Before committing to one-way door decisions
- When you need a "sanity check" on design

## Jason's Key Questions

- "Are we solving the right problem?"
- "Will this work at 10x scale?"
- "Can the team operate this at 3 AM?"
- "What happens when this fails?"
- "Is this complexity earning its place?"
- "What are we assuming that might be wrong?"
