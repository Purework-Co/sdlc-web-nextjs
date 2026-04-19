---
name: nextjs-fullstack
description: Build full-stack Next.js applications with App Router, Prisma, PostgreSQL, and NextAuth
---

## Commands

### /init-project
Initialize new Next.js project with full-stack setup.

Initialize a new Next.js 14+ project with App Router. Setup:
- Prisma with PostgreSQL (UUIDv4 as default ID)
- NextAuth.js with Google OAuth
- Shadcn UI
- Redis for sessions
- Multi-tenant architecture base

Follow .opencode/docs/01-sdlc-guidance.md for project structure.

### /create-feature
Create complete feature from PRD.

Create a complete feature including:
- Prisma schema updates
- REST API endpoints
- Frontend components
- Tests
- Postman collection update

Reference .opencode/docs/02-prd-guidance.md for requirements format.

### /api-crud
Generate CRUD API endpoints.

Create REST API CRUD endpoints for a resource:
1. Route handler in src/app/api/v1/[resource]/route.ts
2. Service layer in src/lib/services/
3. JWT validation middleware
4. Postman collection entry
5. Unit tests

Use .opencode/docs/06-api-guidance.md patterns.