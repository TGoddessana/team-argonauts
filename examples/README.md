# Team Argonauts Examples

Real-world examples of using Team Argonauts for code review.

## Directory Structure

- `security-review-example.md` - Security review finding SQL injection
- `backend-review-example.md` - Backend review catching missing timeouts
- `full-review-example.md` - Complete team review of API endpoint
- `workflow-example.md` - Typical development workflow with Argonauts

## Quick Start

1. **For Security Issues:**
   ```
   /team-argonauts:security-review src/auth
   ```
   See: `security-review-example.md`

2. **For Backend Code:**
   ```
   /team-argonauts:backend-review src/api
   ```
   See: `backend-review-example.md`

3. **Full Team Review:**
   ```
   /team-argonauts:review
   ```
   See: `full-review-example.md`

## Common Scenarios

### Scenario 1: Before Deployment
```
# Review all changes since last commit
/team-argonauts:review
```

### Scenario 2: New Feature Development
```
# Get architecture guidance first
/team-argonauts:architecture-review

# Implement feature
# ...

# Review implementation
/team-argonauts:review src/features/new-feature
```

### Scenario 3: Security-Critical Code
```
# Security review of auth changes
/team-argonauts:security-review src/auth

# If issues found, fix and re-review
/team-argonauts:security-review src/auth
```

### Scenario 4: Performance Optimization
```
# Backend performance review
/team-argonauts:backend-review src/api/slow-endpoint.ts

# Frontend performance review
/team-argonauts:frontend-review src/components/slow-component.tsx
```

## Example Outputs

See the individual example files for detailed review outputs from each agent.
