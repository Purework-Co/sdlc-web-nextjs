# Prisma Migration Standardization

This document defines the standardization for database migrations in this full-stack application.

## 1. Migration Philosophy

### Core Principles
1. **All changes MUST use migration files** - Never use `prisma db push` in production
2. **Migrations are version controlled** - Every change tracked in git
3. **Migrations are irreversible** - Plan before implementing
4. **Test before applying** - Sanity check each migration
5. **Backward compatible** - Avoid breaking changes when possible

### Migration vs db push

| Command | Use Case |
|---------|----------|
| `npx prisma migrate dev` | Development - creating new migrations |
| `npx prisma migrate deploy` | Production - applying migrations |
| `npx prisma db push` | Local development only - prototyping |

## 2. Directory Structure

```
prisma/
├── migrations/
│   ├── 20240115103000_initial_schema/
│   │   ├── migration.sql
│   │   └── sanity_check.sql
│   ├── 20240120150000_create_tenant_table/
│   │   ├── migration.sql
│   │   └── sanity_check.sql
│   └── ...
├── schema.prisma
└── seed.ts
```

## 3. Migration Naming Convention

### Format
```
YYYYMMDDHHMMSS_description.sql
```

### Rules
- **Timestamp**: UTC-based, 14 digits (YYYYMMDDHHMMSS)
- **Description**: Lowercase, underscores for spaces, max 50 chars
- **Examples**:
  - `20240115103000_create_users_table.sql`
  - `20240120150000_add_tenant_id_to_users.sql`
  - `20240125140000_create_rbac_tables.sql`

### NEVER use
- Sequential numbers (`001_create_users.sql`)
- Feature names without timestamp
- Spaces in filenames

## 4. Migration File Structure

### 4.1 Main Migration File (migration.sql)

```sql
-- Migration: 20240115103000_create_users_table
-- Description: Create users table with multi-tenant support
-- Created: 2024-01-15 10:30:00 UTC
-- Author: AI Agent

-- ============================================
-- UP Migration: Apply changes
-- ============================================

BEGIN;

-- Create users table
CREATE TABLE "users" (
    "id" UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    "email" VARCHAR(255) NOT NULL UNIQUE,
    "name" VARCHAR(255),
    "password_hash" VARCHAR(255),
    "role_id" UUID,
    "tenant_id" UUID NOT NULL,
    "is_active" BOOLEAN DEFAULT true,
    "email_verified" TIMESTAMP,
    "created_at" TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    "updated_at" TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Create indexes
CREATE INDEX "users_tenant_id_idx" ON "users"("tenant_id");
CREATE INDEX "users_email_idx" ON "users"("email");
CREATE INDEX "users_role_id_idx" ON "users"("role_id");

-- Add comments
COMMENT ON TABLE "users" IS 'User accounts with multi-tenant isolation';
COMMENT ON COLUMN "users.tenant_id" IS 'Tenant identifier for row-level isolation';

COMMIT;

-- ============================================
-- DOWN Migration: Rollback changes (if needed)
-- ============================================

-- DROP INDEX IF EXISTS "users_tenant_id_idx";
-- DROP INDEX IF EXISTS "users_email_idx";
-- DROP INDEX IF EXISTS "users_role_id_idx";
-- DROP TABLE IF EXISTS "users";
```

### 4.2 Sanity Check File (sanity_check.sql)

```sql
-- Sanity Check: 20240115103000_create_users_table
-- Run this after migration to verify integrity

-- Check table exists
SELECT EXISTS (
   SELECT FROM information_schema.tables
   WHERE table_schema = 'public'
   AND table_name = 'users'
);

-- Check columns
SELECT column_name, data_type, is_nullable, column_default
FROM information_schema.columns
WHERE table_name = 'users'
ORDER BY ordinal_position;

-- Check indexes
SELECT indexname, indexdef
FROM pg_indexes
WHERE tablename = 'users';

-- Check constraints
SELECT conname, contype, pg_get_constraintdef(oid)
FROM pg_constraint
WHERE conrelid = 'users'::regclass;
```

## 5. Migration Types

