---
name: smart-review
description: Intelligent review that automatically finds relevant files and selects appropriate reviewers based on natural language input
argument-hint: "<natural language description> (e.g., 'backend only', 'security critical files', 'frontend components')"
allowed-tools: ["Read", "Grep", "Glob", "Task", "AskUserQuestion"]
---

# Smart Review with Auto-Discovery

Execute an intelligent code review that automatically:
1. Translates natural language to exploration query
2. Uses Explore agent to discover relevant files
3. Analyzes discovered files to select appropriate reviewers
4. Executes targeted review with selected Argonauts

## How It Works

### Phase 1: Intent to Query Translation

Translate user's natural language input into an Explore agent query:

| User Input | Explore Query |
|------------|---------------|
| "백엔드만", "backend only" | "Find all backend API, service, and controller files" |
| "보안 관련", "security files" | "Find authentication, authorization, and security-related files" |
| "프론트엔드 컴포넌트", "frontend" | "Find React/Vue components, pages, and hooks" |
| "배포 설정", "infra", "deploy" | "Find Docker, Kubernetes, CI/CD, and deployment configuration files" |
| "데이터베이스", "database", "db" | "Find database models, repositories, migrations, and SQL files" |
| "테스트", "tests" | "Find test files and test utilities" |

### Phase 2: Explore Agent Discovery

Use the **Explore** agent (subagent_type=Explore) to intelligently find files:

```markdown
Task: Explore Agent
Prompt: "Find all [domain] files in this codebase.
        Look for typical patterns and naming conventions for [domain].
        Include related configuration and implementation files.
        Exclude node_modules, dist, build directories."
Thoroughness: "medium"
```

**Why Explore Agent?**
- ✅ Adapts to project structure automatically
- ✅ Understands context and naming conventions
- ✅ No hardcoded patterns needed
- ✅ Handles edge cases and non-standard structures
- ✅ Already optimized for codebase exploration

**Example Explore Outputs:**
```
Query: "Find all backend API files"
Result:
  - src/api/users.ts
  - src/api/auth.ts
  - src/controllers/payment.controller.ts
  - src/services/billing.service.ts
  - routes/api.js

Query: "Find security-related files"
Result:
  - src/auth/jwt.ts
  - src/middleware/auth.middleware.ts
  - lib/security/encryption.ts
  - config/security.yml
```

### Phase 3: File Analysis & Agent Selection

Analyze the files returned by Explore agent to determine appropriate reviewers:

**Analysis Method:**
1. **File Path Analysis** - Check directories and file extensions
2. **File Name Analysis** - Look for keywords (auth, test, service, component)
3. **Content Sampling** (if needed) - Quick scan for imports/keywords

**Agent Selection Rules:**

| Detected Characteristics | Auto-Select Agents | Rationale |
|--------------------------|-------------------|-----------|
| API/Service/Controller files | Heracles, Lynceus, Atalanta | Backend + Security + Tests |
| Component/Hook/Page files | Orpheus, Calliope, Atalanta | Frontend + Docs + Tests |
| Auth/Security keywords | Lynceus, Heracles, Jason | Security + Backend + Architecture |
| Docker/K8s/CI files | Argus, Lynceus | DevOps + Security |
| Model/Repository/Migration | Heracles, Lynceus | Data access + Injection prevention |
| Test files | Atalanta, Jason | QA + Architecture validation |
| Markdown/Documentation | Calliope | Tech writing |
| Large architectural scope (>10 files) | Jason | Lead architect oversight |

**Smart Defaults:**
- If >10 files of any type → Always add **Jason** (architecture oversight)
- If ANY auth/security files → Always add **Lynceus** (security critical)
- If ANY production code → Always add **Atalanta** (test coverage)

### Phase 4: Review Execution

Execute reviews with selected agents in parallel:
- Each agent receives the **full file list** from Explore
- Agents focus on their domain expertise
- Parallel execution for speed

## Examples

