---
status: backlog
priority: HIGH
category: IMPLEMENTATION
date_created: 2026-06-22
phase: 4_robot_logistics
---

# Phase 4: Robot Logistics & Site Hardening Implementation

## Overview
Implement Phase 4 (robot_logistics_site_hardening) for Luna Precursor Mission V2. This phase deploys surface charging stations, validates CAR-300 robot readiness, and initiates ISRU stockpiling for Mission 2 construction materials.

## Architecture (Approved)

### Phase ID
`robot_logistics_site_hardening`

### Mission Goal
Establish autonomous robot support infrastructure and harden the ISRU supply chain for continuous operation.

### Task Sequence (Create in tasks_v2/)

| Task ID | Description | Preconditions | Effects |
|---------|-------------|---------------|---------|
| deploy_robot_charging_port | Deploy and anchor robot charging stations; connect to PPMU power grid. | deploy_puh_and_ppmu complete | Deploy 2x robot_charging_port; Connect to planetary_power_management_unit |
| car_300_charge_cycle | CAR-300 robots dock at charging ports, complete full charge cycle, run diagnostics. *Note: CAR-300 robots are already deployed in Phases 1-2 manifest but currently idle; this task transitions them from idle → charging → charged state using the new ports.* | deploy_robot_charging_port complete | Set robot state to charged; Run diagnostic check |
| isru_stockpile_initiation | Trigger TEU/PVE/Gas Separator chain to produce initial batch of processed regolith & cryo-fuels. *Note: This is a simulation trigger, not a real-time production loop. The engine should mark ISRU units as producing and add initial material quantities to settlement inventory for Mission 2 prep.* | Phase 3 ISRU units operational | Start production cycle; Track material output to settlement inventory |
| construction_zone_leveling | CAR-300 robots clear, compact, and level designated construction pad for future habitat modules. | car_300_charge_cycle complete | Mark zone as leveled; Consume robot power/fuel |

### Manifest Status
✅ CAR-300 robots (2x) — already staged in lunar_precursor_manifest_v2_DRAFT.json
✅ Robot charging ports (2x) — already staged in manifest
✅ Total Phase 4 mass: ~5,900 kg (well within HLT 150,000 kg capacity)

## ⚠️ Prerequisite Fixes (MUST Complete Before Implementation)

**Blocking Issues from 2026-06-23 debugging session:**

1. **Gas Separator Blueprint Duplicate** — Delete one file to prevent lookup collision:
   - **Delete**: `data/json-data/blueprints/units/infrastructure/gas_separator_unit_bp.json` (1800kg duplicate, malformed from troubleshooting)
   - **Keep**: `data/json-data/blueprints/units/production/refineries/gas_separator_unit_bp.json` (1200kg, canonical ISRU unit)
   - Reason: BlueprintLookupService picks alphabetically first; without deletion, rake may nondeterministically pick wrong version
   - Status: Cleanup task (2026-06-19) will handle this; Phase 4 implementation cannot proceed until complete

2. **Verify Robot Charging Port Cleanup Complete**:
   - Confirm `modules/energy/robot_charging_port_bp.json` (250kg module, malformed id) has been deleted
   - Confirm only `units/infrastructure/robot_charging_port_bp.json` (350kg unit, id: "robot_charging_port") remains
   - Status: ✅ Already fixed 2026-06-23 morning (commit 1dfb2a0d)

**Gate**: Do NOT create Phase 4 task files or wire into profile until both prerequisites verified. Cleanup task must move to completed/ with clean final rake run (8/8 Phases 1-3 tasks passing) before starting here.

## Implementation Steps

1. **Create phase_4_robot_logistics.json** (luna_base_establishment/phases/)
   - Reference the 4 tasks above
   - Set task_ref entries pointing to tasks_v2/ files

2. **Create individual task files in tasks_v2/**
   - task_deploy_robot_charging_port.json
   - task_car_300_charge_cycle.json
   - task_isru_stockpile_initiation.json
   - task_construction_zone_leveling.json
   - Follow V2 effects-based model (deploy_unit, connect_units, set_unit_state, check_unit_state)

3. **Wire into luna_settlement_profile_v1.json**
   - Add phase_4_robot_logistics to phases array
   - Maintain proper sequencing (Phase 3 → Phase 4)

4. **Validate with TaskExecutionEngineV2**
   - Run final rake: `docker-compose -f docker-compose.dev.yml exec -T web bundle exec rake luna_mission:execute 2>&1`
   - Verify all 4 Phase 4 tasks execute cleanly
   - Report per-task pass/fail and final settlement state

## Testing
- Unit/connection state assertions for charging port deployment
- CAR-300 robot state transitions (idle → charging → charged)
- ISRU production cycle completion tracking
- Construction zone marking in settlement operational_data

## Success Criteria
✅ All 4 Phase 4 tasks execute without errors
✅ Final rake shows 12/12 total tasks complete (3 Phase 1 + 2 Phase 2 + 3 Phase 3 + 4 Phase 4)
✅ Phase 4 manifest items deployed and connected per architecture
✅ No blueprint or port allocation errors
✅ Settlement ready for Mission 2 (Lava Tube Construction phase)

## References
- Blueprint: `units/infrastructure/robot_charging_port_bp.json` (350kg, canonical ✓)
- Engine: `app/services/ai_manager/task_execution_engine_v2.rb`
- Manifest: `manifests_v2/lunar_precursor_manifest_v2_DRAFT.json`
- Profile: `luna_settlement_profile_v1.json`
- Phases: `luna_base_establishment/phases/`
