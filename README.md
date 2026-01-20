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

### Smart Review

Simply describe what you want reviewed in natural language:

```bash
# Backend review
/team-argonauts:smart-review "백엔드만"
/team-argonauts:smart-review "backend API code"

# Security review
/team-argonauts:smart-review "보안 관련 파일"
/team-argonauts:smart-review "authentication and security"

# Frontend review
/team-argonauts:smart-review "프론트엔드 컴포넌트"
/team-argonauts:smart-review "React components"

# Infrastructure review
/team-argonauts:smart-review "배포 설정"
/team-argonauts:smart-review "deployment configs"

# Database review
/team-argonauts:smart-review "데이터베이스 코드"
/team-argonauts:smart-review "database and migrations"

# Combined reviews
/team-argonauts:smart-review "backend security"
/team-argonauts:smart-review "인증 관련 백엔드 코드만"

# Full review
/team-argonauts:smart-review "전체 프로젝트"
/team-argonauts:smart-review "everything"
```

### How It Works

1. **🔍 Intelligent Discovery**
   - Translates your natural language input to an exploration query
   - Uses Explore agent to find relevant files (no hardcoded patterns!)
   - Adapts to any project structure automatically

2. **🧠 Smart Agent Selection**
   - Analyzes discovered files (path, extension, content)
   - Automatically selects appropriate team members
   - Combines multiple experts when needed

3. **👥 Expert Review**
   - Runs selected agents in parallel for speed
   - Each expert reviews from their domain perspective
   - P0-P4 severity classification

4. **✅ Consolidated Report**
   - Unified findings across all reviewers
   - Prioritized action items
   - Clear verdict: SHIP / SHIP WITH NOTES / REVISE / BLOCK

### Why Smart Review?

**Traditional approach (deleted):**
- ❌ `/backend-review src/api` - Need to know exact paths
- ❌ `/review-with heracles,lynceus` - Need to know which agents
- ❌ Multiple commands to learn

**Smart Review:**
- ✅ Natural language: "backend security files"
- ✅ Auto-discovery: finds files you didn't know existed
- ✅ Auto-selection: picks the right experts
- ✅ One command to rule them all

### Natural Language

Examples:

```bash
# Simple domain-based
/team-argonauts:smart-review "backend"
/team-argonauts:smart-review "frontend"
/team-argonauts:smart-review "security"

# Specific focus
/team-argonauts:smart-review "API endpoints"
/team-argonauts:smart-review "database queries"
/team-argonauts:smart-review "React hooks"

# Combined concerns
/team-argonauts:smart-review "payment processing backend"
/team-argonauts:smart-review "user authentication flow"
/team-argonauts:smart-review "CI/CD and deployment"
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
# 🧠 Smart Review Report

**Intent Recognized:** Backend security review
**Files Discovered:** 8 files across backend + security domains
**Reviewers Auto-Selected:** Heracles, Lynceus, Atalanta

## Discovery Summary
| Pattern | Files Found |
|---------|-------------|
| Backend API | 4 files |
| Security | 2 files |
| Tests | 2 files |

**Review Scope:**
- src/api/users.ts
- src/api/auth.ts
- src/middleware/auth.middleware.ts
- src/services/user.service.ts
- tests/api/auth.test.ts
- (3 more...)

---

## 💪 Heracles (Backend Engineer)

### P1: Missing Timeout on External API
**Location:** `src/services/user.service.ts:23`
**Issue:** External call to email service has no timeout
**Recommendation:** Add 5s timeout with proper error handling
...

---

## 👁️ Lynceus (Security Engineer)

### P0: SQL Injection Vulnerability
**Location:** `src/api/users.ts:45`
**Issue:** User input directly concatenated into SQL query
**Recommendation:** Use parameterized queries
...

---

## 🏹 Atalanta (QA Engineer)

### P2: Missing Test Coverage
**Location:** `src/api/auth.ts`
**Issue:** No tests for password reset flow
**Recommendation:** Add integration tests for critical auth paths
...

---

## Overall Verdict: REVISE

### Action Items
**Must Fix (P0-P1):**
- [ ] Fix SQL injection in users.ts:45
- [ ] Add timeout to email service call

**Should Fix (P2):**
- [ ] Add password reset tests
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
