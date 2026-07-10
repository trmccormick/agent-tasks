# RESEARCH: Atmospheric Tracking for MVP Skimmer Operations — Audit Report

**Date**: 2026-07-09 (updated 2026-07-10)
**Task**: `2026-06-17-HIGH-FEATURE-PHASE5-ATMOSPHERIC-SIMULATION-VALIDATION.md`
**Researcher**: Research Agent (Qwen3.6-35b)

---

## Executive Summary

Atmospheric composition tracking **partially exists** for skimmer operations across multiple services. The `AtmosphereSimulationService` is intentionally a passive climate simulator (no skimmer integration needed — HLT skimmers extract negligible amounts at planetary scale). The `AtmosphericTransferService` handles gas extraction/delivery correctly. The `AIManager::AtmosphericHarvesterService` returns mock data.

**Critical correction**: Skimmers are **AstroLift-owned commercial assets**, not LDC infrastructure. They dock at L1 Depot (primary), Luna surface (fallback when L1 full), or LEO Station (last resort refueling). Processing during docking windows is adequate — the real bottleneck is cryo tank capacity, not processing throughput.

**Corporate structure**:
- **LDC** — Lunar infrastructure/ISRU operations; provides bulk materials for L1 construction
- **AstroLift** — Orbital logistics; owns skimmers/cyclers; joint L1 owner
- **Zenith Orbital** — Station construction; assembles L1 Depot (paid contractor, no ownership)
- **Vector Hauling** — Interplanetary cargo transport

---

## Audit Results by Component

### 1. AtmosphereSimulationService (`TerraSim::AtmosphereSimulationService`)
**Status**: ✅✅ Correctly designed — NO skimmer integration needed

| Feature | Exists? | Notes |
|---|---|---|
| Greenhouse gas tracking (CO2, CH4, N2O, H2O) | ✅ | `GREENHOUSE_GASES` constant, mass-based calculations |
| Greenhouse effect calculation | ✅ | Uses gas masses to adjust surface temp |
| Atmospheric loss (solar wind) | ✅ | Fixed 0.01% loss factor per cycle |
| Temperature updates | ✅ | Surface/polar/tropic temps clamped to valid ranges |
| **Skimmer extraction integration** | ✅ Not needed | Passive climate simulator — reads state modified by other services |
| **Gas composition shift tracking** | ✅ Not needed | HLT skimmers extract negligible amounts at planetary scale |
| **Venting mechanics** | ✅ Not needed | Only random/occasional large-scale events need simulation-level testing |

**Key Finding**: This service is a passive climate simulator. It reads current gas masses for greenhouse/temperature calculations but never modifies atmosphere state. Skimmer extraction modifies atmosphere via the model directly (`AtmosphericTransferService` calls `atmosphere.remove_gas`/`add_gas`). The simulation service just reads whatever state exists when it runs — no integration needed.

**Why no skimmer integration is correct**: HLT skimmers extract tiny fractions of atmospheric mass. Composition shifts are imperceptible at the planetary scale. Cumulative effects from repeated skimmer ops are irrelevant to climate simulation. Only random/occasional large-scale atmospheric events need simulation-level testing.

---

### 2. AtmosphericTransferService (`TerraSim::AtmosphericTransferService`)
**Status**: ✅✅ Core skimmer extraction logic EXISTS and is well-structured

| Feature | Exists? | Notes |
|---|---|---|
| Raw transfer (proportional) | ✅ | Extracts all gases proportionally by percentage |
| Selective transfer | ✅ | Extracts specific gases by name |
| Processed transfer (MOXIE-style) | ✅ | CO2 → O2 + CO processing |
| Titan extraction limit (5%) | ✅ | `extraction_limit = 0.05` for Titan |
| Venus unlimited extraction | ✅ | `Float::INFINITY` — cycler capacity only constraint |
| Transport efficiency (98%) | ✅ | Applied to all delivered gases |
| Gas-by-gas result tracking | ✅ | `results[:gases_extracted]`, `results[:gases_delivered]` |
| **Venting specific gases back** | ⚠️ | Partial — raw transfer removes from source, adds to target. No "vent to space" mode |
| **Cumulative composition over cycles** | ✅ (implicit) | Each call modifies `atmosphere.gases` directly via `remove_gas`/`add_gas` |

