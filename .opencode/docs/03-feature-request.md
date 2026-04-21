# Feature Request Guidance

This document guides AI coding agents in processing and implementing feature requests.

## Feature Request Workflow

1. **Receive** - Get feature request from user/stakeholder
2. **Validate** - Check completeness and feasibility
3. **Clarify** - Ask questions if needed
4. **Plan** - Create technical implementation plan
5. **Create Tests** - Invoke `qa-engineer` for Jest unit/integration + Playwright E2E stubs
6. **Implement Backend** - Invoke `backend-engineer` for API/Prisma/services (MUST pass tests)
7. **Implement Frontend** - Invoke `frontend-developer` for UI/components
8. **Document** - Invoke `documenter` for docs
9. **Verify** - User acceptance testing

## Feature Request Types

| Type | Description | Priority |
|------|-------------|----------|
| P0 - Critical | Security, data integrity, blocking bugs | Immediate |
| P1 - High | Core functionality, major improvements | 1-2 weeks |
| P2 - Medium | Enhancements, UX improvements | 2-4 weeks |
| P3 - Low | Nice-to-have, polish | Backlog |

## Required Information

For a valid feature request, gather:

1. **Title**: Clear, concise feature name
2. **Description**: What and why
3. **Requirements**: What needs to be built
4. **Acceptance Criteria**: How to verify completion
5. **Priority**: Business value and urgency
6. **Dependencies**: What else is needed

## Processing Steps

### Step 1: Validate Request
Check if feature request has:
- Clear user benefit
- Defined scope
- Measurable success criteria

If incomplete, ask clarifying questions.

### Step 2: Technical Assessment
- Database changes needed?
- New API endpoints?
- RBAC/permissions required?
- Multi-tenant impact?
- External integrations?

### Step 3: Implementation Planning
Use template from `.opencode/templates/feature-request-template.md`

### Step 4: Implementation
Invoke subagents in sequence:

1. **Tests (QA)** - Invoke `qa-engineer` FIRST:
   - Create Jest unit tests for service functions
   - Create Jest integration tests for API endpoints
   - Create Playwright E2E test stubs (with TODO comments for UI-dependent flows)
   - Tests MUST be created BEFORE backend implementation starts

2. **Backend** - Invoke `backend-engineer`:
   - Update schema (if needed)
   - Implement API endpoints
   - Implement service layer
   - Add RBAC checks
   - Tests MUST pass before frontend can start

3. **Frontend** - Invoke `frontend-developer`:
   - Implement UI components
   - Implement pages
   - Add permission guards

4. **Documentation** - Invoke `documenter`:
   - Update API documentation
   - Update Postman collection

### Step 5: Verification
After subagent implementation completes:
1. Verify acceptance criteria met
2. Verify tests passing
3. Run linting and type checking
4. Verify documentation updated
5. Coordinate user acceptance testing via consultant

## Common Feature Patterns

### CRUD Operations
- Create Prisma model
- Create service layer
- Create REST API endpoints
- Create UI components
- Add tests
- Update Postman collection

### Multi-Tenant Features
- Always filter by tenantId
- Add tenant context validation
- Include tenant in telemetry

### RBAC-Protected Features
- Check permission in service layer
- Add UI hiding (optional)
- Log access attempts

## Template Location

Use the feature request template:
`.opencode/templates/feature-request-template.md`

Fill in all sections before implementation begins.