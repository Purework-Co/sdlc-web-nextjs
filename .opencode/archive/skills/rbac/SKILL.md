---
name: rbac
description: Role-Based Access Control implementation
---

## Commands

### /rbac-setup
Setup RBAC system.

Implement RBAC:
1. Create Role, Permission models in Prisma
2. Seed default roles (admin, manager, user)
3. Create permission service
4. Add middleware for route protection
5. Create UI for role management

### /rbac-check
Add RBAC check to feature.

Add RBAC to existing feature:
1. Define required permissions
2. Add check in service/API layer
3. Create permission guard component
4. Add audit logging