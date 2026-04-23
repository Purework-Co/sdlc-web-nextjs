# Quick Reference: Optimized OpenCode Configuration

## 🎯 At a Glance

**Optimized for**: Free AI models (Minimax M2.5, Claude 3.5 Haiku, Qwen, etc)  
**Token savings**: ~48K tokens per session (73% reduction)  
**Model efficiency**: Baseline reduced from ~66K → ~18K tokens  

---

## 👤 For Non-Technical Users

### 4-Step Workflow

```
Step 1: /clarify
  → "What are you trying to accomplish?"
  → "Who will use this?"
  → "What's the expected outcome?"

Step 2: /document
  → System creates formal specification

Step 3: /validate
  → "Is this correct?"

Step 4: /translate
  → See technical implementation plan

Then: /create-feature
  → Development team builds it
```

---

## 🛠️ Core Commands (17)

| Category | Commands |
|----------|----------|
| Consultant | `/clarify`, `/document`, `/validate`, `/translate` |
| Build | `/create-feature`, `/component`, `/page`, `/form` |
| API | `/api-crud`, `/auth-setup` |
| Database | `/db-model`, `/db-migration` |
| Docs | `/doc-spec`, `/doc-implementation` |
| QA | `/test`, `/build`, `/lint`, `/typecheck` |

---

## 🔄 Optional Features (Load on Demand)

| Feature | Trigger |
|---------|---------|
| **Testing** | `/test` command |
| **RBAC** | Feature mentions roles/permissions |
| **ClickUp** | User asks to push to ClickUp |
| **Deployment** | Docker/Podman requests |

---

## 📂 Key Files

| File | Purpose |
|------|---------|
| `.opencode/opencode.json` | Main config (optimized) |
| `.opencode/docs/README.md` | User documentation index |
| `.opencode/config/lazy-load.json` | Feature lazy-loading rules |
| `.opencode/config/user-prompts.json` | Structured prompts |
| `.opencode/OPTIMIZATION_REPORT.md` | Full optimization details |

---

## 📚 Documentation

**Core (Always Available)**:
- `01-sdlc-guidance.md` - Tech stack & structure
- `03-feature-request.md` - Feature workflow
- `06-api-guidance.md` - API patterns

**On-Demand** (in `archive/docs/`):
- Testing guidance → Load on `/test`
- RBAC guide → Load if feature needs roles
- Database migrations → Load for DB changes

---

## ✅ What Works

✅ Non-tech user guidance (4-step workflow)  
✅ Full-stack development (Next.js, Prisma, NextAuth)  
✅ API creation & documentation  
✅ Component & page generation  
✅ Database migrations  
✅ Code quality checks  
✅ Optional testing & RBAC  
✅ Optional ClickUp integration  

---

## 🚀 Getting Started

1. **User describes need**
   ```
   "I need a login page with email verification"
   ```

2. **System clarifies**
   ```
   /clarify → asks 4 structured questions
   ```

3. **Creates specification**
   ```
   /document → formal spec created
   ```

4. **Gets approval**
   ```
   /validate → user confirms
   ```

5. **Converts to technical plan**
   ```
   /translate → developers see implementation plan
   ```

6. **Builds it**
   ```
   /create-feature → full feature implemented
   ```

---

## 📊 Token Efficiency

| Phase | Tokens |
|-------|--------|
| Before optimization | ~66K |
| Baseline (after) | ~18K |
| **Saved** | **~48K** ✅ |
| Available for work | +48K tokens |

---

## 🔄 Archiving Strategy

All removed features are preserved in `.opencode/archive/`:
- 23 archived commands
- 2 archived agents
- 5 archived skills
- 11 archived docs
- 5 archived templates

**Restore anytime**: Move from `archive/` back to main folder if needed.

---

## 🎯 Model Compatibility

✅ **Works great with**:
- Minimax M2.5
- Claude 3.5 Haiku
- Qwen 2.5 1.5B
- Other <10B parameter models

⚠️ **Not optimal for**:
- Very large models (Claude 3, GPT-4) - Use full config instead
- Offline-only environments - Needs context7 MCP

---

## 📞 Support

**Need more info?**
- Read: `.opencode/docs/README.md`
- Report: `https://github.com/anomalyco/opencode`

**Want to customize?**
- Edit: `.opencode/config/lazy-load.json`
- Re-enable: Move files from `archive/` folder
- Adjust: `.opencode/agents/consultant.md`

---

**Commit**: `2e02d9f`  
**Optimized for**: Free AI models  
**Status**: ✅ Production Ready
