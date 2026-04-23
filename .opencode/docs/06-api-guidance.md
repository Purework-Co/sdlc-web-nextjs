# REST API Guidance

## Endpoint Structure

```
src/app/api/v1/
├── auth/login/route.ts
├── users/route.ts          # GET, POST
├── users/[id]/route.ts     # GET, PATCH, DELETE
└── [module]/route.ts
```

## Response Format

**Success Response**:
```json
{
  "success": true,
  "data": { ... },
  "message": "Operation completed"
}
```

**Error Response**:
```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human readable message"
  }
}
```

**Paginated Response**:
```json
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

## JWT Authentication Pattern

1. Request includes: `Authorization: Bearer <token>`
2. Middleware verifies token
3. Extract `user` and `tenantId` from token
4. Pass to service layer
5. Service handles business logic

**Token Payload**:
```json
{
  "userId": "uuid",
  "email": "user@example.com",
  "role": "admin|user",
  "tenantId": "uuid",
  "iat": 1234567890,
  "exp": 1234571490
}
```

## HTTP Methods

| Method | Purpose | Auth Required |
|--------|---------|---------------|
| GET | Retrieve data | Yes |
| POST | Create new record | Yes |
| PATCH | Update existing | Yes |
| DELETE | Remove record | Yes |

## Error Status Codes

| Code | Meaning |
|------|---------|
| 400 | Bad Request (invalid input) |
| 401 | Unauthorized (no/invalid token) |
| 403 | Forbidden (insufficient permissions) |
| 404 | Not Found |
| 500 | Server Error |

## Service Layer Pattern

Services handle all business logic:

```
src/lib/services/
├── userService.ts
├── productService.ts
└── orderService.ts
```

Each service exports functions like:
- `create()`, `read()`, `update()`, `delete()`
- Validates business rules
- Calls database (Prisma)
- Returns plain objects or errors
