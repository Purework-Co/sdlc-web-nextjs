# ClickUp Integration Standardization

This document defines the standardization for integrating OpenCode with ClickUp for task management and documentation sync in the sdlc-web project.

## 1. Overview

### 1.1 Purpose
- Define ClickUp workspace structure for this project
- Standardize task naming conventions
- Guide one-way documentation sync (OpenCode → ClickUp)
- Enable real-time task board management

### 1.2 Workspace Information

| Property | Value |
|----------|-------|
| **Workspace ID** | `90182640362` |
| **Space Name** | `sdlc-web` |
| **Connection Type** | MCP (mcp-remote) |

### 1.3 MCP Configuration
```json
// .opencode/opencode.json
{
  "mcp": {
    "clickup": {
      "type": "local",
      "command": ["npx", "-y", "mcp-remote", "https://mcp.clickup.com/mcp"],
      "enabled": true
    }
  }
}
```

### 1.4 ClickUp Tools Available
- Task creation, update, deletion
- Task filtering and search
- Time tracking
- Comments and attachments
- Custom fields
- List/folder management

## 2. Workspace Structure

### 2.1 Recommended Structure
```
ClickUp Workspace: 90182640362 (sdlc-web)
│
├── Space: sdlc-web
│   │
│   ├── List: Documentation (for OpenCode docs)
│   │   └── Tasks: DOC-XXX format
│   │
│   ├── List: Task Board (for development)
│   │   ├── Features: FEAT-XXX format
│   │   ├── Bugs: BUG-XXX format
│   │   └── Releases: REL-XXX format
│   │
│   └── List: Sprint (optional - for agile)
│       └── Tasks grouped by sprint
```

### 2.2 List Purposes

| List Name | Purpose | Task Format |
|----------|---------|------------|
| **Documentation** | OpenCode docs sync | DOC-[number] |
| **Task Board** | Development tracking | FEAT-[number], BUG-[number] |
| **Sprint** | Agile sprint planning | SPR-[number]-[task-ref] |

## 3. Task Naming Conventions

### 3.1 Task ID Format
```
[TYPE]-[NUMBER] - [Task Title]

Examples:
- FEAT-001 - User authentication
- BUG-002 - Login page crash on mobile
- DOC-014 - ClickUp integration standards
- SPEC-001 - Technical spec for auth system
- REL-1.0.0 - Release v1.0.0
```

### 3.2 Task Types

| Type | Prefix | List | Description |
|------|--------|------|-------------|
| Feature | FEAT- | Task Board | New functionality |
| Bug | BUG- | Task Board | Bug report/fix |
| Documentation | DOC- | Documentation | OpenCode documentation |
| Technical Spec | SPEC- | Documentation | Implementation specification |
| Release | REL- | Task Board | Version release |
| Spike | SPIKE- | Task Board | Research/investigation |

### 3.3 Task Title Guidelines
- Use clear, concise titles (max 60 characters)
- Include action verb: "Add", "Fix", "Implement", "Update"
- Avoid generic titles like "Update code" or "Fix bug"

### 3.4 Priority Levels

| Priority | Label | Icon | Use Case |
|----------|-------|------|----------|
| P1 | Urgent | 🔴 | Critical bugs, blockers |
| P2 | High | 🟠 | Must have for release |
| P3 | Normal | 🟡 | Should have |
| P4 | Low | 🟢 | Nice to have |

### 3.5 Status Workflow

#### Documentation List (One-way: OpenCode → ClickUp)
```
Draft → In Review → Complete
```

#### Task Board (Bidirectional)
```
Backlog → In Progress → In Review → Done → Closed
       ↳ Blocked ← Investigation
```

## 4. Documentation Sync Process

### 4.1 One-Way Sync: OpenCode → ClickUp

**Sync Direction**: OpenCode (source of truth) → ClickUp (mirror)

This means:
- ✅ Create ClickUp task when new OpenCode doc is created
- ✅ Update ClickUp task when OpenCode doc is updated
- ❌ Do NOT read/open ClickUp tasks to update OpenCode files
- ❌ Do NOT sync FROM ClickUp to OpenCode

### 4.2 Sync Trigger

| Event | Action |
|-------|--------|
| New doc created in `.opencode/docs/` | Create ClickUp task |
| Doc significantly updated | Update ClickUp task description |
| Doc marked as complete | Update task status to Complete |

### 4.3 Task Content Template

When syncing a doc to ClickUp, include:

```markdown
# [Doc Title]

## Metadata
- **File**: `.opencode/docs/XX-[doc-name].md`
- **Version**: X.X
- **Status**: Draft/In Review/Complete

## Summary
[Brief description of what this document covers]

## Sections
- [ ] Section 1
- [ ] Section 2
- [ ] Section N

## Related
- Related doc: DOC-[XX]
- Related feature: FEAT-[XX]
```

### 4.4 Existing Documentation Mapping

| OpenCode Doc | ClickUp Task ID | Status |
|-------------|---------------|--------|
| 01-sdlc-guidance.md | DOC-001 | Complete |
| 02-prd-guidance.md | DOC-002 | Complete |
| 03-feature-request.md | DOC-003 | Complete |
| 04-testing-guidance.md | DOC-004 | Complete |
| 05-issue-report.md | DOC-005 | Complete |
| 06-api-guidance.md | DOC-006 | Complete |
| 07-crud-guidance.md | DOC-007 | Complete |
| 08-admin-web-standardization.md | DOC-008 | Complete |
| 09-prisma-migration-standardization.md | DOC-009 | Complete |
| 10-git-usage-standardization.md | DOC-010 | Complete |
| 11-documenter-workflow.md | DOC-011 | Complete |
| 12-rbac-standardization.md | DOC-012 | Complete |
| 13-landing-page-standardization.md | DOC-013 | Complete |
| 14-clickup-integration-standardization.md | DOC-014 | Complete |
| 15-opencode-structure-reference.md | DOC-015 | Complete |

