# Code Review Checklist

## General Principles
- [ ] Code follows established style guides
- [ ] Code is self-documenting with clear variable/function names
- [ ] Code has appropriate comments explaining "why" not "what"
- [ ] No commented-out code or unnecessary logging
- [ ] Code handles errors appropriately

## Design & Architecture
- [ ] Code follows SOLID principles
- [ ] Functions are small and focused (single responsibility)
- [ ] No duplicated logic (DRY principle)
- [ ] Dependencies are minimized and properly managed
- [ ] Code is testable and loosely coupled

## Security
- [ ] Input validation is implemented
- [ ] Sensitive data is not logged
- [ ] Authentication/authorization is properly handled
- [ ] SQL injection/XSS prevention measures are in place
- [ ] Dependencies are up-to-date with no known vulnerabilities

## Performance
- [ ] Algorithms are efficient for expected data sizes
- [ ] Database queries are optimized
- [ ] Caching strategies are appropriate
- [ ] Memory leaks are prevented
- [ ] Asynchronous operations are used where appropriate

## Testing
- [ ] New code is covered by unit tests
- [ ] Edge cases are tested
- [ ] Integration tests cover key workflows
- [ ] Test names clearly describe expected behavior
- [ ] Tests are independent and repeatable

## Documentation
- [ ] Public APIs are documented
- [ ] Complex logic is explained
- [ ] README updates are included if needed
- [ ] Changelog is updated for user-facing changes

## Deployment & Operations
- [ ] Configuration is externalized
- [ ] Logging is appropriate for debugging
- [ ] Monitoring/metrics are in place
- [ ] Backward compatibility is maintained
- [ ] Rollback strategy is considered