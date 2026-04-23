# CRUD Guidance

This document defines standards for building Create, Read, Update, Delete operations with pagination, search, sort, and filter capabilities.

## API Endpoint Standards

### REST Endpoints Pattern

```
GET    /api/v1/[resource]          # List with pagination, search, sort, filter
POST   /api/v1/[resource]          # Create new record
GET    /api/v1/[resource]/:id      # Get single record
PATCH  /api/v1/[resource]/:id      # Update record
DELETE /api/v1/[resource]/:id      # Delete (soft) record
```

### Query Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `page` | number | 1 | Current page |
| `limit` | number | 10 | Items per page (10, 25, 50, 100) |
| `search` | string | "" | Global search term |
| `sortBy` | string | "createdAt" | Column to sort by |
| `order` | string | "desc" | Sort order (asc, desc) |
| `filter[field]` | string | - | Filter by field value |

### Example URL
```
GET /api/v1/users?page=1&limit=25&search=john&sortBy=name&order=asc&filter[role]=admin&filter[status]=active
```

### Response Format

```typescript
// Success Response
{
  "success": true,
  "data": [
    { "id": "uuid", "name": "John", "email": "john@test.com", "role": "admin", "status": "active", "createdAt": "2024-01-01T00:00:00Z" }
  ],
  "pagination": {
    "page": 1,
    "limit": 25,
    "total": 150,
    "totalPages": 6,
    "hasNext": true,
    "hasPrev": false
  },
  "meta": {
    "sortBy": "name",
    "order": "asc",
    "search": "john",
    "filters": { "role": "admin", "status": "active" }
  }
}

// Single Record Response
{
  "success": true,
  "data": { "id": "uuid", "name": "John", ... }
}
```

## Database Schema Pattern (Prisma)

### Base Model with UUID

```prisma
model User {
  id        String   @id @default(uuid())
  name      String
  email     String
  role      String   @default("user")
  status    String   @default("active")
  tenantId  String

  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  deletedAt DateTime?

  @@index([email])
  @@index([tenantId, createdAt])
  @@map("users")
}
```

### Searchable Columns Definition

| Column Type | Search Method |
|-------------|---------------|
| String | contains (case-insensitive) |
| Date | equals or range |
| Enum | equals |
| Boolean | equals |

## Service Layer Pattern

### List Query Builder

```typescript
// src/lib/services/userService.ts
interface ListParams {
  tenantId: string;
  page: number;
  limit: number;
  search: string;
  sortBy: string;
  order: 'asc' | 'desc';
  filters: Record<string, string>;
}

interface ListResult<T> {
  data: T[];
  pagination: {
    page: number;
    limit: number;
    total: number;
    totalPages: number;
    hasNext: boolean;
    hasPrev: boolean;
  };
}

export async function listUsers(params: ListParams): Promise<ListResult<User>> {
  const { tenantId, page, limit, search, sortBy, order, filters } = params;

  const where: Prisma.UserWhereInput = {
    tenantId,
    deletedAt: null,
  };

  // Search - global search across searchable columns
  if (search) {
    where.OR = [
      { name: { contains: search, mode: 'insensitive' } },
      { email: { contains: search, mode: 'insensitive' } },
    ];
  }

  // Filters
  if (filters.role) where.role = filters.role;
  if (filters.status) where.status = filters.status;
  // Add more filters as needed

  // Sort - validate column exists
  const validSortColumns = ['name', 'email', 'createdAt', 'updatedAt'];
  const sortColumn = validSortColumns.includes(sortBy) ? sortBy : 'createdAt';
  const sortOrder = order === 'asc' ? 'asc' : 'desc';

  const skip = (page - 1) * limit;

  const [data, total] = await Promise.all([
    prisma.user.findMany({
      where,
      orderBy: { [sortColumn]: sortOrder },
      skip,
      take: limit,
    }),
    prisma.user.count({ where }),
  ]);

  return {
    data,
    pagination: {
      page,
      limit,
      total,
      totalPages: Math.ceil(total / limit),
      hasNext: page * limit < total,
      hasPrev: page > 1,
    },
  };
}
```

## API Route Handler