**Key Finding**: This is the PRIMARY service for skimmer atmospheric operations. It correctly implements:
- Mixed atmospheric gas extraction (NOT pre-sorted — aligns with skimmer intent)
- Source-specific limits (Titan 5%, Venus unlimited)
- Gas-by-gas tracking in results hash

**Gap**: No "vent to space" mode — gases are always extracted from source and delivered to target. Skimmers that vent excess N2 back into Titan's atmosphere would need a separate mechanism.

---

### 3. Atmosphere Model (`CelestialBodies::Spheres::Atmosphere`)
**Status**: ✅✅ Core gas manipulation methods exist

| Method | Purpose |
|---|---|
| `add_gas(name, mass)` | Add gas to atmosphere |
| `remove_gas(name, mass)` | Remove gas from atmosphere |
| `recalculate_mass!` | Update total atmospheric mass |
| `update_pressure_from_mass!` | Recalculate pressure from gas masses |
| `gas_percentage(name)` | Get percentage of specific gas |
| `total_atmospheric_mass` | Total mass of all gases |
| `gases` (association) | Collection of Gas objects with name/mass/percentage |

**Key Finding**: The model correctly maintains stateful gas composition. When `remove_gas` is called, the gas mass decreases and percentages are recalculated on next access. This means **cumulative extraction effects DO accumulate** across multiple calls.

---

### 4. AIManager::AtmosphericHarvesterService
**Status**: ❌ Returns MOCK data — NOT production-ready

```ruby
def collect_nitrogen(titan)
  titan[:nitrogen_collected] = 200  # Hardcoded mock value
end

def collect_methane(titan)
  titan[:methane_collected] = 150   # Hardcoded mock value
end
```

**Key Finding**: This service is a PLACEHOLDER. It does NOT use `AtmosphericTransferService` or modify actual atmosphere state. Must be replaced or wired to the real transfer service.

---

### 5. Resource::AtmosphericHarvester
**Status**: ⚠️ Partially functional

- `harvest_gases(gas_name, amount)` — calls `atmosphere.remove_gas` correctly
- `complete_contracted_harvesting_job(job)` — has Titan-specific logic for Methane/Nitrogen/Ammonia
- Uses OpenStruct harvester mock in contract path
- **Gap**: Only harvests ONE gas at a time, not mixed atmospheric extraction

---

### 6. SkimmerCyclerHandshakeService
**Status**: ⚠️ Processes cargo but conflicts with skimmer intent

```ruby
def process_cargo(skimmer, cycler)
  required_energy = skimmer.raw_cargo.values.sum * 2
  return false if cycler.energy_reserve < required_energy
  cycler.energy_reserve -= required_energy
  skimmer.processed_cargo = skimmer.raw_cargo.transform_values { |v| v * 0.9 }
  skimmer.raw_cargo = {}
  skimmer.available = true
  true
end
```

**Key Finding**: This service processes raw cargo to processed cargo at 90% efficiency during cycler transit. **CONFLICT**: Skimmer intent says "cargo is NOT pre-sorted" and onboard processing only refills skimmer's own fuel tanks. This service processes ALL cargo, which contradicts the MVP design constraint.

---

## Skimmer Docking Architecture (Corrected)

### Docking Priority Order
| Priority | Destination | Role |
|---|---|---|
| **1** | **L1 Depot** (Luna orbit) | Primary destination — continuous solar power, real-time processing |
| **2** | **Luna surface** | Fallback when L1 Depot at capacity — storage + limited processing |
| **3** | **LEO Station** | Last resort refueling/offload for Earth-bound craft |

### Why L1 Depot is Primary
- **Continuous solar power** at L1 (no eclipses) vs Luna's day/night cycle (~14 Earth days of darkness)
- **Real-time processing** as skimmers arrive — no batch delays
- **Same equipment = 2x throughput** vs Luna surface (which can only process ~50% with RTG backup)
- **Processing is adequate during docking windows** — the real bottleneck is cryo tank capacity, not processing speed

