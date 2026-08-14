---
name: "Migrate CO2 Oxygen Production Data Schema and Fix Stoichiometry"
priority: LOW
phase: phase6+
created: 2026-06-21
status: completed
type: data
relocated_from: reorganization_attempt_3
relocated_reason: "Life support needed for lava-tube base verification loop in phase6+"
---

# TASK: Migrate CO2 Oxygen Production Unit Data to New Schema and Fix Stoichiometry

**Priority**: LOW  
**Phase**: phase6+ (Lava-Tube Base Verification)  
**Type**: data migration  
**Created**: 2026-06-21  
**Last Updated**: 2026-07-27  

---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/phase06-lava-tube-base/2026-04-03-LOW-DATA-CO2-OXYGEN-PRODUCTION-UNIT-SCHEMA-AND-STOICHIOMETRY.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/phase06-lava-tube-base/2026-04-03-LOW-DATA-CO2-OXYGEN-PRODUCTION-UNIT-SCHEMA-AND-STOICHIOMETRY.md \
         projects/galaxy_game/tasks/active/2026-04-03-LOW-DATA-CO2-OXYGEN-PRODUCTION-UNIT-SCHEMA-AND-STOICHIOMETRY.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-04-03-LOW-DATA-CO2-OXYGEN-PRODUCTION-UNIT-SCHEMA-AND-STOICHIOMETRY.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-DATA-CO2-OXYGEN-SCHEMA-MIGRATION.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

## Context

The CO2 Oxygen Production Unit is a **life support unit** (category: `life_support`, subcategory: `air_production`) that provides breathable oxygen to habitats and settlements. It serves a different function than the Gas Conversion Unit (GCU):

| Unit | Category | Purpose | Primary Output |
|---|---|---|---|
| **CO2 Oxygen Production** | life_support/air_production | Habitat life support — breathable O₂ for crews | O₂ (oxygen) |
| **GCU (Gas Conversion Unit)** | production/refinery | ISRU propellant production — methane fuel | CH₄ (methane) + O₂ byproduct |

**These are separate units with different purposes. Do NOT merge them.**

The CO2 unit's chemistry is pure CO₂ electrolysis:
- **Reaction**: CO₂ → C + O₂ (direct solid-oxide electrolysis)
- **Mass balance**: 44 kg CO₂ → 12 kg Carbon + 32 kg O₂
- **Energy**: ~5.4 kWh per kg of O₂ produced

The GCU uses a different chemistry (Sabatier + electrolysis loop: CO₂ + 2H₂O → CH₄ + 2O₂) and is already correctly implemented in the new schema format.

---

## Problem Statement

`data/json-data/operational_data/units/life_support/co2_oxygen_production_data.json` is the operational data file for the CO2 Oxygen Production Unit, a life support unit that splits atmospheric CO2 into breathable oxygen. The file was never migrated from the old `resource_management.consumables/generated` schema to the current `input_resources` / `output_resources` / `processing_capabilities` schema used by all ISRU units (GCU, TEU, Gas Separator). Additionally its chemistry is physically impossible — inputs and outputs do not mass-balance.

**Current**: Old schema with `resource_management.consumables/generated` structure; chemically impossible stoichiometry (45 kg in → 65 kg out)  
**Expected**: New schema matching GCU pattern; chemically valid CO₂ → C + O₂ conversion

---

## Evidence of Incompleteness

### Schema Issues
The file uses the old flat-key consumables format:
```json
"resource_management": {
  "consumables": {
    "co2_kg": { "rate": 40.0, "current_usage": 0 },
    "hydrogen_kg": { "rate": 5.0, "current_usage": 0 }
  },
  "generated": {
    "oxygen_kg": { "rate": 30.0, "current_output": 0 },
    "water_l": { "rate": 35.0, "current_output": 0 }
  }
}
```

The canonical GCU format uses:
```json
"input_resources": [
  { "id": "CO2", "amount": 200.0, "unit": "kilogram" },
  { "id": "H2O", "amount": 163.64, "unit": "kilogram" }
],
"output_resources": [
  { "id": "CH4", "amount": 69.09, "unit": "kilogram" },
  { "id": "O2", "amount": 276.36, "unit": "kilogram" }
],
"processing_capabilities": { ... },
"operational_properties": { ... }
```

### Stoichiometry Issues (Current)
| Input | Rate (kg/hr) | Output | Rate (kg/hr) |
|---|---|---|---|
| CO₂ | 40.0 | O₂ | 30.0 |
| H₂ | 5.0 | H₂O | 35.0 |
| Energy | 18.0 kWh | Heat | 12.0 kW |
| **Total** | **45.0 kg** | **Total** | **65.0 kg** |

**Problem**: 45 kg in → 65 kg out. Mass is not conserved. The chemistry also mixes two different processes (CO₂ electrolysis and Sabatier) without clarifying which one the unit implements.

### Correct Stoichiometry (CO₂ Electrolysis)
| Input | Rate (kg/hr) | Output | Rate (kg/hr) |
|---|---|---|---|
| CO₂ | 44.0 | Carbon (solid) | 12.0 |
| Energy | ~65 kWh | O₂ | 32.0 |
| **Total** | **~65 kWh + 44 kg** | **Total** | **44 kg** |

