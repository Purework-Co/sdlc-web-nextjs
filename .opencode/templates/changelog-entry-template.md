# Changelog Entry Template

Use this template to create individual changelog entries.

---

## Metadata

| Field | Value |
|-------|-------|
| **Title** | [Feature or Change Name] |
| **Version** | vX.Y.Z |
| **Date** | YYYY-MM-DD |
| **Type** | feature/fix/improvement/breaking |
| **Documenter** | [Documenter Name] |
| **Related Issues** | [PRD-XXX, BUG-XXX] |

---

## Summary

[Brief description of the change - 1-2 sentences]

---

## Changes

### Added
- [New feature 1]
- [New feature 2]

### Changed
- [Changed behavior 1]
- [Changed behavior 2]

### Fixed
- [Fixed issue 1]
- [Fixed issue 2]

### Removed
- [Removed feature 1]
- [Deprecated feature]

### Breaking Changes
- [Breaking change 1]
- Migration steps for users:
  1. [Step 1]
  2. [Step 2]

---

## Technical Details

### Database
- Migration: `YYYYMMDDHHMMSS_description.sql`
- Schema changes: [Description]

### API
- New endpoints: [List]
- Modified endpoints: [List]

### Frontend
- New components: [List]
- Modified pages: [List]

---

## Related Documentation

| Document | Link |
|----------|------|
| Technical Spec | `.opencode/docs/technical/[feature-name]/SPEC.md` |
| Implementation | `.opencode/docs/technical/[feature-name]/IMPLEMENTATION.md` |
| API Docs | `.opencode/docs/api/[module].md` |
| Issue Resolution | `.opencode/docs/issues/[issue-id]/resolution.md` |

---

## Version Bump Rationale

| Change | Type | Rationale |
|--------|------|-----------|
| [Change] | feature/fix/breaking | [Reason] |

---

## Usage

After creating this entry:
1. Save to `.opencode/docs/changelog/entries/[date]-[feature].md`
2. Update main `.opencode/docs/changelog/CHANGELOG.md`
3. Add new version at the top of the changelog

---

## Example Entry

```markdown
---
title: User Login with Google OAuth
version: v1.1.0
date: 2024-01-15
type: feature
related:
  - PRD-123
  - FR-456
---

## Summary

Implemented Google OAuth login functionality with session management.

## Changes

### Added
- Google OAuth provider in NextAuth
- Redis session storage
- Login page with Google button
- User session management

### Changed
- Updated NextAuth configuration
- Modified user model to include OAuth fields

### Fixed
- None

### Removed
- None

## Technical Details

### Database
- Added `email_verified` field to users table
- Migration: `20240115103000_add_oauth_fields.sql`

### API
- New endpoints: None (uses existing auth endpoints)
- Modified: `/api/auth/[...nextauth]`

### Frontend
- New components: `src/components/auth/login-button.tsx`
- New pages: `src/app/login/page.tsx`

## Version Bump Rationale

| Change | Type | Rationale |
|--------|------|-----------|
| Google OAuth | feature | New functionality added |

## Related Documentation

| Document | Link |
|----------|------|
| Technical Spec | `.opencode/docs/technical/user-login/SPEC.md` |
| Implementation | `.opencode/docs/technical/user-login/IMPLEMENTATION.md` |
| API Docs | `.opencode/docs/api/auth.md` |
```