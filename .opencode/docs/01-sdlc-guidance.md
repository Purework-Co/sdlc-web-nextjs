# SDLC Guidance

Quick reference for building full-stack applications.

## Technology Stack

| Component | Technology |
|-----------|------------|
| Framework | Next.js 14+ (App Router) |
| Database | PostgreSQL + Prisma |
| UI | Shadcn UI components |
| Auth | NextAuth.js + Google OAuth |
| API | REST with JWT |
| Sessions | Redis |

## Project Structure

```
src/
├── app/
│   ├── (public)/          # Landing pages
│   ├── (auth)/            # Login, register
│   ├── (dashboard)/       # Protected routes
│   └── api/v1/            # REST API endpoints
├── components/
│   ├── ui/                # Shadcn UI components
│   └── shared/            # Reusable components
├── lib/
│   ├── auth.ts            # NextAuth config
│   ├── db.ts              # Prisma client
│   ├── jwt.ts             # JWT utilities
│   └── services/          # Business logic
├── types/                 # TypeScript types
└── utils/                 # Helper functions

prisma/
├── schema.prisma          # Database schema
└── migrations/            # DB migrations
```

## Git Branch Strategy

```
main (production - NO direct commits)
├── develop (staging - NO direct commits)
│   ├── feature/TICKET-xxx
│   ├── fix/TICKET-xxx
│   └── refactor/TICKET-xxx
```

**Rules**:
- Create branch for every task from `develop`
- ALL commits must include ticket: `type(TICKET-xxx): description`
- Types: `feat`, `fix`, `refactor`, `chore`, `docs`, `test`

## ID Format

All models use `UUID v4` as primary key:
```prisma
@id @default(uuid())
```