### Example 1: Backend Only
```
User: "백엔드 디렉토리 관련만 찾아서 리뷰해줘"

[Phase 1] Translation
  Intent: Backend code review
  Explore Query: "Find all backend API, service, and controller files"

[Phase 2] Explore Agent
  Task: Explore (medium thoroughness)
  Prompt: "Find all backend API, service, and controller files.
           Look for typical backend patterns and naming conventions.
           Exclude test files, node_modules, and build artifacts."

  Result:
    - src/api/users.ts
    - src/api/auth.ts
    - src/api/payment.ts
    - src/services/email.service.ts
    - src/services/notification.service.ts
    - src/controllers/admin.controller.ts
    - routes/api.js
    (23 files total)

[Phase 3] Agent Selection
  Analysis:
    - API files: 3
    - Service files: 2
    - Controller files: 1
    - Auth detected: YES (auth.ts, payment.ts need security review)

  Selected:
    - Heracles (Backend) - PRIMARY
    - Lynceus (Security) - Auth/payment files
    - Atalanta (QA) - Test coverage validation
    - Jason (Architect) - >10 files, needs oversight

[Phase 4] Review Execution
  → 4 agents run in parallel
  → Full context: all 23 files
  → P0-P4 consolidated report
```

### Example 2: Security Critical Files
```
User: "보안 관련 코드만 체크해줘"

[Phase 1] Translation
  Intent: Security review
  Explore Query: "Find authentication, authorization, and security-related files"

[Phase 2] Explore Agent
  Task: Explore (very thorough)
  Prompt: "Find all authentication, authorization, and security-related files.
           Include password handling, token management, encryption code.
           Look for .env files, secrets, API keys."

  Result:
    - src/auth/jwt.ts
    - src/auth/password-hash.ts
    - src/middleware/auth.middleware.ts
    - lib/security/encryption.ts
    - config/security.yml
    - .env.example
    (6 files total)

[Phase 3] Agent Selection
  Analysis:
    - Security keywords: auth, password, encryption, jwt
    - Config files: YES (security.yml)
    - Secrets check needed: YES (.env.example)

  Selected:
    - Lynceus (Security) - PRIMARY
    - Heracles (Backend) - Implementation review
    - Jason (Architect) - Security architecture design

[Phase 4] Review Execution
  → 3 agents run in parallel
  → Focus: OWASP Top 10, threat modeling
  → Verdict: SECURE/CONDITIONAL/INSECURE
```

### Example 3: Frontend Components
```
User: "프론트엔드 컴포넌트만 리뷰"

[Phase 1] Translation
  Intent: Frontend review
  Explore Query: "Find React/Vue components, hooks, and page files"

[Phase 2] Explore Agent
  Task: Explore (medium thoroughness)
  Prompt: "Find all React/Vue components, custom hooks, and page files.
           Include state management and routing files."

  Result:
    - src/components/Button.tsx
    - src/components/Modal.tsx
    - src/components/Form.tsx
    - src/hooks/useAuth.ts
    - src/hooks/useForm.ts
    - src/pages/Dashboard.tsx
    - src/pages/Login.tsx
    - src/store/user.ts
    (18 files total)

[Phase 3] Agent Selection
  Analysis:
    - Components: 3
    - Hooks: 2
    - Pages: 2
    - State management: 1
    - Auth hook detected: useAuth.ts

  Selected:
    - Orpheus (Frontend) - PRIMARY
    - Calliope (Docs) - Component naming/props
    - Atalanta (QA) - Frontend test coverage
    - Jason (Architect) - >10 files, state architecture

[Phase 4] Review Execution
  → 4 agents run in parallel
  → Focus: UX, accessibility, performance
  → Check: a11y, loading states, error handling
```

## Instructions

### Step 1: Translate Intent to Explore Query

Parse user's natural language and create an Explore agent query:

```python
# Translation examples
user_input_keywords = {
  "백엔드", "backend", "api", "서비스":
    "Find all backend API, service, and controller files",

  "보안", "security", "auth", "인증":
    "Find authentication, authorization, and security-related files",

  "프론트엔드", "frontend", "ui", "컴포넌트", "component":
    "Find React/Vue components, hooks, and page files",

  "배포", "deploy", "infra", "인프라":
    "Find Docker, Kubernetes, CI/CD, and deployment configuration",

  "데이터베이스", "database", "db":
    "Find database models, repositories, migrations, and SQL files",

  "테스트", "test":
    "Find test files and test utilities"
}
```

**Add context to query:**
- "Exclude node_modules, dist, build, and test files (unless specifically searching for tests)"
- "Look for common naming conventions and patterns"
- "Include related configuration files"

### Step 2: Invoke Explore Agent

Use Task tool with subagent_type=Explore:

