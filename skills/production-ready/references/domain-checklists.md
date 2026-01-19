# Domain-Specific Production Ready Checklists

Detailed checklists for each engineering domain. Use these as comprehensive review guides.

## Backend Engineering Checklist

### API Design
- [ ] RESTful conventions followed (or GraphQL best practices)
- [ ] Consistent error response format
- [ ] Appropriate HTTP status codes
- [ ] API versioning strategy
- [ ] Request validation at boundaries
- [ ] Response pagination for lists
- [ ] Rate limiting headers

### Fault Tolerance
- [ ] Timeouts configured (connect, read, total)
- [ ] Retry with exponential backoff + jitter
- [ ] Circuit breaker on external dependencies
- [ ] Bulkhead isolation for critical paths
- [ ] Graceful degradation (what works when X is down?)
- [ ] Fallback values/behaviors defined

### Data Access
- [ ] Connection pooling configured
- [ ] Query parameterization (no string concatenation)
- [ ] Transaction boundaries appropriate
- [ ] Optimistic vs pessimistic locking considered
- [ ] Read replicas utilized where appropriate
- [ ] Batch operations for bulk data

### Performance
- [ ] N+1 queries eliminated
- [ ] Appropriate caching strategy
- [ ] Async processing for slow operations
- [ ] Pagination implemented correctly
- [ ] Indexes support query patterns

---

## Security Engineering Checklist

### Input Handling
- [ ] All user input validated
- [ ] Input length limits enforced
- [ ] SQL injection prevented (parameterized queries)
- [ ] Command injection prevented (no shell interpolation)
- [ ] Path traversal prevented (canonicalization)
- [ ] SSRF prevented (URL validation)
- [ ] XSS prevented (output encoding + CSP)

### Authentication
- [ ] Passwords hashed with modern algorithm (bcrypt, argon2)
- [ ] Salts are unique per user
- [ ] Brute force protection (rate limiting, lockout)
- [ ] Session tokens are random and sufficient length
- [ ] Session expiration configured
- [ ] Secure cookie flags (HttpOnly, Secure, SameSite)

### Authorization
- [ ] Every endpoint has auth check
- [ ] IDOR prevention (user owns resource?)
- [ ] Privilege escalation prevented
- [ ] Least privilege principle applied
- [ ] Role/permission checks consistent

### Data Protection
- [ ] Sensitive data encrypted at rest
- [ ] TLS for all data in transit
- [ ] PII minimization (collect only what's needed)
- [ ] No secrets in code, logs, or error messages
- [ ] Audit trail for sensitive operations

---

## Frontend Engineering Checklist

### Performance
- [ ] Bundle size reasonable (<250KB gzipped initial)
- [ ] Code splitting for routes
- [ ] Lazy loading for heavy components
- [ ] Images optimized and lazy loaded
- [ ] No unnecessary re-renders
- [ ] Memory leaks prevented (cleanup effects)

### Accessibility (WCAG 2.1 AA)
- [ ] Semantic HTML elements used
- [ ] All interactive elements keyboard accessible
- [ ] Focus indicators visible
- [ ] Color contrast meets AA (4.5:1 text, 3:1 UI)
- [ ] ARIA labels on custom components
- [ ] Screen reader tested
- [ ] Focus management on route changes

### User Experience
- [ ] Loading states for all async operations
- [ ] Error states with recovery actions
- [ ] Empty states are helpful
- [ ] Form validation is immediate and helpful
- [ ] Optimistic updates where appropriate
- [ ] Offline handling if applicable

### State Management
- [ ] State location is appropriate (local vs global)
- [ ] No prop drilling > 3 levels
- [ ] Server state vs UI state separated
- [ ] Derived state computed, not stored

---

## Database Engineering Checklist

### Schema Design
- [ ] Primary keys appropriate (natural vs surrogate)
- [ ] Foreign key constraints defined
- [ ] Normalization level appropriate
- [ ] Data types are correct and sized
- [ ] NULL vs NOT NULL intentional
- [ ] Default values sensible

### Indexing
- [ ] Primary key indexed (automatic)
- [ ] Foreign keys indexed
- [ ] Query patterns have supporting indexes
- [ ] Composite index column order correct
- [ ] No redundant indexes
- [ ] Index maintenance considered

### Query Optimization
- [ ] EXPLAIN ANALYZE reviewed
- [ ] No full table scans on hot paths
- [ ] Joins use indexes
- [ ] Subqueries vs JOINs evaluated
- [ ] Pagination uses cursor, not OFFSET
- [ ] Batch sizes reasonable

### Operations
- [ ] Migration has rollback plan
- [ ] Migration is backward compatible
- [ ] Locks minimized during migration
- [ ] Backup/restore tested
- [ ] Connection limits understood

---

## DevOps/SRE Checklist

### Deployment
- [ ] Zero-downtime deployment strategy
- [ ] Rollback takes < 5 minutes
- [ ] Feature flags for risky changes
- [ ] Canary/gradual rollout option
- [ ] Database migration separated from code deploy

### Containers
- [ ] Non-root user in container
- [ ] Minimal base image
- [ ] No secrets baked in image
- [ ] Multi-stage build for size
- [ ] Health checks defined (liveness + readiness)
- [ ] Resource limits set

### Observability
- [ ] Structured logging (JSON)
- [ ] Log levels appropriate
- [ ] No PII in logs
- [ ] Metrics for RED (Rate, Errors, Duration)
- [ ] Distributed tracing enabled
- [ ] Alerts are actionable (not noisy)

### Security
- [ ] Secrets in secret manager
- [ ] Least privilege IAM roles
- [ ] Network policies restrict traffic
- [ ] Dependencies scanned for CVEs
- [ ] Container images scanned

---

## QA Engineering Checklist

### Test Coverage
- [ ] Happy path covered (critical flows)
- [ ] Error paths covered
- [ ] Boundary values tested
- [ ] Integration points tested
- [ ] Auth/authz tested

### Test Quality
- [ ] Tests are deterministic (not flaky)
- [ ] Tests are independent (no shared state)
- [ ] Test names describe behavior
- [ ] Assertions verify behavior, not implementation
- [ ] Mocks are minimal and necessary

### Edge Cases
- [ ] Empty inputs
- [ ] Null/undefined inputs
- [ ] Maximum length inputs
- [ ] Special characters
- [ ] Concurrent operations
- [ ] Network failures
- [ ] Time-based scenarios
