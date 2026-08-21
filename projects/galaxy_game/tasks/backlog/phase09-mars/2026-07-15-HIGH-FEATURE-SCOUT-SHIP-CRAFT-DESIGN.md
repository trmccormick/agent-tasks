---
status: backlog
priority: HIGH
type: feature
system_domain: CRAFT_DESIGN
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase9+/2026-07-15-HIGH-FEATURE-SCOUT-SHIP-CRAFT-DESIGN.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/phase9+/2026-07-15-HIGH-FEATURE-SCOUT-SHIP-CRAFT-DESIGN.md \
         projects/galaxy_game/tasks/active/2026-07-15-HIGH-FEATURE-SCOUT-SHIP-CRAFT-DESIGN.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-15-HIGH-FEATURE-SCOUT-SHIP*"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-FEATURE-SCOUT-SHIP-CRAFT-DESIGN.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Scout Ship Craft Design — BaseCraft Entry + JSON Files
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-07-15
**Last Updated**: 2026-07-15

---

## Context

Scout ships are needed for seismic survey missions that classify asteroids before conversion to stations (Eden AWS Anchor). This task creates the scout ship as a **BaseCraft entry** with JSON blueprint and operational data files — no new model needed.

The scout ship is a specialized vessel designed for:
- Deep-space scouting missions to candidate systems
- Seismic survey payload for asteroid structural integrity classification
- Tug capability for emergency asteroid relocation during surveys
- Long-range sensor arrays for system reconnaissance

**Relevant Architecture Docs** — read before starting:
- `docs/architecture/systems/asteroid_conversion_physics.md` — physics thresholds (density < 1.5 = rubble pile, ice > 20% = thermal risk)
- `docs/architecture/systems/survey_and_handshake_protocol.md` — survey result handshake format
- `docs/new_agent/projects/galaxy_game/tasks/backlog/phase9+/2026-07-15-HIGH-FEATURE-SEISMIC-SURVEY-MODE.md` — companion task (seismic mode)

---

## Problem Statement

No scout ship craft exists in the codebase. The AI Manager's asteroid conversion strategy requires a vessel capable of:
1. Deploying to candidate asteroid systems
2. Performing seismic surveys (structural integrity classification)
3. Reporting results back via wormhole scouting handshake protocol
4. Optionally relocating asteroids if survey results are favorable

**Current behavior**: No scout class in any craft registry; no JSON blueprint or operational data files exist for scout ships.

**Expected behavior**: Scout ship exists as a BaseCraft entry with:
- Blueprint file (`scout_ship_bp.json`) using `base_craft_v1.6` template
- Operational data file (`scout_ship_data.json`) using `craft_operational_data_v1.7` template
- DB row in `base_crafts` table (via seed/factory)

---

## Design Constraints

**Use BaseCraft — do NOT create a new model.**
- Scout ship is a craft type, not a new domain
- Follow the pattern of `asteroid_relocation_tug` exactly: JSON files + DB row
- Only create a new model if BaseCraft cannot support the design (unlikely)

---

## Files Involved

### Primary Files — you will create these
| File | Purpose | Template |
|---|---|---|
| `data/json-data/blueprints/crafts/space/spacecraft/scout_ship_bp.json` | Scout ship blueprint | `base_craft_v1.6` (copy from template) |
| `data/json-data/operational_data/crafts/space/spacecraft/scout_ship_data.json` | Scout ship operational data | `craft_operational_data_v1.7` (copy from template) |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `data/json-data/blueprints/crafts/space/spacecraft/asteroid_relocation_tug_bp.json` | Pattern reference for blueprint structure |
| `data/json-data/operational_data/crafts/space/spacecraft/asteroid_relocation_tug_data.json` | Pattern reference for operational data structure |
| `galaxy_game/app/models/craft/base_craft.rb` | BaseCraft model — understand schema before creating entries |
| `data/json-data/templates/craft_blueprint_v1.6.json` | Blueprint template with all fields |
| `data/json-data/templates/craft_operational_data_v1.7.json` | Operational data template with all fields |

### Migration
- [x] No migration needed — uses existing `base_crafts` table schema

---

## Implementation Steps

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/phase9+/2026-07-15-HIGH-FEATURE-SCOUT-SHIP-CRAFT-DESIGN.md \
       projects/galaxy_game/tasks/active/2026-07-15-HIGH-FEATURE-SCOUT-SHIP-CRAFT-DESIGN.md