```markdown
Task Parameters:
- subagent_type: "Explore"
- description: "[domain] file discovery"
- prompt: "[Translated query from Step 1]"
- model: "haiku" (fast, cost-effective for discovery)

Thoroughness guidance:
- Quick exploration (1-5 expected files): thoroughness="quick"
- Medium exploration (5-30 expected files): thoroughness="medium"
- Comprehensive search: thoroughness="very thorough"
```

**Capture Explore Output:**
- Store list of discovered files
- Note any patterns or insights from Explore agent
- If Explore finds 0 files, ask user for clarification

### Step 3: Analyze Files & Select Agents

From Explore results, determine which agents to invoke:

**Analysis steps:**
1. Count files by apparent type (based on path/extension)
2. Check for security-critical keywords in filenames
3. Determine scope size (small <5, medium 5-20, large >20)

**Selection logic:**
```python
selected_agents = []

# File type analysis
if has_backend_files:
    selected_agents.append("heracles")
if has_frontend_files:
    selected_agents.append("orpheus")
if has_infra_files:
    selected_agents.append("argus")

# Security check (high priority)
if has_auth_files or has_security_keywords:
    selected_agents.append("lynceus")

# Cross-cutting concerns
if total_files > 10:
    selected_agents.append("jason")  # Architecture oversight
if has_production_code:
    selected_agents.append("atalanta")  # Test coverage

# Documentation check
if has_docs or has_many_exports:
    selected_agents.append("calliope")
```

### Step 4: Confirm Scope (If Large)

If discovered files >30 or scope is ambiguous:

Use AskUserQuestion to show:
- Summary of discovered files
- Selected reviewers
- Estimated review scope

Ask: "Proceed with this scope?" or offer to narrow down

### Step 5: Execute Parallel Reviews

For each selected agent:

```markdown
Task Parameters:
- subagent_type: [agent-name]
- description: "[domain] review of [N] files"
- prompt: "Review the following files from your domain expertise:
          [File list from Explore]

          Provide P0-P4 findings with specific recommendations."
```

**Execute all agent reviews in parallel** using multiple Task tool calls in same message

### Step 6: Compile Consolidated Report

Collect all agent outputs and generate unified report

## Output Format

```markdown
# 🧠 Smart Review Report

**Intent Recognized:** [Parsed intent]
**Files Discovered:** [count] files across [domains]
**Reviewers Auto-Selected:** [Agent names]

## Discovery Summary
| Pattern | Files Found |
|---------|-------------|
| Backend API | 12 files |
| Services | 8 files |
| Tests | 15 files |

**Review Scope:**
- src/api/users.ts
- src/api/auth.ts
- src/services/payment.ts
- ... (showing top 10, [N] more)

---

## 💪 Heracles (Backend Engineer)
[Review output]

---

## 👁️ Lynceus (Security Engineer)
[Review output]

---

## 🏹 Atalanta (QA Engineer)
[Review output]

---

## Overall Verdict: [SHIP | SHIP WITH NOTES | REVISE | BLOCK]

### Action Items
[Consolidated P0-P4 findings]
```

## Why Explore Agent?

**Traditional approach problems:**
- ❌ Hardcoded patterns break on non-standard projects
- ❌ Maintenance burden for every new framework
- ❌ Can't handle creative project structures
- ❌ Misses files that don't match expected patterns

**Explore agent benefits:**
- ✅ Adapts to any project structure automatically
- ✅ Understands semantic intent ("backend" vs "src/api")
- ✅ Already optimized for codebase exploration
- ✅ No pattern maintenance needed
- ✅ Handles edge cases intelligently
- ✅ Fast with thoroughness control

## Error Handling

| Situation | Action |
|-----------|--------|
| **Explore finds 0 files** | Ask user to clarify intent or provide specific path |
| **Ambiguous results** | Show discovered files summary, ask for confirmation |
| **Too many files (>50)** | Suggest narrowing scope or auto-prioritize recent changes |
| **No clear agent match** | Default to Jason (general architecture review) |
| **Explore agent fails** | Fall back to asking user for specific file paths |

## When to Use Smart Review

- ✅ "Just check the backend stuff"
- ✅ "Review security-critical code"
- ✅ "Look at frontend components only"
- ✅ "Check the deployment config"
- ✅ "Review database changes"

## When NOT to Use (Use Regular Review Instead)

- ❌ Specific file path already known: `/team-argonauts:review src/api/users.ts`
- ❌ Specific agents already chosen: `/team-argonauts:review-with heracles src/`
- ❌ Recent changes review: `/team-argonauts:review` (defaults to git diff)
