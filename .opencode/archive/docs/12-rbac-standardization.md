# RBAC Standardization

This document defines the complete Role-Based Access Control (RBAC) system for this full-stack application.

## 1. RBAC Philosophy

### Core Principles
1. **Database-driven** - All roles, menus, and permissions stored in database
2. **Admin-configurable** - Admin can create, edit, delete roles via UI
3. **Flexible** - Both menu-level (visibility) and entity-level (actions) control
4. **Hierarchical** - Roles inherit permissions through explicit assignment
5. **Audit-able** - All access attempts logged to telemetry

### What RBAC Controls

| Level | Controls | Example |
|-------|----------|---------|
| **Menu Level** | Which menus user can see in sidebar | Officer sees "booking", not "settings" |
| **Entity Level** | What actions user can perform | Supervisor can view but not edit users |
| **Field Level** | (Future) Which fields user can access | Not in scope v1.0 |

## 2. Database Schema

### 2.1 Core Models

```prisma
// Role - Users are assigned to roles
model Role {
  id          String   @id @default(uuid())
  name        String   @unique  // admin, officer, accountant, supervisor
  description String?
  isDefault   Boolean  @default(false)  // Default role for new users
  isActive    Boolean  @default(true)
  
  // Relations
  users       User[]
  menuAccess  RoleMenu[]
  permissions RolePermission[]
  
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  
  @@map("roles")
}

// Menu - Sidebar navigation items (database-driven)
model Menu {
  id          String   @id @default(uuid())
  name        String   @unique  // users, products, booking, tax, reports
  label       String   // Display label: "Users", "Products", "Tax"
  path        String   // Route path: /dashboard/users
  icon        String?  // Icon name from Lucide: Users, Box, Calendar
  parentId    String?  // Parent menu for nested menus
  sortOrder   Int      @default(0)
  isActive    Boolean  @default(true)
  
  // Relations
  children    Menu[]  @relation("MenuHierarchy")
  parent      Menu?   @relation("MenuHierarchy", fields: [parentId], references: [id])
  roleAccess  RoleMenu[]
  
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  
  @@map("menus")
}

// RoleMenu - Role's access to menus (visibility + actions on menu)
model RoleMenu {
  roleId    String
  menuId    String
  canView   Boolean  @default(true)   // Can see menu in sidebar
  canCreate Boolean  @default(false)  // Can create in this menu
  canEdit   Boolean  @default(false)  // Can edit in this menu
  canDelete Boolean  @default(false)  // Can delete in this menu
  
  role      Role     @relation(fields: [roleId], references: [id], onDelete: Cascade)
  menu      Menu     @relation(fields: [menuId], references: [id], onDelete: Cascade)
  
  @@id([roleId, menuId])
  @@map("role_menus")
}

// Permission - Action-level permissions
model Permission {
  id          String   @id @default(uuid())
  name        String   @unique  // user:read, user:write, user:delete
  resource    String   // user, product, booking, tax
  action      String   // read, write, edit, delete
  description String?
  
  roles       RolePermission[]
  
  createdAt DateTime @default(now())
  
  @@map("permissions")
}

// RolePermission - Role's action permissions
model RolePermission {
  roleId       String
  permissionId String
  
  role         Role       @relation(fields: [roleId], references: [id], onDelete: Cascade)
  permission   Permission @relation(fields: [permissionId], references: [id], onDelete: Cascade)
  
  @@id([roleId, permissionId])
  @@map("role_permissions")
}
```

### 2.2 User Model Integration

```prisma
model User {
  id        String   @id @default(uuid())
  email     String   @unique
  name      String?
  password  String?  // Nullable for OAuth users
  roleId    String?
  tenantId  String
  
  // Relations
  role      Role?    @relation(fields: [roleId], references: [id])
  tenant    Tenant   @relation(fields: [tenantId], references: [id])
  
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  
  @@index([roleId])
  @@map("users")
}
```

## 3. Default Roles

### 3.1 Initial Roles (Seed Data)

| Role | Description | Default Menu Access | Default Permissions |
|------|-------------|---------------------|---------------------|
| **admin** | Full system access | All menus (view all) | All permissions |
| **officer** | Standard operational role | dashboard, booking, customers | Limited CRUD |

