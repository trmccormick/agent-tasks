# Planning Review: Phase 4 Robot Logistics & Site Hardening
**Date**: 2026-07-03
**Reviewer**: Planning Agent (Qwen)
**Task File**: `2026-06-22-HIGH-IMPLEMENTATION-PHASE4-ROBOT-LOGISTICS-SITE-HARDENING.md`

---

## Review Summary

**Status**: ⚠️ **NEEDS CORRECTIONS BEFORE HANDOFF** — 5 mismatches found, all fixable.
Task is structurally sound but references stale paths and missing files.

---

## Mismatches Found

### 1. Gas Separator Duplicate Path — STALE
**Task says**: Delete `data/json-data/blueprints/units/infrastructure/gas_separator_unit_bp.json` (1800kg duplicate)
**Reality**: That file does NOT exist at that path. The actual files are:
- `data/json-data/blueprints/modules/production/gas_separator_bp.json` (id: "gas_separator") — module version, no mass_kg
- `data/json-data/blueprints/units/production/refineries/gas_separator_unit_bp.json` (id: "gas_separator_unit") — unit version, no mass_kg

**Impact**: Medium. The prerequisite fix path is wrong. Both files lack `mass_kg`, which may cause lookup issues but neither is the "1800kg malformed duplicate" described in the task.

### 2. Phase 4 Task Files — PARTIALLY MISSING
**Task says**: Create 4 task files:
- `task_deploy_robot_charging_port.json` ✅ EXISTS
- `task_car_300_charge_cycle.json` ❌ DOES NOT EXIST
- `task_isru_stockpile_initiation.json` ❌ DOES NOT EXIST
- `task_construction_zone_leveling.json` ❌ DOES NOT EXIST

**Impact**: High. Only 1 of 4 Phase 4 tasks exists. The other 3 need to be created as part of implementation.

### 3. Profile Phases — NO PHASE 4 YET
**Task says**: Wire Phase 4 into `luna_settlement_profile_v1.json` phases array
**Reality**: Profile has only 3 phases: `power_comms`, `isru_deployment`, `gas_processing`. No Phase 4 entry exists.

**Impact**: Low. This is expected — Phase 4 hasn't been wired in yet. The task correctly identifies this as work to do.

### 4. Manifest File Name — MISMATCH
**Task says**: Reference `lunar_precursor_manifest_v2_DRAFT.json`
**Reality**: Two manifest files exist:
- `data/json-data/missions/luna_base_establishment/luna_base_establishment_manifest_v2.json` (referenced by profile)
- `data/json-data/missions/manifests_v2/lunar_precursor_manifest_v2_DRAFT.json` (referenced by task)

**Impact**: Medium. The task references a different manifest than the profile uses. Need to clarify which is canonical for Phase 4 deployment.

### 5. Engine State — ✅ CURRENT
**Task says**: Use `deploy_unit`, `connect_units`, `set_unit_state`, `check_unit_state` effects
**Reality**: Both `connect_units` (line 342) and `register_to_bus` (line 360) cases exist in TaskExecutionEngineV2.

**Impact**: None. Engine supports all effects the task references. The new `register_to_bus` case is available if Phase 4 tasks need hub routing.

---

## Classification Check

**Task location**: `backlog/phase5+/`
**Task calls itself**: "Phase 4" (Luna mission phase)

**Assessment**: This is NOT a classification error. The task's "Phase 4" refers to the **Luna mission phase numbering** (power_comms=1, isru_deployment=2, gas_processing=3, robot_logistics=4), not the project folder structure. The `phase5+/` folder is correct for Luna precursor work per the canonical phase alignment.

---

## Recommended Corrections to Task File

### Correction 1: Fix Gas Separator Prerequisite Path
Replace the stale deletion path with actual file paths and clarify which is the duplicate.

### Correction 2: Clarify Manifest Reference
Note that `luna_base_establishment_manifest_v2.json` (in luna_base_establishment/) is the profile-referenced manifest, while `lunar_precursor_manifest_v2_DRAFT.json` (in manifests_v2/) is a separate draft. Clarify which Phase 4 tasks should target.

### Correction 3: Note Missing Task Files
Add a note that only `task_deploy_robot_charging_port.json` exists; the other 3 need to be created during implementation.

---

## Readiness Assessment

| Criterion | Status |
|-----------|--------|
| Prerequisites verified | ⚠️ Gas separator path stale, needs correction |
| Engine supports required effects | ✅ Yes (connect_units + register_to_bus both available) |
| Profile can accept Phase 4 | ✅ Yes (just needs phases array update) |
| Task files exist for all steps | ❌ Only 1 of 4 exists |
| Manifest reference accurate | ⚠️ Two manifests, needs clarification |

**Verdict**: **Ready for implementation handoff after corrections to task file.** The work is valid and the scope is clear — only documentation corrections needed.

---

## Delta Summary

- ✅ Phase classification correct (Luna mission phase numbering, not project folder)
- ⚠️ Gas separator duplicate path stale — actual files differ from task description
- ❌ 3 of 4 Phase 4 task files missing — must be created during implementation
- ⚠️ Manifest name mismatch between profile and task file references
- ✅ Engine state current — all required effect types supported
