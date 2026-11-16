# STATUS SYNTHESIS REPORT: V2 Mission Migration & Autonomy Audit

**Status**: PENDING APPROVAL
**Priority**: HIGH
**Type**: research/audit
**Created**: 2026-07-05
**Input task**: V2 Mission Migration & Autonomy Audit

---

## 1. Scope Assessment

### Files/Codebase Under Review

| Category | Items | Status |
|----------|-------|--------|
| **Legacy Phase Definitions** | `data/json-data/missions/profiles/phases/` directory | ❌ DOES NOT EXIST — never committed to git |
| **Legacy V1 Profiles** | 9 profiles in `data/json-data/missions/profiles/` | ✅ Local only (gitignored) |
| **V2 Working Profile** | `luna_base_establishment/luna_settlement_profile_v1.json` | ✅ Local only |
| **V2 Phase Files** | 4 phases in `luna_base_establishment/phases/` | ✅ Local only |
| **V2 Manifests** | `manifests_v2/` — 5 files including DRAFT | ✅ Local only |
| **V2 Tasks** | 148+ tasks in `tasks_v2/` | ✅ Local only |
| **Engine** | `TaskExecutionEngineV2` (Ruby) | ✅ Tracked in git |
| **Rake Task** | `luna_mission.rake` | ✅ Tracked in git |
| **Research Summary** | `2026-07-05-RESEARCH-PROFILES-V2-ARCHITECTURE.md` | ✅ Committed to agent-tasks |

### Key Finding: No Legacy Phase Files Exist on Disk

The task mentions "10+ legacy mission phase definitions (e.g., `isru_infrastructure_setup_v1.json`, `habitat_construction_v1.json`)" but:
- `data/json-data/missions/profiles/phases/` directory **does not exist**
- These files were referenced by Gen 1 profiles but **never committed to git**
- The closest legacy phases are in `super-mars-relocation/phases_v1.json` and `psyche_mining_hub/phases/`

### What Actually Exists for Migration

| Legacy Source | Type | Location |
|---------------|------|----------|
| Gen 1 profiles (9 files) | Old-style mission profiles | `data/json-data/missions/profiles/` |
| Super-Mars relocation phases | V1 phases | `data/json-data/missions/super-mars-relocation/phases_v1.json` |
| Psyche mining hub phases (3 files) | V1 phases | `data/json-data/missions/psyche_mining_hub/phases/` |
| Working profile + 4 phases | Current V2 style | `luna_base_establishment/` |

---

## 2. Implementation vs. Intent

### Current State (Implementation)

**Execution Model:**
- **Rake-driven**: `luna_mission.rake` is the sole orchestrator — loads profile JSON, resolves phase paths, seeds inventory, executes phases sequentially
- **Engine treats profiles as manifests**: `TaskExecutionEngineV2.new(target, manifest_or_path)` accepts a profile hash and reads only `phases[]`, `resources`, and `required_hardware[]`
- **Phase files are ultra-minimal**: Engine only reads `tasks[].task_ref` — everything else is metadata
- **No dynamic selection**: Phases execute in hardcoded order (`["power_comms", "isru_deployment", "gas_processing"]`)
- **No AI Manager integration**: AI Manager does NOT read profiles directly

**Data Structure:**
```
Profile (Hash) → phases[] → task_list_file → Phase JSON → tasks[].task_ref → Task JSON → effects[]
```

### Target State (Intent)

**Desired Architecture:**
- **Modular phase registry**: AI Manager queries phases by `task_affinity`, `prerequisites`, `body_type`
- **Dynamic sequencing**: DAG-based phase ordering based on dependencies, not array position
- **World-agnostic profiles**: Profiles parameterized by body type, not hardcoded to Luna
- **AI-driven execution**: Phases triggered programmatically, not via Rake tasks

### Discrepancies Identified

| Gap | Current | Target | Impact |
|-----|---------|--------|--------|
| Phase selection | Hardcoded array order | Dynamic based on `task_affinity` + prerequisites | AI Manager cannot adapt to different body types |
| Phase dependencies | None (array position only) | Explicit DAG with `dependencies[]` field | No conditional phase execution |
| Profile-body mapping | Static `world_requirements.body_type` | Registry lookup by body type | Cannot auto-select profiles for new bodies |
| Execution trigger | Rake task only | AI Manager API calls | No programmatic mission control |
| Success checking | `success_conditions` exists but never evaluated | Engine checks completion criteria | No automated mission status |
| Data accessibility | All JSON gitignored | Gemini needs embedded data in summaries | Agent handoff requires markdown summaries |

---

## 3. Proposed Action

### Phase 1: Audit (No Changes — Analysis Only)

**1a. Engine Autonomy Gap Audit**
- Map every field `TaskExecutionEngineV2` reads from profiles/phases/tasks
- Identify missing hooks for dynamic phase triggering
- Document what the engine CAN do vs what it CANNOT do
- Output: `MANAGER_AUTONOMY_GAP.md` with API signatures needed

**1b. Legacy Data Inventory**
- List ALL legacy V1/V2 files that exist locally (not just referenced ones)
- Categorize by: can migrate, needs rewrite, obsolete
- Note which are gitignored vs tracked
- Output: Migration inventory table in summary report

### Phase 2: Design (Pending Approval)

**2a. Phase Registry Schema Design**
- Define `phase_registry.json` structure for AI Manager lookup
- Standardize V2 phase schema with `dependencies[]`, `conditions`, `applicable_body_types`
- Map existing phases to new schema

**2b. Profile-Body Mapping Design**
- Design registry that maps body types → applicable profiles
- Define parameterization rules for world-agnostic templates

### Phase 3: Implementation (Pending Approval)

**3a. Phase Normalization**
- Convert existing V2 phases to standardized schema
- Add `dependencies`, `conditions`, `applicable_body_types` fields
- Create `phase_registry.json` from normalized phases

**3b. Autonomy Hooks**
- Add profile registry lookup service (new Ruby class)
- Add phase dependency resolver (DAG evaluation)
- Add dynamic phase selector for AI Manager

---

## 4. Critical Questions Before Proceeding

1. **Which legacy files should be migrated?** The task mentions "10+ legacy phase definitions" but the referenced directory doesn't exist. Should I work with:
   - Super-Mars relocation phases?
   - Psyche mining hub phases?
   - Both?
   - Or design the schema first and migrate later?

2. **Scope of autonomy hooks:** Should Phase 3 include:
   - Just the registry/lookup layer (AI Manager queries phases)?
   - Plus DAG-based execution engine changes?
   - Or just document what's needed (Phase 1b) without implementing?

3. **Migration priority:** Is Luna ISRU the only migration target, or should the schema support all mission types (Mars, Psyche, orbital, etc.)?

---

## 5. Stop Conditions

- Legacy phase files referenced in task don't exist — flagged above
- Any architectural decision required before audit can continue — waiting for user input on questions in Section 4
- If `data/` gitignore policy changes — would need to reassess data handoff strategy

---

**Report saved to**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/summaries/2026-07-05-V2-MISSION-MIGRATION-AUDIT-SYNTHESIS.md`
**Awaiting**: User approval to proceed with Phase 1 audit
