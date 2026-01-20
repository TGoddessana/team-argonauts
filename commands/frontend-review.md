---
name: frontend-review
description: Quick frontend review with Orpheus (Frontend Engineer)
argument-hint: "[file or directory path] (optional - defaults to recent changes)"
allowed-tools: ["Read", "Grep", "Glob", "Task"]
---

# Frontend Review with Orpheus

Execute a focused frontend review with Orpheus, the Frontend Engineer. Specialized in UX, performance, accessibility, and production-ready frontend applications.

## What Orpheus Reviews

- **Performance:** Bundle size, code splitting, render optimization, Core Web Vitals
- **Accessibility:** WCAG 2.1 AA compliance, keyboard navigation, screen readers
- **State Management:** Appropriate state location, avoiding prop drilling
- **User Experience:** Loading states, error states, empty states
- **Component Design:** Reusability, composability, proper patterns

## Instructions

1. **Determine Review Scope**
   - If a path argument is provided, review that specific file or directory
   - If no argument, identify recently modified files (use `git diff` or `git status`)
   - If no git changes, ask the user what to review

2. **Execute Frontend Review**
   - Use the Task tool to invoke the `orpheus` agent
   - Provide full code context for frontend analysis
   - Collect findings in P0-P4 format with user impact

3. **Compile Frontend Report**

## Output Format

```markdown
# 🎵 Orpheus' Frontend Review

**Scope:** [Files/directories reviewed]
**Date:** [Current date]

## Summary
[2-3 sentences: Overall health and top concern]

## Findings

### P[0-4]: [Issue Title]
**Category:** Performance | Accessibility | State | UX
**Location:** `file:line`
**Issue:** [What's wrong, user impact]
**Fix:**
```[language]
// Concrete fix
```

## Accessibility Status
- Keyboard: ✅/❌ [notes]
- Screen reader: ✅/❌ [notes]
- Color contrast: ✅/❌ [notes]

## Performance Concerns
| Issue | User Impact | Fix |
|-------|-------------|-----|
| [Issue] | [Impact] | [Solution] |

## Verdict: [SHIP | SHIP WITH NOTES | REVISE | BLOCK]
```

## Common Issues Orpheus Catches

- Missing loading states
- Missing error states
- Accessibility violations (no keyboard nav, poor contrast)
- Unnecessary re-renders
- Missing memo/useCallback where needed
- Bundle bloat from large dependencies
- Memory leaks (uncleaned subscriptions)
- No lazy loading for heavy components
- Missing focus management
- Poor mobile responsiveness

## Examples

**Review React components:**
```
/team-argonauts:frontend-review src/components
```

**Review entire frontend:**
```
/team-argonauts:frontend-review src/
```

**Review recent changes:**
```
/team-argonauts:frontend-review
```

## When to Use

- Before deploying new UI features
- When performance is a concern
- After major component refactoring
- When accessibility compliance is required
- Before user-facing releases
- When UX quality matters
