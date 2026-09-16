# Status Synthesis Report — Epoxy Resin Sourcing Rework (BLOCKED)

**Task**: `2026-09-02-HIGH-DATA-REWORK-EPOXY-RESIN-SOURCING-STRUCTURE.md`
**Date**: 2026-09-16
**Status**: **BLOCKED** — Re-scoped as planning/reconciliation task. No implementation authorized.

---

## Executive Summary (Updated 2026-09-16)

The original task premise was based on stale data (the task file described an empty `production` block and flat `sourcing` that don't match current disk state). The initial synthesis report proposed adding per-location sourcing keys (`earth`, `lunar`, `martian`) — which the user correctly rejected as incompatible with galaxyGame's procedural-world model.

**This task is now BLOCKED.** No new executable `sourcing` fields may be added to `epoxy_resin.json` until a geography-agnostic sourcing schema is approved by the human. The existing epoxy `production` data, `sourcing_strategy`, and all other material JSON must remain unchanged.

---

## 1. Current State of `epoxy_resin.json` (on disk RIGHT NOW)

**File path**: `/Users/tam0013/Documents/git/galaxyGame/data/json-data/resources/materials/processed/polymers/epoxy_resin.json`

### CRITICAL FINDING: The task file describes a DIFFERENT version of this file.

The task file states the current `sourcing` block is:
```json
"sourcing": {
  "method": "earth_import",
  "origin": "Earth chemical supply chain",
  "logistics_note": "..."
}
```
And production is empty:
```json
"production": {
  "facility_type": "",
  "energy_kwh_per_kg": 0.0,
  "input_materials": [],
  "byproducts": []
}
```

**Reality on disk**: There is NO `sourcing` block at all. Instead there is a `sourcing_strategy` field:
```json
"sourcing_strategy": {
  "phase_1_early": "Earth import only (Cape Canaveral and other spaceports supply)",
  "phase_2_plus": "Chemical synthesis plants come online...",
  "isru_incentive": "Local production (7500 USD/kg + input costs) beats Earth import..."
}
```

The `production` block is **already fully populated** (not empty):
```json
"production": {
  "facility_type": "chemical_synthesis_plant",
  "input_materials": [
    { "material": "hydrocarbon_feedstock", "quantity": 2.0, "unit": "liter", "notes": "..." },
    { "material": "chlorine", "quantity": 0.8, "unit": "kg", "notes": "..." },
    { "material": "sodium_hydroxide", "quantity": 0.3, "unit": "kg", "notes": "..." }
  ],
  "byproducts": [
    { "material": "saline_waste", "quantity_per_output_kg": 0.1, "unit": "liter", "notes": "..." }
  ],
  "energy_kwh_per_kg": 8.5,
  "production_time_hours": 0.5,
  "reaction_temperature_k": 353,
  "reaction_pressure_kpa": 300,
  "notes": "Polymerization occurs during synthesis..."
}
```

Also present: `pricing.lunar_production` block with cost/facility/energy data.

---

## 2. Existing Multi-Source Pattern (Reference Files)

### Sourcing patterns found in codebase:

| File | Structure |
|---|---|
| `regolith_composite.json` | Per-location keys: `{lunar: {...}, martian: {...}, earth: {...}}` with `availability`, `extraction_method`, `in_situ` |
| `aerogel_insulation.json` | Single-location key: `{lunar: {...}}` with `availability`, `manufacturing_difficulty`, `in_situ` |
| `regolith_shielding_layer.json` | Multi-location: `{lunar: {...}, martian: {...}}` with `availability`, `extraction_method`, `in_situ` |

**Pattern**: All three use a top-level `sourcing` object with location keys (`lunar`, `martian`, `earth`). Each location value has `availability` + either `extraction_method` or `manufacturing_difficulty` + `in_situ`.

### Epoxy resin's current pattern:
- Uses `sourcing_strategy` (NOT `sourcing`) — a completely different field name with phase-based text descriptions
- This is the ONLY material in the codebase that uses `sourcing_strategy` instead of `sourcing`

---

## 3. Existing Production Block Pattern (Reference Files)

| File | input_materials format | facility_type | energy_kwh_per_kg |
|---|---|---|---|
| `methane.json` | `{id, amount}` objects | `"sabatier_reactor"` | 1.2 |
| `precision_components.json` | **String array** (`["steel", "aluminum_alloy"]`) | `"machine_shop"` | 2.0 |
| `manufacturing_dust.json` | `{id, amount}` objects | `"recycling_unit"` | 0.5 |

**Dominant pattern**: `{id, amount}` object format (2 of 3). String array exists in `precision_components.json`.

### Epoxy resin's current production format:
- Uses `{material, quantity, unit, notes}` — **different from all reference files**
- No `id` field (uses `material`)
- Has extra `unit` and `notes` fields not present in references
- Has extra top-level fields: `production_time_hours`, `reaction_temperature_k`, `reaction_pressure_kpa`

---

## 4. Proposed New `sourcing` Structure

**Decision needed**: Epoxy resin currently has NO `sourcing` block — only `sourcing_strategy`. The task wants to add a `sourcing` block matching the per-location key pattern.

### Option A: Add `sourcing` alongside existing `sourcing_strategy` (non-breaking)
```json
"sourcing": {
  "earth": {
    "availability": "high",
    "method": "import",
    "in_situ": false,
    "logistics_note": "Available from Phase 1; imported in bulk for later composite fabrication."
  },
  "lunar": {
    "availability": "none",
    "note": "No local production path yet. Future: polymer synthesis from local volatiles."
  },
  "martian": {
    "availability": "none",
    "note": "No local production path yet. Future: polymer synthesis from ISRU-derived feedstocks."
  }
},
"sourcing_strategy": { ...existing content preserved... }
```

### Option B: Replace `sourcing_strategy` with `sourcing` (breaking if any code reads `sourcing_strategy`)
- Would need to verify no consumer reads `sourcing_strategy` directly from material JSON

**Recommendation**: Option A — add `sourcing` block without removing `sourcing_strategy`. This is the safest approach and matches how other materials coexist with their own patterns.

---

## 5. Proposed New `production` Structure

**Decision needed**: The current `production` block already exists and is populated. The task wants to restructure it to match reference file patterns.

### Current format (epoxy_resin):
```json
"input_materials": [
  { "material": "...", "quantity": N, "unit": "...", "notes": "..." }
]
```

### Target format (matching methane/manufacturing_dust):
```json
"production": {
  "facility_type": "chemical_synthesis_plant",
  "energy_kwh_per_kg": 8.5,
  "input_materials": [
    { "id": "hydrocarbon_feedstock", "amount": 2.0 },
    { "id": "chlorine", "amount": 0.8 },
    { "id": "sodium_hydroxide", "amount": 0.3 }
  ],
  "byproducts": [
    { "id": "saline_waste", "amount": 0.1 }
  ],
  "notes": "Polymerization occurs during synthesis..."
}
```

**Changes required**:
- Rename `material` → `id` in input_materials/byproducts
- Rename `quantity` → `amount` in input_materials/byproducts
- Remove `unit` field (not present in reference files)
- Remove `notes` from individual items (keep one top-level `notes`)
- Keep existing `production_time_hours`, `reaction_temperature_k`, `reaction_pressure_kpa` as extras (they don't conflict with references)

---

## 6. What Will NOT Be Changed

- `properties` block — untouched
- `cost_data` block — untouched (including `import_config`)
- `applications` array — untouched
- `dependency_of` array — untouched
- `pricing` block — untouched
- `metadata` block — untouched
- `classification` block — untouched
- `storage` / `handling` blocks — untouched
- `sourcing_strategy` — preserved (Option A approach)

---

## 7. Codebase Audit Results

### `sourcing.method` consumption: **NONE FOUND**
- Grep for `sourcing\.method|sourcing\[:method\]` in `galaxy_game/app/` → 0 matches
- No code reads `sourcing.method` directly — safe to add per-location keys

### `production.*` field consumption: **MINIMAL**
- Grep for `production\.facility_type|production\.input_materials|production\.energy_kwh_per_kg` in `galaxy_game/app/` → 0 matches
- No app code reads production fields from material JSON files
- The only references are in blueprint JSON (graphene_composite_bp.json) which uses its own `required_materials` structure

### `input_materials` consumption: **MINIMAL**
- Only found in `task_execution_engine.rb` lines 530/536 — reads from `effect['inputs']`, NOT from material JSON files
- No material loading service appears to parse `production.input_materials` from JSON

### `sourcing_strategy` consumption: **4 matches** (all in AI manager services)
- `mission_planner_service.rb` line 60, 823 — uses `calculate_sourcing_strategy` method (computes it, doesn't read from JSON)
- `economic_forecaster_service.rb` lines 289, 313, 323, 371 — reads `@planner_results[:sourcing_strategy]` (computed value, not from material JSON)

**Conclusion**: The `sourcing_strategy` field in the JSON file is **not consumed by any app code**. It's a documentation/authoring artifact. Adding a `sourcing` block alongside it will not break anything.

---

## 8. Verification Plan

1. **JSON validity**: `python3 -c "import json; json.load(open('...epoxy_resin.json')); print('OK')"`
2. **graphene_composite_bp.json**: Already verified — references `epoxy_resin` via `required_materials.epoxy_resin` (string key, not JSON file path). Changing epoxy_resin's internal structure won't affect this resolution.
3. **Material loading**: No app code reads `sourcing` or `production` from material JSON files — no risk of breakage.
4. **RSpec specs**: Run any specs that reference epoxy_resin (likely none given no app code consumes these fields).

---

## 9. BLOCKED — Re-scoped as Planning/Reconciliation Task (2026-09-16)

**The task is BLOCKED pending human approval of a geography-agnostic sourcing model.**

### Prohibited Changes (effective immediately)
- **NO new executable `sourcing` fields** may be added to `epoxy_resin.json`
- **NO normalization of `production.input_materials`** — separate schema-planning task
- **Existing epoxy `production` data must remain unchanged** — current `{material, quantity, unit, notes}` format preserved
- **`sourcing_strategy` field remains unchanged** — narrative/authoring metadata

### Why the Original Proposal Was Rejected
The proposed per-location sourcing keys (`earth`, `lunar`, `martian`) embed Sol-world assumptions incompatible with galaxyGame's procedural-world model. The user correctly identified that:
- Sol worlds are only starting points, not the universal geography model
- Material sourcing data must be portable across arbitrary celestial bodies, colonies, settlements, and systems
- Adding Earth-specific import behavior (`from_earth`) duplicates pricing/logistics/facility/feedstock/energy info that belongs in runtime systems or already exists in `production`

### Identified Separate Future Tasks

**Task A — Procedural-world pricing resolution** (more urgent):
- `pricing.lunar_production` hardcoding in `npc_price_calculator.rb` at 4 locations
- All use `dig('pricing', 'lunar_production', 'cost_per_kg')` — won't work for arbitrary bodies
- Requires geography-agnostic resolver that works with procedurally generated world names

**Task B — Production-input normalization** (separate schema-planning task):
- Three different `input_materials` formats across 20 materials: `{id, amount}`, `{material, quantity, unit, notes}`, string arrays
- No app code currently consumes these from material JSON files
- Requires schema planning independent of sourcing redesign

### Required Future Handoff (when geography-agnostic model is approved)

```text
You are Qwen acting as a READ-ONLY PLANNING AND EVIDENCE agent in the
galaxyGame repository.

Task type: Geography-agnostic sourcing schema planning — evidence inventory.

Do not create, edit, move, rename, delete, stage, commit, stash, reset, clean,
rebase, checkout, generate repository reports, or otherwise alter any file or
Git state. Return findings only in your response.

Required read-only investigation:

1. All `pricing.lunar_production` consumers — list every file, line number, and
   the exact dig path used. Identify which are hardcoded to "luna" vs. computed.
2. All body-name checks across the codebase — grep for "luna", "earth",
   "martian", "deep_space_location?", and any celestial-body name literals used
   in pricing/sourcing logic.
3. PrecursorCapabilityService inputs — what body properties does it check?
   Does it accept arbitrary body names or only known Sol bodies?
4. Candidate geography-agnostic resolver boundaries — where would a runtime
   pricing resolver plug in without breaking existing EAP/extraction/capex logic?

Return the required inventory report only. Make no repository changes.
```

---

## 10. Risk Assessment

| Risk | Severity | Mitigation |
|---|---|---|
| `sourcing_strategy` field becomes orphaned | Low | Keep it alongside new `sourcing` block |
| Format change breaks any consumer | None confirmed | No app code reads these fields from JSON |
| `graphene_composite_bp.json` resolution | None | Uses string key `epoxy_resin`, not file structure |
| `cost_data.import_config` coupling | Low | Not touching `cost_data` block |
| New format inconsistent with other materials | Medium | Follow dominant pattern (`{id, amount}`) |

---

**Awaiting approval to proceed with implementation.**
