# Git Usage Standardization

This document defines the standardization for using Git in this full-stack application development.

## 1. Branch Strategy

### 1.1 Branch Structure

```
main (production)
└── develop (staging)
    ├── feature/TICKET-description
    ├── fix/TICKET-description
    ├── refactor/TICKET-description
    └── chore/TICKET-description
```

| Branch | Purpose | Protected |
|--------|---------|-----------|
| `main` | Production-ready code | Yes |
| `develop` | Staging/QA environment | Yes |
| `feature/*` | New features | No |
| `fix/*` | Bug fixes | No |
| `refactor/*` | Code refactoring | No |
| `chore/*` | Maintenance tasks | No |

### 1.2 Branch Naming Rules

**Format**: `type/TICKET-description`

```
feature/PRD-123-user-login
fix/BUG-456-login-redirect-error
refactor/REF-789-auth-service-cleanup
chore/CHORE-101-update-dependencies
```

**Rules**:
- `type`: Must be one of: `feature`, `fix`, `refactor`, `chore`, `docs`
- `TICKET`: Reference to tracking issue (e.g., PRD-123, BUG-456, REF-789)
- `description`: Lowercase, hyphens for spaces, max 50 characters
- NO spaces in branch name
- NO special characters except hyphens

**Examples**:
```
✅ good
- feature/PRD-123-add-user-dashboard
- fix/BUG-456-resolve-login-error
- refactor/REF-789-cleanup-auth-module
- chore/CHORE-101-update-npm-deps
- docs/DOC-50-update-readme

❌ bad
- feature/Add new feature (no ticket)
- fix/fix-bug (no ticket, spaces)
- feature/PR-123 (no description)
- fix/bug fix (spaces)
```

### 1.3 Creating Branches

```bash
# From develop, create new feature branch
git checkout develop
git pull origin develop
git checkout -b feature/PRD-123-description

# From develop, create fix branch
git checkout develop
git pull origin develop
git checkout - fix/BUG-456-description
```

## 2. Commit Conventions

### 2.1 Commit Message Format

**Structure**:
```
type(TICKET): subject

[optional body]

[optional footer]
```

**Example**:
```
feat(PRD-123): add user login with Google OAuth

- Implement Google OAuth provider in NextAuth
- Add user session management with Redis
- Add login page with Google button

Closes PRD-123
```

### 2.2 Types

| Type | Description | Used For |
|------|-------------|----------|
| `feat` | New feature | Feature development |
| `fix` | Bug fix | Bug resolution |
| `refactor` | Code refactoring | No functional change |
| `chore` | Maintenance | Dependencies, configs |
| `docs` | Documentation | README, docs |
| `test` | Tests | Unit, integration, e2e |
| `style` | Formatting | Prettier, lint fixes |
| `perf` | Performance | Performance improvements |

### 2.3 Rules

1. **Subject Line**:
   - Max 50 characters
   - Lowercase first letter
   - No period at end
   - Use imperative mood ("add" not "added")

2. **Body**:
   - Max 72 characters per line
   - Explain "what" and "why", not "how"
   - Use bullet points for multiple changes

3. **Footer**:
   - Reference issues: `Closes #123`, `Fixes BUG-456`
   - Multiple issues: `Closes #123, Closes #456`

### 2.4 Commit Examples

```
✅ good commits
feat(PRD-123): add user authentication with Google OAuth
fix(BUG-456): resolve redirect loop after login
refactor(REF-789): extract auth logic to service layer
chore(CHORE-101): update Next.js to version 14
docs(DOC-50): add API documentation
test(TEST-25): add unit tests for user service

❌ bad commits
Fixed the bug
Update stuff
WIP
Merge branch
asdf
```

### 2.5 Making Commits

```bash
# Stage specific files
git add src/lib/auth.ts src/app/api/auth/login/route.ts

# Commit with message
git commit -m "feat(PRD-123): add Google OAuth login

- Implement Google provider in NextAuth
- Add session management
- Create login page UI

Closes PRD-123"

# Amend last commit (if needed, before push)
git commit --amend -m "feat(PRD-123): add Google OAuth login"
```

## 3. Workflow

### 3.1 Feature Development Flow

```bash
# 1. Start from develop
git checkout develop
git pull origin develop

# 2. Create feature branch
git checkout -b feature/PRD-123-description

# 3. Make commits (follow conventions)
git add .
git commit -m "feat(PRD-123): add initial implementation"

# 4. Keep branch up to date with develop
git fetch origin
git merge origin/develop
# Resolve conflicts if any

# 5. Push branch to remote
git push origin feature/PRD-123-description

# 6. Create Pull Request
# Review, get approvals, merge to develop
```

### 3.2 Pull Request Process

**Before creating PR**:
- [ ] All tests passing
- [ ] Code runs without errors
- [ ] No merge conflicts with develop
- [ ] PR description filled out

