# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [v0.0.1] - Initial Setup

### Added
- Project initialization with Next.js 14+ App Router
- Prisma ORM with PostgreSQL (UUIDv4 as default ID)
- NextAuth.js with Google OAuth
- Shadcn UI components
- Redis for sessions
- Multi-tenant architecture base
- RBAC system (super_admin, admin, manager, user, consultant roles)
- Admin web layout with collapsible sidebar and top navbar
- Prisma migration standardization
- Git usage standardization
- Documentation system with consultant, developer, and documenter workflow

### Documentation
- SDLC guidance
- PRD guidance
- Feature request guidance
- Testing guidance
- Issue report guidance
- API guidance with JWT and Postman collection
- CRUD guidance with pagination, search, sort, filter
- Admin web standardization
- Prisma migration standardization
- Git usage standardization
- Documenter workflow

---

## [Unreleased]

<!-- Add new entries above this section. Use the format:

## [vX.Y.Z] - YYYY-MM-DD

### Added
- New feature description

### Changed
- Change description

### Fixed
- Fix description

### Removed
- Removed feature

-->

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| [v0.0.1] | - | Initial setup |
| [Unreleased] | - | Work in progress |

---

## How to Add Entries

When adding new changes, create an entry file in `.opencode/docs/changelog/entries/` using the template:

```markdown
---
title: Feature or Change Name
version: vX.Y.Z
date: YYYY-MM-DD
type: feature|fix|improvement|breaking
related:
  - PRD-XXX
  - BUG-XXX
---

## Summary
Brief description of the change.

## Changes

### Added
- Added item 1
- Added item 2

### Changed
- Changed item 1

### Fixed
- Fixed item 1

### Removed
- Removed item 1
```

Then update this main CHANGELOG.md with the new version.

---

## Version Bump Guidelines

| Change Type | Version Bump |
|-------------|--------------|
| New feature | MINOR (e.g., v1.15.0 → v1.16.0) |
| Bug fix | PATCH (e.g., v1.15.0 → v1.15.1) |
| Breaking change | MAJOR (e.g., v1.15.0 → v2.0.0) |
| Multiple features | MINOR |
| Security fix | PATCH |

---

*Last updated: Project initialization*
*Maintained by: Documenter agent*