### 3.2 Admin Creates Additional Roles

Admin can create:
- `accountant` - Only accesses tax and reports menus
- `front_office` - Only accesses booking menu
- `supervisor` - Views multiple menus but read-only
- Any custom role as needed

### 3.3 Default Role for New Users

```typescript
// When creating new user, assign default role
const defaultRole = await prisma.role.findFirst({
  where: { isDefault: true }
});
```

## 4. Menu Access Control

### 4.1 Concept

**Menu Access** controls which menus appear in the sidebar. Each role has explicit menu visibility.

```typescript
// Example: Role → Menu mapping
const roleMenuAccess = {
  admin: {
    dashboard: { canView: true, canCreate: true, canEdit: true, canDelete: true },
    users: { canView: true, canCreate: true, canEdit: true, canDelete: true },
    products: { canView: true, canCreate: true, canEdit: true, canDelete: true },
    booking: { canView: true, canCreate: true, canEdit: true, canDelete: true },
    tax: { canView: true, canCreate: true, canEdit: true, canDelete: true },
    settings: { canView: true, canCreate: true, canEdit: true, canDelete: true },
  },
  officer: {
    dashboard: { canView: true },
    booking: { canView: true, canCreate: true, canEdit: true, canDelete: false },
    customers: { canView: true, canCreate: true, canEdit: false },
  },
  accountant: {
    dashboard: { canView: true },
    tax: { canView: true, canCreate: true, canEdit: true, canDelete: false },
    reports: { canView: true },
  },
  supervisor: {
    dashboard: { canView: true },
    users: { canView: true, canCreate: false, canEdit: false, canDelete: false },
    products: { canView: true, canCreate: false, canEdit: false, canDelete: false },
    booking: { canView: true, canCreate: false, canEdit: false, canDelete: false },
  },
};
```

### 4.2 Implementation - Get Menus for Role

```typescript
// src/lib/services/rbac/menuService.ts
export async function getMenusForRole(roleId: string): Promise<MenuWithAccess[]> {
  const roleMenus = await prisma.roleMenu.findMany({
    where: {
      roleId,
      canView: true,
    },
    include: {
      menu: {
        include: {
          children: {
            include: {
              roleAccess: {
                where: { roleId },
              },
            },
          },
        },
      },
    },
  });

  return roleMenus.map(rm => ({
    ...rm.menu,
    access: {
      canCreate: rm.canCreate,
      canEdit: rm.canEdit,
      canDelete: rm.canDelete,
    },
  }));
}
```

### 4.3 Sidebar Integration

```typescript
// src/components/layout/sidebar.tsx
'use client';

import { useQuery } from '@tanstack/react-query';

export function Sidebar() {
  const { data: menuData } = useQuery({
    queryKey: ['menus'],
    queryFn: () => fetch('/api/rbac/menus').then(res => res.json())
  });

  const menus = menuData?.data || [];

  return (
    <nav>
      {menus.map(menu => (
        <MenuItem key={menu.id} menu={menu} />
      ))}
    </nav>
  );
}
```

## 5. Entity Access Control

### 5.1 Concept

**Entity Access** controls what actions user can perform on entities (CRUD operations).

```typescript
// Example: Role → Entity → Actions
const roleEntityAccess = {
  admin: {
    users: { create: true, read: true, edit: true, delete: true },
    products: { create: true, read: true, edit: true, delete: true },
  },
  officer: {
    users: { create: false, read: true, edit: false, delete: false },
    booking: { create: true, read: true, edit: true, delete: false },
  },
  supervisor: {
    users: { create: false, read: true, edit: false, delete: false },
    products: { create: false, read: true, edit: false, delete: false },
    booking: { create: false, read: true, edit: false, delete: false },
  },
};
```

### 5.2 Permission Check in Service Layer