### 5.1 Schema Migration (CREATE/ALTER)

```sql
-- 20240115103000_create_users_table.sql

BEGIN;

-- Create new table
CREATE TABLE "roles" (
    "id" UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    "name" VARCHAR(50) NOT NULL UNIQUE,
    "description" TEXT,
    "created_at" TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    "updated_at" TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Add foreign key to existing table
ALTER TABLE "users"
ADD CONSTRAINT "users_role_id_fkey"
FOREIGN KEY ("role_id")
REFERENCES "roles"("id")
ON DELETE SET NULL;

COMMIT;
```

### 5.2 Data Migration (Seed/Update)

```sql
-- 20240120150000_seed_default_roles.sql

BEGIN;

-- Insert default roles
INSERT INTO "roles" ("name", "description", "created_at", "updated_at") VALUES
('super_admin', 'Full system access across all tenants', NOW(), NOW()),
('admin', 'Full access within tenant', NOW(), NOW()),
('manager', 'Team management role', NOW(), NOW()),
('user', 'Standard user role', NOW(), NOW()),
('consultant', 'View-only with user interaction', NOW(), NOW())
ON CONFLICT (name) DO NOTHING;

-- Update existing users without role
UPDATE "users"
SET "role_id" = (SELECT "id" FROM "roles" WHERE "name" = 'user')
WHERE "role_id" IS NULL;

COMMIT;
```

### 5.3 Mixed Migration (Schema + Data)

```sql
-- 20240125140000_create_permissions_table_with_seeds.sql

BEGIN;

-- Create permissions table
CREATE TABLE "permissions" (
    "id" UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    "name" VARCHAR(100) NOT NULL UNIQUE,
    "description" TEXT,
    "created_at" TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Create junction table
CREATE TABLE "_role_to_permission" (
    "A" UUID NOT NULL REFERENCES "roles"("id") ON DELETE CASCADE,
    "B" UUID NOT NULL REFERENCES "permissions"("id") ON DELETE CASCADE,
    PRIMARY KEY ("A", "B")
);

-- Seed permissions
INSERT INTO "permissions" ("name", "description") VALUES
('user:read', 'View user list and details'),
('user:write', 'Create and update users'),
('user:delete', 'Delete or deactivate users'),
('role:read', 'View roles'),
('role:write', 'Create and update roles'),
('rbac:manage', 'Full RBAC management'),
('settings:read', 'View settings'),
('settings:write', 'Modify settings')
ON CONFLICT (name) DO NOTHING;

-- Assign permissions to roles
INSERT INTO "_role_to_permission" ("A", "B")
SELECT r.id, p.id
FROM "roles" r
CROSS JOIN "permissions" p
WHERE r.name = 'super_admin';

COMMIT;
```

## 6. Multi-Tenant Migration Patterns

### 6.1 Creating Tenant Table

```sql
-- 20240115103000_create_tenant_table.sql

BEGIN;

CREATE TABLE "tenants" (
    "id" UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    "name" VARCHAR(255) NOT NULL,
    "slug" VARCHAR(100) NOT NULL UNIQUE,
    "domain" VARCHAR(255) UNIQUE,
    "is_active" BOOLEAN DEFAULT true,
    "created_at" TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    "updated_at" TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX "tenants_slug_idx" ON "tenants"("slug");
CREATE INDEX "tenants_domain_idx" ON "tenants"("domain");

-- Add tenant_id to all existing tables
ALTER TABLE "users" ADD COLUMN "tenant_id" UUID NOT NULL;
ALTER TABLE "users" ADD CONSTRAINT "users_tenant_id_fkey"
    FOREIGN KEY ("tenant_id") REFERENCES "tenants"("id") ON DELETE CASCADE;

-- Update existing records with default tenant
INSERT INTO "tenants" ("id", "name", "slug", "created_at", "updated_at")
VALUES (gen_random_uuid(), 'Default Tenant', 'default', NOW(), NOW())
ON CONFLICT DO NOTHING;

UPDATE "users" SET "tenant_id" = (SELECT id FROM "tenants" WHERE slug = 'default')
WHERE "tenant_id" IS NULL;

-- Add NOT NULL constraint after data is populated
ALTER TABLE "users" ALTER COLUMN "tenant_id" SET NOT NULL;

COMMIT;
```

