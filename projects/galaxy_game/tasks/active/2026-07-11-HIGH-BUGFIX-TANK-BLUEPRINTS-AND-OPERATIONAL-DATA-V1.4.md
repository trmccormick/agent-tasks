---
status: backlog
priority: HIGH
type: bug-fix
system_domain: UNITS
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/current/2026-07-11-HIGH-BUGFIX-TANK-BLUEPRINTS-AND-OPERATIONAL-DATA-V1.4.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv docs/new_agent/projects/galaxy_game/tasks/backlog/current/2026-07-11-HIGH-BUGFIX-TANK-BLUEPRINTS-AND-OPERATIONAL-DATA-V1.4.md \
         docs/new_agent/projects/galaxy_game/tasks/active/2026-07-11-HIGH-BUGFIX-TANK-BLUEPRINTS-AND-OPERATIONAL-DATA-V1.4.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find docs/new_agent/projects/galaxy_game/tasks -name "2026-07-11-HIGH-BUGFIX-TANK-BLUEPRINTS-AND-OPERATIONAL-DATA-V1.4.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

CRITICAL: data/json-data/ is gitignored — do NOT git add or force-add JSON files.
  JSON changes are local + Time Machine backed only. Only Ruby files get committed.
  NO GIT COMMITS without explicit human approval in chat.
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Fix Tank Blueprints and Operational Data — V1.4 Template Compliance
**Status**: BACKLOG
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-07-11

## Prerequisites
- Read: `data/json-data/blueprints/units/` (scan for existing tank files)
- Read: `data/json-data/templates/unit_blueprint_v1.4.json`
- Read: `data/json-data/templates/unit_operational_data_v1.3.json`
- Understand: `storage_properties` block is the canonical capacity source for storage units
- Understand: All operational data files follow `*_data.json` naming convention

## Problem Statement
Two cryogenic fuel tank unit types (LOX and methane) have incomplete or
wrong-format blueprint/operational data files. The skimmer cycler handshake
refactor depends on correct capacity data from these blueprints. Component
blueprint format files exist for both tank types and must be removed —
they are not scanned by UnitLookupService.

## Scope — JSON data only. No Ruby changes. No git commits (data/ is gitignored).

## Work Items

### 1. DELETE — wrong-format component blueprints
- `data/json-data/blueprints/units/storage/lox_tank_bp.json` (template: component_blueprint, id: lox_tank)
- `data/json-data/blueprints/units/storage/methane_tank_bp.json` (template: component_blueprint, id: methane_tank)

Verify paths before deleting:
```bash
find data/json-data/blueprints/units/storage -name "lox_tank_bp.json" -o -name "methane_tank_bp.json"
```

### 2. CREATE — `lox_storage_tank_mk1_bp.json`
New v1.4 unit blueprint. Model after `methane_storage_tank_mk1_bp.json`.
Key values to populate from existing `lox_storage_tank_data.json`:
- `physical_properties.volume_m3`: 129
- `physical_properties.empty_mass_kg`: 4000
- `storage_properties.capacity`: 10000 (kg LOX)
- `storage_properties.stored_material`: "O2"
- `storage_properties.phase`: "liquid"
- `storage_properties.requires_cooling`: true
- `item_produced.id`: "lox_storage_tank_mk1"
- Add `connection_schema` with 2 mounting slots (fluid_port pattern)
- `template_compliance`: "unit_blueprint_v1.4"

### 3. UPDATE — `methane_storage_tank_mk1_bp.json` → v1.4
File is currently `unit_blueprint` but missing `connection_schema`.
Add:
```json
"connection_schema": {
  "mounting_slots": [
    { "slot_id": "fluid_port_1", "bus_type": "fluid_standard", "location": "inlet" },
    { "slot_id": "fluid_port_2", "bus_type": "fluid_standard", "location": "outlet" }
  ]
}
```
Update `metadata.template_compliance` → `"unit_blueprint_v1.4"`

### 4. CREATE — `lox_storage_tank_mk1_data.json` (v1.3 operational data)
This file does NOT exist yet. Create it with:
- `operational_status`: `{ "status": "offline", "condition": 100, "degradation_rate": 0.0 }`
- `operational_modes`: `{ "current_mode": "standby", "available_modes": [] }`
- `metadata.version`: "1.3" and `template_compliance`: "unit_operational_data"

### 5. CREATE — `methane_storage_tank_mk1_data.json` (v1.3 operational data)
This file does NOT exist yet. Create it with:
- `operational_status`: `{ "status": "offline", "condition": 100, "degradation_rate": 0.0 }`
- `operational_modes`: `{ "current_mode": "standby", "available_modes": [] }`
- `metadata.version`: "1.3" and `template_compliance`: "unit_operational_data"

## Acceptance Criteria
- [ ] `lox_tank_bp.json` and `methane_tank_bp.json` deleted
- [ ] `lox_storage_tank_mk1_bp.json` exists, passes JSON lint, id = "lox_storage_tank_mk1"
- [ ] `methane_storage_tank_mk1_bp.json` has `connection_schema`, template_compliance = v1.4
- [ ] Both `_data.json` files have `operational_status` and `operational_modes` blocks
- [ ] JSON lint on all modified files:
```bash
  python3 -m json.tool [filepath] > /dev/null && echo "valid"
```
- [ ] NO git add or commit on any data/ files

## Implementation Notes
- LOX stored_material should be "O2" (chemical formula convention — never "liquid_oxygen"
  or "LOX" in backend data)
- Verify exact paths of all files in container before editing
- Use Step 0: find files first, then read, then edit — never assume paths