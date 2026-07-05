---
status: backlog
priority: HIGH
type: feature
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
date_created: 2026-07-04
phase: 4_robot_logistics
supersedes: 2026-06-22-HIGH-IMPLEMENTATION-PHASE4-ROBOT-LOGISTICS-SITE-HARDENING.md
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase5+/2026-07-04-HIGH-FEATURE-PHASE4-ROBOT-LOGISTICS-SITE-HARDENING.md

LIFECYCLE: current → active → completed (use git mv, never copy)
READ FIRST: Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Create STATUS SYNTHESIS REPORT in chat BEFORE starting any work (template in task file).
```

---

# TASK: Phase 4 — Robot Logistics & Site Hardening Implementation

**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-07-04
**Phase**: phase5+ (Luna mission validation)
**Supersedes**: `2026-06-22-HIGH-IMPLEMENTATION-PHASE4-ROBOT-LOGISTICS-SITE-HARDENING.md`

---

## Context

Implement Phase 4 (`robot_logistics_site_hardening`) for Luna Precursor Mission V2. This phase deploys surface charging stations, validates CAR-300 robot readiness, and initiates ISRU stockpiling for Mission 2 construction materials.

**Superseded task note**: The original task (2026-06-22) referenced stale file paths that no longer exist. Prerequisites are already resolved:
- Gas separator duplicate deleted ✅
- Robot charging port cleanup completed ✅
- Manifest path now via `GalaxyGame::Paths::MISSIONS_PATH`

---

## Architecture

### Phase ID
`robot_logistics_site_hardening`

### Mission Goal
Establish autonomous robot support infrastructure and harden the ISRU supply chain for continuous operation.

### Task Sequence (Create in tasks_v2/)

| Task ID | Description | Preconditions | Effects |
|---------|-------------|---------------|---------|
| `deploy_robot_charging_port` | Deploy and anchor robot charging stations; connect to PPMU power grid. | `deploy_puh_and_ppmu` complete | Deploy 2x `robot_charging_port`; Connect to `planetary_power_management_unit` |
| `car_300_charge_cycle` | CAR-300 robots dock at charging ports, complete full charge cycle, run diagnostics. *CAR-300 robots are already deployed in Phases 1-2 manifest but currently idle; this task transitions them from idle → charging → charged state.* | `deploy_robot_charging_port` complete | Set robot state to charged; Run diagnostic check |
| `isru_stockpile_initiation` | Trigger TEU/PVE/Gas Separator chain to produce initial batch of processed regolith & cryo-fuels. *Simulation trigger, not real-time production loop.* | Phase 3 ISRU units operational | Start production cycle; Track material output to settlement inventory |
| `construction_zone_leveling` | CAR-300 robots clear, compact, and level designated construction pad for future habitat modules. | `car_300_charge_cycle` complete | Mark zone as leveled; Consume robot power/fuel |

### Manifest Status (Verified)
- CAR-300 robots (2x) — staged in manifest
- Robot charging ports (2x) — staged in manifest
- Total Phase 4 mass: ~5,900 kg (within HLT 150,000 kg capacity)

---

## Prerequisites — Already Resolved

| Item | Status | Notes |
|------|--------|-------|
| Gas separator duplicate deleted | ✅ Done | `blueprints/units/infrastructure/gas_separator_unit_bp.json` removed; canonical at `blueprints/units/production/refineries/gas_separator_unit_bp.json` |
| Robot charging port cleanup | ✅ Done | `modules/energy/robot_charging_port_bp.json` removed; canonical at `units/infrastructure/robot_charging_port_bp.json` |
| Manifest path convention | ✅ Updated | Use `GalaxyGame::Paths::MISSIONS_PATH.join("manifests_v2", "lunar_precursor_manifest_v2_DRAFT.json")` |

**Gate**: All prerequisites verified. Phase 4 implementation can proceed.

---

## Files Involved

### Primary — create/edit these
| File | Purpose |
|------|---------|
| `galaxy_game/app/data/json-data/missions/tasks_v2/task_deploy_robot_charging_port.json` | V2 task: deploy charging ports |
| `galaxy_game/app/data/json-data/missions/tasks_v2/task_car_300_charge_cycle.json` | V2 task: CAR-300 charge cycle |
| `galaxy_game/app/data/json-data/missions/tasks_v2/task_isru_stockpile_initiation.json` | V2 task: ISRU stockpile trigger |
| `galaxy_game/app/data/json-data/missions/tasks_v2/task_construction_zone_leveling.json` | V2 task: construction zone leveling |
| `luna_base_establishment/phases/phase_4_robot_logistics.json` | Phase file referencing 4 tasks above |

### Reference — read only
| File | Why You Need It |
|------|-----------------|
| `galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb` | V2 engine effects model (deploy_unit, connect_units, set_unit_state) |
| `GalaxyGame::Paths::MISSIONS_PATH` | Canonical path to mission data directory |
| `luna_mission.rake` | Rake task that orchestrates Phase 1-4 execution |

---

## Implementation Steps

### Step 1 — Create V2 task files in tasks_v2/

Follow the V2 effects-based model. Each task file should use:
- `task_id`, `name`, `description`
- `effects` array with actions: `deploy_unit`, `connect_units`, `set_unit_state`, `check_unit_state`
- `prerequisites` array referencing previous task IDs

### Step 2 — Create phase_4_robot_logistics.json

In `luna_base_establishment/phases/`:
```json
{
  "phase_id": "robot_logistics_site_hardening",
  "name": "Robot Logistics & Site Hardening",
  "task_list_file": "tasks_v2/phase_4_robot_logistics_tasks.json"
}
```

### Step 3 — Wire into luna_settlement_profile_v1.json

Add `phase_4_robot_logistics` to the phases array after Phase 3. Maintain proper sequencing.

### Step 4 — Validate with TaskExecutionEngineV2

```bash
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rake luna_mission:execute 2>&1 | tail -30
```

Verify all 4 Phase 4 tasks execute cleanly. Report per-task pass/fail and final settlement state.

---

## Acceptance Criteria
- [ ] All 4 V2 task files created in `tasks_v2/`
- [ ] Phase file created in `luna_base_establishment/phases/`
- [ ] Profile wired with Phase 4 after Phase 3
- [ ] All 4 Phase 4 tasks execute without errors via rake
- [ ] Final rake shows 12/12 total tasks complete (Phase 1 + 2 + 3 + 4)
- [ ] No blueprint or port allocation errors
- [ ] Settlement ready for Mission 2 (Lava Tube Construction phase)

---

## Stop Conditions — escalate to user immediately if:
- V2 task file format is unclear — stop and report what's ambiguous
- CAR-300 robots not found in manifest — stop and report
- Blueprint data for `robot_charging_port` or `car_300` missing — stop and report

---

## Commit Instructions
```bash
git add galaxy_game/app/data/json-data/missions/tasks_v2/task_deploy_robot_charging_port.json galaxy_game/app/data/json-data/missions/tasks_v2/task_car_300_charge_cycle.json galaxy_game/app/data/json-data/missions/tasks_v2/task_isru_stockpile_initiation.json galaxy_game/app/data/json-data/missions/tasks_v2/task_construction_zone_leveling.json
git commit -m "feat: add Phase 4 robot logistics tasks (charging, CAR-300, ISRU stockpile, zone leveling)"
git push
```

---

## Completion Report
**Completed by**: [agent]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `[file]` — [description]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

---

## Handoff Summary
HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