```

Then update YAML status: `status: backlog → status: active`

### Step 1 — Read reference patterns

Read `asteroid_relocation_tug_bp.json` and `asteroid_relocation_tug_data.json` to understand the exact JSON structure. Also read `base_craft.rb` model to understand DB schema.

**Deliverable**: Paste the key fields you'll need from each reference file in your synthesis report.

### Step 2 — Create scout ship blueprint (`scout_ship_bp.json`)

Copy from `craft_blueprint_v1.6.json` template and fill in scout-specific values:

```json
{
  "template": "base_craft",
  "id": "scout_ship",
  "name": "Scout Ship",
  "description": "Deep-space reconnaissance vessel equipped with seismic survey payload for asteroid structural integrity classification. Designed for wormhole scouting missions to evaluate conversion candidates (asteroids, moons) for Eden AWS Anchor placement.",
  "type": "craft",
  "category": "spaceship",
  "subcategory": "scout",

  "physical_properties": {
    "length_m": 45.0,
    "width_m": 25.0,
    "height_m": 18.0,
    "empty_mass_kg": 180000.0,
    "volume_m3": 12000.0,
    "drag_coefficient": 0.15
  },

  "mounting_points": [
    "sensor_array",
    "seismic_survey_payload",
    "propulsion",
    "power_plant",
    "fuel_tanks",
    "control_center",
    "long_range_communicator",
    "tug_thruster_backup"
  ],

  "compatible_units": [
    "advanced_sensor_array",
    "seismic_survey_module",
    "nuclear_reactor",
    "fusion_reactor",
    "vasimr_engine",
    "xenon_tank",
    "methane_tank",
    "lox_tank",
    "long_range_communicator",
    "automated_navigation_unit",
    "deep_space_propulsion_unit"
  ],

  "compatible_modules": [
    "seismic_analysis_module",
    "thermal_dissipation_module",
    "radiation_shielding_module",
    "automated_navigation_module",
    "long_duration_life_support",
    "autonomous_repair_system"
  ],

  "ports": {
    "internal_module_ports": 4,
    "external_module_ports": 2,
    "internal_fuel_storage_ports": 3,
    "external_fuel_storage_ports": 1,
    "internal_unit_ports": 6,
    "external_unit_ports": 2,
    "propulsion_ports": 2,
    "storage_ports": 4,
    "internal_rig_ports": 2,
    "external_rig_ports": 1,
    "umbilical_ports": 2
  },

  "crew_capacity": 3,

  "cost_data": {
    "purchase_cost": { "currency": "GCC", "amount": 0 },
    "manufacturing_cost": { "currency": "GCC", "amount": 0 },
    "maintenance": {
      "time_to_repair_hours": 48,
      "repair_cost_gcc": 0,
      "materials_needed_for_repair": []
    }
  },

  "blueprint_data": {
    "base_material_efficiency": 0.75,
    "base_time_efficiency": 0.6,
    "materials": [],
    "assembly_time": 168,
    "research_required": ""
  },

  "metadata": {
    "version": "1.6",
    "type": "base_craft",
    "template_compliance": "base_craft_v1.6"
  }
}
```

> **NOTE**: Cost and material fields are placeholders (amount: 0). These will be filled in during Phase 9a when economics are finalized. The structure is correct — values come from Gemini design session or AI Manager cost analysis.

### Step 3 — Create scout ship operational data (`scout_ship_data.json`)

Copy from `craft_operational_data_v1.7` template and fill in scout-specific values:

```json
{
  "template": "craft_operational_data",
  "id": "scout_ship",
  "name": "Scout Ship",
  "description": "Deep-space reconnaissance vessel with seismic survey payload.",
  "category": "spaceship",
  "subcategory": "scout",

  "operational_properties": {
    "power_consumption_kw": 250,
    "maintenance_interval_hours": 72,
    "efficiency": 0.85
  },

  "operational_flags": {
    "autonomous": true,
    "human_rated": true
  },

  "recommended_fit": {
    "modules": [
      { "id": "seismic_analysis_module", "count": 1, "category": "sensors" },
      { "id": "thermal_dissipation_module", "count": 1, "category": "power" }
    ],
    "units": [
      { "id": "advanced_sensor_array", "count": 2, "category": "sensors" },
      { "id": "nuclear_reactor", "count": 1, "category": "energy" },
      { "id": "vasimr_engine", "count": 2, "category": "propulsion" }
    ],
    "rigs": []
  },

  "input_resources": [
    { "id": "lox", "rate": 15, "unit": "kg/hr" },
    { "id": "methane", "rate": 8, "unit": "kg/hr" },
    { "id": "electricity", "rate": 250, "unit": "kW" }
  ],

  "output_resources": [
    { "id": "survey_data", "rate": 1, "unit": "data_packets/hr" }
  ],

  "waste": {
    "waste_heat_kw": 120
  },

  "deployment": {
    "deployment_locations": ["leo_depot", "l1_depot", "orbital_station"],
    "deployment_time_hours": 4
  },

  "gas_handling_policy": {
    "store": ["lox", "methane"],
    "process": [],
    "vent": []
  },

  "fuel_management": {
    "lox_reserve_threshold_percent": 15,
    "methane_reserve_threshold_percent": 15,
    "preflight_fuel_strategy": "maximize_range",
    "inflight_transfer_rules": {
      "allow_lox_transfer_to_main": true,
      "allow_methane_transfer_to_main": true,
      "autobalance_priority": ["main_tank"]
    }
  },

  "research_required": "",

  "metadata": {
    "version": "1.7",
    "type": "craft_operational_data",
    "template_compliance": "craft_operational_data_v1.7"
  }
}
```

### Step 4 — Verify JSON conformance

Validate both files against their respective templates:
- `scout_ship_bp.json` must have all required fields from `base_craft_v1.6` template
- `scout_ship_data.json` must have all required fields from `craft_operational_data_v1.7` template

**Deliverable**: Confirm both files pass JSON validation (no syntax errors, all required fields present).

### Step 5 — Synthesis Report (before committing)

```
SYNTHESIS REPORT
Files created:
  - data/json-data/blueprints/crafts/space/spacecraft/scout_ship_bp.json (base_craft_v1.6)
  - data/json-data/operational_data/crafts/space/spacecraft/scout_ship_data.json (craft_operational_data_v1.7)

