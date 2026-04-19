---
name: authentication
description: NextAuth.js setup, JWT configuration, OAuth providers
---

## Commands

### /auth-setup
Setup NextAuth with Google OAuth.

Configure NextAuth.js:
1. Create src/lib/auth.ts with Google provider
2. Setup JWT strategy
3. Configure Redis session adapter
4. Add tenant context to session
5. Create login/logout pages

Use environment variables for credentials.

### /auth-api
Create authenticated API endpoints.

Create REST API with JWT auth:
1. Validate JWT from Authorization header
2. Extract user/tenant from token
3. Add RBAC check
4. Return proper 401/403 responses
5. Log to Redis telemetry