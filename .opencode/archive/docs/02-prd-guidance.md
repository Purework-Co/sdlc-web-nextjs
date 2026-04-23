# PRD Guidance

This document guides AI coding agents in creating and processing Product Requirements Documents.

## When to Create PRD

Create a PRD when:
- New feature requires database changes
- New API endpoints needed
- Complex business logic involved
- Multiple components affected
- External system integration

## PRD Structure

### 1. Overview Section
- **Feature Name**: Clear, descriptive name
- **Description**: 2-3 sentence summary
- **Problem Statement**: What problem does this solve?
- **Target Users**: Who benefits from this?

### 2. Goals & Non-Goals
- **Goals**: What will be achieved? (max 5)
- **Non-Goals**: What's out of scope?

### 3. Technical Requirements

#### Database Changes
```markdown
### Database
- New tables/models required
- Schema updates
- Migrations needed
```

#### API Requirements
```markdown
### API Endpoints
- REST endpoints needed
- Request/Response format
- Authentication required
```

#### Third-Party Services
```markdown
### External Services
- Service name
- Integration points
- API keys needed
```

### 4. Functional Requirements

For each feature:
- **ID**: FR-001
- **Title**: Feature title
- **Priority**: P0/P1/P2/P3
- **Description**: Detailed description
- **Acceptance Criteria**: Conditions for completion

### 5. User Stories

Format:
```
As a [user type], I want [goal], so that [benefit].
```

### 6. UI/UX Requirements
- Wireframes needed: Yes/No
- Design system: Shadcn components
- Responsive requirements
- Accessibility needs

### 7. Security Requirements
- Authentication needed
- Authorization (RBAC)
- Data validation
- Rate limiting

### 8. Multi-Tenant Considerations
- Tenant isolation required
- Tenant-specific configuration
- Cross-tenant features (if any)

### 9. Performance Requirements
- API response time < X ms
- Page load time < X seconds
- Concurrent users supported

### 10. Testing Requirements
- Unit tests needed
- Integration tests needed
- E2E tests needed
- Load testing required

## PRD Review Checklist

- [ ] Clear problem statement
- [ ] All user stories have acceptance criteria
- [ ] Technical implementation defined
- [ ] Security requirements specified
- [ ] Multi-tenant impact assessed
- [ ] Test requirements defined
- [ ] Dependencies identified

## Example: User Management Feature PRD

### Overview
- **Feature**: User Management
- **Description**: Admin interface to manage tenant users
- **Problem**: Admins cannot add/remove users in their organization
- **Target Users**: Tenant administrators

### Goals
1. Create new users with email invitation
2. Edit existing user details
3. Deactivate user accounts
4. Assign roles to users

### Non-Goals
- User self-registration (use OAuth)
- Cross-tenant user management
- User profile customization

### Functional Requirements

#### FR-01: Create User
- **Priority**: P0
- **Description**: Admin creates user by entering email
- **Acceptance Criteria**:
  - Email validation
  - Unique email check
  - Invitation email sent
  - User appears in user list

#### FR-02: Edit User
- **Priority**: P1
- **Description**: Admin edits user name, role
- **Acceptance Criteria**:
  - Name is required
  - Role must be valid
  - Changes saved immediately

#### FR-03: Deactivate User
- **Priority**: P1
- **Description**: Admin deactivates user account
- **Acceptance Criteria**:
  - User cannot login
  - User data retained
  - Audit log created

### Database
```prisma
model User {
  id        String   @id @default(uuid())
  email     String   @unique
  name      String?
  roleId    String
  role      Role     @relation(fields: [roleId], references: [id])
  tenantId  String
  tenant    Tenant   @relation(fields: [tenantId], references: [id])
  isActive  Boolean  @default(true)

  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

### API Endpoints
- `POST /api/v1/users` - Create user
- `GET /api/v1/users` - List users
- `GET /api/v1/users/:id` - Get user
- `PATCH /api/v1/users/:id` - Update user
- `DELETE /api/v1/users/:id` - Deactivate user

### RBAC
- Required permission: `user:write`
- Roles: admin, manager

### Testing
- Unit tests for service functions
- Integration tests for API
- E2E: create → edit → deactivate flow