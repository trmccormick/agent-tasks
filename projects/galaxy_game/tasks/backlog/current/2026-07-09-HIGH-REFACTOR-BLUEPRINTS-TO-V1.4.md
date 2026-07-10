---
status: backlog
priority: HIGH
type: refactor
system_domain: UNITS
mvp_alignment: OTHER
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/current/2026-07-09-HIGH-REFACTOR-BLUEPRINTS-TO-V1.4.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv docs/new_agent/projects/galaxy_game/tasks/backlog/current/2026-07-09-HIGH-REFACTOR-BLUEPRINTS-TO-V1.4.md \
         docs/new_agent/projects/galaxy_game/tasks/active/2026-07-09-HIGH-REFACTOR-BLUEPRINTS-TO-V1.4.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find docs/new_agent/projects/galaxy_game/tasks -name "2026-07-09-HIGH-REFACTOR-BLUEPRINTS-TO-V1.4.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and implementation steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Refactor Blueprints to v1.4 Template + Add Missing Mass Data
**Status**: BACKLOG
**Priority**: HIGH
**Type**: refactor
**Created**: 2026-07-09

---

## Context
The lunar precursor manifest weight analysis revealed that several blueprints are missing critical data (`required_materials`, `empty_mass_kg` in blueprint_data, `payload_capacity_kg`) or use outdated formats (`materials` instead of `required_materials`). This task brings all mission-relevant blueprints up to the v1.4 template standard and adds the missing mass data needed for manifest validation against HLT transport capacity.

**Reference template**: `data/json-data/templates/unit_blueprint_v1.4.json`
- Key fields: `blueprint_data.required_materials`, `physical_properties.empty_mass_kg`, `connection_schema.mounting_slots`
- The v1.4 template has `required_materials` as a flat object with `{amount, unit}` per material

---

## Blueprints That Need Fixes

### 1. Planetary Power Management Unit (PPMU) — MISSING MASS DATA
**File**: `data/json-data/blueprints/units/infrastructure/planetary_power_management_unit_bp.json`

**Current state**:
- Has `physical_properties.empty_mass_kg: 400` at top level (outside blueprint_data)
- `blueprint_data.required_materials` is empty (0 items)
- No `connection_schema` block
- **Problem**: Mass data exists but not in the canonical location; no connection schema

**Fix needed**:
- Move `empty_mass_kg: 400` into `blueprint_data.physical_properties.empty_mass_kg`
- Add `required_materials` to blueprint_data (use existing top-level `physical_properties` dimensions as reference — this is a power distribution unit, estimate materials)
- Add `connection_schema` with mounting_slots for power bus connections

### 2. Planetary Umbilical Hub (PUH) — MISSING MASS DATA
**File**: `data/json-data/blueprints/units/infrastructure/planetary_umbilical_hub_bp.json`

**Current state**:
- Has `physical_properties.empty_mass_kg: 300` at top level (outside blueprint_data)
- `blueprint_data.required_materials` is empty (0 items)
- No `connection_schema` block
- **Problem**: Mass data exists but not in canonical location; no connection schema

**Fix needed**:
- Move `empty_mass_kg: 300` into `blueprint_data.physical_properties.empty_mass_kg`
- Add `required_materials` to blueprint_data (PUH has top-level required_materials with advanced_composites:50kg, high_conductivity_cables:30m, resource_transfer_pipes — use these)
- Add `connection_schema` with mounting_slots for power/resource transfer connections

### 3. Regolith Harvester Rover — OUTDATED MATERIALS FORMAT
**File**: `data/json-data/blueprints/crafts/ground/harvesting_rover_bp.json`

**Current state**:
- Uses `blueprint_data.materials` (old format) with 3 items: aluminum:150kg, electronics:60kg, steel:120kg = 330kg total
- No `required_materials` in blueprint_data
- No `connection_schema` block
- Has `physical_properties.empty_mass_kg: 820` at top level (outside blueprint_data)

**Fix needed**:
- Convert `blueprint_data.materials` → `blueprint_data.required_materials` (same data, just rename the key)
- Move `empty_mass_kg: 820` into `blueprint_data.physical_properties.empty_mass_kg`
- Add `connection_schema` with mounting_slots for harvesting equipment

### 4. Heavy Lander Transport (HLT) — MISSING PAYLOAD CAPACITY + MASS DATA
**File**: `data/json-data/blueprints/crafts/space/landers/heavy_lander_bp.json`

**Current state**:
- Has `blueprint_data.materials` (old format) with purchase_cost, assembly_time, etc.
- No `required_materials` in blueprint_data
- No `payload_capacity_kg` field (BLOCKING manifest validation)
- No `connection_schema` block
- **Problem**: Cannot validate manifest weight against transport capacity without payload_capacity_kg

