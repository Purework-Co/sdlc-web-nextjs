# API Guidance

This document guides AI coding agents in creating REST APIs with JWT authentication and Postman collections.

## API Structure

### REST Endpoints Location
```
src/app/api/v1/
├── auth/
│   ├── login/route.ts
│   ├── logout/route.ts
│   └── refresh/route.ts
├── users/
│   └── route.ts          # GET, POST
├── users/[id]/
│   └── route.ts          # GET, PATCH, DELETE
└── [module]/
    └── route.ts
```

### Response Format
```typescript
// Success
{
  "success": true,
  "data": { ... },
  "message": "Operation completed"
}

// Error
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human readable message"
  }
}

// Paginated
{
  "success": true,
  "data": [...],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 100,
    "totalPages": 5
  }
}
```

## JWT Authentication

### Middleware Pattern
```typescript
// src/lib/middleware.ts
import { NextRequest, NextResponse } from 'next/server';
import { verifyJWT } from './jwt';

export async function authMiddleware(request: NextRequest) {
  const authHeader = request.headers.get('authorization');

  if (!authHeader?.startsWith('Bearer ')) {
    return NextResponse.json(
      { success: false, error: { code: 'UNAUTHORIZED', message: 'Missing token' } },
      { status: 401 }
    );
  }

  const token = authHeader.replace('Bearer ', '');
  const payload = await verifyJWT(token);

  if (!payload) {
    return NextResponse.json(
      { success: false, error: { code: 'INVALID_TOKEN', message: 'Invalid or expired token' } },
      { status: 401 }
    );
  }

  return { user: payload.user, tenantId: payload.tenantId };
}
```

### Route Handler Pattern
```typescript
// src/app/api/v1/users/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { authMiddleware } from '@/lib/middleware';
import { userService } from '@/lib/services/userService';

export async function GET(request: NextRequest) {
  const auth = await authMiddleware(request);
  if (auth instanceof NextResponse) return auth;

  const { user, tenantId } = auth;

  try {
    const users = await userService.getUsers(tenantId);
    return NextResponse.json({ success: true, data: users });
  } catch (error) {
    return NextResponse.json(
      { success: false, error: { code: 'FETCH_FAILED', message: 'Failed to fetch users' } },
      { status: 500 }
    );
  }
}

export async function POST(request: NextRequest) {
  const auth = await authMiddleware(request);
  if (auth instanceof NextResponse) return auth;

  const { user, tenantId } = auth;

  const hasPermission = await checkPermission(user.id, 'user:write');
  if (!hasPermission) {
    return NextResponse.json(
      { success: false, error: { code: 'FORBIDDEN', message: 'Insufficient permissions' } },
      { status: 403 }
    );
  }

  const body = await request.json();

  try {
    const newUser = await userService.createUser({ ...body, tenantId });
    return NextResponse.json({ success: true, data: newUser }, { status: 201 });
  } catch (error) {
    return NextResponse.json(
      { success: false, error: { code: 'CREATE_FAILED', message: 'Failed to create user' } },
      { status: 400 }
    );
  }
}
```

## RBAC in API

```typescript
async function checkPermission(userId: string, permission: string): Promise<boolean> {
  const user = await prisma.user.findUnique({
    where: { id: userId },
    include: {
      role: {
        include: { permissions: true }
      }
    }
  });

  return user?.role.permissions.some(p => p.name === permission) ?? false;
}
```

## Multi-Tenant Isolation

```typescript
// Always include tenantId in queries
async function getUsers(tenantId: string) {
  return prisma.user.findMany({
    where: { tenantId },
    include: { role: true }
  });
}
```

## Postman Collection

### Collection Structure
```json
{
  "info": {
    "name": "{{app_name}} API",
    "description": "REST API for {{app_name}}",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "variable": [
    {
      "key": "baseUrl",
      "value": "{{baseUrl}}",
      "type": "string"
    },
    {
      "key": "accessToken",
      "value": "",
      "type": "string"
    },
    {
      "key": "tenantId",
      "value": "",
      "type": "string"
    }
  ],
  "auth": {
    "type": "bearer",
    "bearer": [
      {
        "key": "token",
        "value": "{{accessToken}}"
      }
    ]
  },
  "item": [
    {
      "name": "Auth",
      "item": [
        {
          "name": "Login",
          "request": {
            "method": "POST",
            "url": "{{baseUrl}}/api/v1/auth/login",
            "header": [
              {
                "key": "Content-Type",
                "value": "application/json"
              }
            ],
            "body": {
              "mode": "raw",
              "raw": "{\"email\":\"admin@test.com\",\"password\":\"password\"}"
            }
          }
        }
      ]
    },
    {
      "name": "Users",
      "item": [
        {
          "name": "List Users",
          "request": {
            "method": "GET",
            "url": {
              "raw": "{{baseUrl}}/api/v1/users",
              "query": [
                {"key": "page", "value": "1"},
                {"key": "limit", "value": "20"}
              ]
            },
            "header": [
              {"key": "X-Tenant-ID", "value": "{{tenantId}}"}
            ]
          }
        },
        {
          "name": "Create User",
          "request": {
            "method": "POST",
            "url": "{{baseUrl}}/api/v1/users",
            "header": [
              {"key": "Content-Type", "value": "application/json"},
              {"key": "X-Tenant-ID", "value": "{{tenantId}}"}
            ],
            "body": {
              "mode": "raw",
              "raw": "{\"email\":\"newuser@test.com\",\"name\":\"New User\"}"
            }
          }
        }
      ]
    }
  ]
}
```

### Environment Variables
```json
{
  "name": "Development",
  "values": [
    {
      "key": "baseUrl",
      "value": "http://localhost:3000",
      "enabled": true
    },
    {
      "key": "accessToken",
      "value": "",
      "enabled": true
    },
    {
      "key": "tenantId",
      "value": "",
      "enabled": true
    }
  ]
}
```

## API Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| UNAUTHORIZED | 401 | Missing or invalid token |
| FORBIDDEN | 403 | Insufficient permissions |
| NOT_FOUND | 404 | Resource not found |
| VALIDATION_ERROR | 422 | Invalid request data |
| CONFLICT | 409 | Resource already exists |
| RATE_LIMITED | 429 | Too many requests |
| SERVER_ERROR | 500 | Internal server error |

## Telemetry for APIs

Log all API calls to Redis:
```typescript
async function logApiCall(
  tenantId: string,
  userId: string,
  endpoint: string,
  method: string,
  statusCode: number,
  duration: number
) {
  await redis.setex(
    `telemetry:api:${Date.now()}`,
    604800, // 7 days
    JSON.stringify({
      type: 'api_call',
      tenantId,
      userId,
      endpoint,
      method,
      statusCode,
      duration,
      timestamp: Date.now()
    })
  );
}
```

## Postman Collection Generation

After creating new API endpoints:
1. Scan `src/app/api/v1/` for route files
2. Extract method, path, params, body schema
3. Add to collection in `docs/api/`
4. Update environment with new variables

Store collections in: `docs/api/[module]-collection.json`