### Ownership & Economic Model
| Entity | Owns | Role |
|---|---|---|
| **AstroLift** | HLT skimmers, cyclers, Earth tankers | Independent commercial operator; sells processed/raw gases via AI Manager |
| **LDC** | L1 Depot (joint), Luna surface infrastructure | Base operator / gas processor; buys raw gas, sells fuel to skimmers |
| **Zenith Orbital** | Construction services (paid contractor) | Assembles L1 Depot using LDC materials + AstroLift transport |

### Skimmer Docking = Business Opportunity for AstroLift
```
Skimmer docks at L1 Depot (or Luna surface if L1 full)
    ↓
While docked (until next launch window):
  1. Maintenance on craft
  2. Refueling (LOX/CH4 from base)
  3. Sell processed gases → market (via AstroLift AI Manager)
  4. Sell raw gas → LDC or market (via AstroLift AI Manager)
  5. LDC can buy raw gas from AstroLift, process it, sell on market
    ↓
Launch window arrives → skimmer departs for next harvest cycle
```

### Key Economic Constraint
**Cryo tank capacity at L1 Depot determines how much gas can be received per docking window.** Undersized tanks = wasted skimmer capacity and potential gas venting. This is an AstroLift revenue problem, not just a simulation problem.

---

## MVP Scenario Validation (Corrected)

### Scenario 1 — Titan CH4 Harvesting with Venting
**Current State**: Partially supported via `AtmosphericTransferService`

- ✅ `AtmosphericTransferService` can extract raw mixed gas from Titan (raw mode)
- ✅ Titan has 5% extraction limit to preserve atmosphere
- ✅ Gas-by-gas tracking in results
- ❌ No venting mechanism — excess N2 cannot be returned to Titan's atmosphere
- ❌ `AIManager::AtmosphericHarvesterService` returns mock data, not real extraction

**What would work**: Using `AtmosphericTransferService.raw_transfer({capacity: cycler_capacity})` extracts proportional mix of all Titan gases (N2 ~95%, CH4 ~5%). Results hash tracks what was extracted vs delivered.

**What's missing**: A "vent" mode that removes gas from skimmer cargo and returns it to source atmosphere.

### Scenario 2 — Venus CO2/N2 Delivery
**Current State**: Partially supported via `AtmosphericTransferService`

- ✅ `AtmosphericTransferService` can extract raw mixed gas from Venus (raw or selective mode)
- ✅ Venus has unlimited extraction (Float::INFINITY) — cycler capacity only constraint
- ✅ Transport efficiency (98%) applied correctly
- ❌ No mechanism for LOX self-fueling deduction from extracted CO2
- ❌ No mechanism for delivering mixed N2+CO2 payload to Luna

**What would work**: Using `AtmosphericTransferService.raw_transfer({capacity: cycler_capacity})` extracts proportional mix of all Venusian gases (CO2 ~96%, N2 ~3.5%). Results hash tracks what was extracted vs delivered.

**What's missing**: 
1. LOX self-fueling deduction (Sabatier reactor consumes some CO2 for skimmer fuel)
2. Mixed payload delivery tracking to Luna tank farm

### Scenario 3 — Cumulative Effects Over Extended Simulation
**Current State**: ✅ Implicitly works via model state

- ✅ `Atmosphere.gases` are persistent records — each `remove_gas` call permanently reduces mass
- ✅ Percentages recalculate on next access after mass changes
- ⚠️ No simulation-level tracking of "before/after" composition snapshots
- ⚠️ No audit trail of extraction events

**What works**: Multiple calls to `AtmosphericTransferService` will cumulatively reduce Titan's CH4 and increase Luna's delivered CH4. The atmosphere model correctly maintains stateful composition.

**What's missing**: Composition change logging for debugging/validation purposes.

---

## Gap Analysis: MVP vs Phase 9+

### MVP Gaps (Must Fix Before Skimmer Fuel Delivery Works)
| # | Gap | Severity | Fix Required |
|---|---|---|---|
| 1 | `AIManager::AtmosphericHarvesterService` returns mock data | 🔴 CRITICAL | Wire to `AtmosphericTransferService` or replace |
| 2 | No venting mechanism for excess gases | 🟡 HIGH | Add `vent_gas(gas_name, mass)` mode to transfer service |
| 3 | No LOX self-fueling deduction from Venus skimmer cargo | 🟡 HIGH | Deduct Sabatier reactor CO2 consumption before delivery |
| 4 | `SkimmerCyclerHandshakeService` processes ALL cargo (conflicts with intent) | 🟡 HIGH | Limit to fuel-tank-only processing per skimmer intent |

