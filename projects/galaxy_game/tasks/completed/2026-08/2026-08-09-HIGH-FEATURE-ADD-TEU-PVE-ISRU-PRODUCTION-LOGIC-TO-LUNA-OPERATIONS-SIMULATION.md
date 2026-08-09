---
title: Add TEU/PVE ISRU Production Logic to LunaOperationsSimulationService
priority: HIGH
status: completed
completion_date: 2026-08-09
phase: phase5
owner: AI Manager / task-v2 runner
type: feature
tags:
  - luna
  - ai-manager
  - isru
  - teus
  - pve
  - simulation
  - production
dependencies:
  - Luna settlement exists (confirmed: ID 103)
  - TEU Mk1 deployed to Luna base
  - PVE Mk1 deployed to Luna base
  - Regolith inventory present
relocated_from: null
---

## Goal

Add Thermal Extraction Unit (TEU) and Planetary Volatiles Extractor (PVE) production logic to `LunaOperationsSimulationService#calculate_blueprint_production()` so that a 50-day `luna:simulate_operations` run produces non-zero, sane oxygen/hydrogen/He3 outputs tied to regolith consumption and deployed hardware.

## Context

Today's full Luna MVP validation run confirmed the build/deploy sequence works (17/17) and water consumption is correctly modeled (bugfix verified). The ONE remaining gap for Luna MVP closure: `calculate_blueprint_production()` only implements I-beam/construction-material production. No TEU or PVE production logic exists — oxygen, hydrogen, and He3 all show zero output across a full 50-day simulation despite regolith, crew, and deployed hardware all being present and correct.

### Current Architecture Baseline (as of 2026-08-08)

