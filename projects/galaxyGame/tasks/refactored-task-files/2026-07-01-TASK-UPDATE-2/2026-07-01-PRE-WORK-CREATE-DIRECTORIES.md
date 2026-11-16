---
name: "Pre-Work: Create Missing Directories for Guardrails Migration"
priority: HIGH
phase: 5
type: infrastructure
status: backlog
created: 2026-07-01
---

# PRE-WORK TASK: Create Missing Directories

**Purpose**: Prerequisite work needed before guardrails refactoring executor can proceed  
**Blocker**: `2026-07-01-TASK-GUARDRAILS-UNIFIED-RECONCILIATION.md` cannot execute without these directories  

---

## Scope

Create two missing directories that Qwen validation identified:

1. **`docs/systems/`** — Does not exist (currently only `docs/architecture/systems/` exists)
   - Needed for: `em_power_shield_tiers.md` (or clarify if should go to `docs/architecture/stations/` instead)
   - Action: `mkdir -p docs/systems/`

2. **`docs/architecture/terrain/`** — Does not exist
   - Needed for: Terrain generation content migration (~120 lines from GUARDRAILS.md section 7.5)
   - Action: `mkdir -p docs/architecture/terrain/`

---

## Success Criteria

- [ ] `docs/systems/` directory created and empty
- [ ] `docs/architecture/terrain/` directory created and empty
- [ ] Directories added to git (commit message below)

---

## Commit Instructions

```bash
mkdir -p docs/systems docs/architecture/terrain
git add -A docs/systems docs/architecture/terrain
git commit -m "chore: create missing docs directories for guardrails migration

- Add docs/systems/ for em_power_shield system documentation
- Add docs/architecture/terrain/ for terrain generation content
- Prerequisite for TASK-GUARDRAILS-UNIFIED-RECONCILIATION execution"
```

---

## Related Tasks

- Blocker: `2026-07-01-TASK-GUARDRAILS-UNIFIED-RECONCILIATION.md` (awaiting this pre-work)
- Validation: `2026-07-01-TASK-GUARDRAILS-VALIDATION-QWEN.md` (identified this requirement)
