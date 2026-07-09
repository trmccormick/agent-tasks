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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-08-LOW-HYDROLOX-ENGINE-NEW.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-08-LOW-HYDROLOX-ENGINE-NEW.md \
         projects/galaxy_game/tasks/active/2026-07-08-LOW-HYDROLOX-ENGINE-NEW.md
  Then open the moved file and change: status: completed → status: active
  Paste the output of both commands in chat before proceeding.

LIFECYCLE: backlog → active → completed (git mv only, never cp or plain mv)
Verify with: find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
  -name "2026-07-08-LOW-HYDROLOX-ENGINE-NEW.md"
Only ONE result should exist. Paste this output before starting synthesis.
```

---

# HYDROLOX ENGINE — NEW BLUEPRINT + OPERATIONAL DATA

**Status**: BACKLOG
**Priority**: LOW
**Type**: implementation
**Created**: 2026-07-08

---

## Context

No H2/LOX chemical combustion engine exists in the blueprint or operational data set.
This is a propulsion gap — hydrolox is the highest ISP chemical engine available and
is the natural fit for craft using electrolysis-derived H2 from PSR ice water.

**Strategic relevance**: The AI Manager's propellant strategy evaluation for Luna→L1
logistics includes PSR ice → electrolysis → H2 + O2 as one option. Without a hydrolox
engine, craft cannot actually use that H2. The engine must exist before the AI Manager
can cost-evaluate the PSR ice sourcing path against Titan skimmer CH4 delivery.

---

## What Exists

| File | Status |
|---|---|
| Any hydrolox blueprint | ❌ Missing |
| Any hydrolox operational data | ❌ Missing — verify with `find data/json-data -name "*hydro*" -o -name "*lh2*" -o -name "*hydrogen*engine*"` before creating |

---

## Design Parameters

Use real hydrolox performance as reference baseline:

| Property | Value | Notes |
|---|---|---|
| ISP vacuum | ~450s | Best chemical ISP — significantly above CH4/LOX at 350s |
| Fuel | LH2 (liquid hydrogen) | Requires cryogenic storage |
| Oxidizer | LOX | |
| Thrust class | Similar to methane engine | Scale to HLT mission requirements |
| Complexity | Higher than CH4 | LH2 cryogenic handling, lower density = larger tanks |
| Cost | Higher than methane engine (~350,000 GCC) | Cryogenic complexity premium |

**Tradeoff for AI Manager evaluation**:
- Higher ISP = less propellant mass needed = better for high delta-V missions
- LH2 cryogenic storage = larger tanks, more infrastructure
- H2 available from PSR ice electrolysis (local Luna source)
- CH4 requires Sabatier or Titan skimmer delivery

---

## Implementation Steps

### Step 1: Verify nothing exists

```bash
find data/json-data -name "*hydro*" -o -name "*lh2*" -o -name "*hydrogen*engine*"
```

Paste output before proceeding.

### Step 2: Create operational data file

Path: `data/json-data/operational_data/units/propulsion/hydrolox_engine_mk1_data.json`

Use `ethane_rocket_engine_mk1_data.json` as template for structure. Key fields:

```json
{
  "id": "hydrolox_engine_mk1",
  "name": "Hydrolox Engine Mk1",
  "description": "High-efficiency liquid hydrogen/liquid oxygen staged combustion engine. Highest ISP of any chemical engine — optimal for missions with access to electrolysis-derived hydrogen.",
  "fuel_requirements": {
    "primary_fuel": ["LH2"],
    "oxidizer": ["O2"]
  },
  "operational_properties": {
    "isp_vacuum_s": 450,
    "maintenance_interval_hours": 400
  },
  "special_handling": ["cryogenic_propellant_handling", "LH2_boiloff_management"]
}
```

### Step 3: Create blueprint file

Path: `data/json-data/blueprints/units/propulsion/hydrolox_engine_mk1_bp.json`

Performance benchmarks must be based on RL10/SSME heritage (Vacuum ISP ~450s). You must use `unit_blueprint_v1.4.json` as your structural template to ensure `connection_schema` compliance. Key differences:
- Higher material cost (~350,000 GCC)
- `required_technology`: `hydrolox_staged_combustion`
- `required_facility_type`: `advanced_manufacturing`
- Add `cryogenic_propellant_handling` to `special_handling`
- `operational_data_reference.file` points to Step 2 file

### Step 4: Validate both files

```bash
python3 -m json.tool data/json-data/operational_data/units/propulsion/hydrolox_engine_mk1_data.json
python3 -m json.tool data/json-data/blueprints/units/propulsion/hydrolox_engine_mk1_bp.json
```

---

## Stop Conditions

- Any existing hydrolox file found in Step 1 — read it, compare, document before creating anything
- Cryogenic storage unit for LH2 also missing — flag but do not scope-creep into this task

---

## Completion Report

**Completed by**: [agent]
**Completion date**: YYYY-MM-DD
**Files created**:
- `data/json-data/operational_data/units/propulsion/hydrolox_engine_mk1_data.json`
- `data/json-data/blueprints/units/propulsion/hydrolox_engine_mk1_bp.json`
**Existing files found in Step 1**: [paste find output]
