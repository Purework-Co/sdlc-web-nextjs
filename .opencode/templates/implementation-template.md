# Implementation Details Template

Use this template to document the detailed implementation of a completed feature.

---

## Metadata

| Field | Value |
|-------|-------|
| **Feature Name** | [Feature Name] |
| **Document ID** | IMPL-[feature-name]-[date] |
| **Version** | 1.0 |
| **Created** | YYYY-MM-DD |
| **Author** | Documenter |
| **Related Spec** | [SPEC-XXX] |

---

## 1. Implementation Approach

### 1.1 Architecture Decision
[Explain why this approach was chosen over alternatives]

### 1.2 Design Patterns Used
| Pattern | Where Applied | Reason |
|---------|---------------|--------|
| [Pattern Name] | [Location] | [Reason] |
| [Pattern Name] | [Location] | [Reason] |

---

## 2. Database Implementation

### 2.1 Schema

```prisma
// Complete model definition as implemented
model [ModelName] {
  id            String    @id @default(uuid())
  [field1]      [Type]   [Constraints]
  [field2]      [Type]   [Constraints]
  
  // Relations
  [relation]    [RelationType] @relation([fields], [references])
  
  // Timestamps
  createdAt     DateTime  @default(now()) @map("created_at")
  updatedAt     DateTime  @updatedAt @map("updated_at")
  deletedAt     DateTime? @map("deleted_at")

  // Indexes
  @@index([field])
  @@map("table_name")
}
```

### 2.2 Migrations

```sql
-- Migration: YYYYMMDDHHMMSS_description.sql
-- Description: [What this migration does]

BEGIN;

-- [SQL operations]

COMMIT;
```

### 2.3 Seed Data (if applicable)
```typescript
// Seed file location: prisma/seed.ts
const [data] = {
  // seed data
};
```

---

## 3. API Implementation

### 3.1 Route Handler

```typescript
// File: src/app/api/v1/[resource]/route.ts

import { NextRequest, NextResponse } from 'next/server';
// imports

// Handler implementations
export async function GET(request: NextRequest) {
  // implementation
}

export async function POST(request: NextRequest) {
  // implementation
}

// ... other methods
```

### 3.2 Service Layer

```typescript
// File: src/lib/services/[service].ts

export async function [functionName](params: Params): Promise<Result> {
  // Implementation details
  
  // 1. Validation
  // 2. Business logic
  // 3. Database operation
  // 4. Return result
}
```

### 3.3 Middleware

```typescript
// File: src/lib/middleware.ts

export async function authMiddleware(request: NextRequest) {
  // implementation
}
```

---

## 4. Frontend Implementation

### 4.1 Components

```typescript
// File: src/components/[component].tsx
'use client';

export function [ComponentName](props: Props) {
  // Implementation
  
  return (
    // JSX
  );
}
```

### 4.2 Pages

```typescript
// File: src/app/(dashboard)/[entity]/page.tsx
'use client';

// Page implementation
```

### 4.3 State Management

```typescript
// Hook: src/hooks/use[Feature].ts
export function use[Feature]() {
  // State and methods
}
```

---

## 5. RBAC Implementation

### 5.1 Permissions Added

| Permission | Description | Resource | Action |
|------------|-------------|----------|--------|
| `[permission:name]` | [Description] | [Resource] | [Action] |

### 5.2 Role Updates

| Role | Permissions Added |
|------|-------------------|
| [Role Name] | [Permission list] |

### 5.3 Frontend RBAC

```typescript
// Permission check in component
const { hasPermission } = usePermissions();
// hasPermission('[permission:name]')
```

---

## 6. Multi-Tenant Implementation

### 6.1 Tenant Isolation
- All queries include `tenantId` filter
- Tenant context extracted from JWT or header

### 6.2 Tenant-Specific Features
| Feature | Implementation |
|---------|---------------|
| [Feature] | [How tenant isolation works] |

---

## 7. Integration Points

### 7.1 External Services

| Service | Integration Method | Files |
|---------|---------------------|-------|
| [Service Name] | [API/Library] | [Files] |

### 7.2 Internal Dependencies

| Module | Interface | Files |
|--------|-----------|-------|
| [Module] | [Interface type] | [Files] |

---

## 8. Configuration

### 8.1 Environment Variables

```env
# .env
NEW_FEATURE_ENABLED=true
NEW_FEATURE_API_KEY=
```

### 8.2 Runtime Config

```typescript
// src/lib/config.ts
export const config = {
  // configuration
};
```

---

## 9. Error Handling

### 9.1 Error Types

| Error Type | HTTP Status | Handling |
|------------|-------------|----------|
| [Error] | [Status] | [How handled] |

### 9.2 Error Responses

```typescript
// Standard error response format
{
  success: false,
  error: {
    code: 'ERROR_CODE',
    message: 'Human readable message'
  }
}
```

---

## 10. Performance Considerations

### 10.1 Optimizations

| Optimization | Implementation | Impact |
|--------------|----------------|--------|
| [Optimization] | [How implemented] | [Impact] |

### 10.2 Caching Strategy

| Data | Cache Method | TTL |
|------|--------------|-----|
| [Data] | [Redis/Memory] | [TTL] |

---

## 11. Security Considerations

| Consideration | Implementation |
|---------------|----------------|
| [Security aspect] | [How handled] |

---

## 12. Code Snippets

### 12.1 Key Functions

```typescript
// [Function name]
async function [functionName](params: Params): Promise<Result> {
  // Critical implementation details
}
```

### 12.2 Important Logic

```typescript
// [Logic description]
// [Code with comments]
```

---

## 13. Testing Coverage

### 13.1 Unit Tests

| Test Suite | Tests | Coverage |
|------------|-------|----------|
| [Test file] | [Number] | [Percentage] |

### 13.2 Integration Tests

| Test Suite | Tests | Status |
|------------|-------|--------|
| [Test file] | [Number] | [Passing/Failing] |

---

## 14. Rollback Instructions

### 14.1 Database Rollback
```bash
npx prisma migrate rollback
```

### 14.2 Code Rollback
```bash
git revert [commit-hash]
```

### 14.3 Configuration Cleanup
[Steps to clean up configuration changes]

---

## 15. Related Files Summary

| Category | Files |
|----------|-------|
| Database | [Files] |
| API | [Files] |
| Frontend | [Files] |
| Tests | [Files] |
| Config | [Files] |