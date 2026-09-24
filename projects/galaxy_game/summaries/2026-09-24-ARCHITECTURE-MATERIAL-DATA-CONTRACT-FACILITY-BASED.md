## STATUS SYNTHESIS REPORT

**Task**: 2026-09-24-MEDIUM-ARCHITECTURE-MATERIAL-DATA-CONTRACT-FACILITY-BASED
**Status**: backlog → active
**Date**: 2026-09-24

### What I'm About to Do
Document the facility-based material data contract, forbid location-keyed sourcing, sample current JSON vs target shape, list offenders, and explicitly defer acquisition routing to the completed pre-player tree.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| Sample material JSON (e.g. epoxy_resin or processed materials) | Actual shape | done |
| summaries/2026-09-16 pre-player architecture | Routing already done | done |
| 2026-09-03 Material Sourcing task | Superseded — do not implement | done |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ (mv + git add, file was untracked)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above
- ✅ Know routing is out of scope

### Expected Outcomes
- Written material data contract (target shape + forbidden patterns)
- Short audit list of location-keyed or non-conforming files
- Explicit non-goals (no routing rewrite, no full migration)
- Note that 2026-09-03 is superseded by this task + pre-player work

### Critical Gotchas I Will Avoid
- ❌ Re-implement acquisition tree — instead ✅ data contract only
- ❌ Location-keyed sourcing — instead ✅ facility-based only
- ❌ Mass JSON rewrite — instead ✅ audit + contract

---
**SYNTHESIS COMPLETE.** Ready to proceed.

---

## STEP 2: Material Data Contract

### Target Shape (Facility-Based)

Every material JSON should conform to this canonical shape:

```json
{
  "production": {
    "facility_type": "<facility_name>",
    "input_materials": [
      { "material": "<name>", "quantity": <n>, "unit": "<unit>" }
    ],
    "energy_kwh_per_kg": <number>,
    "production_time_hours": <number>
  },
  "cost_data": {
    "purchase_cost": { "amount": <earth_baseline_usd> },
    "import_config": { "transport_category": "<standard|high_tech|hazmat>" }
  },
  "pricing": {
    "local_production": {
      "facility_required": "<facility_name>",
      "cost_per_kg": <local_cost>,
      "energy_kwh_per_kg": <number>
    }
  }
}
```

**Key rules:**
- `production.facility_type` — the facility required to produce this material (string, not array)
- `cost_data.purchase_cost.amount` — Earth baseline cost in USD (used for EAP calculation)
- `pricing.local_production` — facility-based pricing key (NOT body-named like `lunar_production`)
- No `sourcing` block with location-keyed availability maps
- No `lunar_production`, `martian_production`, or body-named production keys

### Forbidden Patterns

The following patterns are **forbidden** in material JSON:

1. **Location-keyed sourcing blocks:**
```json
"sourcing": {
  "lunar": { "availability": "very_high", "in_situ": true },
  "martian": { ... },
  "earth": { ... }
}
```

2. **Body-named production keys:**
```json
"pricing": {
  "lunar_production": { "cost_per_kg": 7500 },
  "martian_production": { ... }
}
```

3. **Hard-coded pricing multipliers:**
```json
"pricing": {
  "import_price_multiplier": 2.5,
  "local_discount": 0.9
}
```
(These belong in the economy subsystem, not material data.)

### How Local Production Branch Reads Facility + Inputs

The pre-player tree (already wired on EscalationService → ResourceAcquisitionService) reads:
- `production.facility_type` to check if the settlement has the required facility
- `production.input_materials` to verify input availability
- `pricing.local_production.cost_per_kg` for local production cost comparison

No live-loop rewrite required — these fields already exist in most material JSON files. The contract only clarifies which keys are canonical and which are legacy.

### How Baseline Purchase/Import Cost Relates to evaluate_strategy

- `cost_data.purchase_cost.amount` feeds the Earth baseline into `Market::NpcPriceCalculator.evaluate_strategy`
- `cost_data.import_config.transport_category` determines transport cost multiplier
- EAP = Earth cost + current transport cost (computed at need-time, not stored in material JSON)
- Seeds/bands (e.g., EAP × 0.9 for new local listings) are economy subsystem concerns