```typescript
// src/app/api/v1/users/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { authMiddleware } from '@/lib/middleware';
import { listUsers, createUser } from '@/lib/services/userService';

const ALLOWED_SORT_COLUMNS = ['name', 'email', 'role', 'status', 'createdAt', 'updatedAt'];
const ALLOWED_FILTER_FIELDS = ['role', 'status'];
const LIMIT_OPTIONS = [10, 25, 50, 100];

export async function GET(request: NextRequest) {
  const auth = await authMiddleware(request);
  if (auth instanceof NextResponse) return auth;

  const { tenantId } = auth;
  const { searchParams } = new URL(request.url);

  // Parse query params with validation
  const page = Math.max(1, parseInt(searchParams.get('page') || '1'));
  const limit = Math.min(100, Math.max(1, parseInt(searchParams.get('limit') || '10')));
  const search = searchParams.get('search') || '';
  const sortBy = ALLOWED_SORT_COLUMNS.includes(searchParams.get('sortBy') || '')
    ? searchParams.get('sortBy')!
    : 'createdAt';
  const order = ['asc', 'desc'].includes(searchParams.get('order') || '')
    ? searchParams.get('order') as 'asc' | 'desc'
    : 'desc';

  // Build filters
  const filters: Record<string, string> = {};
  ALLOWED_FILTER_FIELDS.forEach(field => {
    const value = searchParams.get(`filter[${field}]`);
    if (value) filters[field] = value;
  });

  try {
    const result = await listUsers({
      tenantId,
      page,
      limit,
      search,
      sortBy,
      order,
      filters,
    });

    return NextResponse.json({
      success: true,
      data: result.data,
      pagination: result.pagination,
      meta: { sortBy, order, search, filters },
    });
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

  // RBAC check
  const hasPermission = await checkPermission(user.id, 'user:write');
  if (!hasPermission) {
    return NextResponse.json(
      { success: false, error: { code: 'FORBIDDEN', message: 'Insufficient permissions' } },
      { status: 403 }
    );
  }

  const body = await request.json();

  try {
    const newUser = await createUser({ ...body, tenantId });
    return NextResponse.json({ success: true, data: newUser }, { status: 201 });
  } catch (error) {
    return NextResponse.json(
      { success: false, error: { code: 'CREATE_FAILED', message: 'Failed to create user' } },
      { status: 400 }
    );
  }
}
```

## Frontend Data Table Component

### Table with Pagination, Search, Sort, Filter

