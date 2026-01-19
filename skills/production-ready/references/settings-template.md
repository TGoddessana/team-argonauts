# Team Argonauts Settings Template

Copy this file to `.claude/team-argonauts.local.md` in your project to customize behavior.

```markdown
---
# Team Argonauts Configuration

# Auto-review behavior after code changes
# Options: 'suggest' (default), 'auto', 'off'
#   - suggest: Shows tip to run /team-argonauts:review
#   - auto: Automatically runs quick review after significant changes
#   - off: No automatic suggestions
auto_review: suggest

# Default reviewers for /team-argonauts:review command
# Leave empty for full team review
default_reviewers: []
# Example: default_reviewers: [jason, heracles, argus]

# Severity threshold for blocking
# Findings at or above this level will result in BLOCK verdict
# Options: P0, P1, P2
block_threshold: P0

# Focus areas (optional)
# Emphasize certain aspects in reviews
focus_areas:
  - security
  - scalability
  # - performance
  # - accessibility
  # - testing
---

# Project-Specific Context

Add any project-specific information here that reviewers should know:

## Architecture Notes
- [Describe your architecture]

## Known Technical Debt
- [List known issues reviewers shouldn't flag]

## Standards Exceptions
- [Any standards that don't apply to this project]
```

## Usage

1. Create `.claude/` directory in your project root
2. Copy this template to `.claude/team-argonauts.local.md`
3. Customize settings as needed
4. The file is gitignored by default (personal preferences)

## Settings Reference

| Setting | Default | Description |
|---------|---------|-------------|
| `auto_review` | `suggest` | Controls automatic review suggestions |
| `default_reviewers` | `[]` (all) | Default agents for review command |
| `block_threshold` | `P0` | Severity that triggers BLOCK verdict |
| `focus_areas` | `[]` | Areas to emphasize in reviews |