### 6.2 Adding Soft Delete

```sql
-- 20240130100000_add_soft_delete_to_all_tables.sql

BEGIN;

-- Add deleted_at to all tables that need it
ALTER TABLE "users" ADD COLUMN "deleted_at" TIMESTAMP WITH TIME ZONE;
ALTER TABLE "roles" ADD COLUMN "deleted_at" TIMESTAMP WITH TIME ZONE;
ALTER TABLE "permissions" ADD COLUMN "deleted_at" TIMESTAMP WITH TIME ZONE;

-- Create indexes for performance
CREATE INDEX "users_deleted_at_idx" ON "users"("tenant_id", "deleted_at") WHERE "deleted_at" IS NULL;
CREATE INDEX "roles_deleted_at_idx" ON "roles"("deleted_at") WHERE "deleted_at" IS NULL;

COMMIT;
```

## 7. RBAC Migration Patterns

**IMPORTANT**: For complete RBAC schema, see `.opencode/docs/12-rbac-standardization.md`

### 7.1 Complete RBAC Schema (Database-Driven)

```sql
-- 20240201093000_create_rbac_schema.sql
-- See .opencode/docs/12-rbac-standardization.md for complete schema

BEGIN;

-- 1. Roles table
CREATE TABLE "roles" (
    "id" UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    "name" VARCHAR(50) NOT NULL UNIQUE,
    "description" TEXT,
    "is_default" BOOLEAN DEFAULT false,
    "is_active" BOOLEAN DEFAULT true,
    "created_at" TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    "updated_at" TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- 2. Menus table (database-driven, admin-configurable)
CREATE TABLE "menus" (
    "id" UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    "name" VARCHAR(50) NOT NULL UNIQUE,
    "label" VARCHAR(100) NOT NULL,
    "path" VARCHAR(255) NOT NULL,
    "icon" VARCHAR(50),
    "parent_id" UUID REFERENCES "menus"("id") ON DELETE SET NULL,
    "sort_order" INTEGER DEFAULT 0,
    "is_active" BOOLEAN DEFAULT true,
    "created_at" TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    "updated_at" TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- 3. Permissions table
CREATE TABLE "permissions" (
    "id" UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    "name" VARCHAR(100) NOT NULL UNIQUE,
    "resource" VARCHAR(50) NOT NULL,
    "action" VARCHAR(50) NOT NULL,
    "description" TEXT,
    "created_at" TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    UNIQUE("resource", "action")
);

-- 4. Role-Menu access (menu-level control)
CREATE TABLE "role_menus" (
    "role_id" UUID NOT NULL REFERENCES "roles"("id") ON DELETE CASCADE,
    "menu_id" UUID NOT NULL REFERENCES "menus"("id") ON DELETE CASCADE,
    "can_view" BOOLEAN DEFAULT true,
    "can_create" BOOLEAN DEFAULT false,
    "can_edit" BOOLEAN DEFAULT false,
    "can_delete" BOOLEAN DEFAULT false,
    PRIMARY KEY ("role_id", "menu_id")
);

-- 5. Role-Permission (entity-level control)
CREATE TABLE "role_permissions" (
    "role_id" UUID NOT NULL REFERENCES "roles"("id") ON DELETE CASCADE,
    "permission_id" UUID NOT NULL REFERENCES "permissions"("id") ON DELETE CASCADE,
    "created_at" TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY ("role_id", "permission_id")
);

-- 6. Add role_id to users
ALTER TABLE "users" ADD COLUMN "role_id" UUID REFERENCES "roles"("id") ON DELETE SET NULL;

-- Indexes
CREATE INDEX "role_menus_role_id_idx" ON "role_menus"("role_id");
CREATE INDEX "role_menus_menu_id_idx" ON "role_menus"("menu_id");
CREATE INDEX "role_permissions_role_id_idx" ON "role_permissions"("role_id");
CREATE INDEX "role_permissions_permission_idx" ON "role_permissions"("permission_id");
CREATE INDEX "menus_parent_id_idx" ON "menus"("parent_id");

-- Seed default roles
INSERT INTO "roles" ("name", "description", "is_default") VALUES
('admin', 'Full system access', false),
('officer', 'Standard operational role', true)
ON CONFLICT (name) DO NOTHING;

-- Seed default menus
INSERT INTO "menus" ("name", "label", "path", "icon", "sort_order") VALUES
('dashboard', 'Dashboard', '/dashboard', 'LayoutDashboard', 1),
('users', 'Users', '/dashboard/users', 'Users', 2),
('products', 'Products', '/dashboard/products', 'Package', 3),
('booking', 'Booking', '/dashboard/booking', 'Calendar', 4),
('tax', 'Tax', '/dashboard/tax', 'Calculator', 5),
('settings', 'Settings', '/dashboard/settings', 'Settings', 6)
ON CONFLICT (name) DO NOTHING;

-- Seed default permissions
INSERT INTO "permissions" ("resource", "action", "name", "description") VALUES
('user', 'read', 'user:read', 'View users'),
('user', 'create', 'user:create', 'Create users'),
('user', 'edit', 'user:edit', 'Edit users'),
('user', 'delete', 'user:delete', 'Delete users'),
('product', 'read', 'product:read', 'View products'),
('product', 'create', 'product:create', 'Create products'),
('product', 'edit', 'product:edit', 'Edit products'),
('product', 'delete', 'product:delete', 'Delete products'),
('booking', 'read', 'booking:read', 'View bookings'),
('booking', 'create', 'booking:create', 'Create bookings'),
('booking', 'edit', 'booking:edit', 'Edit bookings'),
('booking', 'delete', 'booking:delete', 'Delete bookings')
ON CONFLICT (name) DO NOTHING;

-- Give admin full access to all menus
INSERT INTO "role_menus" (role_id, menu_id, can_view, can_create, can_edit, can_delete)
SELECT r.id, m.id, true, true, true, true
FROM roles r
CROSS JOIN menus m
WHERE r.name = 'admin'
ON CONFLICT DO NOTHING;

-- Give admin all permissions
INSERT INTO "role_permissions" (role_id, permission_id)
SELECT r.id, p.id
FROM roles r
CROSS JOIN permissions p
WHERE r.name = 'admin'
ON CONFLICT DO NOTHING;

COMMIT;
```