Mass balance: 44 kg CO₂ → 12 kg C + 32 kg O₂ ✓

---

## Files to Edit

| File | Purpose | Key Section |
|------|---------|-------------|
| `data/json-data/operational_data/units/life_support/co2_oxygen_production_data.json` | CO2 production data | Migrate to new schema, fix stoichiometry |

### Reference Files (read but do not edit)
| File | Why You Need It |
|------|-----------------|
| `data/json-data/operational_data/units/production/refineries/gas_conversion_unit_data.json` | Canonical GCU format — use as schema template |
| `galaxy_game/app/services/logistics/isru_capability_manager.rb` | ISRU capability lookup — verify CO2 unit loads correctly |
| `galaxy_game/spec/services/logistics/isru_capability_manager_spec.rb` | ISRU spec — verify no regressions |

---

## Acceptance Criteria

- [ ] Data migrated from old `resource_management.consumables/generated` schema to `input_resources`/`output_resources`/`processing_capabilities` format
- [ ] Chemistry is physically valid: CO₂ → C + O₂ (44 kg CO₂ → 12 kg C + 32 kg O₂)
- [ ] Schema matches canonical GCU structure (template, description, processing_capabilities, operational_properties, error_states, telemetry, metadata, base_cost_eap, usd_import_fee)
- [ ] Unit is clearly identified as **life_support** unit (NOT merged with GCU production/refinery unit)
- [ ] `UnitLookupService` can load the data without errors
- [ ] ISRU evaluator can read `output_resources` and `processing_capabilities` correctly

---

## Implementation Steps

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/review/2026-04-03-LOW-DATA-CO2-OXYGEN-PRODUCTION-UNIT-SCHEMA-AND-STOICHIOMETRY.md \
       projects/galaxy_game/tasks/active/2026-04-03-LOW-DATA-CO2-OXYGEN-PRODUCTION-UNIT-SCHEMA-AND-STOICHIOMETRY.md
```

Then open the moved file and change YAML status: `status: backlog` → `status: active`

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-04-03-LOW-DATA-CO2-OXYGEN-PRODUCTION-UNIT-SCHEMA-AND-STOICHIOMETRY.md"
```

### Step 1 — Read Canonical GCU Format

Read `data/json-data/operational_data/units/production/refineries/gas_conversion_unit_data.json` to understand the target schema structure. Note which fields are present and their types.

### Step 2 — Migrate Schema (Preserve Life Support Identity)

Migrate `co2_oxygen_production_data.json` to new format:
- Keep `"category": "life_support"` and `"subcategory": "air_production"`
- Add `"template": "unit_operational_data"` and `"description"` documenting CO₂ electrolysis chemistry
- Replace `resource_management.consumables` with `input_resources`: `{id: "CO2", amount: 44.0, unit: "kilogram"}`
- Replace `resource_management.generated` with `output_resources`: `{id: "C", amount: 12.0, unit: "kilogram"}`, `{id: "O2", amount: 32.0, unit: "kilogram"}`
- Add `processing_capabilities.atmospheric_processing` (enabled, types: ["co2_electrolysis"])
- Add `operational_properties` with power consumption (~65 kWh/hr for CO₂ electrolysis), heat generation, failure rate, maintenance interval
- Add `error_states`: ["catalyst_depleted", "overtemperature_shutdown", "input_gas_depleted", "output_blocked"]
- Add `telemetry`, `metadata`, `base_cost_eap`, `usd_import_fee`

### Step 3 — Fix Stoichiometry

Ensure all rates are consistent with CO₂ → C + O₂ chemistry:
- Input: 44 kg CO₂ per cycle
- Output: 12 kg Carbon + 32 kg O₂ per cycle
- Energy: ~65 kWh per cycle (solid-oxide electrolysis)
- No hydrogen input (that's the GCU's job)
- No water output (that's also the GCU's loop)

### Step 4 — Verify UnitLookupService Loads Correctly

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/logistics/isru_capability_manager_spec.rb --format progress 2>&1 | tail -20'
```

Expected: all existing examples pass, no regressions.

---

## Stop Conditions — escalate to user immediately if:
- `UnitLookupService` or ISRU evaluator has schema requirements not documented in GCU
- The CO2 unit is referenced by a blueprint file that also needs migration
- Any architectural decision conflicts with locked decisions in `DECISIONS.md`

---

## Commit Instructions

```bash
git add data/json-data/operational_data/units/life_support/co2_oxygen_production_data.json
git commit -m "fix: migrate CO2 oxygen production data to new schema and fix stoichiometry (CO2 -> C + O2)"
```

**Task file move on completion:**
```bash
git mv projects/galaxy_game/tasks/active/2026-04-03-LOW-DATA-CO2-OXYGEN-PRODUCTION-UNIT-SCHEMA-AND-STOICHIOMETRY.md \
       projects/galaxy_game/tasks/completed/2026-07/2026-04-03-LOW-DATA-CO2-OXYGEN-PRODUCTION-UNIT-SCHEMA-AND-STOICHIOMETRY.md

git commit -m "chore: move 2026-04-03-LOW-DATA-CO2-OXYGEN-PRODUCTION-UNIT-SCHEMA-AND-STOICHIOMETRY.md to completed/"
```

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]  
**Completion date**: YYYY-MM-DD  

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