```typescript
// src/lib/services/rbac/permissionService.ts
export async function checkPermission(
  roleId: string,
  resource: string,
  action: 'create' | 'read' | 'edit' | 'delete'
): Promise<boolean> {
  // Map action to permission
  const actionToPermission: Record<string, string> = {
    create: `${resource}:create`,
    read: `${resource}:read`,
    edit: `${resource}:edit`,
    delete: `${resource}:delete`,
  };

  const permissionName = actionToPermission[action];

  const hasPermission = await prisma.rolePermission.findFirst({
    where: {
      roleId,
      permission: {
        name: permissionName,
      },
    },
  });

  // Log to telemetry
  await logPermissionCheck(roleId, permissionName, !!hasPermission);

  return !!hasPermission;
}
```

### 5.3 Using Permission in API

```typescript
// src/app/api/v1/users/route.ts
export async function POST(request: NextRequest) {
  const auth = await authMiddleware(request);
  if (auth instanceof NextResponse) return auth;

  const { user, tenantId } = auth;

  // Check permission
  const canCreate = await checkPermission(user.roleId, 'users', 'create');
  if (!canCreate) {
    return NextResponse.json(
      { success: false, error: { code: 'FORBIDDEN', message: 'No permission to create user' } },
      { status: 403 }
    );
  }

  // ... proceed with creation
}
```

### 5.4 Using Permission in Frontend

```typescript
// src/components/users/user-list.tsx
'use client';

export function UserList() {
  const { data: permissions } = useQuery({
    queryKey: ['permissions', 'users'],
    queryFn: () => fetch('/api/rbac/permissions/users').then(res => res.json())
  });

  const canCreate = permissions?.canCreate ?? false;
  const canEdit = permissions?.canEdit ?? false;
  const canDelete = permissions?.canDelete ?? false;

  return (
    <div>
      {canCreate && <Button>Create User</Button>}
      {/* ... table with conditional action buttons */}
    </div>
  );
}
```

## 6. Admin Role Management UI

### 6.1 Role Management Page

Admin can manage roles via UI:
- `/dashboard/roles` - List all roles
- `/dashboard/roles/new` - Create new role
- `/dashboard/roles/[id]` - Edit role

### 6.2 Role Edit Interface

```typescript
// Admin UI for editing role permissions
interface RoleEditForm {
  name: string;
  description: string;
  isDefault: boolean;
  menuAccess: {
    menuId: string;
    canView: boolean;
    canCreate: boolean;
    canEdit: boolean;
    canDelete: boolean;
  }[];
  permissions: string[]; // permission IDs
}
```

### 6.3 Copy Permissions Feature

```typescript
// Admin can copy permissions from existing role
async function copyRolePermissions(sourceRoleId: string, targetRoleId: string) {
  const sourceMenus = await prisma.roleMenu.findMany({
    where: { roleId: sourceRoleId }
  });

  const sourcePermissions = await prisma.rolePermission.findMany({
    where: { roleId: sourceRoleId }
  });

  // Create menu access for target role
  await prisma.roleMenu.createMany({
    data: sourceMenus.map(m => ({
      roleId: targetRoleId,
      menuId: m.menuId,
      canView: m.canView,
      canCreate: m.canCreate,
      canEdit: m.canEdit,
      canDelete: m.canDelete,
    }))
  });

  // Create permissions for target role
  await prisma.rolePermission.createMany({
    data: sourcePermissions.map(p => ({
      roleId: targetRoleId,
      permissionId: p.permissionId,
    }))
  });
}
```

## 7. Seed Data

### 7.1 Default Roles

```typescript
// prisma/seed.ts
const defaultRoles = [
  {
    name: 'admin',
    description: 'Full system access with all permissions',
    isDefault: false,
  },
  {
    name: 'officer',
    description: 'Standard operational role with limited access',
    isDefault: true, // Default for new users
  },
];

// Seed roles
for (const role of defaultRoles) {
  await prisma.role.upsert({
    where: { name: role.name },
    update: {},
    create: role,
  });
}
```

### 7.2 Default Menus

