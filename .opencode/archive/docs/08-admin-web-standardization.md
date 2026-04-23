# Admin Web Standardization

This document defines the standardization for building admin panels in this full-stack application.

## 1. Layout Structure

### 1.1 Overall Layout
```
+------------------+--------------------------------+
|                  |          Top Navbar            |
|    Sidebar      +--------------------------------+
|    (Left)       |                                |
|                 |         Main Content           |
|   Collapsible   |         (Right Area)            |
|                 |                                |
+------------------+--------------------------------+
```

- **Layout Type**: Two-column (Sidebar + Main Content)
- **Full Height**: 100vh viewport
- **Sidebar**: Fixed left, collapsible
- **Navbar**: Fixed top, full width
- **Content Area**: Scrollable independently

## 2. Sidebar Navigation

### 2.1 Sidebar Behavior
- **Collapsible**: Toggle button to expand/collapse
- **Expanded Width**: 240px (full labels)
- **Collapsed Width**: 64px (icons only)
- **Animation**: Smooth transition (200-300ms)
- **Toggle Button**: Hamburger or chevron icon at top

### 2.2 Navigation Item Order

```
📊 Dashboard          → /dashboard (DEFAULT)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[CRUD Entities]
   ├─ Users           → /dashboard/users
   ├─ Products        → /dashboard/products
   ├─ Orders          → /dashboard/orders
   └─ ... (dynamic)   → /dashboard/[entity]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Settings]
   ├─ RBAC            → /dashboard/rbac
   ├─ Roles           → /dashboard/roles
   ├─ Permissions     → /dashboard/permissions
   ├─ General         → /dashboard/settings
   └─ ... (if exist)  → /dashboard/settings/[sub]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### 2.3 Sidebar Structure

| Section | Description | Route Pattern |
|---------|-------------|---------------|
| Dashboard | Default landing page | `/dashboard` |
| CRUD Entities | Dynamic entity list | `/dashboard/[entity]` |
| Settings | RBAC + General | `/dashboard/rbac`, `/dashboard/settings` |

### 2.4 Sidebar Component Implementation

```typescript
// src/components/layout/sidebar.tsx
'use client';

import { useState } from 'react';
import Link from 'next/link';
import { usePathname } from 'next/navigation';
import { cn } from '@/lib/utils';
import {
  LayoutDashboard,
  ChevronLeft,
  ChevronRight,
  Users,
  Box,
  ShoppingCart,
  Settings,
  Shield,
  UserCog,
} from 'lucide-react';

interface SidebarItem {
  title: string;
  href: string;
  icon: React.ReactNode;
  permission?: string;
  children?: SidebarItem[];
}

const sidebarItems: SidebarItem[] = [
  {
    title: 'Dashboard',
    href: '/dashboard',
    icon: <LayoutDashboard className="h-5 w-5" />,
  },
  {
    title: 'Users',
    href: '/dashboard/users',
    icon: <Users className="h-5 w-5" />,
    permission: 'user:read',
  },
  {
    title: 'Products',
    href: '/dashboard/products',
    icon: <Box className="h-5 w-5" />,
    permission: 'product:read',
  },
  {
    title: 'Orders',
    href: '/dashboard/orders',
    icon: <ShoppingCart className="h-5 w-5" />,
    permission: 'order:read',
  },
  // Add more CRUD entities here
];

const settingsItems: SidebarItem[] = [
  {
    title: 'RBAC',
    href: '/dashboard/rbac',
    icon: <Shield className="h-5 w-5" />,
    permission: 'rbac:manage',
  },
  {
    title: 'Roles',
    href: '/dashboard/roles',
    icon: <UserCog className="h-5 w-5" />,
    permission: 'role:read',
  },
  {
    title: 'Settings',
    href: '/dashboard/settings',
    icon: <Settings className="h-5 w-5" />,
    permission: 'settings:read',
  },
];

