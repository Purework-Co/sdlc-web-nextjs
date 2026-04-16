# Agentic AI SDLC Web Application Template

This workspace is a **reusable template** for building full-stack web applications using an **agentic AI-driven Software Development Life Cycle (SDLC)**. It handles **both new project creation from scratch (0) and ongoing development**.

---

## Overview

| Component | Technology |
|-----------|------------|
| **Framework** | Next.js 14+ (App Router) |
| **Database** | PostgreSQL with Prisma ORM |
| **ID Format** | UUID v4 (`@default(uuid())`) |
| **UI** | Shadcn UI components |
| **Auth** | NextAuth.js (Auth.js) with Google OAuth |
| **Sessions** | Redis |
| **API** | REST with JWT authentication |
| **Container** | Podman / Docker |

---

## Project Creation (From 0)

This workspace handles **creating new projects from scratch** as well as ongoing development. The AI agents can initialize a complete project structure:

### Initialization Process

```
Empty Directory → AI Agent Init → Complete Project Structure
```

### What Gets Created

| Component | Description |
|------------|-------------|
| **Next.js App** | Next.js 14+ with App Router, TypeScript |
| **Prisma Schema** | Multi-tenant ready with UUID IDs |
| **Authentication** | NextAuth.js with Google OAuth + JWT |
| **Database** | PostgreSQL with migrations |
| **UI Components** | Shadcn UI base components |
| **Redis** | Session storage and telemetry |
| **Admin Layout** | Collapsible sidebar + navbar |
| **RBAC** | Database-driven role/menu/permissions |
| **REST API** | JWT-secured endpoints with CRUD patterns |
| **Git** | Standardized branch/commit workflows |
| **Documentation** | All guidance docs and templates |

### Start New Project

```bash
# 1. Create new directory
mkdir my-new-project
cd my-new-project

# 2. Copy .opencode folder from this template
cp -r /path/to/sdlc-web/.opencode ./

# 3. Initialize Next.js project
npx create-next-app@latest . --typescript --tailwind --eslint

# 4. Setup Prisma
npx prisma init

# 5. Run AI agent to initialize full-stack
# Use /init-project skill from consultant agent

# 6. Configure environment
cp .env.example .env
# Fill in all required variables

# 7. Run development
npm run dev
```

The AI agents will use the guidance in `.opencode/docs/01-sdlc-guidance.md` to set up the complete project structure.

---

## AI Agent Architecture

This workspace defines **7 specialized AI agents** that work together to build web applications:

| Agent | Purpose | Key Commands |
|-------|---------|--------------|
| **consultant** | Interface with real users, gather requirements | `/clarify`, `/document`, `/validate`, `/translate`, `/liaison` |
| **full-stack-developer** | End-to-end feature development | `/build`, `/api`, `/db`, `/ui` |
| **backend-engineer** | API, database, auth, services | `/api`, `/service`, `/auth`, `/db` |
| **frontend-developer** | Pages, components, UI | `/page`, `/component`, `/form` |
| **devops-engineer** | Containers, CI/CD, deployment | `/docker`, `/deploy`, `/ci` |
| **qa-engineer** | Testing and validation | `/test`, `/e2e`, `/postman` |
| **documenter** | Technical documentation | `/doc-spec`, `/doc-api`, `/doc-changelog`, `/doc-issue` |

---

## Documentation Structure

### Guidance Documents (`.opencode/docs/`)

| Document | Purpose |
|----------|---------|
| `01-sdlc-guidance.md` | Development workflow, multi-tenant, RBAC |
| `02-prd-guidance.md` | Product Requirements Document structure |
| `03-feature-request.md` | Feature request workflow |
| `04-testing-guidance.md` | Unit, integration, E2E testing patterns |
| `05-issue-report.md` | Bug report handling |
| `06-api-guidance.md` | REST API with JWT + Postman |
| `07-crud-guidance.md` | CRUD with pagination, search, sort, filter |
| `08-admin-web-standardization.md` | Admin panel layout with sidebar |
| `09-prisma-migration-standardization.md` | Database migration patterns |
| `10-git-usage-standardization.md` | Branch strategy, commit conventions |
| `11-documenter-workflow.md` | Documentation workflow |
| `12-rbac-standardization.md` | Role-Based Access Control |

### Templates (`.opencode/templates/`)