```typescript
// prisma/seed.ts
const defaultMenus = [
  { name: 'dashboard', label: 'Dashboard', path: '/dashboard', icon: 'LayoutDashboard', sortOrder: 1 },
  { name: 'users', label: 'Users', path: '/dashboard/users', icon: 'Users', sortOrder: 2 },
  { name: 'products', label: 'Products', path: '/dashboard/products', icon: 'Package', sortOrder: 3 },
  { name: 'booking', label: 'Booking', path: '/dashboard/booking', icon: 'Calendar', sortOrder: 4 },
  { name: 'customers', label: 'Customers', path: '/dashboard/customers', icon: 'UserCog', sortOrder: 5 },
  { name: 'tax', label: 'Tax', path: '/dashboard/tax', icon: 'Calculator', sortOrder: 6 },
  { name: 'reports', label: 'Reports', path: '/dashboard/reports', icon: 'BarChart', sortOrder: 7 },
  { name: 'rbac', label: 'RBAC', path: '/dashboard/rbac', icon: 'Shield', sortOrder: 8 },
  { name: 'settings', label: 'Settings', path: '/dashboard/settings', icon: 'Settings', sortOrder: 9 },
];
```

### 7.3 Default Permissions

```typescript
// prisma/seed.ts
const defaultPermissions = [
  // Users
  { name: 'users:create', resource: 'users', action: 'create' },
  { name: 'users:read', resource: 'users', action: 'read' },
  { name: 'users:edit', resource: 'users', action: 'edit' },
  { name: 'users:delete', resource: 'users', action: 'delete' },
  // Products
  { name: 'products:create', resource: 'products', action: 'create' },
  { name: 'products:read', resource: 'products', action: 'read' },
  { name: 'products:edit', resource: 'products', action: 'edit' },
  { name: 'products:delete', resource: 'products', action: 'delete' },
  // Booking
  { name: 'booking:create', resource: 'booking', action: 'create' },
  { name: 'booking:read', resource: 'booking', action: 'read' },
  { name: 'booking:edit', resource: 'booking', action: 'edit' },
  { name: 'booking:delete', resource: 'booking', action: 'delete' },
];
```

## 8. API Endpoints

### 8.1 RBAC API

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/rbac/roles` | List all roles |
| POST | `/api/v1/rbac/roles` | Create role |
| GET | `/api/v1/rbac/roles/:id` | Get role details |
| PATCH | `/api/v1/rbac/roles/:id` | Update role |
| DELETE | `/api/v1/rbac/roles/:id` | Delete role |
| GET | `/api/v1/rbac/menus` | Get menus for current user role |
| GET | `/api/v1/rbac/permissions/:resource` | Get permissions for resource |

### 8.2 Role Response Format

```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "admin",
    "description": "Full system access",
    "isDefault": false,
    "isActive": true,
    "menuAccess": [
      { "menuId": "uuid", "canView": true, "canCreate": true, "canEdit": true, "canDelete": true }
    ],
    "permissions": [
      { "id": "uuid", "name": "users:create" }
    ]
  }
}
```

## 9. Implementation Checklist

### Database
- [ ] Create Role, Menu, Permission models
- [ ] Create RoleMenu, RolePermission junction tables
- [ ] Add roleId to User model
- [ ] Create migration files

### Seed Data
- [ ] Seed default roles (admin, officer)
- [ ] Seed default menus
- [ ] Seed default permissions

### Backend
- [ ] Create RBAC service layer
- [ ] Create menu access API
- [ ] Create permission check utility
- [ ] Integrate with existing APIs

### Frontend
- [ ] Update sidebar to fetch menus based on role
- [ ] Create role management UI
- [ ] Create permission check hooks
- [ ] Hide/disable actions based on permissions

### Testing
- [ ] Test role-menu access
- [ ] Test permission checks
- [ ] Test admin role management
- [ ] Test multi-tenant RBAC isolation

## 10. Best Practices

### Do
- Use database-driven menus (admin can change via UI)
- Log all permission checks to telemetry
- Use roleId for permission checks (not userId)
- Provide copy-from-existing-role feature

### Don't
- Hard-code menus in code
- Check permissions per-user (use role)
- Skip permission checks in any API
- Allow users without roles

## 11. Related Documentation

| Document | Location |
|----------|----------|
| SDLC Guidance | `.opencode/docs/01-sdlc-guidance.md` |
| CRUD Guidance | `.opencode/docs/07-crud-guidance.md` |
| Admin Web Standardization | `.opencode/docs/08-admin-web-standardization.md` |
| Prisma Migration | `.opencode/docs/09-prisma-migration-standardization.md` |
| Changelog | `.opencode/docs/changelog/CHANGELOG.md` |