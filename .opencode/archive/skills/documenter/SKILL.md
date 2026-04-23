---
name: documenter
description: Create and maintain technical documentation. STRICTLY document ALL features, issues, and adjustments using templates in .opencode/templates/
---

## Commands

### /doc-spec
Create technical specification from completed feature.

FOLLOW .opencode/templates/technical-spec-template.md:
1. Review completed implementation against original requirements
2. Document actual implementation details:
   - Database schema changes
   - API endpoints created/modified
   - Frontend components
   - RBAC/permissions
   - Multi-tenant considerations
3. Document any deviations from original spec
4. Include code references (file paths, line numbers)
5. Save to .opencode/docs/technical/[feature-name]/SPEC.md

IMPORTANT: Document ALL changes, not just planned ones.

### /doc-implementation
Create implementation details document.

FOLLOW .opencode/templates/implementation-template.md:
1. Document implementation approach
2. Include code snippets and patterns used
3. Document integration points
4. Document configuration requirements
5. Include database migrations if any
6. Save to .opencode/docs/technical/[feature-name]/IMPLEMENTATION.md

### /doc-api
Update API documentation.

FOLLOW .opencode/templates/api-doc-template.md:
1. Document all new endpoints
2. Include: URL, method, params, body, response, errors
3. Add Postman request examples
4. List required permissions/RBAC
5. Save to .opencode/docs/api/[module].md
6. Update existing API docs if endpoints changed

### /doc-changelog
Create/update changelog entry.

FOLLOW .opencode/templates/changelog-entry-template.md:
1. Determine version bump type:
   - MAJOR (breaking changes)
   - MINOR (new features)
   - PATCH (bug fixes, improvements)
2. Format: vMAJOR.MINOR.PATCH (e.g., v1.15.100)
3. Create entry with:
   - Version number
   - Date
   - Changes list (features, fixes, improvements)
   - Related issues/tickets
4. Save to .opencode/docs/changelog/entries/[date]-[feature].md
5. Update main .opencode/docs/changelog/CHANGELOG.md

IMPORTANT: Maintain detailed historical record.

### /doc-issue
Document issue resolution.

FOLLOW .opencode/templates/issue-resolution-template.md:
1. Document issue description
2. Document root cause analysis
3. Document solution implemented
4. Document verification steps
5. Include code references if applicable
6. Save to .opencode/docs/issues/[issue-id]/resolution.md

### /doc-review
Review existing documentation for completeness.

Checklist:
1. All features have technical specs
2. All APIs are documented
3. All issues have resolution docs
4. Changelog is up to date
5. Cross-references between docs are valid
6. Templates are being used correctly

Report any gaps or inconsistencies found.