**PR Title Format**: `[type] TICKET: Description`

```
[feat] PRD-123: Add user login with Google OAuth
[fix] BUG-456: Resolve login redirect loop
[refactor] REF-789: Clean up auth service
```

**PR Description Template**:
```markdown
## Summary
Brief description of changes

## Changes
- Change 1
- Change 2

## Testing
- [ ] Unit tests added/updated
- [ ] Manual testing done
- [ ] No console errors

## Screenshots (if applicable)
[Screenshots here]

## Related Issues
Closes PRD-123
```

### 3.3 Merging Strategy

**Rule**: Use **merge** (not squash, not rebase) to keep full history

```bash
# When merging PR to develop
git checkout develop
git pull origin develop
git merge origin/feature/PRD-123-description
git push origin develop
```

**Why merge (not squash)**:
- Preserves complete history of changes
- Each commit remains traceable to author
- Easier to bisect and find when bugs were introduced
- Maintains context of related changes

**Why merge (not rebase)**:
- Preserves original commit timestamps
- No risk of rewriting shared history
- Clearer merge points in history

### 3.4 Merge Conflicts Resolution

```bash
# When conflict occurs during merge
git status  # See conflicting files

# Edit files to resolve conflicts
# Remove <<<<<<<, =======, >>>>>>> markers

# Stage resolved files
git add .

# Continue merge
git commit -m "Merge branch 'feature/PRD-123-description' into develop"
```

## 4. Versioning and Tags

### 4.1 Tag Format

**Semantic Versioning**: `vMAJOR.MINOR.PATCH`

| Type | Description | Example |
|------|-------------|---------|
| `v1.0.0` | Major release | First production release |
| `v1.1.0` | Minor release | New features |
| `v1.1.1` | Patch release | Bug fixes |

### 4.2 Creating Tags

```bash
# Create annotated tag
git tag -a v1.0.0 -m "Release version 1.0.0"

# Push tag to remote
git push origin v1.0.0

# List tags
git tag -l
```

## 5. Gitignore

### 5.1 Required Patterns

```gitignore
# Dependencies
node_modules/
.pnp
.pnp.js

# Build outputs
.next/
out/
build/
dist/

# Environment variables
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# Testing
coverage/

# IDE
.idea/
.vscode/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Logs
*.log
npm-debug.log*

# Cache
.cache/
.parcel-cache/
```

### 5.2 Project-Specific Patterns

```gitignore
# Prisma
prisma/migrations/

# Database
*.db
*.sqlite

# Temporary files
tmp/
temp/
*.tmp

# Media
uploads/
assets/uploads/
```

## 6. Common Commands Reference

### 6.1 Daily Workflow

```bash
# Start new feature
git checkout develop
git pull
git checkout -b feature/PRD-123-description

# Save work in progress
git add .
git commit -m "feat(PRD-123): WIP: initial implementation"

# Update with latest develop
git fetch origin
git merge origin/develop

# Push to remote
git push origin feature/PRD-123-description

# Create PR (via GitHub CLI or web)
gh pr create --base develop --head feature/PRD-123-description
```

### 6.2 Useful Aliases

```bash
# Add to ~/.gitconfig
[alias]
  co = checkout
  br = branch
  st = status
  lg = log --oneline --graph --decorate
  last = log -1 HEAD
  undo = reset --soft HEAD~1
```

### 6.3 Undo Operations

```bash
# Unstage files
git reset

# Undo last commit (keep changes)
git reset --soft HEAD~1

# Undo last commit (discard changes)
git reset --hard HEAD~1

# Revert pushed commit
git revert <commit-hash>
git push origin develop
```

## 7. Checklist

### Before Commit

- [ ] Follows commit message format
- [ ] Type matches change (feat/fix/refactor/chore/docs)
- [ ] Includes ticket reference (TICKET-xxx)
- [ ] Subject line ≤ 50 characters
- [ ] No unnecessary files staged

### Before Push

- [ ] Branch name follows naming rules
- [ ] Tests passing locally
- [ ] No linting errors

### Before Merge (PR)

- [ ] PR follows title format
- [ ] Description filled out
- [ ] All checks passing
- [ ] At least 1 approval (for critical changes)
- [ ] No merge conflicts

### After Merge

- [ ] Branch deleted locally (optional)
- [ ] Branch deleted remotely (optional)
- [ ] Feature tested in staging

## 8. Quick Reference

| Task | Command |
|------|---------|
| Create branch | `git checkout -b type/TICKET-description` |
| Stage all | `git add .` |
| Commit | `git commit -m "type(TICKET): description"` |
| Push | `git push origin branch-name` |
| Pull develop | `git pull origin develop` |
| Merge | `git merge branch-name` |
| Create tag | `git tag -a v1.0.0 -m "message"` |
| View history | `git log --oneline --graph` |
| View branches | `git branch -a` |