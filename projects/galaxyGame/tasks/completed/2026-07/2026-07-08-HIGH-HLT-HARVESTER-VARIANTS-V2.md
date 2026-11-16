---
status: active
priority: HIGH
type: implementation
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
date_created: 2026-07-08
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase5/2026-07-08-HIGH-HLT-HARVESTER-VARIANTS-V2.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/galaxy_game/tasks/backlog/phase5/2026-07-08-HIGH-HLT-HARVESTER-VARIANTS-V2.md \
         projects/galaxy_game/tasks/active/2026-07-08-HIGH-HLT-HARVESTER-VARIANTS-V2.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.

LIFECYCLE: backlog → active → completed (git mv only, never cp or plain mv)
Verify with: find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
  -name "2026-07-08-HIGH-HLT-HARVESTER-VARIANTS-V2.md"
Only ONE result should exist. Paste this output before starting synthesis.
```

---

# HLT HARVESTER VARIANTS — V2.1 DATA CORRECTION + MODULE CREATION

**Status**: BACKLOG
**Priority**: HIGH
**Type**: implementation
**Created**: 2026-07-08
**Phase**: 5 (prerequisite — AI Manager needs these craft options before propellant strategy evaluation)

---

## Context

The Heavy Lift Transport harvester variants (Venus, Mars, Titan) exist as operational data files
but have multiple correctness issues that must be fixed before the AI Manager can evaluate them
as propellant sourcing options. These craft are prerequisites to Luna→L1 logistics — the AI
Manager weighs skimmer deployment against PSR ice + Sabatier as competing CH4/N2 sourcing
strategies. Bad data = bad cost evaluation.

**Strategic role of each variant:**
- **Venus**: O2 + N2 production, CO as potential economic byproduct
- **Mars**: O2 + N2 + CO production, CO2 splitting primary
- **Titan**: CH4 + N2 production — primary CH4 source if skimmer strategy wins ROI evaluation

---

## What Exists (Do Not Recreate)

| File | Path | Status |
|------|------|--------|
| Venus operational data | `data/json-data/operational_data/crafts/space/spacecraft/heavy_lift_transport_harvester_venus_data.json` | ⚠️ Needs fixes |
| Mars operational data | `data/json-data/operational_data/crafts/space/spacecraft/heavy_lift_transport_harvester_mars_data.json` | ⚠️ Needs fixes |
| Titan operational data | `data/json-data/operational_data/crafts/space/spacecraft/heavy_lift_transport_harvester_titan_data.json` | ⚠️ Needs fixes |
| HLT MK1 blueprint | `data/json-data/blueprints/crafts/heavy_lift_transport_mk1_bp.json` | ⚠️ Legacy port format |
| PORT_CONNECTION_SYSTEM.md | `docs/PORT_CONNECTION_SYSTEM.md` | ⚠️ Stale — superseded content |

---

## Issues to Fix

### 1. Chemical Formula Violations (All 3 Files)

`input_resources`, `output_resources`, AND `gas_handling_policy` use full English names.
Convention: Ruby/JSON backend always uses chemical formulas. Display names are UI/JSON layer only.

| Wrong | Correct |
|---|---|
| `oxygen` | `O2` |
| `nitrogen` | `N2` |
| `carbon_dioxide` | `CO2` |
| `carbon_monoxide` | `CO` |
| `argon` | `Ar` |
| `methane` | `CH4` |
| `exhaust_gases` | `exhaust_gases` (acceptable — not a chemical formula) |
| `atmospheric_gases` | `atmospheric_gases` (acceptable — composite input) |
| `electric_power` | `electric_power` (acceptable — not a material) |

### 2. Venus gas_handling_policy — CO Store vs Vent

Current file vents CO. Gemini design session proposed storing CO as economic product.
**Correct approach**: make this a dynamic/configurable policy, not hardcoded.
Add a `policy_mode` field:

```json
"gas_handling_policy": {
  "policy_mode": "configurable",
  "store": ["O2", "N2"],
  "store_if_value_positive": ["CO"],
  "process": ["CO2"],
  "vent": []
}
```

AI Manager evaluates CO market value at decision time and sets policy accordingly.

### 3. Titan gas_handling_policy — Duplicate Entries

Current file lists `methane` in both `store` and `process`. After formula correction to `CH4`,
clarify intent — Titan harvests CH4 from atmosphere and stores it; processing is separation only.

```json
"gas_handling_policy": {
  "store": ["CH4", "N2"],
  "process": [],
  "vent": []
}
```

### 4. Metadata Version Bump

All 3 files are at `template_compliance: craft_operational_data_v1.7`. After fixes, bump to `v2.1`:

```json
"metadata": {
  "version": "2.1",
  "type": "craft_operational_data",
  "template_compliance": "craft_operational_data_v2.1"
}
```

### 5. Add connection_schema to All 3 Files

V2.1 requires `connection_schema` with `mounting_slots`. Use Venus as the reference pattern
(confirmed correct schema from design session). Adapt slot types per variant:

```json
"connection_schema": {
  "mounting_slots": [
    { "slot_id": "ext_mod_01", "bus_id": "bus_scrubber_01", "type": "module_port", "location": "intake_manifold" },
    { "slot_id": "int_mod_01", "bus_id": "bus_process_01", "type": "production_port", "location": "core_process" },
    { "slot_id": "pwr_mod_01", "bus_id": "power_bus_a", "type": "energy_port", "location": "dorsal" }
  ]
}
```

---

## New Files to Create

### Module Files (referenced in recommended_fit, do not exist yet)

Confirm each module file is missing before creating. Check path:
`data/json-data/operational_data/modules/`

| Module ID | Category | Needed By |
|---|---|---|
| `harvester_control_module` | control | All 3 variants |
| `atmospheric_harvester_system` | harvester | All 3 variants |
| `so2_scrubber_cartridge` | maintenance | Venus |
| `soxe_processing_stack` | production | Venus |
| `solar_expansion_rig` | energy | Venus |
| `co2_splitter` | production | Venus + Mars |
| `solar_array` | energy | Venus + Mars |
| `gas_separator` | production | Titan |

Use `unit_operational_data` template. Include `operational_properties`, `fuel_requirements`
where applicable, and `metadata` with `version: "1.0"`.

---

## PORT_CONNECTION_SYSTEM.md Update

File at `docs/PORT_CONNECTION_SYSTEM.md` contains superseded content:
- References `$variable` interpolation (`$target_bus_id`, `$slot_location`) — out of scope, not implemented
- References `utility_ports` — dropped in favor of `mounting_slots`
- "Validation: Pending" for LegacyPortAdapter — already implemented production code

Rewrite to reflect current V2.1 standard:
- `connection_schema` + `mounting_slots` is the canonical schema
- `recommended_fit` uses `count`-based bill of materials (not `bus_id` per module)
- LegacyPortAdapter is implemented — remove "pending" status
- Remove all `$variable` interpolation references

---

## Stop Conditions

- If any operational data file is not found at expected path — document and stop
- If module file already exists with different schema — document conflict and stop
- If PORT_CONNECTION_SYSTEM.md has been updated since 2026-07-08 — check diff before rewriting

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: Implementation Agent (GitHub Copilot)
**Completion date**: 2026-07-09
**Files modified**: 4 (3 operational data files + PORT_CONNECTION_SYSTEM.md)
**Files created**: 8 module files (harvester_control_module, atmospheric_harvester_system, so2_scrubber_cartridge, soxe_processing_stack, solar_expansion_rig, co2_splitter, solar_array, gas_separator)
**Formula violations corrected**: 14 total across 3 craft files (oxygen→O2, nitrogen→N2, argon→Ar, carbon_monoxide→CO, methane→CH4 in input/output/policy fields)
**Modules created**: 8

### Notes
- All 3 operational data files validated as correct JSON after edits
- Data/ directory is gitignored — files modified on disk but not committed to galaxy_game repo
- PORT_CONNECTION_SYSTEM.md committed to galaxy_game as `0fb171c1`
- Venus gas_handling_policy uses configurable mode with store_if_value_positive for CO (AI Manager evaluates at decision time)
- Titan gas_handling_policy clarified: CH4 stored only, process array empty (separation not processing)