## 8. Enum Migration Patterns

### 8.1 Create Enum Type

```sql
-- 20240201140000_create_user_status_enum.sql

BEGIN;

-- Create enum type
CREATE TYPE "user_status" AS ENUM ('active', 'inactive', 'suspended', 'pending');

-- Add column with enum type
ALTER TABLE "users" ADD COLUMN "status" USER_STATUS DEFAULT 'active';

-- Add check constraint
ALTER TABLE "users" ADD CONSTRAINT "user_status_check"
CHECK (status IN ('active', 'inactive', 'suspended', 'pending'));

COMMIT;
```

### 8.2 Add New Enum Value

```sql
-- 20240202100000_add_verified_to_user_status.sql

BEGIN;

-- Add new enum value (PostgreSQL allows this without rewrite)
ALTER TYPE "user_status" ADD VALUE IF NOT EXISTS 'verified';

-- Update existing records
UPDATE "users" SET "status" = 'verified' WHERE "email_verified" IS NOT NULL;

COMMIT;
```

## 9. Index Migration Patterns

### 9.1 Performance Indexes

```sql
-- 20240203150000_add_performance_indexes.sql

BEGIN;

-- Composite index for tenant isolation + common queries
CREATE INDEX IF NOT EXISTS "users_tenant_active_idx"
ON "users"("tenant_id", "is_active", "created_at DESC");

-- Partial index for active records only
CREATE INDEX IF NOT EXISTS "orders_tenant_status_idx"
ON "orders"("tenant_id", "status")
WHERE "status" IN ('pending', 'processing');

-- Text search index for global search
CREATE INDEX IF NOT EXISTS "users_email_search_idx"
ON "users" USING gin("email" gin_trgm_ops);

-- Unique constraint with composite tenant scope
CREATE UNIQUE INDEX IF NOT EXISTS "products_tenant_sku_idx"
ON "products"("tenant_id", "sku")
WHERE "deleted_at" IS NULL;

COMMIT;
```

