# Contributing to Team Argonauts

Thank you for your interest in contributing to Team Argonauts! This guide will help you add new agents, commands, skills, or hooks to the plugin.

## Table of Contents

- [Plugin Structure](#plugin-structure)
- [Adding a New Agent](#adding-a-new-agent)
- [Adding a New Command](#adding-a-new-command)
- [Adding a New Skill](#adding-a-new-skill)
- [Adding a New Hook](#adding-a-new-hook)
- [Development Workflow](#development-workflow)
- [Coding Standards](#coding-standards)

---

## Plugin Structure

```
team-argonauts/
├── .claude-plugin/
│   └── plugin.json          # Plugin metadata
├── agents/                  # Agent definitions (subagents)
│   ├── jason.md
│   ├── heracles.md
│   ├── orpheus.md
│   ├── argus.md
│   ├── lynceus.md
│   ├── atalanta.md
│   └── calliope.md
├── commands/                # Slash commands
│   ├── review.md
│   ├── review-with.md
│   ├── security-review.md
│   └── ...
├── skills/                  # Skills (knowledge modules)
│   └── production-ready/
│       ├── SKILL.md
│       └── references/
├── hooks/                   # Event-driven hooks
│   └── hooks.json
├── examples/                # Usage examples
├── README.md
└── CONTRIBUTING.md
```

---

## Adding a New Agent

Agents are specialized subagents that review code from their domain perspective.

### 1. Create Agent File

Create `agents/your-agent-name.md`:

```markdown
---
name: your-agent-name
description: |
  Brief description of when to use this agent.

  <example>
  Context: When to trigger
  user: "User question"
  assistant: "When this agent should be used"
  </example>

model: inherit
color: blue  # blue, green, red, yellow, magenta, cyan, orange
tools: ["Read", "Grep", "Glob", "Task"]
---

You are **AgentName**, [Role] of Team Argonauts.

[Agent personality and philosophy]

## Operation Modes

**IMPLEMENT:** [When to write code]
**REVIEW:** [When to review]
**DEBUG:** [When to debug]
**DECIDE:** [When to make decisions]

## Core Philosophy

1. [Principle 1]
2. [Principle 2]

## Review Output

```markdown
## [Emoji] AgentName's Review

### Summary
[2-3 sentences]

### Findings

#### P[0-4]: [Title]
**Category:** [Category]
**Location:** `file:line`
**Issue:** [What's wrong]
**Fix:**
```[language]
// Code fix
```

### Verdict: [SHIP | SHIP WITH NOTES | REVISE | BLOCK]
```

## Team Collaboration

| Concern | Hand off to |
|---------|-------------|
| [Area] | **AgentName** |
```

### 2. Agent Design Guidelines

- **Clear Domain:** One agent, one domain (backend, frontend, security, etc.)
- **Actionable Output:** Always provide concrete fixes, not just descriptions
- **Consistent Format:** Use P0-P4 severity levels
- **Trade-off Aware:** Acknowledge what each decision costs
- **Team Player:** Know when to hand off to other agents

### 3. Test Your Agent

```bash
# In a test project
claude

# Try triggering your agent
"Can [agent-name] review this code?"
```

---

## Adding a New Command

Commands are slash commands users can invoke directly.

### 1. Create Command File

Create `commands/your-command.md`:

```markdown
---
name: your-command
description: Short description of what this command does
argument-hint: "[required-arg] (optional-arg)"
allowed-tools: ["Read", "Grep", "Glob", "Task"]
---

# Your Command Name

Full description of what this command does and when to use it.

## Instructions

1. Parse arguments
2. Execute logic
3. Format output

## Output Format

```markdown
[Expected output structure]
```

## Examples

```
/team-argonauts:your-command arg1 arg2
```
```

### 2. Command Design Guidelines

- **Single Purpose:** One command, one job
- **Clear Arguments:** Use argument-hint to guide users
- **Helpful Errors:** If arguments are wrong, show examples
- **Consistent Output:** Follow team formatting conventions

---

## Adding a New Skill

Skills are knowledge modules that can be referenced during conversations.

### 1. Create Skill Directory

```bash
mkdir -p skills/your-skill-name/references
```

### 2. Create SKILL.md

Create `skills/your-skill-name/SKILL.md`:

```markdown
---
name: Skill Name
description: |
  Use this skill when [trigger conditions].

  Triggers: "keyword1", "keyword2", "phrase"
version: 1.0.0
---

# Skill Name

[Skill content - best practices, checklists, guidelines]

## When to Use

[Clear usage criteria]

## Key Concepts

[Core information]

## Checklists

- [ ] Item 1
- [ ] Item 2

## References

For detailed information, see files in `references/` directory.
```

### 3. Add Reference Materials

Create detailed guides in `skills/your-skill-name/references/`:

```
references/
├── detailed-guide.md
├── examples.md
└── checklists.md
```

---

## Adding a New Hook

Hooks execute automatically in response to events.

### 1. Edit hooks.json

Add to `hooks/hooks.json`:

```json
{
  "hooks": [
    {
      "matcher": "EventName",
      "hooks": [
        {
          "type": "prompt",
          "prompt": "Your hook logic here. Check conditions and take action if needed."
        }
      ]
    }
  ]
}
```

### 2. Available Events

- `Stop` - After user's task completes
- `PreToolUse` - Before a tool is used
- `PostToolUse` - After a tool is used
- `SessionStart` - When session begins
- `SessionEnd` - When session ends
- `UserPromptSubmit` - When user submits a message

### 3. Hook Design Guidelines

- **Non-intrusive:** Don't interrupt flow unnecessarily
- **Fast:** Hooks should execute quickly
- **Helpful:** Provide value, not noise
- **Contextual:** Check if action is needed before suggesting

---

## Development Workflow

### 1. Fork and Clone

```bash
git clone https://github.com/goddessana/team-argonauts.git
cd team-argonauts
```

### 2. Create Feature Branch

```bash
git checkout -b feature/your-feature-name
```

### 3. Make Changes

Follow the guidelines above for your contribution type.

### 4. Test Locally

```bash
# Link plugin to Claude Code
claude config --plugin-dir /path/to/team-argonauts

# Test in a sample project
cd ~/test-project
claude
```

### 5. Commit with Conventional Commits

```bash
git commit -m "feat(agents): add performance engineer agent"
git commit -m "fix(commands): correct argument parsing in review command"
git commit -m "docs: update CONTRIBUTING with hook examples"
```

**Commit Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation
- `refactor`: Code refactoring
- `test`: Test additions
- `chore`: Maintenance

### 6. Push and Create PR

```bash
git push origin feature/your-feature-name
```

Then create a Pull Request on GitHub.

---

## Coding Standards

### Agent Writing Style

- **Voice:** Second person ("You are...")
- **Tone:** Professional but approachable
- **Specificity:** Concrete over abstract
- **Examples:** Show, don't just tell

### Markdown Formatting

- Use `---` for horizontal rules
- Use tables for structured data
- Use code blocks with language hints
- Use emoji consistently (see README for team emoji)

### YAML Frontmatter

- Always include required fields
- Use consistent indentation (2 spaces)
- Quote strings with special characters

### Severity Levels

Use consistent P0-P4 definitions:

- **P0:** Critical - blocks deployment
- **P1:** High - fix before release
- **P2:** Medium - schedule fix
- **P3:** Low - fix when touching code
- **P4:** Nitpick - optional

---

## Questions?

- Open an issue for clarification
- Check existing agents/commands for examples
- Review the Claude Code plugin documentation

---

## Code of Conduct

- Be respectful and professional
- Provide constructive feedback
- Focus on code quality, not personal preferences
- Help newcomers learn

---

## License

By contributing, you agree that your contributions will be licensed under the MIT License.
