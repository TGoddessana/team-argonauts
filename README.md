# Team Argonauts

> FAANG-level senior engineers reviewing your code for production readiness.

**Team Argonauts** is a Claude Code plugin that provides expert code review from 7 specialized AI agents, each embodying a senior engineer with deep domain expertise. Like the mythical Argonauts who sailed with Jason, this team navigates the complexities of production-ready code.

## The Team

| Agent | Role | Expertise |
|-------|------|-----------|
| 🏛️ **Jason** | Tech Lead / Architect | Architecture decisions, orchestration, trade-off analysis |
| 💪 **Heracles** | Backend Engineer | API design, scalability, fault tolerance, data modeling |
| 🎵 **Orpheus** | Frontend Engineer | UX, performance, accessibility, state management |
| ⚓ **Argus** | DevOps / SRE | Deployment, CI/CD, monitoring, infrastructure |
| 👁️ **Lynceus** | Security Engineer | Vulnerabilities, auth, threat modeling, data protection |
| 🏹 **Atalanta** | QA Engineer | Test coverage, edge cases, quality standards, bug hunting |
| 📝 **Calliope** | Technical Writer | Documentation, naming, code clarity, API docs |

## Philosophy

> *"We've seen enough production incidents to know: code that 'works on my machine' is not code that survives Black Friday traffic, a datacenter failover, or a determined attacker."*

The Argonauts review with:
- **Paranoid but Pragmatic** — Assume failure, but ship with mitigations
- **Explicit Trade-offs** — Every choice sacrifices something; document what
- **Scale Thinking** — "Will this work at 10x?" is always relevant
- **Defense in Depth** — Never rely on a single control
- **Observable by Default** — If you can't measure it, you can't fix it

## Installation

### From Marketplace (Recommended)

```bash
# Add the marketplace
/plugin marketplace add TGoddessana/team-argonauts

# Install the plugin
/plugin install team-argonauts@team-argonauts

# Verify installation
/plugin list
```

### Alternative: Direct Git URL

```bash
/plugin marketplace add https://github.com/TGoddessana/team-argonauts.git
/plugin install team-argonauts@team-argonauts
```

## Usage

### Full Team Review

Run a comprehensive review with all relevant team members:

```
/team-argonauts:review
/team-argonauts:review src/api
```

### Targeted Review

Select specific experts for focused review:

```
/team-argonauts:review-with lynceus              # Security-focused review
/team-argonauts:review-with heracles,lynceus     # Backend + Security review
/team-argonauts:review-with jason src/           # Architecture review of src/
/team-argonauts:review-with orpheus src/components # Frontend review
```

### Quick Access Commands

Direct access to specific reviewers:

```
/team-argonauts:security-review      # Quick security check with Lynceus
/team-argonauts:backend-review       # Backend review with Heracles
/team-argonauts:frontend-review      # Frontend review with Orpheus
/team-argonauts:architecture-review  # Architecture review with Jason
```

### Smart Review (Recommended)

**NEW:** Let AI automatically find relevant files and select reviewers:

```
/team-argonauts:smart-review "backend only"
/team-argonauts:smart-review "security critical files"
/team-argonauts:smart-review "frontend components"
/team-argonauts:smart-review "deployment configs"
```

**How it works:**
1. 🔍 Uses Explore agent to intelligently find files matching your description
2. 🧠 Analyzes discovered files to determine appropriate reviewers
3. 👥 Automatically selects expert team members
4. ✅ Executes parallel review and generates consolidated report

**Benefits:**
- No need to specify exact file paths
- No need to manually choose reviewers
- Adapts to any project structure
- Works with natural language

### Natural Language

The agents also trigger naturally in conversation:

- "Is this API design scalable?" → Heracles
- "Review this authentication flow" → Lynceus
- "What edge cases am I missing?" → Atalanta
- "Is this component accessible?" → Orpheus

## Severity Levels (P0-P4)

| Level | Name | Meaning |
|-------|------|---------|
| **P0** | Critical | Will cause outage, data loss, or security breach. BLOCK. |
| **P1** | High | Significant issues. Fix before major milestone. |
| **P2** | Medium | Suboptimal but manageable. Schedule fix. |
| **P3** | Low | Minor improvements. Fix when touching code. |
| **P4** | Nitpick | Style preference. Take or leave. |

## Verdicts

| Verdict | Meaning |
|---------|---------|
| **SHIP** | Ready for production |
| **SHIP WITH NOTES** | Ready, minor issues tracked |
| **REVISE** | Address P1 issues, re-review |
| **BLOCK** | Fix P0 issues immediately |

## Configuration (Optional)

Create `.claude/team-argonauts.local.md` in your project:

```yaml
---
auto_review: suggest  # 'suggest', 'auto', or 'off'
default_reviewers: [jason, heracles, argus]
block_threshold: P0
focus_areas:
  - security
  - scalability
---

# Project Context

Any project-specific notes for reviewers...
```

## Example Output

```markdown
# 🚀 Team Argonauts Review Report

**Scope:** src/api/users.ts
**Date:** 2024-01-15

## Summary
- **Critical (P0):** 1
- **High (P1):** 2
- **Medium (P2):** 3

## Overall Verdict: REVISE

---

## 👁️ Lynceus (Security Engineer)

### P0: SQL Injection Vulnerability
**Location:** `src/api/users.ts:45`
**Issue:** User input directly concatenated into SQL query
**Recommendation:** Use parameterized queries
...
```

## Troubleshooting

### Commands not showing up?

1. Verify plugin is installed and enabled:
   ```bash
   /plugin list
   ```

2. Check if plugin is enabled:
   ```bash
   /plugin enable team-argonauts
   ```

3. Try the full command format with colon:
   ```bash
   /team-argonauts:review
   ```
   Note: Use `:` (colon), not `/` (slash) between plugin name and command!

4. Restart Claude Code if needed

### Getting "Command not found"?

Make sure you're using the correct format:
- ✅ Correct: `/team-argonauts:review`
- ❌ Wrong: `/team-argonauts/review`
- ❌ Wrong: `/team-argonauts review`

## Contributing

Contributions welcome! Each agent's prompt is in `agents/[name].md`.

## License

MIT
