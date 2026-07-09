---
status: completed
priority: LOW
type: implementation
system_domain: OPERATIONAL_DATA
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
date_created: 2026-07-08
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-08-LOW-ETHANE-ENGINE-BLUEPRINT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-08-LOW-ETHANE-ENGINE-BLUEPRINT.md \
         projects/galaxy_game/tasks/active/2026-07-08-LOW-ETHANE-ENGINE-BLUEPRINT.md
  Then open the moved file and change: status: completed → status: active
  Paste the output of both commands in chat before proceeding.

LIFECYCLE: backlog → active → completed (git mv only, never cp or plain mv)
Verify with: find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
  -name "2026-07-08-LOW-ETHANE-ENGINE-BLUEPRINT.md"
Only ONE result should exist. Paste this output before starting synthesis.
```

---

# ETHANE ROCKET ENGINE MK1 — MISSING BLUEPRINT

**Status**: BACKLOG
**Priority**: LOW
**Type**: implementation
**Created**: 2026-07-08

---

## Context

`ethane_rocket_engine_mk1` has an operational data file but no corresponding blueprint.
This is a data gap — any system attempting to manufacture or cost-evaluate this engine
will fail to find the blueprint. The engine is Titan-specific (designed to run on ethane
harvested from Titan's atmosphere) and relevant to Phase 12 Titan expansion, but the
blueprint gap should be closed as a correctness fix independent of phase gating.

---

## What Exists

| File | Path | Status |
|------|------|--------|
| Operational data | `data/json-data/operational_data/units/propulsion/ethane_rocket_engine_mk1_data.json` | ✅ Exists |
| Blueprint | `data/json-data/blueprints/units/propulsion/ethane_rocket_engine_mk1_bp.json` | ❌ Missing |

---

## Reference — Existing Operational Data Key Fields

```json
{
  "id": "ethane_rocket_engine_mk1",
  "operational_properties": {
    "power_output_kw": 1600,
    "fuel_consumption_kg_per_hour": 480,
    "efficiency": 0.88,
    "maintenance_interval_hours": 450,
    "operational_temperature_c": 3400
  },
  "fuel_requirements": {
    "primary_fuel": ["ethane"],
    "oxidizer": ["liquid_oxygen"]
  }
}
```

---

## Implementation Steps

### Step 1: Verify blueprint is actually missing

```bash
find data/json-data/blueprints -name "*ethane*"
```

If a file exists — read it, compare to operational data, document any gaps. Do not recreate.

### Step 2: Create blueprint

Use `unit_blueprint_v1.4.json` as the structural template. Ensure the `connection_schema` block is populated correctly for the `propulsion` bus. Adapt performance values from operational data file. Key fields to set:

- `id`: `ethane_rocket_engine_mk1`
- `port_type`: `propulsion` (match methane engine)
- `fuel_requirements`: `primary_fuel: ethane`, `oxidizer: liquid_oxygen`
- `operational_data_reference.file`: point to existing operational data file
- Performance: ethane/LOX ISP ~340s (slightly below CH4/LOX at 350s — ethane is denser,
  lower ISP but better volumetric efficiency, appropriate for Titan where ethane is local)
- Cost: slightly below methane engine (~200,000 GCC) — simpler combustion chemistry
- `required_technology`: `ethane_combustion_engines`
- `required_facility_type`: `advanced_manufacturing`

### Step 3: Verify JSON is valid

```bash
python3 -m json.tool data/json-data/blueprints/units/propulsion/ethane_rocket_engine_mk1_bp.json
```

---

## Stop Conditions

- Blueprint already exists with different content — document conflict, do not overwrite

---

## Completion Report

**Completed by**: [agent]
**Completion date**: YYYY-MM-DD
**File created**: `data/json-data/blueprints/units/propulsion/ethane_rocket_engine_mk1_bp.json`
