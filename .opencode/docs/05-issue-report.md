# Issue Report Guidance

This document guides AI coding agents in handling bug reports and issues.

## Issue Types

| Type | Description |
|------|-------------|
| Bug | Unexpected behavior, error, crash |
| Security | Vulnerability, data exposure |
| Performance | Slow response, high resource usage |
| UX | Confusing UI, unclear flow |
| Feature Gap | Missing expected functionality |

## Issue Severity

| Severity | Description | SLA |
|----------|-------------|-----|
| Critical | System down, data loss, security breach | 1 hour |
| High | Major functionality broken, blocking issue | 4 hours |
| Medium | Feature not working, workaround exists | 24 hours |
| Low | Minor issue, cosmetic, enhancement | 1 week |

## Issue Report Structure

### 1. Summary
- **Title**: Clear, concise (e.g., "Login fails with Google OAuth")
- **Type**: Bug/Security/Performance/UX/Feature Gap
- **Severity**: Critical/High/Medium/Low

### 2. Environment
- **Browser**: Chrome 120, Firefox, etc.
- **OS**: macOS 14, Windows 11, etc.
- **App Version**: v1.2.3 or commit hash
- **API Version**: v1 (if applicable)

### 3. Steps to Reproduce
Numbered list of actions that trigger the issue:
1. Go to login page
2. Click "Continue with Google"
3. Select Google account
4. Observe error

### 4. Expected Behavior
What should happen:
- User should be redirected to dashboard
- User session should be created

### 5. Actual Behavior
What actually happens:
- Error: "OAuth callback failed"
- Console shows: "Error: Failed to exchange token"

### 6. Related Code
- File path and line numbers
- Error logs
- Stack trace

### 7. Impact
- How many users affected?
- Workaround available?

## Investigation Steps

### Step 1: Reproduce
Attempt to reproduce the issue locally following the steps provided.

### Step 2: Gather Information
- Check browser console
- Check server logs
- Check Redis telemetry
- Verify database state

### Step 3: Identify Root Cause
- Is it a code bug?
- Is it a configuration issue?
- Is it a dependency issue?

### Step 4: Fix
Following SDLC guidance:
1. Create branch
2. Fix the issue
3. Add tests to prevent regression

### Step 5: Verify
- Issue no longer reproduces
- All tests pass
- No security vulnerabilities introduced

## Security Issues

For security issues:
- **Do not** include sensitive data in report
- **Do not** publicly disclose before fix
- Follow responsible disclosure
- Escalate immediately to team lead

## Common Issue Patterns

### Authentication Issues
- Check NextAuth configuration
- Verify Google OAuth credentials
- Check Redis session storage
- Verify JWT token validation

### Database Issues
- Check Prisma query errors
- Verify migration status
- Check connection pool
- Verify tenant isolation

### API Issues
- Verify JWT validation
- Check request/response format
- Verify RBAC permissions
- Check rate limiting

### Performance Issues
- Check Redis connection
- Verify database indexes
- Check for N+1 queries
- Monitor API response times

## Issue Report Template

Use `.opencode/templates/issue-template.md` for formatting.

## Resolution Checklist

- [ ] Issue reproduced
- [ ] Root cause identified
- [ ] Fix implemented
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] Fix verified
- [ ] Issue closed