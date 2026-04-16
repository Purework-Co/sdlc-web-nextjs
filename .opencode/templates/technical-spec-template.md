# Technical Specification Template

Use this template to document the technical specification for a completed feature.

---

## Metadata

| Field | Value |
|-------|-------|
| **Feature Name** | [Feature Name] |
| **Document ID** | SPEC-[feature-name]-[date] |
| **Version** | 1.0 |
| **Status** | Draft/In Review/Complete |
| **Created** | YYYY-MM-DD |
| **Updated** | YYYY-MM-DD |
| **Author** | Documenter |
| **Related Feature Request** | [FR-XXX] |
| **Related Technical Spec** | [If updates to existing] |

---

## 1. Overview

### 1.1 Purpose
[What this feature does and why it exists]

### 1.2 Scope
- **In Scope**: [What is included]
- **Out of Scope**: [What is NOT included]

### 1.3 Target Users
- [Primary user type]
- [Secondary user type]

---

## 2. Requirements Comparison

### 2.1 Original Requirements (from Consultant)
| Requirement | Status | Notes |
|-------------|--------|-------|
| [Requirement 1] | Implemented/Modified/Not Implemented | [Notes] |
| [Requirement 2] | Implemented/Modified/Not Implemented | [Notes] |
| [Requirement 3] | Implemented/Modified/Not Implemented | [Notes] |

### 2.2 Deviations from Original
| Original | Implemented | Reason |
|----------|-------------|--------|
| [Original requirement] | [What was actually done] | [Reason for deviation] |

---

## 3. Implementation Summary

### 3.1 Database Changes

#### Schema Changes
```prisma
// Added/Modified models
model [ModelName] {
  // fields
}
```

#### Migrations
| Migration File | Description |
|----------------|-------------|
| `YYYYMMDDHHMMSS_description.sql` | [Description] |

### 3.2 API Endpoints

| Method | Endpoint | Description | Status |
|--------|----------|-------------|--------|
| GET | `/api/v1/[resource]` | List with pagination | Implemented |
| POST | `/api/v1/[resource]` | Create | Implemented |
| GET | `/api/v1/[resource]/:id` | Get single | Implemented |
| PATCH | `/api/v1/[resource]/:id` | Update | Implemented |
| DELETE | `/api/v1/[resource]/:id` | Delete | Implemented |

### 3.3 Frontend Components

| Component | Location | Purpose |
|-----------|----------|---------|
| [Component Name] | `src/components/[path]` | [Purpose] |
| [Page Name] | `src/app/[path]/page.tsx` | [Purpose] |

### 3.4 RBAC/Permissions

| Permission | Description | Roles |
|------------|-------------|-------|
| `[permission:read]` | View [resource] | [Roles] |
| `[permission:write]` | Create/Update [resource] | [Roles] |
| `[permission:delete]` | Delete [resource] | [Roles] |

### 3.5 Multi-Tenant

- [ ] Tenant isolation implemented
- [ ] Tenant context in queries
- [ ] Cross-tenant access: [Allowed/Not Allowed]

---

## 4. Code References

### 4.1 Key Files

| File | Description | Lines |
|------|-------------|-------|
| `src/lib/services/[service].ts` | [Description] | [Start]-[End] |
| `src/app/api/v1/[resource]/route.ts` | [Description] | [Start]-[End] |
| `src/components/[component].tsx` | [Description] | [Start]-[End] |

### 4.2 Integration Points

| Integration | Location | Description |
|-------------|----------|-------------|
| [External Service] | [File] | [Description] |

---

## 5. Testing

### 5.1 Unit Tests
| Test File | Coverage |
|-----------|----------|
| `src/__tests__/[test].test.ts` | [Coverage %] |

### 5.2 Integration Tests
| Test File | Coverage |
|-----------|----------|
| `tests/integration/[test].test.ts` | [Coverage %] |

### 5.3 E2E Tests
| Test File | Test Cases |
|-----------|------------|
| `tests/e2e/[test].spec.ts` | [Test cases] |

---

## 6. Configuration

### 6.1 Environment Variables
| Variable | Required | Description | Default |
|----------|----------|-------------|---------|
| `NEW_VAR` | Yes/No | [Description] | [Default] |

### 6.2 Feature Flags
| Flag | Default | Description |
|------|---------|-------------|
| `FEATURE_NEW_THING` | false | [Description] |

---

## 7. Rollback Plan

[How to rollback this feature if needed]

---

## 8. Related Documentation

| Document | Location |
|----------|----------|
| Implementation Details | `.opencode/docs/technical/[feature-name]/IMPLEMENTATION.md` |
| API Documentation | `.opencode/docs/api/[module].md` |
| Feature Request | `.opencode/templates/feature-request-template.md` |
| Changelog Entry | `.opencode/docs/changelog/entries/[date]-[feature].md` |

---

## 9. Sign-off

| Role | Name | Date | Status |
|------|------|------|--------|
| Documenter | | | |
| Developer | | | |
| QA (if applicable) | | | |