```typescript
// src/components/data-table.tsx
'use client';

import { useState, useEffect, useCallback } from 'react';
import { useRouter, useSearchParams, usePathname } from 'next/navigation';
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeaderRow,
  TableRow,
} from '@/components/ui/table';
import { Input } from '@/components/ui/input';
import { Button } from '@/components/ui/button';
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from '@/components/ui/select';
import { Badge } from '@/components/ui/badge';
import { ChevronUp, ChevronDown, ChevronLeft, ChevronRight, X } from 'lucide-react';

interface Column<T> {
  key: keyof T | 'no';
  label: string;
  sortable?: boolean;
  searchable?: boolean;
  render?: (value: unknown, row: T) => React.ReactNode;
}

interface DataTableProps<T> {
  data: T[];
  columns: Column<T>[];
  pagination: {
    page: number;
    limit: number;
    total: number;
    totalPages: number;
    hasNext: boolean;
    hasPrev: boolean;
  };
  meta: {
    sortBy: string;
    order: 'asc' | 'desc';
    search: string;
    filters: Record<string, string>;
  };
  baseUrl: string;
  permissions: {
    canCreate?: boolean;
    canEdit?: boolean;
    canDelete?: boolean;
  };
}

export function DataTable<T extends { id: string }>({
  data,
  columns,
  pagination,
  meta,
  baseUrl,
  permissions,
}: DataTableProps<T>) {
  const router = useRouter();
  const pathname = usePathname();
  const searchParams = useSearchParams();

  const [search, setSearch] = useState(meta.search);
  const [debouncedSearch, setDebouncedSearch] = useState(meta.search);

  // Debounce search
  useEffect(() => {
    const timer = setTimeout(() => setDebouncedSearch(search), 300);
    return () => clearTimeout(timer);
  }, [search]);

  // Update URL when params change
  const updateParams = useCallback((updates: Record<string, string | undefined>) => {
    const params = new URLSearchParams(searchParams.toString());
    Object.entries(updates).forEach(([key, value]) => {
      if (value === undefined || value === '') {
        params.delete(key);
      } else {
        params.set(key, value);
      }
    });
    router.push(`${pathname}?${params.toString()}`);
  }, [pathname, router, searchParams]);

  // Handlers
  const handleSort = (column: string) => {
    if (meta.sortBy === column) {
      updateParams({ order: meta.order === 'asc' ? 'desc' : 'asc' });
    } else {
      updateParams({ sortBy: column, order: 'asc' });
    }
  };

  const handlePageChange = (newPage: number) => {
    updateParams({ page: String(newPage) });
  };

  const handleLimitChange = (newLimit: string) => {
    updateParams({ limit: newLimit, page: '1' });
  };

  const handleSearch = (value: string) => {
    setSearch(value);
    updateParams({ search: value, page: '1' });
  };

  const handleFilterChange = (key: string, value: string) => {
    if (value === 'all') {
      updateParams({ [`filter[${key}]`]: undefined, page: '1' });
    } else {
      updateParams({ [`filter[${key}]`]: value, page: '1' });
    }
  };

  const clearFilters = () => {
    const params = new URLSearchParams();
    params.set('page', '1');
    router.push(`${pathname}?${params.toString()}`);
  };

  const hasFilters = Object.values(meta.filters).some(v => v);

  return (
    <div className="space-y-4">
      {/* Search and Filter Bar */}
      <div className="flex flex-col sm:flex-row gap-4 justify-between">
        <div className="w-full sm:w-72">
          <Input
            placeholder="Search..."
            value={search}
            onChange={(e) => handleSearch(e.target.value)}
            className="w-full"
          />
        </div>

        <div className="flex gap-2">
          {/* Filter Dropdowns */}
          {Object.entries(meta.filters).map(([key, value]) => (
            <Select
              key={key}
              value={value || 'all'}
              onValueChange={(v) => handleFilterChange(key, v)}
            >
              <SelectTrigger className="w-32">
                <SelectValue placeholder={key} />
              </SelectTrigger>
              <SelectContent>
                <SelectItem value="all">All {key}</SelectItem>
                <SelectItem value="admin">Admin</SelectItem>
                <SelectItem value="user">User</SelectItem>
              </SelectContent>
            </Select>
          ))}

          {hasFilters && (
            <Button variant="ghost" size="sm" onClick={clearFilters}>
              <X className="h-4 w-4 mr-1" />
              Clear
            </Button>
          )}
        </div>
      </div>

      {/* Table */}
      <div className="border rounded-md">
        <Table>
          <TableHeaderRow>
            {columns.map((col) => (
              <TableHead key={String(col.key)}>
                {col.key === 'no' ? (
                  '#'
                ) : col.sortable ? (
                  <button
                    className="flex items-center gap-1 hover:text-primary"
                    onClick={() => handleSort(String(col.key))}
                  >
                    {col.label}
                    {meta.sortBy === col.key && (
                      meta.order === 'asc' ? <ChevronUp className="h-4 w-4" /> : <ChevronDown className="h-4 w-4" />
                    )}
                  </button>
                ) : (
                  col.label
                )}
              </TableHead>
            ))}
          </TableHeaderRow>
          <TableBody>
            {data.map((row, index) => (
              <TableRow key={row.id}>
                {columns.map((col) => (
                  <TableCell key={String(col.key)}>
                    {col.key === 'no'
                      ? (pagination.page - 1) * pagination.limit + index + 1
                      : col.render
                      ? col.render((row as Record<string, unknown>)[String(col.key)], row)
                      : String((row as Record<string, unknown>)[String(col.key)] || '')}
                  </TableCell>
                ))}
              </TableRow>
            ))}
          </TableBody>
        </Table>
      </div>

      {/* Pagination */}
      <div className="flex flex-col sm:flex-row items-center justify-between gap-4">
        <div className="flex items-center gap-2">
          <span className="text-sm text-muted-foreground">
            Showing {((pagination.page - 1) * pagination.limit) + 1} to {Math.min(pagination.page * pagination.limit, pagination.total)} of {pagination.total}
          </span>
          <Select value={String(pagination.limit)} onValueChange={handleLimitChange}>
            <SelectTrigger className="w-20">
              <SelectValue />
            </SelectTrigger>
            <SelectContent>
              <SelectItem value="10">10</SelectItem>
              <SelectItem value="25">25</SelectItem>
              <SelectItem value="50">50</SelectItem>
              <SelectItem value="100">100</SelectItem>
            </SelectContent>
          </Select>
        </div>

        <div className="flex items-center gap-2">
          <Button
            variant="outline"
            size="sm"
            onClick={() => handlePageChange(pagination.page - 1)}
            disabled={!pagination.hasPrev}
          >
            <ChevronLeft className="h-4 w-4" />
          </Button>
          <span className="text-sm">
            Page {pagination.page} of {pagination.totalPages}
          </span>
          <Button
            variant="outline"
            size="sm"
            onClick={() => handlePageChange(pagination.page + 1)}
            disabled={!pagination.hasNext}
          >
            <ChevronRight className="h-4 w-4" />
          </Button>
        </div>
      </div>
    </div>
  );
}
```

### Usage Example

