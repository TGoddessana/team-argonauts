# Team Argonauts

> Production-ready code review by FAANG-level senior engineers.

Claude Code plugin with 7 specialized AI agents that review your code from architecture, security, performance, testing, and operational perspectives.

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

```bash
# Add marketplace
claude plugin marketplace add TGoddessana/team-argonauts

# Install plugin
claude plugin install team-argonauts@team-argonauts
```

## Updates

When new versions are released:

```bash
# Update marketplace index
claude plugin marketplace update team-argonauts

# Update plugin
claude plugin update team-argonauts@team-argonauts

# Restart Claude Code to apply changes
```

## Features

### 🔍 Smart Review

Natural language code review with automatic file discovery and expert selection:

```bash
/team-argonauts:smart-review "backend security"
/team-argonauts:smart-review "React components"
/team-argonauts:smart-review "database and API"
```

Output: Detailed review report with P0-P4 severity issues.

### 📋 Smart Plan

Split review reports into actionable plan files:

```bash
/team-argonauts:smart-plan BACKEND_REVIEW_2026-01-20.md
```

Output: Individual plan files in `plans/` directory, one per issue.

### 🚀 Smart Workflow

End-to-end automated workflow from review to implementation:

```bash
/team-argonauts:smart-workflow "backend security"
```

Automatically runs:
1. Smart Review → generates report
2. Smart Plan → creates plan files
3. Shows summary → asks which issues to fix
4. Sequential execution → implements fixes one by one

## Quick Start

1. Review your code:
   ```bash
   /team-argonauts:smart-review "backend security"
   ```

2. Generate action plans:
   ```bash
   /team-argonauts:smart-plan BACKEND_REVIEW_2026-01-20.md
   ```

3. Or run everything automatically:
   ```bash
   /team-argonauts:smart-workflow "backend security"
   ```

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

## Example Output

Each review includes:
- **Severity levels** (P0-P4): Prioritized issues from critical to nitpicks
- **Expert analysis**: Multiple agents review from their domain perspective
- **Actionable fixes**: Specific code recommendations with line numbers
- **Overall verdict**: SHIP / SHIP WITH NOTES / REVISE / BLOCK

Example finding:
```markdown
## 👁️ Lynceus (Security Engineer)

### P0: SQL Injection Vulnerability
**Location:** `src/api/users.ts:45`
**Issue:** User input directly concatenated into SQL query
**Fix:** Use parameterized queries or ORM
```

## Troubleshooting

**Commands not showing up?**
```bash
claude plugin list  # Verify installation
claude plugin enable team-argonauts  # Enable if disabled
```

**Correct command format:**
- ✅ `/team-argonauts:smart-review "backend"`
- ❌ `/team-argonauts/smart-review` (wrong separator)

## Contributing

Contributions welcome! See [CONTRIBUTING.md](CONTRIBUTING.md) for details.

## License

MIT License - see [LICENSE](LICENSE) for details.