### Phase 9+ Gaps (Terraforming — Separate Task)
| # | Gap | Severity | Notes |
|---|---|---|---|
| 5 | No CO2→O2 terraforming conversion efficiency | 🟢 LOW | Split to `2026-07-05-LOW-RESEARCH-TERRAFORMING-ATMOSPHERIC-GAP-ANALYSIS.md` |
| 6 | No multi-world atmospheric interaction via Cycler routes | 🟢 LOW | Requires bulk resource movement first |
| 7 | Data-driven extraction limits vs hardcoded world names | 🟢 LOW | Refactor case statement to config-driven |

---

## Recommended Test Scenarios for RSpec

### Priority 1 — MVP Fuel Delivery Validation (Must Have)

```ruby
# spec/services/terra_sim/atmospheric_transfer_service_spec.rb

describe 'Titan CH4 extraction (MVP fuel delivery)' do
  it 'extracts CH4 proportionally from Titan atmosphere'
  it 'applies 5% extraction limit to preserve Titan atmosphere'
  it 'tracks extracted vs delivered gas in results hash'
  it 'applies 98% transport efficiency'
end

describe 'Venus CO2/N2 extraction (MVP fuel delivery)' do
  it 'extracts proportional mix of all Venusian gases in raw mode'
  it 'has no percentage limit for Venus (Float::INFINITY)'
  it 'tracks which gases were extracted vs delivered'
end

describe 'Cumulative extraction effects' do
  it 'accumulates composition changes across multiple transfer calls'
  it 'recalculates gas percentages after each extraction'
end
```

### Priority 2 — Venting Mechanics (High)

```ruby
describe 'Gas venting to space' do
  it 'removes gas from skimmer cargo without delivering to target'
  it 'can vent specific gases back to source atmosphere'
end
```

### Priority 3 — LOX Self-Fueling Deduction (High)

```ruby
describe 'Venus skimmer LOX self-fueling' do
  it 'deducts Sabatier reactor CO2 consumption from extracted cargo'
  it 'delivers remaining mixed N2+CO2 to Luna tank farm'
end
```

---

## Architecture Recommendations

1. **Use `AtmosphericTransferService` as the primary skimmer extraction service** — it correctly handles mixed atmospheric gas, source-specific limits, and gas-by-gas tracking.

2. **Replace/mock `AIManager::AtmosphericHarvesterService`** — it returns hardcoded mock values and must not be used for MVP validation.

3. **Add venting mode to `AtmosphericTransferService`** — a new transfer mode that removes gas from atmosphere without delivering to target (vents to space).

4. **Fix `SkimmerCyclerHandshakeService.process_cargo`** — limit processing to fuel-tank-only per skimmer intent, not full cargo processing.

5. **Add composition snapshot logging** — before/after snapshots for debugging and validation of cumulative effects.

---

## Files Referenced in This Audit

| File | Purpose |
|---|---|
| `app/services/terra_sim/atmosphere_simulation_service.rb` | Climate simulator (NO skimmer integration) |
| `app/services/terra_sim/atmospheric_transfer_service.rb` | ✅ Primary skimmer extraction service |
| `app/services/ai_manager/atmospheric_harvester_service.rb` | ❌ Mock data placeholder |
| `app/services/resource/atmospheric_harvester.rb` | ⚠️ Partial — single-gas harvesting |
| `app/services/ai_manager/skimmer_cycler_handshake_service.rb` | ⚠️ Conflicts with skimmer intent |
| `app/models/celestial_bodies/spheres/atmosphere.rb` | ✅ Core gas state management |
| `spec/services/terra_sim/atmosphere_simulation_service_spec.rb` | Existing spec (climate only, no skimmer tests) |

---

**RESEARCH COMPLETE.** Ready for next agent to implement MVP validation tests based on findings above.