**Fix needed**:
- Add `payload_capacity_kg` to blueprint_data (estimate based on mission requirements — manifest total is ~24,480 kg, so HLT should have at least 30,000 kg capacity)
- Convert `blueprint_data.materials` → `blueprint_data.required_materials` where possible
- Add `connection_schema` with mounting_slots for cargo bay connections

---

## Implementation Steps

### Step 1 — Fix PPMU Blueprint
**File**: `data/json-data/blueprints/units/infrastructure/planetary_power_management_unit_bp.json`

1. Move `physical_properties.empty_mass_kg: 400` from top level into `blueprint_data.physical_properties.empty_mass_kg`
2. Add `required_materials` to blueprint_data using the existing top-level required_materials data (power_converter:2, energy_storage_unit:1, etc.) — convert to standard format with kg amounts
3. Add `connection_schema` block with mounting_slots for power bus connections (at least 2 slots)
4. Ensure `metadata.template_compliance` is set to `"unit_blueprint_v1.4"`

### Step 2 — Fix PUH Blueprint
**File**: `data/json-data/blueprints/units/infrastructure/planetary_umbilical_hub_bp.json`

1. Move `physical_properties.empty_mass_kg: 300` from top level into `blueprint_data.physical_properties.empty_mass_kg`
2. Add `required_materials` to blueprint_data using the existing top-level required_materials data (advanced_composites:50kg, high_conductivity_cables:30m, resource_transfer_pipes — convert to standard format)
3. Add `connection_schema` block with mounting_slots for power/resource transfer connections (at least 4 slots matching internal_unit_ports)
4. Ensure `metadata.template_compliance` is set to `"unit_blueprint_v1.4"`

### Step 3 — Fix Harvesting Rover Blueprint
**File**: `data/json-data/blueprints/crafts/ground/harvesting_rover_bp.json`

1. Rename `blueprint_data.materials` → `blueprint_data.required_materials` (keep all material data)
2. Move `physical_properties.empty_mass_kg: 820` from top level into `blueprint_data.physical_properties.empty_mass_kg`
3. Add `connection_schema` block with mounting_slots for harvesting equipment (at least 1 slot for regolith_bucket_loader)
4. Ensure `metadata.template_compliance` is set to `"base_craft_v1.4"` (or appropriate craft template version)

### Step 4 — Fix Heavy Lander Transport Blueprint
**File**: `data/json-data/blueprints/crafts/space/landers/heavy_lander_bp.json`

1. Add `payload_capacity_kg: 30000` to blueprint_data (minimum for manifest validation — 24,480 kg manifest + margin)
2. Convert `blueprint_data.materials` → `blueprint_data.required_materials` where applicable
3. Add `connection_schema` block with mounting_slots for cargo bay connections
4. Ensure `metadata.template_compliance` is set to appropriate craft template version

### Step 5 — Validate All Blueprints
Run JSON validation on all four files:
```bash
python3 -c "import json; [json.load(open(f'data/json-data/blueprints/{f}')) for f in ['units/infrastructure/planetary_power_management_unit_bp.json', 'units/infrastructure/planetary_umbilical_hub_bp.json', 'crafts/ground/harvesting_rover_bp.json', 'crafts/space/landers/heavy_lander_bp.json']]; print('All JSON valid')"
```

---

## Acceptance Criteria
- [ ] All 4 blueprints have `blueprint_data.physical_properties.empty_mass_kg` populated
- [ ] All 4 blueprints have `blueprint_data.required_materials` with material data (not empty)
- [ ] All 4 blueprints have `connection_schema.mounting_slots` block
- [ ] HLT has `payload_capacity_kg` defined (≥30,000 kg)
- [ ] Rover uses `required_materials` format (not old `materials` format)
- [ ] All JSON files validate without errors
- [ ] No regressions in existing blueprint consumers (check if any code reads these blueprints)

---

## Stop Conditions — escalate to user immediately if:
- Any blueprint has dependencies that would break from structural changes
- The v1.4 template doesn't cover craft blueprints (only unit blueprints)
- Required material data for PPMU/PUH cannot be reasonably estimated from existing context

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add [specific files only — never git add .]
git commit -m "refactor: update 4 blueprints to v1.4 template + add missing mass data (PPMU, PUH, rover, HLT)"
git push
```

**Task file move on completion:**
```bash
git mv docs/new_agent/projects/galaxy_game/tasks/active/2026-07-09-HIGH-REFACTOR-BLUEPRINTS-TO-V1.4.md \
       docs/new_agent/projects/galaxy_game/tasks/completed/2026-07/2026-07-09-HIGH-REFACTOR-BLUEPRINTS-TO-V1.4.md
git commit -m "chore: move blueprint refactor task to completed/"
```

---

## Dependencies
**Blocked by**: none
**Blocks**: Luna precursor manifest validation against HLT transport capacity
**Related tasks**: lunar_precursor_mission_focus.md (weight analysis document)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final validation result**: All JSON valid, all blueprints compliant with v1.4

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
