# Testing Guidance

This document defines testing strategies and patterns for AI coding agents.

## Testing Pyramid

```
        /\
       /e2e\      <- Few, expensive, business-critical
      /----\
     /integ\     <- Medium, more coverage
    /------\
   /  unit  \   <- Many, fast, cheap
  /----------\
```

## Test Types

### 1. Unit Tests
- **Location**: `src/__tests__/` or `*.test.ts`
- **Framework**: Vitest (recommended) or Jest
- **Coverage Target**: >80%

**What to test:**
- Service functions
- Utility functions
- Validation functions
- Business logic

**Example:**
```typescript
// services/userService.test.ts
import { describe, it, expect, vi } from 'vitest';
import { createUser } from '../userService';

vi.mock('../lib/db', () => ({
  prisma: {
    user: {
      create: vi.fn().mockResolvedValue({ id: '1', email: 'test@test.com' })
    }
  }
}));

describe('createUser', () => {
  it('should create user with valid data', async () => {
    const result = await createUser({ email: 'test@test.com', tenantId: 't1' });
    expect(result.email).toBe('test@test.com');
  });

  it('should throw on duplicate email', async () => {
    await expect(createUser({ email: 'exists@test.com', tenantId: 't1' }))
      .rejects.toThrow('Email already exists');
  });
});
```

### 2. Integration Tests
- **Location**: `tests/integration/`
- **Database**: Test database (not production)
- **External Services**: Mock or testcontainers

**What to test:**
- API endpoints
- Service layer integration
- Database operations
- Auth flows

**Example:**
```typescript
// tests/integration/users.test.ts
import { test, expect } from '@playwright/test';

test('create user via API', async ({ request }) => {
  const response = await request.post('/api/v1/users', {
    data: { email: 'new@test.com', name: 'New User' }
  });

  expect(response.ok()).toBeTruthy();
  const user = await response.json();
  expect(user.email).toBe('new@test.com');
});
```

### 3. E2E Tests
- **Location**: `tests/e2e/`
- **Framework**: Playwright
- **Browser**: Chromium (default)

**What to test:**
- User flows
- Authentication
- Multi-tenant isolation
- Critical paths

**Example:**
```typescript
// tests/e2e/user-flows.spec.ts
import { test, expect } from '@playwright/test';

test('admin creates and edits user', async ({ page }) => {
  await page.goto('/login');
  await page.fill('[name=email]', 'admin@test.com');
  await page.fill('[name=password]', 'password');
  await page.click('button[type=submit]');

  await page.goto('/admin/users');
  await page.click('button:has-text("Add User")');
  await page.fill('[name=email]', 'newuser@test.com');
  await page.click('button:has-text("Create")');

  await expect(page.locator('text=newuser@test.com')).toBeVisible();
});
```

## Test Organization

```
tests/
├── unit/                  # Unit tests
│   ├── services/
│   └── utils/
├── integration/           # Integration tests
│   └── api/
└── e2e/                  # E2E tests
    ├── auth/
    └── flows/
```

## Testing Patterns

### Multi-Tenant Testing
```typescript
test('should only show users from same tenant', async () => {
  const user1 = await createUser({ tenantId: 'tenant-1', email: 'user1@test.com' });
  const user2 = await createUser({ tenantId: 'tenant-2', email: 'user2@test.com' });

  const results = await getUsersForTenant('tenant-1');

  expect(results).toHaveLength(1);
  expect(results[0].email).toBe('user1@test.com');
});
```

### RBAC Testing
```typescript
test('non-admin cannot delete users', async () => {
  const regularUser = await createUser({ role: 'user' });

  await expect(deleteUser(regularUser.id, regularUser))
    .rejects.toThrow('Permission denied');
});
```

### Auth Testing
```typescript
test('unauthenticated request returns 401', async () => {
  const response = await request.get('/api/v1/users');

  expect(response.status()).toBe(401);
});

test('valid JWT grants access', async () => {
  const token = await generateJWT({ userId: '1', tenantId: 't1' });
  const response = await request.get('/api/v1/users', {
    headers: { Authorization: `Bearer ${token}` }
  });

  expect(response.ok()).toBeTruthy();
});
```

## Running Tests

```bash
# Run all tests
npm run test

# Run unit tests
npm run test:unit

# Run integration tests
npm run test:integration

# Run E2E tests
npm run test:e2e

# Run with coverage
npm run test:coverage

# Watch mode
npm run test:watch
```

## Test Coverage Requirements

| Type | Minimum | Target |
|------|---------|--------|
| Unit | 70% | 80% |
| Integration | 60% | 70% |
| E2E | Key flows | All critical paths |

## Postman for API Testing

- Use Postman collections for manual API testing
- Include auth token in requests
- Test all CRUD operations
- Verify error responses

## Quality Gates

- [ ] Unit tests passing
- [ ] Integration tests passing
- [ ] E2E tests passing (critical paths)
- [ ] Coverage above threshold
- [ ] No security vulnerabilities (npm audit)