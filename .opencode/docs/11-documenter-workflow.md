# Documenter Workflow

This document defines the workflow and procedures for the documenter agent.

## Overview

The documenter is responsible for creating and maintaining all technical documentation in the project. Documentation is mandatory for ALL features, issues, and adjustments.

## Complete Workflow

```
Real User → Consultant → Technical Spec → Developer → Documenter → Final Docs + Changelog
                                      (based on this)  (completes)   (creates this)
```

## Documentation Triggers

The documenter MUST create/update documentation when:

| Trigger | Document Type | Location |
|---------|---------------|----------|
| New feature completed | Technical Spec + Implementation | `.opencode/docs/technical/[feature-name]/` |
| API endpoint added/changed | API Documentation | `.opencode/docs/api/[module].md` |
| Bug/issue resolved | Issue Resolution | `.opencode/docs/issues/[issue-id]/` |
| Database change | Migration Documentation | `.opencode/docs/database/` |
| Any code change | Changelog Entry | `.opencode/docs/changelog/` |

## Folder Structure

```
.opencode/
├── docs/
│   ├── technical/                    # Technical specifications
│   │   └── [feature-name]/
│   │       ├── SPEC.md              # Requirement-based spec
│   │       └── IMPLEMENTATION.md    # Implementation details
│   ├── api/                          # API documentation
│   │   ├── users.md
│   │   ├── auth.md
│   │   └── [module].md
│   ├── issues/                       # Issue resolutions
│   │   └── [issue-id]/
│   │       └── resolution.md
│   ├── database/                     # Database documentation
│   │   └── migrations/
│   │       └── [migration-name].md
│   └── changelog/                    # CHANGELOG FOLDER
│       ├── CHANGELOG.md             # Main changelog
│       └── entries/                 # Individual entries
│           ├── 20240115-feature-a.md
│           └── 20240116-bug-fix.md
└── templates/                        # Document templates
    ├── technical-spec-template.md
    ├── implementation-template.md
    ├── api-doc-template.md
    ├── issue-resolution-template.md
    └── changelog-entry-template.md
```

## Documenter Responsibilities

### 1. Technical Specification
- Review completed implementation
- Compare against original requirements from consultant
- Document actual implementation
- Note any deviations from original spec
- Include code references

### 2. API Documentation
- Document all new/changed endpoints
- Include request/response formats
- Add Postman examples
- Document RBAC requirements
- Update existing API docs

### 3. Issue Resolution
- Document issue description
- Document root cause
- Document solution
- Include verification steps

### 4. Changelog
- Create entry for each change
- Maintain semantic versioning
- Update main CHANGELOG.md
- Keep detailed historical record

## Semantic Versioning

**Format**: `vMAJOR.MINOR.PATCH`

| Component | Increment When |
|-----------|---------------|
| **MAJOR** | Breaking changes, incompatible API changes |
| **MINOR** | New features, backward-compatible additions |
| **PATCH** | Bug fixes, backward-compatible fixes |

### Examples:
```
v1.0.0   - Initial release
v1.1.0   - Added new feature (e.g., user profile)
v1.1.1   - Bug fix (e.g., login redirect fix)
v2.0.0   - Breaking change (e.g., auth flow changes)
v2.1.5   - 5 patches since v2.1.0
```

## Changelog Format

### Main CHANGELOG.md Structure:
```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [v1.15.100] - 2024-01-15

### Added
- Feature description

### Changed
- Change description

### Fixed
- Fix description

### Removed
- Removed description

## [v1.15.99] - 2024-01-14
[previous changes...]
```

### Individual Entry Format:
```markdown
---
title: Feature Name
version: v1.15.100
date: 2024-01-15
type: feature
related:
  - PRD-123
  - FR-456
---

# Change Log Entry: Feature Name

## Summary
Brief description of changes

## Changes

### Added
- New feature details

### Changed
- Changes made

### Fixed
- Fixes applied

## Related Issues
- PRD-123
- BUG-456
```

## Documenter Workflow Steps

### Step 1: Receive Completed Work
- Get notification from developer that work is complete
- Review the completed code/implementation
- Gather all related information

### Step 2: Review Against Requirements
- Read original feature request from consultant
- Compare implementation with requirements
- Identify any deviations or additions

### Step 3: Create Technical Specification
- Use `.opencode/templates/technical-spec-template.md`
- Save to `.opencode/docs/technical/[feature-name]/SPEC.md`

### Step 4: Create Implementation Details
- Use `.opencode/templates/implementation-template.md`
- Save to `.opencode/docs/technical/[feature-name]/IMPLEMENTATION.md`

### Step 5: Update API Documentation
- Use `.opencode/templates/api-doc-template.md`
- Update `.opencode/docs/api/[module].md`

### Step 6: Create Issue Resolution (if applicable)
- Use `.opencode/templates/issue-resolution-template.md`
- Save to `.opencode/docs/issues/[issue-id]/resolution.md`

### Step 7: Create Changelog Entry
- Use `.opencode/templates/changelog-entry-template.md`
- Determine version bump
- Save to `.opencode/docs/changelog/entries/[date]-[feature].md`
- Update main `.opencode/docs/changelog/CHANGELOG.md`

### Step 8: Final Review
- Verify all documents are complete
- Check cross-references
- Ensure consistent formatting

## Document Naming Conventions

| Document Type | Format | Example |
|---------------|--------|---------|
| Technical Spec | `[feature-name]-spec.md` | `user-login-spec.md` |
| Implementation | `[feature-name]-impl.md` | `user-login-impl.md` |
| API Doc | `[module].md` | `users.md` |
| Issue Resolution | `[issue-id]-resolution.md` | `BUG-456-resolution.md` |
| Changelog Entry | `[date]-[feature].md` | `20240115-user-login.md` |

## Version Bump Decision Matrix

| Change Type | Version Bump |
|-------------|--------------|
| New feature | MINOR |
| Bug fix | PATCH |
| Breaking change | MAJOR |
| New feature + breaking change | MAJOR |
| Multiple features | MINOR |
| Security fix | PATCH (or MINOR if API change) |

## Best Practices

1. **Document IMMEDIATELY** - Don't wait, document right after completion
2. **Be DETAILED** - Include code snippets, file paths, line numbers
3. **Cross-REFERENCE** - Link related documents
4. **Use TEMPLATES** - Always use standardized templates
5. **Review** - Self-review before finalizing
6. **MAINTAIN** - Keep changelog updated, don't let it fall behind

## NOT Allowed

- Skip documentation for any change
- Use non-standard templates
- Incomplete documentation
- Inconsistent naming
- Outdated changelog

## Quality Checklist

Before marking documentation complete:

- [ ] Technical spec reflects actual implementation
- [ ] All API endpoints documented with examples
- [ ] Changelog entry created with correct version
- [ ] Related issues referenced
- [ ] Cross-references validated
- [ ] Templates used correctly
- [ ] No spelling/grammar errors