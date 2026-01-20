# Team Argonauts Examples

Real-world examples of using Team Argonauts smart review.

## Directory Structure

- `security-review-example.md` - Security review finding SQL injection

## Quick Start

Simply describe what you want reviewed:

```bash
# Security review
/team-argonauts:smart-review "security and authentication"

# Backend review
/team-argonauts:smart-review "backend API code"

# Full project review
/team-argonauts:smart-review "entire codebase"
```

## Common Scenarios

### Scenario 1: Before Deployment
```bash
# Review all recent changes
/team-argonauts:smart-review "recent changes"

# Or review everything
/team-argonauts:smart-review "all production code"
```

### Scenario 2: New Feature Development
```bash
# Review the feature you just built
/team-argonauts:smart-review "payment processing feature"

# Or be specific
/team-argonauts:smart-review "payment API and frontend"
```

### Scenario 3: Security-Critical Code
```bash
# Comprehensive security review
/team-argonauts:smart-review "authentication and authorization"

# Or focus on specific area
/team-argonauts:smart-review "password handling and token management"
```

### Scenario 4: Performance Optimization
```bash
# Backend performance
/team-argonauts:smart-review "API endpoints and database queries"

# Frontend performance
/team-argonauts:smart-review "React components and hooks"
```

### Scenario 5: Infrastructure Changes
```bash
# Deployment review
/team-argonauts:smart-review "Docker and Kubernetes configs"

# CI/CD review
/team-argonauts:smart-review "GitHub Actions workflows"
```

### Scenario 6: Database Changes
```bash
# Database review
/team-argonauts:smart-review "database models and migrations"

# Or combined
/team-argonauts:smart-review "database and API layer"
```

## How It Works

1. **You describe what to review** (natural language)
2. **Explore agent finds relevant files** (no hardcoded patterns)
3. **Smart selection picks reviewers** (based on file analysis)
4. **Experts review in parallel** (fast, comprehensive)
5. **Consolidated report** (P0-P4 findings, clear verdict)

## Example Outputs

See `security-review-example.md` for a detailed review output.
