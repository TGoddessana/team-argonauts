---
name: smart-review
description: Auto-discover files and select reviewers via natural language
argument-hint: "<scope> (e.g., 'backend security', 'frontend components')"
allowed-tools: ["Task", "AskUserQuestion"]
---

# Smart Review

Automatically discovers relevant files and selects expert reviewers based on natural language input.

## Quick Example

```
User: "backend security"
  ↓
Explore: Finds 23 backend + auth files
  ↓
Auto-select: Heracles, Lynceus, Atalanta, Jason
  ↓
Parallel review: Consolidated P0-P4 report
```

## Instructions

### Step 1: Translate Intent to Explore Query

Map user's natural language to an Explore agent query:

| User Input | Explore Query |
|------------|---------------|
| "backend", "api", "service" | "Find all backend API, service, and controller files" |
| "security", "auth" | "Find authentication, authorization, and security-related files" |
| "frontend", "ui", "component" | "Find React/Vue components, hooks, and page files" |
| "infra", "deploy" | "Find Docker, Kubernetes, CI/CD, and deployment configuration" |
| "database", "db" | "Find database models, repositories, migrations, and SQL files" |
| "test" | "Find test files and test utilities" |

**Add to all queries:**
- "Exclude node_modules, dist, build directories"
- "Look for common naming conventions and patterns"

### Step 2: Launch Explore Agent

```markdown
Task:
  subagent_type: "Explore"
  description: "[domain] file discovery"
  prompt: "[Translated query from Step 1]"
  model: "haiku"

Thoroughness:
  - <5 expected files: "quick"
  - 5-30 expected files: "medium"
  - >30 expected files: "very thorough"
```

**Capture output:** Store list of discovered files

### Step 3: Analyze Files & Select Agents

From discovered files, determine which agents to invoke based on their expertise:

**Agent expertise reference:**

| Agent | Expertise | Trigger Keywords |
|-------|-----------|------------------|
| **Heracles** | Backend, API, data modeling, scalability | api, service, controller, model, repository, migration, query, database |
| **Orpheus** | Frontend, UI, components, accessibility | component, hook, page, react, vue, angular, tsx, jsx, ui, frontend |
| **Argus** | DevOps, CI/CD, infrastructure, monitoring | docker, kubernetes, k8s, ci, cd, pipeline, deploy, infra, yaml, terraform |
| **Lynceus** | Security, auth, vulnerabilities, OWASP | auth, security, password, token, jwt, crypto, permission, session, secret |
| **Atalanta** | QA, testing, edge cases, quality gates | test, spec, e2e, integration, mock, fixture, coverage |
| **Jason** | Architecture, system design, strategy | Large scope (>10 files), cross-cutting concerns, architectural decisions |
| **Calliope** | Documentation, clarity, API docs | README, docs, comments, api-docs, markdown, documentation |

**Selection logic:**

```python
selected_agents = []

# Primary domain selection (based on file analysis)
if has_backend_files():        → heracles
if has_frontend_files():       → orpheus
if has_infra_files():          → argus
if has_test_files():           → atalanta
if has_documentation():        → calliope

# Security is ALWAYS checked if relevant
if has_security_keywords():    → lynceus (high priority)
if has_auth_files():           → lynceus (high priority)

# Cross-cutting concerns
if total_files > 10:           → jason (architecture oversight)
if has_production_code():      → atalanta (test coverage check)

# Default fallback
if len(selected_agents) == 0:  → jason (general review)
```

**Common file patterns → Agent selection:**

| File Pattern | Selected Agents | Rationale |
|--------------|-----------------|-----------|
| `src/api/`, `services/`, `controllers/` | Heracles, Lynceus, Atalanta | Backend code needs scalability + security + test review |
| `components/`, `pages/`, `hooks/` | Orpheus, Atalanta, Calliope | Frontend needs UX + tests + clarity |
| `auth/`, `security/`, `middleware/auth` | Lynceus, Heracles, Jason | Security-critical code needs threat modeling + implementation + architecture |
| `Dockerfile`, `k8s/`, `.github/workflows/` | Argus, Lynceus | Infrastructure needs ops expertise + security hardening |
| `*.test.ts`, `*.spec.js`, `__tests__/` | Atalanta, Jason | Tests need QA review + coverage strategy |
| `README.md`, `docs/`, API comments | Calliope | Documentation needs clarity expert |
| Large codebase (>10 files, mixed domains) | Jason + domain experts | Architecture oversight needed |

### Step 4: Confirm Scope (If Large)

If discovered files >30:

```markdown
AskUserQuestion:
  Show:
    - Discovered files summary
    - Selected reviewers
    - Estimated scope

  Ask: "Proceed with this scope?"
  Options:
    - "Yes, proceed"
    - "Narrow down scope"
```

### Step 5: Execute Parallel Reviews

Launch all selected agents in parallel (multiple Task calls in one message):

```markdown
For each selected agent:

Task:
  subagent_type: [agent-name]
  description: "[domain] review of [N] files"
  prompt: "Review the following files from your domain expertise:

          [File list from Explore]

          Provide P0-P4 findings with:
          - Specific file locations
          - Impact assessment
          - Concrete recommendations"
```

### Step 6: Compile Consolidated Report

Collect all agent outputs and generate:

```markdown
# 🧠 Smart Review Report

**Intent Recognized:** [User's parsed intent]
**Files Discovered:** [count] files
**Reviewers Selected:** [Agent names]

## Discovery Summary
| Category | Files |
|----------|-------|
| Backend API | X |
| Services | Y |
| ... | ... |

**Review Scope:**
- file1.ts
- file2.py
... (top 10, then "X more")

---

## 💪 Heracles (Backend)
[Findings]

---

## 👁️ Lynceus (Security)
[Findings]

---

## Overall Verdict: [SHIP | SHIP WITH NOTES | REVISE | BLOCK]

### Action Items
[Consolidated P0-P4 findings]
```

## Why Explore Agent?

- ✅ Adapts to any project structure automatically
- ✅ No hardcoded patterns to maintain
- ✅ Handles non-standard naming intelligently
- ✅ Fast with thoroughness control

## Error Handling

| Situation | Action |
|-----------|--------|
| 0 files found | Ask user to clarify or provide specific path |
| >50 files | Suggest narrowing scope or prioritize recent changes |
| No agent match | Default to Jason (general review) |
| Explore fails | Ask user for specific file paths |