## 5. Task Board Management

### 5.1 When to Create Tasks

Create a ClickUp task when:

| Condition | Example |
|----------|----------|
| New feature requested | Client wants "user login" |
| Bug discovered | "Login crashes on iOS" |
| Technical decision needed | "Choose payment provider" |
| Release planned | "Ship v1.0.0" |

### 5.2 Task Required Fields

| Field | Required | Description |
|-------|----------|-------------|
| Name | ✅ | Task title with prefix |
| List | ✅ | Task Board |
| Status | ✅ | Backlog/In Progress/Done |
| Priority | 🔲 | P1-P4 |
| Due Date | 🔲 | Target completion date |
| Assignee | 🔲 | Team member |
| Tags | 🔲 | Feature/Bug/Documentation |

### 5.3 Task Description Template

```markdown
## Description
[What needs to be done]

## Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2

## Technical Notes
[Any technical considerations]

## Related
- Blocks: [Task ID]
- Blocked by: [Task ID]
- Related: [Task ID]
```

### 5.4 Common Task Flows

#### Feature Flow
```
FEAT-001 Create user login
  ↓
[FEAT-001: In Progress]
  ↓
[FEAT-001: In Review]
  ↓
[FEAT-001: Done]
```

#### Bug Flow
```
BUG-001: Login crashes
  ↓
[BUG-001: In Progress]
  ↓
[BUG-001: In Review]
  ↓
[BUG-001: Done]
```

## 6. Agent Integration

### 6.1 Responsibilities by Agent

| Agent | ClickUp Tasks | List |
|-------|--------------|------|
| **Consultant** | Feature requests (FEAT-) | Task Board |
| **Frontend Developer** | Frontend tasks, UI/UX | Task Board |
| **Backend Engineer** | API tasks, database | Task Board |
| **QA Engineer** | Testing tasks, bugs found | Task Board |
| **Documenter** | Documentation sync (DOC-) | Documentation |
| **DevOps** | Deployment tasks (REL-) | Task Board |

### 6.2 Creating Tasks from Commands

Each agent should create ClickUp tasks when:

| Command | Creates Task | List |
|---------|-------------|------|
| `/create-feature` | FEAT-[N] | Task Board |
| `/doc-spec` | SPEC-[N] | Documentation |
| `/create-feature` (with bug) | BUG-[N] | Task Board |
| `/doc-changelog` | REL-[version] | Task Board |

### 6.3 Task Update Protocol

**For Documentation List (One-way)**:
1. Documenter creates task when new doc is added to `.opencode/docs/`
2. Documenter updates task when doc is significantly changed
3. Documenter marks Complete when doc is final

**For Task Board (Bidirectional)**:
1. Agent creates task when feature/bug is identified
2. Agent updates progress as they work
3. Agent marks Done when implementation is complete
4. QA verifies and closes task

## 7. Best Practices

### 7.1 DO
- Use consistent naming with prefixes
- Include clear acceptance criteria
- Link related tasks (blocks/blocked by)
- Update task status regularly
- Use due dates for time-sensitive tasks
- Include task assignees

### 7.2 DON'T
- Don't create tasks for trivial changes
- Don't leave tasks in "In Progress" for > 1 week without update
- Don't create duplicate tasks
- Don't use inconsistent naming
- Don't forget to close completed tasks

### 7.3 Task Hygiene
- Close tasks when done to keep board clean
- Archive or delete rejected/deferred tasks
- Use "Blocker" status when task cannot proceed
- Add relevant tags for filtering

## 8. Quick Reference

### 8.1 Task ID Summary

| Prefix | List | Example |
|--------|------|---------|
| FEAT- | Task Board | FEAT-001 Add login |
| BUG- | Task Board | BUG-001 Fix crash |
| DOC- | Documentation | DOC-014 ClickUp docs |
| SPEC- | Documentation | SPEC-001 Auth spec |
| REL- | Task Board | REL-1.0.0 Release |

### 8.2 Status Summary

| Status | List | Meaning |
|--------|------|---------|
| Backlog | Task Board | Not started |
| In Progress | Task Board | Being worked on |
| In Review | Task Board | Awaiting review |
| Done | Task Board | Completed |
| Blocked | Task Board | Stuck |
| Draft | Documentation | Being written |
| In Review | Documentation | Under review |
| Complete | Documentation | Final/Approved |

### 8.3 Priority Summary

| Priority | Color | When |
|----------|-------|------|
| Urgent | 🔴 | Now |
| High | 🟠 | This sprint |
| Normal | 🟡 | This release |
| Low | 🟢 | Backlog |

---

## Related Documentation

| Document | Purpose |
|----------|---------|
| `01-sdlc-guidance.md` | Overall SDLC process |
| `03-feature-request.md` | Feature request handling |
| `11-documenter-workflow.md` | Documenter workflow |
| `15-opencode-structure-reference.md` | Directory reference |
| `frontend-developer.md` | Frontend agent config |
| `backend-engineer.md` | Backend agent config |