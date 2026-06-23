# Phase 5 — Luna Simulation Testing Prerequisites

**Status**: Active (13 tasks as of 2026-06-22 session)  
**Last Updated**: 2026-06-22

---

## Purpose of Phase 5

Phase 5 is **exclusively** for tasks that are **prerequisites needed to run Luna settlement simulation testing**.

Per **DEVELOPMENT_ROADMAP.md** (canonical):
> "Only create tasks that are prerequisites needed BEFORE the Luna simulation can run. Do NOT add new features to phase5+."

### What Phase 5 Should Contain

✅ **Tasks That Belong in Phase 5**:
- AI Manager decision logic (site selection, resource allocation, escalation, inventory management)
- ISRU production validation (Track A operational data, RSpec test suite, production proofs)
- Luna fuel loop proof (LOX + water production verified)
- Settlement deployment logic (manifest-driven provisioning, no-magic resource sourcing)
- Data-driven mission profiles and phase definitions
- Environmental/atmospheric state tracking and validation
- Monitor/Canvas rendering foundations (critical UI infrastructure)
- GGMap visualization layers (scientific + strategic data display)

❌ **Tasks That Do NOT Belong in Phase 5**:
- Design work (asset generation, style guides, component catalogs)
- Cosmetic UI (landing page redesigns, non-critical user-facing features)
- Post-MVP investigations (terrain quality polish, advanced features)
- Infrastructure for later phases (drone bay design, LEO depot, advanced systems)
- Documentation cleanup or maintenance work

---

## Current Tasks in Phase 5 (13 total)

### AI Manager Services & Infrastructure (7 tasks)
1. `2026-02-11-HIGH-AI_MANAGER-SITE-SELECTION-ALGORITHM.md` — Site selection for optimal settlement placement
2. `2026-02-11-HIGH-FEATURE-AI-MANAGER-ESCALATION-DEPENDENCIES.md` — Escalation service dependency resolution
3. `2026-02-11-HIGH-FEATURE-AI-MANAGER-RESOURCE-ALLOCATION-ENGINE.md` — Resource allocation models and validation
4. `2026-02-11-HIGH-FEATURE-AI-MANAGER-SERVICE-INTEGRATION.md` — Service orchestrator + Sidekiq worker integration
5. `2026-02-15-HIGH-FEATURE-IMPLEMENT-SETTLEMENT-PATTERN-LOGIC.md` — Settlement pattern templates and logic
6. `2026-03-27-HIGH-REFACTOR-ESCALATION-SERVICE-NO-MAGIC-ROBOT-DEPLOYMENT.md` — Fix No-Magic Protocol violations
7. `2026-05-27-HIGH-REFACTOR-AI-MANAGER-USE-SETTLEMENT-DEPLOYMENT-SERVICE.md` — **CRITICAL BLOCKER**: Replace direct BaseSettlement.create! calls in 3 locations with establish_from_craft()

### Luna Simulation Loop & Bootstrap (3 tasks)
8. `2026-06-17-LUNA-SIMULATION-LOOP.md` — Luna Precursor V2 bootstrap testing
9. `2026-06-21-CRITICAL-FEATURE-LUNA-INITIAL-EQUIPMENT-PROVISIONING.md` — Manifest inventory seeding
10. `2026-06-17-HIGH-FEATURE-LUNA-Bootstrap-Manufacturing-Loop.md` — Luna MVP bootstrap sequence

### Task Execution & Data Integration (3 tasks)
11. `2026-05-27-HIGH-ARCHITECTURE-VERIFY-TASK-EXECUTION-ENGINE-TASKS-V2.md` — TaskExecutionEngineV2 verification (confirmed working)
12. `2026-04-04-HIGH-BUGFIX-MATERIAL-PROCESSING-SERVICE-PVE-VOLATILES.md` — PVE ISRU volatiles handling
13. `2026-06-21-HIGH-RESEARCH-HLT-PAYLOAD-CAPACITY-AND-MASS-DATA-GAPS.md` — HLT mass data validation (blocking manifest review)

---

## Critical Blocker Alert ⚠️

**Task**: `2026-05-27-HIGH-REFACTOR-AI-MANAGER-USE-SETTLEMENT-DEPLOYMENT-SERVICE.md`