| Template | Purpose |
|----------|---------|
| `prd-template.md` | Product Requirements Document |
| `feature-request-template.md` | Feature request format |
| `issue-template.md` | Bug report format |
| `technical-spec-template.md` | Technical specification |
| `implementation-template.md` | Implementation details |
| `api-doc-template.md` | API documentation |
| `issue-resolution-template.md` | Bug fix documentation |
| `changelog-entry-template.md` | Changelog entry |

---

## Key Features

### Multi-Tenant Architecture
- Tenant model with row-level isolation
- `tenantId` on all tables
- Tenant context via `X-Tenant-ID` header or JWT

### RBAC System
- **Database-driven**: Admin can configure via UI
- **Menu-level**: Which menus visible in sidebar
- **Entity-level**: CRUD actions based on role
- Default roles: `admin`, `officer`

### REST API Standards
- JWT authentication on all endpoints
- Pagination: 10, 25, 50, 100 per page
- Global search, sort, filter support
- Postman collection for each module

### Admin Panel
- Collapsible sidebar
- Navigation: Dashboard → CRUD Entities → Settings
- Top navbar: Logo, User Profile, Logout
- RBAC-driven menu visibility

---

## Workflow

This workspace handles **two modes** of operation:

### Mode 1: New Project Creation (From 0)

```
Empty Directory → AI Agent Initializes → Complete Project
```

The consultant agent uses `/init-project` to create a full project:

1. **Initialize** - Create Next.js project with TypeScript
2. **Setup** - Configure Prisma, NextAuth, Shadcn
3. **Structure** - Create base folder structure
4. **Configure** - Setup environment variables
5. **Migrate** - Create initial database migrations
6. **Document** - Create initial documentation

### Mode 2: Ongoing Development

```
Real User → Consultant → Technical Docs → Developer → Documenter → Final Docs + Changelog
                                      (based on this)  (completes)    (creates this)
```

### Complete Agent Workflow

1. **Consultant** receives request from real user
2. **Clarifies** ambiguities, **documents** requirements
3. **Translates** to technical specs for developer
4. **Developer** implements based on technical docs
5. **Documenter** creates final documentation + changelog

---

## Changelog

Full changelog maintained in `.opencode/docs/changelog/CHANGELOG.md`

Version format: `vMAJOR.MINOR.PATCH` (e.g., v1.15.100)

---

## Quick Start

```bash
# Install dependencies
npm install

# Setup Prisma
npx prisma generate
npx prisma db push

# Run development server
npm run dev
```

---

## Configuration Files

| File | Purpose |
|------|---------|
| `.opencode/agents.md` | Agent definitions and workflows |
| `.opencode/skills.json` | AI skill commands |
| `.opencode/opencode.json` | OpenCode CLI configuration |
| `.opencode/docs/` | All guidance documents |
| `.opencode/templates/` | Document templates |

---

## Project Structure

```
sdlc-web/
├── src/
│   ├── app/                 # Next.js App Router
│   │   ├── (public)/        # Landing page
│   │   ├── (auth)/          # Auth routes
│   │   ├── (dashboard)/     # Admin routes
│   │   └── api/v1/         # REST API
│   ├── components/          # React components
│   ├── lib/                 # Core libraries
│   │   ├── auth.ts         # NextAuth config
│   │   ├── db.ts           # Prisma client
│   │   ├── redis.ts        # Redis client
│   │   ├── jwt.ts          # JWT utilities
│   │   ├── telemetry.ts    # Redis telemetry
│   │   └── services/       # Business logic
│   └── types/              # TypeScript types
├── prisma/
│   ├── schema.prisma       # Database schema
│   └── migrations/         # Database migrations
├── .opencode/              # AI SDLC Configuration
│   ├── agents.md
│   ├── skills.json
│   ├── docs/
│   ├── templates/
│   └── changelog/
└── package.json
```

---

## Reuse This Template

This workspace handles **both new project creation from scratch and ongoing development**.

### For New Project (From 0)

1. Create new directory: `mkdir my-project && cd my-project`
2. Copy `.opencode` folder: `cp -r /path/to/sdlc-web/.opencode ./`
3. Initialize Next.js: `npx create-next-app@latest . --typescript --tailwind --eslint`
4. Setup Prisma: `npx prisma init`
5. Configure environment variables in `.env`
6. Run `/init-project` skill from consultant agent
7. Start development: `npm run dev`

### For Ongoing Development

1. Use existing project with `.opencode/` folder
2. Work with AI agents following the workflow
3. All documentation and standards are already in place

---

*This workspace is configured for agentic AI-driven full-stack web development, from project creation to ongoing development.*