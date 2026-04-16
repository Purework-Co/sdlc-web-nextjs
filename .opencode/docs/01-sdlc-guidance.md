# SDLC Guidance

This document defines the Software Development Life Cycle for AI coding agents building full-stack applications.

## Technology Stack

| Component | Technology |
|-----------|------------|
| Framework | Next.js 14+ (App Router) |
| Database | PostgreSQL with Prisma ORM |
| ID Format | UUID v4 (@default(uuid())) |
| UI | Shadcn UI components |
| Auth | NextAuth.js (Auth.js) |
| OAuth | Google OAuth |
| Sessions | Redis |
| API | REST with JWT |
| Container | Podman |
| Telemetry | Redis (with TTL expiry) |

## Project Structure

```
src/
├── app/                    # Next.js App Router
│   ├── (public)/           # Landing page routes
│   ├── (auth)/             # Auth routes (login, register)
│   ├── (dashboard)/       # Protected admin routes
│   └── api/v1/             # REST API (accessible via Postman)
│       └── [module]/
│           └── route.ts
├── components/             # React components
│   ├── ui/                 # Shadcn components
│   └── shared/             # Shared components
├── lib/                    # Core libraries
│   ├── auth.ts             # NextAuth configuration
│   ├── db.ts               # Prisma client
│   ├── redis.ts            # Redis client
│   ├── jwt.ts              # JWT utilities
│   ├── telemetry.ts       # Telemetry logging
│   └── services/           # Business logic
├── types/                  # TypeScript types
└── utils/                  # Utility functions

prisma/
├── schema.prisma           # Database schema
└── migrations/             # Database migrations
```

## Branch Strategy

```
main (production)
├── develop (staging)
│   ├── feature/TICKET-xxx-description
│   ├── fix/TICKET-xxx-description
│   └── chore/TICKET-xxx-description
```

### Branch Naming
- `feature/` - New features
- `fix/` - Bug fixes
- `refactor/` - Code refactoring
- `chore/` - Maintenance tasks

### Commit Message Format

```
type(TICKET-xxx): description

[optional body]

[optional footer]
```

Types: `feat`, `fix`, `refactor`, `chore`, `docs`, `test`

Example:
```
feat(PRD-123): add user role management

- Create Role model in Prisma
- Add RBAC middleware
- Create role management UI

Closes PRD-123
```

## Environment Variables

Create `.env` template:

```env
# Database
DATABASE_URL="postgresql://user:pass@localhost:5432/dbname"

# NextAuth
NEXTAUTH_URL="http://localhost:3000"
NEXTAUTH_SECRET="your-secret"

# Google OAuth
GOOGLE_CLIENT_ID=""
GOOGLE_CLIENT_SECRET=""

# JWT
JWT_SECRET="your-jwt-secret"
JWT_EXPIRES_IN="7d"

# Redis
REDIS_URL="redis://localhost:6379"

# App
APP_URL="http://localhost:3000"
```

## Development Workflow

### 1. Setup
```bash
# Install dependencies
npm install

# Setup Prisma
npx prisma generate
npx prisma db push

# Run development server
npm run dev
```

### 2. Creating New Feature
1. Create branch from `develop`
2. Update Prisma schema if needed
3. Create REST API in `src/app/api/v1/`
4. Create frontend components
5. Add tests
6. Update Postman collection

### 3. Database Changes
```bash
# Create migration
npx prisma migrate dev --name description

# Reset database (dev only)
npx prisma migrate reset
```

## Multi-Tenant Architecture