## 10. Migration Workflow

### 10.1 Creating Migration in Development

```bash
# Create migration from schema changes
npx prisma migrate dev --name description_of_change

# Example
npx prisma migrate dev --name add_user_phone_column
```

### 10.2 Applying Migration in Production

```bash
# Deploy migrations (CI/CD)
npx prisma migrate deploy

# Or with schema reset (if needed, DANGEROUS)
npx prisma migrate resolve --applied 20240115103000_description
```

### 10.3 Rolling Back Migration

```bash
# Rollback last migration (development only)
npx prisma migrate rollback

# Or mark as not applied (production)
npx prisma migrate resolve --rolled-back 20240115103000_description
```

## 11. Best Practices

### 11.1 Always Include

- [ ] Clear comments explaining the change
- [ ] BEGIN/COMMIT transactions
- [ ] DOWN migration section (for reference)
- [ ] Indexes for foreign keys
- [ ] Comments on tables/columns
- [ ] Sanity check queries

### 11.2 Never Do

- [ ] Drop table without backup/migration path
- [ ] Rename columns (use add + copy + drop)
- [ ] Change column type without migration
- [ ] Remove NOT NULL without default
- [ ] Delete data without backup

### 11.3 Migration Order

When creating new feature with multiple changes:

1. **Base tables first** (Tenant, Roles)
2. **Dependent tables second** (Users, Permissions)
3. **Data migrations third** (Seeds, Updates)
4. **Indexes last** (Performance)

### 11.4 Testing Migrations

```bash
# Reset database and reapply all migrations
npx prisma migrate reset

# Apply migrations to fresh database
npx prisma migrate deploy

# Verify schema matches
npx prisma db execute --stdin < sanity_check.sql
```

## 12. Migration Checklist

For every database change:

- [ ] Create named migration file with timestamp
- [ ] Include UP migration with transactions
- [ ] Include DOWN migration for reference
- [ ] Add indexes for foreign keys
- [ ] Add table/column comments
- [ ] Create sanity check file
- [ ] Test on local database
- [ ] Verify on staging before production
- [ ] Document in changelog
- [ ] Update Prisma schema to match

## 13. Schema.prisma Sync

After creating migration, always update schema.prisma:

```prisma
// schema.prisma - must match migration

model User {
  id            String    @id @default(uuid())
  email         String    @unique
  name          String?
  passwordHash  String?   @map("password_hash")
  roleId        String?   @map("role_id")
  role          Role?     @relation(fields: [roleId], references: [id])
  tenantId      String    @map("tenant_id")
  tenant        Tenant    @relation(fields: [tenantId], references: [id])
  isActive      Boolean   @default(true) @map("is_active")
  status        UserStatus @default(active)
  emailVerified DateTime? @map("email_verified")
  createdAt     DateTime  @default(now()) @map("created_at")
  updatedAt     DateTime  @updatedAt @map("updated_at")
  deletedAt     DateTime? @map("deleted_at")

  @@index([tenantId])
  @@index([email])
  @@map("users")
}

enum UserStatus {
  active
  inactive
  suspended
  pending
  verified
}
```

## 14. Common Migration Patterns Reference

| Scenario | Pattern |
|----------|---------|
| Add new table | CREATE TABLE + indexes + FK + comments |
| Add column | ALTER TABLE ADD COLUMN + default + index |
| Add FK | ALTER TABLE ADD CONSTRAINT |
| Add enum | CREATE TYPE + ALTER TABLE |
| Seed data | INSERT ON CONFLICT DO NOTHING |
| Rename table | CREATE new + COPY data + DROP old |
| Soft delete | Add deleted_at + partial index |
| Tenant isolation | Add tenant_id + FK + index |
| RBAC | Create permissions + junction table |