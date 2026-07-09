---
status: completed
priority: HIGH
type: implementation
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
date_created: 2026-07-05
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-05-HIGH-PHASE-NORMALIZATION-REGISTRY-CREATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-05-HIGH-PHASE-NORMALIZATION-REGISTRY-CREATION.md \
         projects/galaxy_game/tasks/active/2026-07-05-HIGH-PHASE-NORMALIZATION-REGISTRY-CREATION.md
  Then open the moved file and change: status: completed → status: active
  Paste the output of both commands in chat before proceeding.

LIFECYCLE: backlog → active → completed (git mv only, never cp or plain mv)
Verify with: find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
  -name "2026-07-05-HIGH-PHASE-NORMALIZATION-REGISTRY-CREATION.md"
Only ONE result should exist. Paste this output before starting synthesis.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.
CRITICAL: Save synthesis report as MD file to summaries/ BEFORE starting any work.
```

---

# PHASE REGISTRY CREATION — AI MANAGER LOOKUP INDEX

**Status**: BACKLOG
**Priority**: HIGH
**Type**: implementation
**Created**: 2026-07-05
**Input**: Research summary at `summaries/2026-07-05-RESEARCH-PROFILES-V2-ARCHITECTURE.md`

---

## Context

The V2 mission system canonical examples are complete in `missions_v2/`. All 4 phase files and 17 Luna tasks already exist. This task creates the `phase_registry.json` lookup index that enables the AI Manager to query phases by body type, capabilities, and task affinity.

**Research summary location**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-07-05-RESEARCH-PROFILES-V2-ARCHITECTURE.md`

---

## What Already Exists (Do Not Recreate)

| Item | Path | Status |
|------|------|--------|
| 4 phase files | `missions_v2/phases/*_v2.json` | ✅ Complete |
| 17 Luna tasks | `missions_v2/tasks/*_v2.json` | ✅ Complete |
| Task index | `missions_v2/task_index/all_tasks.json` | ✅ Complete |
| Luna profile | `missions_v2/profiles/luna_base_profile_v2.json` | ✅ Complete |
| Mission plan | `missions_v2/mission_plans/luna_precursor_mission_plan_v2.json` | ✅ Complete |

**Do NOT:**
- Re-normalize the 4 phase files (they are already normalized)
- Re-convert the 17 tasks (already done with v2.1 template)
- Re-map all 148+ tasks (out of scope — Luna only)

---

## Implementation Steps

### Step 1: Verify Phase Files Exist and Are Valid

Check that all 4 phase files are present in `missions_v2/phases/`:
- ✅ `power_comms_v2.json`
- ✅ `isru_deployment_v2.json`
- ✅ `gas_processing_v2.json`
- ✅ `robot_logistics_v2.json`

Parse each file to confirm valid JSON. Verify they have required fields: `phase_id`, `name`, `description`, `applicable_body_types`, `dependencies`, `task_refs`.

### Step 2: Create phase_registry.json

Create `missions_v2/phase_registry.json` that maps each phase to its lookup metadata. The registry enables AI Manager to query phases by body type and task affinity.

**Schema:**

```json
{
  "registry_version": "1.0",
  "last_updated": "2026-07-05",
  "phases": [
    {
      "phase_id": "power_comms",
      "phase_file": "missions_v2/phases/power_comms_v2.json",
      "name": "Power & Communications",
      "applicable_body_types": ["airless_rocky", "thin_atmosphere", "atmospheric"],
      "required_capabilities": ["power_generation", "communications"],
      "task_count": 3,
      "task_affinity": ["deploy_solar_rig", "deploy_puh_and_ppmu", "deploy_comms_equipment"]
    },
    {
      "phase_id": "isru_deployment",
      "phase_file": "missions_v2/phases/isru_deployment_v2.json",
      "name": "ISRU Deployment",
      "applicable_body_types": ["airless_rocky"],
      "required_capabilities": ["resource_extraction", "processing"],
      "task_count": 4,
      "task_affinity": ["deploy_lspu", "deploy_gas_separator", "deploy_pve_unit", "deploy_regolith_harvester_rover"]
    },
    {
      "phase_id": "gas_processing",
      "phase_file": "missions_v2/phases/gas_processing_v2.json",
      "name": "Gas Processing",
      "applicable_body_types": ["airless_rocky"],
      "required_capabilities": ["volatiles_processing", "thermal_extraction"],
      "task_count": 6,
      "task_affinity": ["deploy_thermal_extraction_unit", "deploy_volatiles_storage", "isru_stockpile_initiation", "site_prep_foundation", "surface_preparation_unit_operations", "print_inflatable_tank_shells"]
    },
    {
      "phase_id": "robot_logistics",
      "phase_file": "missions_v2/phases/robot_logistics_v2.json",
      "name": "Robot Logistics",
      "applicable_body_types": ["airless_rocky"],
      "required_capabilities": ["resource_transport", "charging_infrastructure"],
      "task_count": 4,
      "task_affinity": ["deploy_regolith_harvester_rover", "deploy_robot_charging_port", "car_300_charge_cycle", "construction_zone_leveling"]
    }
  ]
}
```

### Step 3: Verify Task Affinity Accuracy

Cross-check that each `task_affinity` array matches the actual `task_refs` in the corresponding phase file. The affinity array maps task file names to readable task identifiers for AI Manager queries.

---

## Stop Conditions

- If any phase file is missing or has invalid JSON — document and stop
- If phase file schema doesn't match expected fields — document and stop
- If task count differs from actual task_refs in phase file — flag and continue with actual data

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent]
**Completion date**: YYYY-MM-DD
**Files created**: 1 (`missions_v2/phase_registry.json`)
**Verification**: All 4 phases registered, 17 tasks mapped
**Output location**: `missions_v2/phase_registry.json`
