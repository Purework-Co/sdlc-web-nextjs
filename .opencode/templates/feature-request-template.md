# Feature Request

## Basic Information

| Field | Value |
|-------|-------|
| **Request ID** | FR-XXX |
| **Title** | [Short descriptive title] |
| **Type** | New Feature / Enhancement / Refactor |
| **Priority** | P0 Critical / P1 High / P2 Medium / P3 Low |
| **Created** | YYYY-MM-DD |
| **Requested By** | [Name/Role] |

---

## 1. Description

### Summary
[One sentence description]

### Detailed Description
[Paragraph explaining what, why, and how]

### User Benefit
[What value does this provide to users?]

---

## 2. Requirements

### Functional Requirements
- [ ] **REQ-01**: [Requirement description]
- [ ] **REQ-02**: [Requirement description]
- [ ] **REQ-03**: [Requirement description]

### Data Requirements
- New fields: [list]
- Database changes: [Yes/No - describe]
- API changes: [Yes/No - describe]

---

## 3. Acceptance Criteria

| ID | Criterion | Test Method |
|----|-----------|--------------|
| AC-01 | [Criterion 1] | [How to verify] |
| AC-02 | [Criterion 2] | [How to verify] |
| AC-03 | [Criterion 3] | [How to verify] |

---

## 4. Technical Notes

### Implementation Approach
[Brief description of how this should be built]

### Dependencies
- [Dependency 1]
- [Dependency 2]

### Potential Challenges
[Known complexities or considerations]

---

## 5. UI/UX (if applicable)

### Wireframe Reference
[Link or attachment reference]

### UI Components Needed
- [Component 1]
- [Component 2]

---

## 6. Security & RBAC

- **Authentication**: Required (JWT)
- **Permission Required**: [permission name]
- **Roles**: [list of roles that can access]

---

## 7. Multi-Tenant

- [ ] Applies to single tenant only
- [ ] Cross-tenant feature
- [ ] Tenant-specific configuration

---

## 8. Out of Scope

- [What is NOT included]
- [What is explicitly excluded]

---

## 9. Success Metrics

| Metric | Target |
|--------|--------|
| [Metric 1] | [Target value] |
| [Metric 2] | [Target value] |

---

## 10. Approval

| Role | Name | Date | Decision |
|------|------|------|----------|
| Product Owner | | | Approved / Rejected |
| Engineering | | | Approved / Rejected |

---

## 11. Test Specifications

### Unit Tests (Jest)
Required for all service functions, utilities, and API handlers (mocked).

| ID | Test Case | Expected Outcome |
|----|-----------|-------------------|
| UT-01 | [Description] | [Expected result] |
| UT-02 | [Description] | [Expected result] |
| UT-03 | [Description] | [Expected result] |

### Integration Tests (Jest)
Required for API endpoints with test database, service layer, and auth flows.

| ID | Test Case | Expected Outcome |
|----|-----------|-------------------|
| IT-01 | [Description] | [Expected result] |
| IT-02 | [Description] | [Expected result] |

### E2E Stubs (Playwright)
Create stubs with TODO comments for UI-dependent flows. Complete after frontend implementation.

| ID | Test Case | Expected Outcome |
|----|-----------|-------------------|
| E2E-01 | [Description] | [Expected result] |
| E2E-02 | [Description] | [Expected result] |

---

## 12. Test Completion Checklist

- [ ] All Unit Tests (UT-*) created and passing
- [ ] All Integration Tests (IT-*) created and passing
- [ ] All E2E Stubs (E2E-*) created with TODO comments
- [ ] Tests reviewed by QA engineer
- [ ] Test coverage >80% for new code