**Issue**: Three locations with direct `BaseSettlement.create!` that bypass `SettlementDeploymentService`:
- `app/services/ai_manager/manager.rb:235`
- `app/services/ai_manager/system_architect.rb:377`
- `app/services/ai_manager/task_execution_engine_v2.rb:107` ← **ACTIVELY USED** in `rake luna_mission:execute`

**Impact**: Skips cargo verification and unit deployment steps required for proper Luna settlement creation.

**Resolution Pattern**: Replace with `SettlementDeploymentService.establish_from_craft(craft, location, manifest_name)`

**Priority**: URGENT — This task blocks Luna simulation testing until refactored.

---

## Next Steps

All tasks are ready to dispatch. Coordinate with Luna simulation agent to ensure:
1. HLT payload research completes (blocking manifest validation)
2. SettlementDeploymentService refactor applied (blocking task execution)
3. All phase5 tasks integrated into luna_mission:execute test harness
- **Phase 6+**: Lava-tube base construction infrastructure
- **Phase 9+**: Advanced systems and post-MVP work
- **design/**: Asset generation, style guides, cosmetic work
- **backlog/**: Archived, obsolete, or post-MVP ideas

**Step 3: Update Task Files**
- Add a header explaining relocation decision and rationale
- Ensure TASK_TEMPLATE format (YAML frontmatter, standard structure)
- Verify all fields correct for the phase it's moving to

**Step 4: Execute the Move**
- Move tasks to their correct folders
- Verify counts match the expected distribution

**Step 5: Close Reorganization Attempt 3**
- Once all tasks are routed, delete the `reorganization_attempt_3/` folder
- Update PHASE_STRUCTURE.md and status.md with final counts

---

## Target Phase 5 Composition

After reclamation is complete, phase5 should contain approximately **25-30 tasks**, all directly supporting Luna simulation testing:

| Category | Example Tasks | Estimated Count |
|----------|----------------|-----------------|
| AI Manager Logic | Site selection, escalation, resource allocation | 6-8 |
| ISRU Production | Track A validation, production proofs, operational data | 5-7 |
| Luna Infrastructure | Settlement deployment, manifest management, fuel loop | 5-7 |
| Data/Config | Mission profiles, phase definitions, state tracking | 4-5 |
| UI Foundations | Monitor rendering, GGMap layers | 3-4 |
| **Total** | | **25-30** |

---

## For Agents Working with Phase 5

⚠️ **Before creating any new phase5 task:**

Ask yourself: **"Does Luna settlement simulation testing require this task to be complete FIRST?"**

- If **YES** → Phase 5 task. Create it.
- If **NO** → Does NOT belong in phase 5. Determine correct phase/folder instead.

**Examples**:
- "AI Manager needs to select settlement sites before simulation can run" → Phase 5 ✅
- "Need to redesign the landing page" → Not Phase 5 ❌
- "ISRU production must work to feed the simulation" → Phase 5 ✅
- "Investigating terrain quality for scientific accuracy" → Not Phase 5 ❌
- "Component catalog for lava-tube base" → Not Phase 5 (phase 6+) ❌

---

## Reference Documents

- [DEVELOPMENT_ROADMAP.md](../../planning/DEVELOPMENT_ROADMAP.md) — Canonical phase definitions and scope
- [PHASE_STRUCTURE.md](../PHASE_STRUCTURE.md) — Overview of all phases and their purposes
- [reorganization_attempt_3/README.md](../reorganization_attempt_3/README.md) — Details on each misplaced task
- [status.md](../../status.md) — Session-by-session project status

---

## Workflow: From Reorganization_Attempt_3 → Phase 5 Reclamation

```
reorganization_attempt_3/ (46-ish tasks)
    ↓ (review each)
    ├─→ phase5/ (add ~25-30 Luna prerequisites)
    ├─→ phase6+/ (add lava-tube infrastructure)
    ├─→ phase9+/ (add post-MVP advanced work)
    ├─→ design/ (move asset/style work)
    └─→ backlog/ (archive old ideas)
    
Once all routed: DELETE reorganization_attempt_3/
```

---

**This folder is ready to receive Phase 5 tasks once the reorganization is complete.**
