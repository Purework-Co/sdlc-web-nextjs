# 📚 Documentation Index

## Always Available (Core Docs)

**1. 01-sdlc-guidance.md** - Technology stack & project structure  
Quick reference for the tech stack and folder organization.

**2. 03-feature-request.md** - How to process user requests  
4-step workflow: Clarify → Validate → Specify → Approve

**3. 06-api-guidance.md** - REST API patterns  
Endpoint structure, response formats, JWT authentication.

---

## On-Demand Documentation (Loaded When Needed)

Available in `.opencode/archive/docs/` - automatically loaded when relevant:

| Topic | File | Triggers |
|-------|------|----------|
| **Testing** | 04-testing-guidance.md | `/test` command, user asks about tests |
| **Database Migrations** | 09-prisma-migration-standardization.md | `/db-migration`, schema changes |
| **Role-Based Access** | 12-rbac-standardization.md | Feature mentions roles/permissions |
| **CRUD Operations** | 07-crud-guidance.md | `/api-crud` command |
| **Git Workflow** | 10-git-usage-standardization.md | Branch/commit guidance |

---

## Command Reference

### Consultant Layer (Non-Tech Users)
```
/clarify         - Ask clarifying questions about your idea
/document        - Create a formal specification
/validate        - Confirm everything is correct
/translate       - See the technical plan
```

### Development Layer (Core Workflows)
```
/create-feature  - Build complete feature from spec
/component       - Create UI component
/page            - Create new page
/form            - Create form component
/api-crud        - Generate CRUD API endpoints
/db-migration    - Make database changes
/db-model        - Create/modify database model
/auth-setup      - Configure authentication
```

### Validation Layer
```
/build           - Compile & validate
/lint            - Check code quality
/typecheck       - Verify type safety
/test            - Run tests (unit/browser/integration)
```

### Documentation
```
/doc-spec        - Create technical specification
/doc-implementation - Document implementation details
```

---

## Workflow Examples

### User Request: "I need a login page"

```
User: "I need a login page"
   ↓
/clarify         → Ask: "Email or social login?", "Remember me?", etc
   ↓
/document        → Create spec from answers
   ↓
/validate        → "Is this correct?"
   ↓
/translate       → "Here's the technical plan"
   ↓
/create-feature  → Build it (auth + page + components)
   ↓
/test (optional) → Run tests if requested
```

### User Request: "Add admin dashboard"

```
User: "Add admin dashboard for staff only"
   ↓
/clarify         → Ask: "What data to show?", "Which staff can see it?", etc
   ↓
/document        → Create feature spec
   ↓
/validate        → Confirm requirements
   ↓
/translate       → Technical plan with RBAC needs
   ↓
(System loads rbac guidance automatically)
   ↓
/create-feature  → Build dashboard (pages + components + APIs)
```

---

## Tips for Non-Technical Users

1. **Start with `/clarify`** - The system will ask questions to understand your needs
2. **Be specific** - "Login page" vs "Login with email and Google, remember me" → more specific = better result
3. **Describe who uses it** - "Admin users only" vs "All customers" → affects implementation
4. **Ask about testing** - Use `/test` if you want automated tests
5. **Document as you go** - Use `/doc-*` commands to keep track of changes

---

## Getting Help

- Can't figure out next step? Try `/clarify` or `/validate`
- Want to see what we'll build? Try `/translate`
- Need a written spec? Try `/document`
- Ready to code? Try `/create-feature`

The system is designed to guide you through every step!
