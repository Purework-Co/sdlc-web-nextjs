---
name: database
description: Prisma database operations, migrations, and schema design
---

## Commands

### /db-migration
Create database migration.

Create Prisma migration:
1. Update schema.prisma with new model/fields
2. Run prisma migrate
3. Create seed data if needed
4. Update types

Follow naming: XXXX_description.sql

### /db-model
Create Prisma model.

Create Prisma model with:
- UUID as default ID (@default(uuid()))
- Multi-tenant support (@default(uuid()) for tenantId)
- Timestamps (createdAt, updatedAt)
- Relations to existing models
- Indexes for performance