---
description: Create authenticated API endpoints
agent: backend-engineer
---

Create REST API with JWT auth: validate JWT from Authorization header, extract user/tenant from token, add RBAC check, return proper 401/403 responses, log to Redis telemetry.