```typescript
// src/app/(dashboard)/users/page.tsx
'use client';

import { DataTable } from '@/components/data-table';
import { Button } from '@/components/ui/button';
import { Edit, Trash2, Eye } from 'lucide-react';

const columns = [
  { key: 'no' as const, label: '#' },
  { key: 'name' as const, label: 'Name', sortable: true, searchable: true },
  { key: 'email' as const, label: 'Email', sortable: true, searchable: true },
  { key: 'role' as const, label: 'Role', sortable: true },
  { key: 'status' as const, label: 'Status', sortable: true },
  {
    key: 'actions' as const,
    label: 'Actions',
    render: (_, row) => (
      <div className="flex gap-2">
        {permissions.canView && (
          <Button variant="ghost" size="sm" onClick={() => router.push(`/users/${row.id}`)}>
            <Eye className="h-4 w-4" />
          </Button>
        )}
        {permissions.canEdit && (
          <Button variant="ghost" size="sm" onClick={() => router.push(`/users/${row.id}/edit`)}>
            <Edit className="h-4 w-4" />
          </Button>
        )}
        {permissions.canDelete && (
          <Button variant="ghost" size="sm" onClick={() => handleDelete(row.id)}>
            <Trash2 className="h-4 w-4" />
          </Button>
        )}
      </div>
    ),
  },
];

export default function UsersPage() {
  const { data, pagination, meta } = useUsers(); // Custom hook

  return (
    <DataTable
      data={data}
      columns={columns}
      pagination={pagination}
      meta={meta}
      baseUrl="/api/v1/users"
      permissions={{
        canCreate: hasPermission('user:write'),
        canEdit: hasPermission('user:write'),
        canDelete: hasPermission('user:delete'),
      }}
    />
  );
}
```

## RBAC for Actions

**IMPORTANT**: For complete RBAC implementation, see `.opencode/docs/12-rbac-standardization.md`

### Overview
- **Database-driven**: Roles, menus, permissions stored in DB (admin-configurable)
- **Two-level access**:
  - Menu-level: Which menus visible in sidebar
  - Entity-level: CRUD actions on entities

### Default Roles

| Role | Description | isDefault |
|------|-------------|-----------|
| admin | Full system access | false |
| officer | Standard operational role | true |
| *(admin can create more)* | Custom roles | - |

### Frontend Permission Check

**Use hooks from RBAC service**:
```typescript
// src/hooks/usePermissions.ts
import { useQuery } from '@tanstack/react-query';

export function usePermissions(resource: string) {
  const { data } = useQuery({
    queryKey: ['permissions', resource],
    queryFn: () => fetch(`/api/v1/rbac/permissions/${resource}`).then(r => r.json())
  });
  
  return {
    canCreate: data?.canCreate ?? false,
    canEdit: data?.canEdit ?? false,
    canDelete: data?.canDelete ?? false,
    canView: data?.canView ?? false,
  };
}

// Usage in component
const { canCreate, canEdit, canDelete } = usePermissions('users');
```

### Backend Permission Check

```typescript
// src/lib/services/rbac/permissionService.ts
import { checkPermission } from '@/lib/services/rbac/permissionService';

// In API handler
const canCreate = await checkPermission(user.roleId, 'users', 'create');
if (!canCreate) {
  return NextResponse.json({ error: 'Forbidden' }, { status: 403 });
}
```
  await logPermissionCheck(userId, permission, hasPermission);

  return hasPermission;
}
```

## Postman Collection Examples

### List Users with All Features

```json
{
  "name": "List Users",
  "request": {
    "method": "GET",
    "url": {
      "raw": "{{baseUrl}}/api/v1/users?page=1&limit=25&search=john&sortBy=createdAt&order=desc&filter[role]=admin",
      "query": [
        {"key": "page", "value": "1"},
        {"key": "limit", "value": "25"},
        {"key": "search", "value": "john"},
        {"key": "sortBy", "value": "createdAt"},
        {"key": "order", "value": "desc"},
        {"key": "filter[role]", "value": "admin"}
      ]
    },
    "header": [
      {"key": "Authorization", "value": "Bearer {{accessToken}}"},
      {"key": "X-Tenant-ID", "value": "{{tenantId}}"}
    ]
  }
}
```

### Pagination Examples

```json
{
  "name": "Pagination - Page 2",
  "request": {
    "method": "GET",
    "url": "{{baseUrl}}/api/v1/users?page=2&limit=50"
  }
}
```

### Filter Examples

```json
{
  "name": "Filter by Status",
  "request": {
    "method": "GET",
    "url": "{{baseUrl}}/api/v1/users?filter[status]=active"
  }
}
```

## Summary Checklist

For every CRUD feature, ensure:

- [ ] API supports: `page`, `limit`, `search`, `sortBy`, `order`, `filter[field]`
- [ ] Pagination options: 10, 25, 50, 100
- [ ] Global search across searchable columns
- [ ] Sort by all relevant columns (ASC/DESC toggle)
- [ ] Filter dropdowns for enum/string fields
- [ ] Table columns: No. #, data columns, Actions
- [ ] Actions visibility based on RBAC permissions
- [ ] URL state sync for shareable links
- [ ] Debounced search (300ms)
- [ ] Proper error handling
- [ ] Postman collection updated
- [ ] Unit tests for service layer