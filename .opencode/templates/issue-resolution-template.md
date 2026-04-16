# Issue Resolution Template

Use this template to document bug fixes and issue resolutions.

---

## Metadata

| Field | Value |
|-------|-------|
| **Issue ID** | BUG-[XXX] or [TICKET-ID] |
| **Title** | [Issue Title] |
| **Severity** | Critical/High/Medium/Low |
| **Status** | Resolved |
| **Reported** | YYYY-MM-DD |
| **Resolved** | YYYY-MM-DD |
| **Reporter** | [Name] |
| **Resolver** | [Developer Name] |
| **Documenter** | [Documenter Name] |

---

## 1. Issue Description

### 1.1 Summary
[Brief description of the issue]

### 1.2 User Impact
- How many users affected: [Number/All]
- Frequency: [Always/Sometimes/Rarely]
- Workaround available: [Yes/No - describe]

### 1.3 Environment
| Component | Version |
|-----------|---------|
| Browser | [e.g., Chrome 120] |
| OS | [e.g., macOS 14] |
| App Version | [e.g., v1.2.3] |
| API Version | [e.g., v1] |

---

## 2. Reproduction

### 2.1 Steps to Reproduce
1. [Step 1]
2. [Step 2]
3. [Step 3]

### 2.2 Expected Behavior
[What should happen]

### 2.3 Actual Behavior
[What actually happens]

---

## 3. Root Cause Analysis

### 3.1 Investigation Process
[How the issue was investigated]

### 3.2 Root Cause
[Technical explanation of why the issue occurred]

### 3.3 Code Location
| File | Lines | Description |
|------|-------|-------------|
| `src/path/file.ts` | 100-150 | [Description] |

---

## 4. Solution

### 4.1 Fix Description
[What was changed to fix the issue]

### 4.2 Code Changes

```typescript
// Before (problematic code)
// src/path/file.ts (lines 100-150)
[old code]

// After (fixed code)
// src/path/file.ts (lines 100-150)
[new code]
```

### 4.3 Database Changes (if applicable)
| Migration | Description |
|-----------|-------------|
| [Migration file] | [What was changed] |

### 4.4 Configuration Changes (if applicable)
| Variable | Change |
|----------|--------|
| [Variable] | [Old] → [New] |

---

## 5. Verification

### 5.1 Test Cases
| Test Case | Result | Notes |
|-----------|--------|-------|
| Reproduction steps | Pass | Issue no longer occurs |
| Edge case 1 | Pass | [Notes] |
| Edge case 2 | Pass | [Notes] |

### 5.2 Environment Tested
| Environment | Status |
|-------------|--------|
| Development | Tested |
| Staging | Tested |
| Production | Pending/Verified |

---

## 6. Prevention

### 6.1 How to Prevent Recurrence
[Steps to ensure this doesn't happen again]

### 6.2 Related Tests Added
| Test File | Test Case |
|-----------|-----------|
| `tests/[file].test.ts` | [Test name] |

### 6.3 Monitoring Added
[Any monitoring/logging added to detect this issue]

---

## 7. Documentation Updates

| Document | Update Made |
|----------|-------------|
| [Document] | [Update] |

---

## 8. Lessons Learned

### 8.1 What Went Well
- [What worked well in debugging/resolution]

### 8.2 What Could Be Improved
- [What could be done better next time]

### 8.3 Additional Notes
[Any other relevant notes]

---

## 9. Related Issues

| Issue | Relationship |
|-------|--------------|
| [Issue ID] | Related/Blocked by/Blocks |

---

## 10. Sign-off

| Role | Name | Date | Status |
|------|------|------|--------|
| Developer | | | |
| QA | | | |
| Documenter | | | |