Scout ship specs:
  [paste key values: length, mass, crew_capacity, ports, compatible_units]

Template compliance:
  ✅ Blueprint matches base_craft_v1.6 structure
  ✅ Operational data matches craft_operational_data_v1.7 structure

DB state:
  [specify: no migration needed, uses existing base_crafts table]

READY TO APPLY? — waiting for approval
```

---

## Acceptance Criteria
- [ ] `scout_ship_bp.json` created with all required fields from `base_craft_v1.6` template
- [ ] `scout_ship_data.json` created with all required fields from `craft_operational_data_v1.7` template
- [ ] Both files are valid JSON (no syntax errors)
- [ ] Scout ship uses `id: "scout_ship"` consistently across both files
- [ ] Compatible units/modules reference existing unit/module IDs in the codebase
- [ ] No new model created — scout ship is a BaseCraft entry only
- [ ] Cost/material fields are placeholders (0 or empty) — to be filled during Phase 9a economics

---

## Stop Conditions — escalate to user immediately if:
- `base_craft.rb` schema has changed significantly from what's expected
- Required compatible unit/module IDs don't exist in the codebase
- Template structure has diverged from v1.6/v1.7 specs
- Any architectural decision is required beyond JSON file creation

---

## Commit Instructions

Run git commands on **host**, not inside container:

```bash
git add data/json-data/blueprints/crafts/space/spacecraft/scout_ship_bp.json
git add data/json-data/operational_data/crafts/space/spacecraft/scout_ship_data.json
git commit -m "feature: scout ship craft design — add BaseCraft entry with blueprint and operational data"
git push
```

---

## Documentation
- [ ] No additional doc changes needed — reference docs already cover this feature

---

## Dependencies
**Blocked by**: none (standalone craft design task)
**Blocks**: Task B (Seismic Survey Mode) — scout ship is the vessel that performs surveys
**Related tasks**: 
- `2026-07-15-HIGH-FEATURE-SEISMIC-SURVEY-MODE.md` — companion task (survey mode logic)
- `2026-07-15-HIGH-FEATURE-ASTEROID-INTEGRITY-GATE.md` — companion task (integrity gate)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was changed
- `data/json-data/blueprints/crafts/space/spacecraft/scout_ship_bp.json` — new scout ship blueprint (base_craft_v1.6)
- `data/json-data/operational_data/crafts/space/spacecraft/scout_ship_data.json` — new scout ship operational data (craft_operational_data_v1.7)

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary

HANDOFF SUMMARY: Scout ship blueprint + operational data created | BaseCraft entry ready | No migration needed