export function Sidebar() {
  const [isCollapsed, setIsCollapsed] = useState(false);
  const pathname = usePathname();

  const isActive = (href: string) => pathname === href || pathname.startsWith(href + '/');

  return (
    <aside
      className={cn(
        'fixed left-0 top-0 z-40 h-screen border-r bg-background transition-all duration-300',
        isCollapsed ? 'w-16' : 'w-60'
      )}
    >
      {/* Toggle Button */}
      <div className="flex h-14 items-center justify-end border-b px-2">
        <button
          onClick={() => setIsCollapsed(!isCollapsed)}
          className="flex h-8 w-8 items-center justify-center rounded-md hover:bg-muted"
        >
          {isCollapsed ? (
            <ChevronRight className="h-4 w-4" />
          ) : (
            <ChevronLeft className="h-4 w-4" />
          )}
        </button>
      </div>

      {/* Navigation */}
      <nav className="flex flex-col gap-1 p-2">
        {/* Dashboard */}
        <Link
          href="/dashboard"
          className={cn(
            'flex items-center gap-3 rounded-md px-3 py-2 text-sm font-medium transition-colors',
            isActive('/dashboard') && !pathname.includes('/dashboard/')
              ? 'bg-primary text-primary-foreground'
              : 'hover:bg-muted'
          )}
        >
          <LayoutDashboard className="h-5 w-5 flex-shrink-0" />
          {!isCollapsed && <span>Dashboard</span>}
        </Link>

        {/* Divider */}
        <div className="my-2 border-t" />

        {/* CRUD Entities */}
        {!isCollapsed && (
          <div className="px-3 py-1 text-xs font-semibold text-muted-foreground">
            Entities
          </div>
        )}
        {sidebarItems.slice(1).map((item) => (
          <Link
            key={item.href}
            href={item.href}
            className={cn(
              'flex items-center gap-3 rounded-md px-3 py-2 text-sm font-medium transition-colors',
              isActive(item.href)
                ? 'bg-primary text-primary-foreground'
                : 'hover:bg-muted'
            )}
          >
            {item.icon}
            {!isCollapsed && <span>{item.title}</span>}
          </Link>
        ))}

        {/* Divider */}
        <div className="my-2 border-t" />

        {/* Settings */}
        {!isCollapsed && (
          <div className="px-3 py-1 text-xs font-semibold text-muted-foreground">
            Settings
          </div>
        )}
        {settingsItems.map((item) => (
          <Link
            key={item.href}
            href={item.href}
            className={cn(
              'flex items-center gap-3 rounded-md px-3 py-2 text-sm font-medium transition-colors',
              isActive(item.href)
                ? 'bg-primary text-primary-foreground'
                : 'hover:bg-muted'
            )}
          >
            {item.icon}
            {!isCollapsed && <span>{item.title}</span>}
          </Link>
        ))}
      </nav>
    </aside>
  );
}
```

## 3. Top Navbar

### 3.1 Navbar Structure

```
+------------------------------------------------------------------+
|  [Logo/App Name]                             [User Profile] [Logout]  |
+------------------------------------------------------------------+
```

| Position | Component | Description |
|----------|-----------|-------------|
| Left | Logo + App Name | Clickable, returns to dashboard |
| Right | User Profile | Name, email, role, avatar |
| Right | Logout Button | Icon + text or icon-only |

### 3.2 Navbar Component Implementation

```typescript
// src/components/layout/top-navbar.tsx
'use client';

import { useRouter } from 'next/navigation';
import { Button } from '@/components/ui/button';
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuLabel,
  DropdownMenuSeparator,
  DropdownMenuTrigger,
} from '@/components/ui/dropdown-menu';
import { Avatar, AvatarFallback, AvatarImage } from '@/components/ui/avatar';
import { LogOut, User, Settings } from 'lucide-react';

interface UserData {
  name: string;
  email: string;
  role: string;
  avatar?: string;
}