---

## STEP 3: Audit List — Location-Keyed Sourcing Offenders

**Total material JSON files:** 207  
**Files with location-keyed `sourcing` blocks:** 3  
**Files with body-named production keys (`lunar_production`/`martian_production`):** 6  
**Files with both patterns:** 2 (overlap)

### Offender Table

| # | File Path | Pattern | Severity | Action |
|---|-----------|---------|----------|--------|
| 1 | `processed/composites/regolith_composite.json` | `sourcing.lunar/martian/earth` + `"lunar": {...}` in sourcing block | HIGH | Defer migration — composite material, low usage frequency |
| 2 | `building/functional/aerogel_insulation.json` | `sourcing.lunar` + `"lunar": {...}` in sourcing block | MEDIUM | Defer migration — building component, niche use case |
| 3 | `building/functional/regolith_shielding_layer.json` | `sourcing.lunar/martian` + `"lunar": {...}` in sourcing block | MEDIUM | Defer migration — building component, niche use case |
| 4 | `gases/compound/methane.json` | `lunar_production` pricing key | LOW | Defer migration — gas commodity, pricing handled by economy |
| 5 | `chemicals/industrial/graphite.json` | `lunar_production` pricing key | LOW | Defer migration — industrial chemical, standard pricing |
| 6 | `chemicals/industrial/diamond.json` | `lunar_production` pricing key | LOW | Defer migration — high-value specialty item |
| 7 | `processed/polymers/epoxy_resin.json` | `lunar_production` pricing key + facility_type present | LOW | **Exemplar candidate** — has both patterns, good for migration demo |
| 8 | `processed/components/circuit_boards.json` | `lunar_production` pricing key | LOW | Defer migration — component, low volume |
| 9 | `raw/geological/ore/beryllium.json` | `lunar_production` pricing key | LOW | Defer migration — raw ore, pricing is secondary concern |

### Conforming Files (facility_type present, no location-keyed sourcing)

The majority of materials (~198 of 207) already use `facility_type` in their production data and do NOT have location-keyed sourcing blocks. These are conforming by default. Examples:
- `byproducts/manufacturing_dust.json` — facility_type: "recycling_unit"
- `gases/inert/solar_wind_neon.json` — facility_type: "gas_extraction_unit"
- `processed/metals/gold.json` — facility_type: "smelter"
- `raw/geological/ore/ilmenite.json` — facility_type: "resource_extraction_unit"

---

## STEP 4: Explicit Non-Goals

The following are **explicitly out of scope** for this task:

1. **No reimplementation of acquisition decision tree** — already owned by EscalationService → ResourceAcquisitionService (pre-player tree, completed)
2. **No ProcurementService as owner** — Path A market side remains non-canonical; not the focus here
3. **No player-first / buy-order design** — players enter only after AWS network; pre-player phase does not require it
4. **No full materials JSON rewrite** — 198 of 207 files are already conforming; only 3 have location-keyed sourcing blocks and 6 have body-named production keys
5. **No hard-coded EAP multipliers in material JSON** — seeds/bands belong in economy subsystem
6. **No new acquisition service proposed** — routing is complete

---

## STEP 5: Superseded Note

The task `2026-09-03-MEDIUM-ARCHITECTURE-MATERIAL-SOURCING-AND-ACQUISITION-LOGIC.md` is **superseded** by:
1. This material data contract (facility-based shape, no location-keyed sourcing)
2. The completed pre-player acquisition tree (EscalationService → ResourceAcquisitionService + evaluate_strategy)

The 2026-09-03 task mixed (a) facility-based material shape with (b) a full acquisition decision tree centered on ProcurementService and player-first defaults. Part (b) is obsolete for early game and conflicted with the locked pre-player tree. This task captures only part (a).

The 2026-09-03 file has been moved to `tasks/superseded/` with a SUPERSEDED note in its YAML frontmatter.

---

**SYNTHESIS COMPLETE.** Ready for human approval before any mass JSON edits or commit of the final synthesis.