**tasks_v2/ missions_v2/** use flat lists with `task_ref` + static parameters — no conditional/scoring logic today. This task is meant to add decision logic on top of that baseline. The AI Manager's current capabilities are limited to:
- `Settlements::CostAnalyzer` — autonomous cost analysis (Phase 1)
- `Logistics::ManifestGenerator` — smart sourcing (player → NPC → Earth priority) (Phase 1)
- `Logistics::ShortageDetector` — resource monitoring ( Phase 1)
- `ImportRequestGenerator` — shortage prediction (Phase 1)
- `StrategySelector` — pattern matching for life-support prioritization (Phase 1)

None of these provide the location-driven expansion option generation this task requires. That logic must be built as a new capability layer.

### L1 Dependency Status — BLOCKED (verified 2026-08-08)

**"L1 settlement exists" is NOT currently true.** Confirmed via codebase search:
- Zero matches for `L1.*Station` in `db/seeds.rb`, `db/migrate/`, or `data/` — no L1 settlement record exists in any seed data.
- The only real L1 content is two bare manifest files (`l1_station_depot_manifest_v1.json`, `leo_depot_construction_manifest_v1.json`) — no profile, no phases, no deployment data.
- Codebase has infrastructure stubs (cost calculations, trade service looking for `/L1.*Station/`, location operations job routing) — these are placeholder gateways that would only activate once an L1 settlement record exists.
- **This is a hard block.** The task cannot proceed until Phase 7 (Depot Building) creates a deployed L1 base with profile, plan, and phases.

### Luna Dependency Status — UNBLOCKED

**"Luna settlement exists" is confirmed true.** Settlement ID 103, Luna Base, seeded and validated per 2026-08-08 simulation work.

## Scope

In scope:
1. **Confirm exactly what `calculate_blueprint_production()` does today for I-beams** — read the method in `galaxy_game/app/services/luna_operations_simulation_service.rb` (line ~158), use it as the reference pattern for how production logic should be structured/wired into the simulation tick loop.
2. **Confirm what TEU and PVE blueprints define as their outputs** — check:
   - `data/json-data/blueprints/units/production/extractors/thermal_extraction_unit_mk1_bp.json` (v1.3)
   - `data/json-data/blueprints/units/production/extractors/planetary_volatiles_extractor_mk1_bp.json`
   - `data/json-data/operational_data/units/production/extractors/thermal_extraction_unit_mk1_data.json`
   - `data/json-data/operational_data/units/production/extractors/planetary_volatiles_extractor_mk1_data.json`
3. **Implement TEU production logic** — thermal processing of raw_regolith → processed_regolith + mixed_volatiles
4. **Implement PVE production logic** — volatile extraction from processed_regolith → O2, H2, He3 (and other volatiles)
5. **Wire production into the simulation tick loop** — respect hardware deployment state, power availability, and regolith feedstock
6. **Ground constants in ECLSS research** — reference `docs/design/ECLSS_PARAMETERS.md` and `docs/design/ECLSS_SYSTEM_ARCHITECTURE.md` for real-world baselines (e.g., electrolysis efficiency, Sabatier methane yield ratios)

Out of scope:
- Skimmer/gas-import logic (separate task)
- Implementing the full expansion system
- Adding new settlement infrastructure beyond simulation tick logic
- Hard-coding future phase systems that have not been validated
- Replacing existing task-v2 conventions or generic task file format

## Implementation Plan

### Step 1: Audit `calculate_blueprint_production()` (Reference Pattern)

Current method at line ~158 of `luna_operations_simulation_service.rb`:

```ruby
def calculate_blueprint_production(inventory, capability_service)
  result = { feedstock_consumption: {} }

  # I-beam production: regolith -> 3D printed I-beam.
  if capability_service.can_produce_locally?('regolith')
    regolith_available = inventory.current_storage_of('regolith')
    if regolith_available >= 75
      ibeam_output = 69.0
      result['ibeam'] = ibeam_output
      result[:feedstock_consumption]['regolith'] = (result[:feedstock_consumption]['regolith'] || 0) + 75
    end
  end

  result
end
```

Pattern to follow:
- Check `capability_service.can_produce_locally?(resource)` for feedstock availability
- Read inventory via `inventory.current_storage_of(resource)`
- Return hash with scalar production keys + `feedstock_consumption` sub-hash
- Caller iterates `feedstock_consumption` separately and applies consumption

### Step 2: Blueprint Data Review

**TEU Mk1** (`thermal_extraction_unit_mk1_data.json`):
- Input: 10 kg raw_regolith per cycle
- Output: 9.95 kg processed_regolith + mixed_volatiles (world-driven, currently amount=0)
- Energy: 50 kWh per cycle
- Thermal processing: 300–800°C, geosphere efficiency 0.995

**PVE Mk1** (`planetary_volatiles_extractor_mk1_data.json`):
- Input: 5 kg processed_regolith per cycle
- Output: mixed_volatiles (0), H2O (0), depleted_regolith (0) — all currently stubbed at 0
- Energy: 120 kWh per cycle
- Geosphere processing efficiency: 0.75
- Storage capacity: 185 kg

**PVE Mk1 Blueprint** (`planetary_volatiles_extractor_mk1_bp.json`):
- Description: "Base-model extractor for breaking down regolith oxides to extract oxygen via high-temperature chemical reduction."
- byproducts: inert_regolith_waste (0)
- connection_schema: recently updated with mounting_slots + utility_ports blocks

### Step 3: Define Production Ratios

Based on blueprint data and ECLSS research:

**TEU Production (per cycle):**
- Consumes: 10 kg raw_regolith
- Produces: 9.95 kg processed_regolith (efficiency 0.995)
- Produces: mixed_volatiles = regolith_volatile_fraction × 10 kg
  - Mars regolith volatile content: ~0.1–1.0% by mass (literature range)
  - Use 0.5% default → 0.05 kg mixed_volatiles per cycle
- Power: 50 kWh

**PVE Production (per cycle):**
- Consumes: 5 kg processed_regolith
- Produces: O2 via high-temp chemical reduction of regolith oxides
  - Mars regolith is ~42% oxygen by mass (FeO, SiO2, Al2O3, MgO, CaO)
  - PVE efficiency: 0.75 → ~31.5% recoverable O2
  - Per cycle: 5 kg × 0.315 = 1.575 kg O2
- Produces: H2 from water ice in volatiles (if H2O present)
  - Electrolysis: 2H2O → 2H2 + O2 (mass ratio H2:H2O = 2:18 = 1:9)
  - Per kg H2O → 0.111 kg H2 + 0.889 kg O2
- Produces: He3 from solar-wind implanted volatiles
  - Mars regolith He3: ~10–50 ppt (parts per trillion) — negligible at game scale
  - Use 0.000001 kg per cycle as placeholder (document as research-driven)
- Power: 120 kWh

### Step 4: Implementation in `calculate_blueprint_production()`

Add two new production blocks following the I-beam pattern:

```ruby
# ── TEU Production ──
# Requires: deployed TEU hardware + raw_regolith feedstock
teu_units = settlement.base_units.where(unit_type: 'thermal_extraction_unit').to_a
if teu_units.any? && capability_service.can_produce_locally?('regolith')
  regolith_for_teu = [
    inventory.current_storage_of('regolith'),
    teu_units.size * GameConstants::TEU_REGOLITH_PER_CYCLE_KG
  ].min

  if regolith_for_teu >= GameConstants::TEU_REGOLITH_PER_CYCLE_KG
    cycles = (regolith_for_teu / GameConstants::TEU_REGOLITH_PER_CYCLE_KG).floor
    processed_regolith = cycles * GameConstants::TEU_PROCESSED_REGOLITH_PER_CYCLE_KG
    mixed_volatiles = cycles * GameConstants::TEU_MIXED_VOLATILES_PER_CYCLE_KG

    result['processed_regolith'] = (result['processed_regolith'] || 0) + processed_regolith
    result[:feedstock_consumption]['regolith'] = (result[:feedstock_consumption]['regolith'] || 0) + regolith_for_teu
    # mixed_volatiles added to production (not feedstock)
    result['mixed_volatiles'] = (result['mixed_volatiles'] || 0) + mixed_volatiles
  end
end

# ── PVE Production ──
# Requires: deployed PVE hardware + processed_regolith feedstock
pve_units = settlement.base_units.where(unit_type: 'planetary_volatiles_extractor').to_a
if pve_units.any? && capability_service.can_produce_locally?('processed_regolith')
  processed_reg_for_pve = [
    inventory.current_storage_of('processed_regolith'),
    pve_units.size * GameConstants::PVE_REGOLITH_PER_CYCLE_KG
  ].min

  if processed_reg_for_pve >= GameConstants::PVE_REGOLITH_PER_CYCLE_KG
    cycles = (processed_reg_for_pve / GameConstants::PVE_REGOLITH_PER_CYCLE_KG).floor

    # O2 from regolith oxides
    o2_yield = cycles * GameConstants::PVE_O2_PER_CYCLE_KG
    result['oxygen'] = (result['oxygen'] || 0) + o2_yield

    # H2 from water electrolysis (if mixed_volatiles contains H2O)
    h2o_available = inventory.current_storage_of('H2O')
    if h2o_available > 0
      h2_from_electrolysis = cycles * GameConstants::PVE_H2_FROM_ELECTROLYSIS_PER_CYCLE_KG
      result['hydrogen'] = (result['hydrogen'] || 0) + h2_from_electrolysis
    end

    # He3 from solar-wind volatiles
    he3_yield = cycles * GameConstants::PVE_HE3_PER_CYCLE_KG
    result['he3'] = (result['he3'] || 0) + he3_yield if he3_yield > 0

    result[:feedstock_consumption]['processed_regolith'] = (result[:feedstock_consumption]['processed_regolith'] || 0) + processed_reg_for_pve
  end
end
```

### Step 5: Define GameConstants in ECLSS module

Add to `GameConstants` (likely `galaxy_game/app/constants/game_constants.rb` or similar):

```ruby
# ── TEU Mk1 Constants ──
TEU_REGOLITH_PER_CYCLE_KG = 10.0
TEU_PROCESSED_REGOLITH_PER_CYCLE_KG = 9.95
TEU_MIXED_VOLATILES_PER_CYCLE_KG = 0.05    # 0.5% volatile fraction (Mars regolith literature)
TEU_POWER_KW = 50

# ── PVE Mk1 Constants ──
PVE_REGOLITH_PER_CYCLE_KG = 5.0
PVE_O2_RECOVERY_RATIO = 0.315              # 42% O2 in regolith × 0.75 efficiency
PVE_O2_PER_CYCLE_KG = PVE_REGOLITH_PER_CYCLE_KG * PVE_O2_RECOVERY_RATIO  # 1.575
PVE_H2_FROM_ELECTROLYSIS_PER_CYCLE_KG = 0.111  # 1 kg H2O → 0.111 kg H2
PVE_HE3_PER_CYCLE_KG = 0.000001            # Solar-wind implanted, negligible at game scale
PVE_POWER_KW = 120

# ── ECLSS Reference Constants (from docs/design/ECLSS_PARAMETERS.md) ──
BASE_WATER_RECOVERY_EFFICIENCY = 0.98
CREW_WATER_DAILY_KG = 3.5
```

### Step 6: Write Acceptance Test

Create or update spec for `LunaOperationsSimulationService` that validates:
- A 50-day simulation with deployed TEU + PVE shows non-zero O2, H2 production
- Production scales linearly with number of deployed units
- Production stops when regolith feedstock is exhausted
- Power consumption is tracked (if power system exists in simulation)

## Acceptance Criteria

1. **Non-zero production**: A 50-day `luna:simulate_operations` run with at least 1 TEU and 1 PVE deployed shows non-zero oxygen, hydrogen, and He3 production values in the simulation output.
2. **Sane ratios**: Production amounts are tied to regolith consumption via documented ratios (not arbitrary numbers). Each kg of processed_regolith consumed yields ~1.575 kg O2 (documented as 42% × 0.75 efficiency).
3. **Feedstock chain validated**: TEU consumes raw_regolith → produces processed_regolith; PVE consumes processed_regolith → produces O2/H2/He3. The chain is visible in simulation deltas.
4. **ECLSS-grounded constants**: All production ratios reference `docs/design/ECLSS_PARAMETERS.md` or `docs/design/ECLSS_SYSTEM_ARCHITECTURE.md` for real-world baselines. Values are clearly labeled as research-derived vs. game-balance.
5. **No skimmer/gas-import scope creep**: This task only validates Luna's own ISRU loop. Skimmer deployment and gas-import logic remain out of scope.
6. **Spec coverage**: New or updated RSpec tests pass with the production logic.

## Questions for review

- What is the correct `unit_type` string for deployed TEU/PVE base_units? (Need to verify against `base_units` table schema)
- Does `capability_service.can_produce_locally?('processed_regolith')` work, or does it need a new entry?
- Should production be per-cycle or per-day in the simulation tick? (Current I-beam logic appears per-tick with no cycle timer — needs clarification)
- How does power availability gate production? (TEU=50kW, PVE=120kW each — does the simulation track available power?)
- What is the correct resource ID for He3 in the game constants? (`'he3'` vs `'helium_3'` vs `'He3'`)

## Deliverables

- Updated `calculate_blueprint_production()` method with TEU + PVE logic
- GameConstants additions for all production ratios (documented with source)
- Simulation output showing non-zero, chain-valid ISRU production over 50 days
- RSpec tests validating the production chain
- Notes on any codebase or data assumptions that need follow-up tasks