interface TopNavbarProps {
  appName?: string;
  user: UserData;
}

export function TopNavbar({ appName = 'My App', user }: TopNavbarProps) {
  const router = useRouter();

  const handleLogout = async () => {
    // Clear session
    await fetch('/api/auth/logout', { method: 'POST' });
    router.push('/login');
  };

  const getInitials = (name: string) => {
    return name
      .split(' ')
      .map((n) => n[0])
      .join('')
      .toUpperCase()
      .slice(0, 2);
  };

  return (
    <header className="fixed top-0 right-0 z-30 flex h-14 w-full items-center justify-between border-b bg-background px-4 lg:px-6">
      {/* Logo / App Name */}
      <div className="flex items-center gap-2">
        <div className="flex h-8 w-8 items-center justify-center rounded-lg bg-primary">
          <span className="text-sm font-bold text-primary-foreground">
            {appName.charAt(0)}
          </span>
        </div>
        <span className="text-lg font-semibold">{appName}</span>
      </div>

      {/* Right Side: User Profile + Logout */}
      <div className="flex items-center gap-4">
        <DropdownMenu>
          <DropdownMenuTrigger asChild>
            <Button variant="ghost" className="relative h-10 w-10 rounded-full">
              <Avatar className="h-10 w-10">
                <AvatarImage src={user.avatar} alt={user.name} />
                <AvatarFallback>{getInitials(user.name)}</AvatarFallback>
              </Avatar>
            </Button>
          </DropdownMenuTrigger>
          <DropdownMenuContent className="w-56" align="end" forceMount>
            <DropdownMenuLabel className="font-normal">
              <div className="flex flex-col space-y-1">
                <p className="text-sm font-medium leading-none">{user.name}</p>
                <p className="text-xs leading-none text-muted-foreground">
                  {user.email}
                </p>
                <p className="text-xs leading-none text-muted-foreground mt-1">
                  Role: {user.role}
                </p>
              </div>
            </DropdownMenuLabel>
            <DropdownMenuSeparator />
            <DropdownMenuItem onClick={() => router.push('/dashboard/profile')}>
              <User className="mr-2 h-4 w-4" />
              Profile
            </DropdownMenuItem>
            <DropdownMenuItem onClick={() => router.push('/dashboard/settings')}>
              <Settings className="mr-2 h-4 w-4" />
              Settings
            </DropdownMenuItem>
            <DropdownMenuSeparator />
            <DropdownMenuItem onClick={handleLogout} className="text-red-600">
              <LogOut className="mr-2 h-4 w-4" />
              Log out
            </DropdownMenuItem>
          </DropdownMenuContent>
        </DropdownMenu>
      </div>
    </header>
  );
}
```

### 3.3 Direct Logout Button (Alternative)

```typescript
// Minimal logout button variant
<Button
  variant="ghost"
  size="sm"
  onClick={handleLogout}
  className="text-muted-foreground hover:text-foreground"
>
  <LogOut className="mr-2 h-4 w-4" />
  <span className="hidden sm:inline">Logout</span>
</Button>
```

## 4. Page Layout with Sidebar

### 4.1 Dashboard Layout

```typescript
// src/app/(dashboard)/layout.tsx
import { Sidebar } from '@/components/layout/sidebar';
import { TopNavbar } from '@/components/layout/top-navbar';