### Database Schema Pattern
```prisma
model Tenant {
  id        String   @id @default(uuid())
  name      String
  domain    String?  @unique
  users     User[]
  // ... other tenant-specific data

  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}

model User {
  id        String   @id @default(uuid())
  email     String
  tenantId  String
  tenant    Tenant   @relation(fields: [tenantId], references: [id])
  // ...

  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

### Tenant Context
- Extract from `X-Tenant-ID` header or JWT claim
- Always include in queries: `where: { tenantId: ... }`
- Validate tenant access for user

## RBAC Implementation

**IMPORTANT**: For complete RBAC implementation details, see `.opencode/docs/12-rbac-standardization.md`

### Overview
- **Database-driven**: Roles, menus, permissions stored in database
- **Admin-configurable**: Admin can create/edit roles via UI
- **Two-level access control**:
  - Menu-level (sidebar visibility)
  - Entity-level (CRUD actions)

### Database Schema
See `.opencode/docs/12-rbac-standardization.md` for complete schema.

### Default Roles
| Role | Description | Default for New Users |
|------|-------------|----------------------|
| `admin` | Full system access | No |
| `officer` | Standard operational role | Yes (isDefault: true) |
| *(admin can create more)* | Custom roles | - |

### Permission Check Pattern
```typescript
import { checkPermission } from '@/lib/services/rbac/permissionService';

// In API handler
const canCreate = await checkPermission(user.roleId, 'users', 'create');
if (!canCreate) {
  return NextResponse.json({ error: 'Forbidden' }, { status: 403 });
}
```

### Menu Access Pattern
```typescript
// Get menus visible to user's role
import { getMenusForRole } from '@/lib/services/rbac/menuService';

const menus = await getMenusForRole(user.roleId);
// Use menus to render sidebar
```

## Security Requirements

### JWT Implementation
1. Access token (short expiry, 15min)
2. Refresh token (longer expiry, 7d)
3. Store refresh token in Redis
4. Include tenant ID in JWT payload

### API Security
```typescript
// Middleware pattern
export async function authMiddleware(request: NextRequest) {
  const token = request.headers.get('authorization')?.replace('Bearer ', '');

  if (!token) {
    return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
  }

  const payload = await verifyJWT(token);
  if (!payload) {
    return NextResponse.json({ error: 'Invalid token' }, { status: 401 });
  }

  return { user: payload.user, tenantId: payload.tenantId };
}
```

### Security Headers
```typescript
// middleware.ts
export function middleware(request: NextRequest) {
  const response = NextResponse.next();

  response.headers.set('X-Content-Type-Options', 'nosniff');
  response.headers.set('X-Frame-Options', 'DENY');
  response.headers.set('X-XSS-Protection', '1; mode=block');
  response.headers.set('Strict-Content-Security-Policy', "...");

  return response;
}
```

## CI/CD Pipeline

### GitHub Actions (Example)
```yaml
name: CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      - run: npm ci
      - run: npm run lint
      - run: npm run typecheck
      - run: npm run test
      - run: npm run build

  deploy-staging:
    if: github.ref == 'refs/heads/develop'
    needs: test
    runs-on: ubuntu-latest
    steps:
      - run: echo "Deploy to staging"
```

## Deployment (Podman)

### Containerfile
```dockerfile
FROM node:20-alpine AS base
WORKDIR /app

FROM base AS deps
COPY package.json package-lock.json ./
RUN npm ci

FROM base AS builder
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

FROM base AS runner
ENV NODE_ENV=production
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static

EXPOSE 3000
CMD ["node", "server.js"]
```

### docker-compose.yml
```yaml
services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DATABASE_URL=postgresql://postgres:postgres@db:5432/app
      - REDIS_URL=redis://redis:6379
    depends_on:
      - db
      - redis

  db:
    image: postgres:15
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      POSTGRES_DB: app
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres

  redis:
    image: redis:7-alpine
    volumes:
      - redis_data:/data

volumes:
  postgres_data:
  redis_data:
```

## Telemetry & Logging

### Redis Telemetry Events
```typescript
interface TelemetryEvent {
  type: 'api_call' | 'auth_event' | 'error' | 'performance';
  tenantId: string;
  userId?: string;
  data: Record<string, unknown>;
  timestamp: number;
  expiresIn: number; // seconds (7 days = 604800)
}
```

### Logged Data
- API request/response (excluding sensitive data)
- Authentication events (login, logout, failures)
- Errors with stack traces
- Performance metrics (response time)
- RBAC permission checks

## Quality Gates

Before merging to `develop`:
- [ ] All tests passing
- [ ] `npm run lint` passes
- [ ] `npm run typecheck` passes
- [ ] Build succeeds
- [ ] Postman collection updated (if API changed)

Before merging to `main`:
- [ ] All CI checks passing
- [ ] QA sign-off
- [ ] Security review