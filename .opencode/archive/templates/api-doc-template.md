# API Documentation Template

Use this template to document REST API endpoints.

---

## Metadata

| Field | Value |
|-------|-------|
| **Module** | [Module Name] |
| **Version** | 1.0 |
| **Last Updated** | YYYY-MM-DD |
| **Document Author** | Documenter |

---

## Overview

[Module description and purpose]

### Base URL
```
Production: https://api.example.com/v1
Staging: https://api-staging.example.com/v1
Development: http://localhost:3000/api/v1
```

### Authentication
All endpoints require JWT authentication via Bearer token.

```
Authorization: Bearer <access_token>
```

### Tenant Context
Include tenant ID in header:
```
X-Tenant-ID: <tenant_id>
```

---

## Endpoints

### 1. [Endpoint Name]

#### Description
[What this endpoint does]

#### Endpoint
```
[METHOD] /[path]
```

#### Request

**Headers**
| Header | Required | Description |
|--------|----------|-------------|
| Authorization | Yes | Bearer token |
| X-Tenant-ID | Yes | Tenant ID |
| Content-Type | Yes (POST/PATCH) | application/json |

**Query Parameters**
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| page | number | No | 1 | Page number |
| limit | number | No | 10 | Items per page (10, 25, 50, 100) |
| search | string | No | - | Global search |
| sortBy | string | No | createdAt | Sort field |
| order | string | No | desc | Sort order (asc, desc) |
| filter[field] | string | No | - | Filter by field |

**Request Body (POST/PATCH)**
```json
{
  "field1": "value1",
  "field2": "value2"
}
```

#### Response

**Success Response (200)**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "field1": "value1",
    "field2": "value2",
    "createdAt": "2024-01-15T10:30:00Z",
    "updatedAt": "2024-01-15T10:30:00Z"
  }
}
```

**List Response (200)**
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "field1": "value1"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 100,
    "totalPages": 10,
    "hasNext": true,
    "hasPrev": false
  },
  "meta": {
    "sortBy": "createdAt",
    "order": "desc",
    "search": "",
    "filters": {}
  }
}
```

**Error Responses**
| Status | Code | Description |
|--------|------|-------------|
| 400 | VALIDATION_ERROR | Invalid request body |
| 401 | UNAUTHORIZED | Missing or invalid token |
| 403 | FORBIDDEN | Insufficient permissions |
| 404 | NOT_FOUND | Resource not found |
| 409 | CONFLICT | Resource already exists |
| 422 | VALIDATION_ERROR | Validation failed |
| 429 | RATE_LIMITED | Too many requests |
| 500 | SERVER_ERROR | Internal server error |

**Error Response Format**
```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human readable error message"
  }
}
```

#### Permissions Required
| Permission | Description |
|------------|-------------|
| `[permission:read]` | View [resource] |
| `[permission:write]` | Create/Update [resource] |
| `[permission:delete]` | Delete [resource] |

#### Example Request (cURL)
```bash
curl -X GET "http://localhost:3000/api/v1/users?page=1&limit=25" \
  -H "Authorization: Bearer <token>" \
  -H "X-Tenant-ID: <tenant_id>"
```

#### Example Request (Postman)
```json
{
  "method": "GET",
  "url": "{{baseUrl}}/api/v1/users",
  "header": {
    "Authorization": "Bearer {{accessToken}}",
    "X-Tenant-ID": "{{tenantId}}"
  }
}
```

---

### 2. [Endpoint Name]

[Same structure as above]

---

## Data Models

### [Model Name]

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | UUID | Yes | Primary key |
| field1 | string | Yes | Description |
| field2 | string | No | Description |
| createdAt | timestamp | Yes | Creation timestamp |
| updatedAt | timestamp | Yes | Last update timestamp |
| deletedAt | timestamp | No | Soft delete timestamp |

---

## Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| UNAUTHORIZED | 401 | Missing or invalid token |
| INVALID_TOKEN | 401 | Token expired or invalid |
| FORBIDDEN | 403 | Insufficient permissions |
| NOT_FOUND | 404 | Resource not found |
| VALIDATION_ERROR | 422 | Invalid input |
| CONFLICT | 409 | Duplicate resource |
| RATE_LIMITED | 429 | Too many requests |
| SERVER_ERROR | 500 | Internal server error |

---

## Rate Limiting

[Rate limiting details if applicable]

---

## Versioning

| Version | Status | Date |
|---------|--------|------|
| v1 | Current | YYYY-MM-DD |

---

## Changelog

| Date | Changes |
|------|---------|
| YYYY-MM-DD | Initial documentation |
| YYYY-MM-DD | Added [endpoint] |

---

## Related Documentation

- Technical Spec: `.opencode/docs/technical/[feature-name]/SPEC.md`
- Implementation: `.opencode/docs/technical/[feature-name]/IMPLEMENTATION.md`
- Postman Collection: `.opencode/docs/api/[module]-collection.json`