export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <div className="min-h-screen">
      {/* Sidebar - Fixed left */}
      <Sidebar />

      {/* Main content area */}
      <div className="pl-60 transition-all duration-300">
        {/* Top Navbar - Fixed top */}
        <TopNavbar
          appName="My App"
          user={{
            name: 'John Doe',
            email: 'john@example.com',
            role: 'Admin',
          }}
        />

        {/* Page Content */}
        <main className="mt-14 min-h-[calc(100vh-3.5rem)] p-6">
          {children}
        </main>
      </div>
    </div>
  );
}
```

### 4.2 Responsive Layout Adjustment

```typescript
// For collapsed sidebar on tablet/mobile
// Update layout.tsx to handle isCollapsed state from sidebar
// Use CSS: pl-16 when sidebar is collapsed, pl-60 when expanded
```

## 5. RBAC Integration

**IMPORTANT**: For complete RBAC implementation, see `.opencode/docs/12-rbac-standardization.md`

### 5.1 Sidebar Visibility Based on Role

**Database-driven**: Menus are fetched from database based on user's role.

```typescript
// src/components/layout/sidebar.tsx
'use client';

import { useQuery } from '@tanstack/react-query';

export function Sidebar() {
  // Fetch menus from API based on role
  const { data } = useQuery({
    queryKey: ['menus'],
    queryFn: () => fetch('/api/v1/rbac/menus').then(r => r.json())
  });

  const menus = data?.data || [];

  return (
    <nav>
      {menus.map(menu => (
        <MenuItem key={menu.id} menu={menu} />
      ))}
    </nav>
  );
}
```

### 5.2 Menu Access from API

```typescript
// GET /api/v1/rbac/menus
// Response: list of menus visible to user's role with access flags

{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "name": "users",
      "label": "Users",
      "path": "/dashboard/users",
      "icon": "Users",
      "access": {
        "canView": true,
        "canCreate": true,
        "canEdit": true,
        "canDelete": true
      }
    }
  ]
}
```

### 5.3 Role-Based Access

| Role | Menu Access | Entity Actions |
|------|-------------|----------------|
| admin | All menus (from DB) | Full CRUD (from permissions) |
| officer | Limited menus | Limited CRUD |
| *(admin configurable)* | Via UI | Via RBAC |

### 5.4 Permission Check Hook

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
  };
}
```

## 6. Component Dependencies

### 6.1 Shadcn Components Used

| Component | Purpose |
|-----------|---------|
| `Button` | Actions, logout |
| `Avatar` | User profile image |
| `DropdownMenu` | User profile dropdown |
| `Collapsible` | Sidebar collapse (optional) |
| `Sheet` | Mobile sidebar (optional) |

### 6.2 Icons (Lucide React)

- Dashboard: `LayoutDashboard`
- Users: `Users`
- Products: `Box`
- Orders: `ShoppingCart`
- Settings: `Settings`
- RBAC: `Shield`
- Roles: `UserCog`
- Logout: `LogOut`
- Profile: `User`

## 7. URL Structure Summary

```
/                          → Landing page (public)
/login                     → Login page
/register                  → Register page

/dashboard                 → Dashboard (default after login)
/dashboard/users           → Users CRUD
/dashboard/products        → Products CRUD
/dashboard/orders          → Orders CRUD
/dashboard/rbac            → RBAC Management
/dashboard/roles           → Role Management
/dashboard/settings        → General Settings
/dashboard/profile         → User Profile
```

## 8. Implementation Checklist

For every new CRUD entity added to admin panel:

- [ ] Add sidebar navigation item with correct icon
- [ ] Create route `/dashboard/[entity]`
- [ ] Add CRUD page components (list, create, edit, view)
- [ ] Integrate RBAC permissions
- [ ] Update Postman collection if API changed
- [ ] Test responsive behavior on mobile/tablet

## 9. Customization

### 9.1 App Name Configuration

```typescript
// Environment variable or tenant config
const appName = process.env.NEXT_PUBLIC_APP_NAME || 'My App';
```

### 9.2 Sidebar Width Customization

```typescript
// In sidebar.tsx, adjust these values:
const EXPANDED_WIDTH = '240px';  // or 'w-60'
const COLLAPSED_WIDTH = '64px';  // or 'w-16'
```

### 9.3 Theme Support

The layout supports both light and dark mode via shadcn/ui's theme system. No additional changes needed - components automatically adapt.