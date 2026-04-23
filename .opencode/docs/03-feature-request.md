# Feature Request Workflow

How to process feature requests from users.

## 4-Step Workflow

### Step 1: Clarify (Ask Questions)
Gather all required information:
1. **What** is the feature? (Clear, concise)
2. **Why** is it needed? (User benefit)
3. **Who** will use it? (Target users)
4. **How** will we know it works? (Success criteria)

### Step 2: Validate Completeness
Ensure request has:
- Clear user benefit
- Defined scope
- Measurable success criteria
- No dependencies blocking it

### Step 3: Create Specification
Document in `feature-request-template.md`:
- Title & description
- Requirements (what to build)
- Acceptance criteria (how to verify)
- Priority (P0-P3)
- Technical impact (DB changes, new APIs, etc)

### Step 4: Get Approval
Show user the spec and ask: "Is this correct?"

## Handoff to Development

Once approved, development team:
1. Creates feature branch
2. Implements feature
3. Runs tests
4. Pushes to develop

## Priority Levels

| Level | Type | Timeframe |
|-------|------|-----------|
| P0 | Critical (security, blocking bugs) | Immediate |
| P1 | High (core functionality) | 1-2 weeks |
| P2 | Medium (enhancements) | 2-4 weeks |
| P3 | Low (nice-to-have) | Backlog |

## Technical Questions to Ask

- Does database need changes?
- Are new API endpoints needed?
- Do users need different permission levels?
- Will this affect multiple features?
- Are external services involved?
