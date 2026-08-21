# TASK_TEMPLATE: WormholeExpansionService — AWS Construction Option Evaluation

**Phase**: phase15+
**Priority**: HIGH
**Type**: REFACTOR
**Status**: BACKLOG
**Created**: 2026-03-29
**Last Updated**: 2026-06-20
**Estimate**: 8 hours (research + implementation + spec rewrite)

---

## 🎯 Objective

Refactor `WormholeExpansionService#find_expansion_opportunities` to correctly evaluate Artificial Wormhole Station (AWS) construction options in newly-discovered systems. Currently crashes with settlement-based queries; should instead analyze celestial body assets and delegate financial/risk evaluation to `StationCostBenefitAnalyzer`.

**Context**: Post-Snap exit shift, newly-discovered system may NOT have its own natural wormhole, but EM exists at the exit point. AWS construction connects the system back to Sol.

---

## 📋 Problem Statement

| Issue | Current Behavior | Expected Behavior |
|-------|------------------|-------------------|
| **Wrong Domain** | Queries settlements via colony traversal | Evaluates system celestial bodies and assets |
| **Settlement Errors** | `NoMethodError`, `PG::UndefinedColumn` crashes | Correct asset detection (asteroids, moons, tugs) |
| **No Integration** | Doesn't call `StationCostBenefitAnalyzer` | Delegates financial decisions to analyzer |
| **Decision Logic** | Missing construction options | 5-option decision tree implemented |
| **EM Assumption** | Assumes local wormhole for harvesting | Accounts for EM at exit point, diffuse sources |

---

## 🔍 Technical Details

### Core Decision Tree

```
System accessed via wormhole exit → AWS needed for re-connection
    ↓
EVALUATE CONSTRUCTION OPTIONS:
    ↓
[Option A] Asteroid Conversion (Preferred — lowest cost)
  • Phobos-sized asteroid exists? (mass 1e15–1e18 kg)
  • Tug with sufficient thrust in system? (operational_data thrust_kn)
  • Hollow asteroid for propellant during transit
  • Cycler delivers conversion materials
  • PRIORITY: 1 (lowest cost, highest success)
    ↓
[Option B] Luna-Type Moon Base Construction
  • Suitable moon exists? (mass >1e20 kg, stable orbit)
  • Resources to bootstrap manufacturing? (ISRU/panel production)
  • Manufacture locally, deliver to AWS site
  • PRIORITY: 2 (medium cost, stable)
    ↓
[Option C] Earth L1-Style Imported Construction
  • No asteroid, no suitable moon
  • Wormhole to Sol still stable? (check origin system capacity)
  • 3D print/manufacture panels in Sol, ship through wormhole
  • PRIORITY: 3 (higher cost, reliable)
    ↓
[Option D] Harvest & Hold (No AWS Yet)
  • All construction options too expensive/risky
  • Keep wormhole open, harvest system EM/resources
  • Build capacity until A/B/C viable
  • PRIORITY: 4 (delay, gather resources)
    ↓
[Option E] Hammer Protocol (Close & Scout)
  • System ROI below threshold (defined in config)
  • Deploy WS MK1-H for EM extraction only
  • Let wormhole decay naturally, scout next system
  • PRIORITY: 5 (abandon if not viable)
    ↓
SELECT OPTIMAL: Pass options to StationCostBenefitAnalyzer#select_optimal_strategy
```

### Key Assumption: New System EM Availability
- ✅ **EM exists** at wormhole exit point (harvested by early equipment/scouts)
- ✅ **Diffuse EM** may exist elsewhere in system atmosphere/solar wind
- ❌ **May NOT have natural wormhole** at the exit location
- ✅ **AWS construction** enables re-connection even without local natural wormhole

### Critical Fix: Option C Logic
- **OLD**: Check `wormhole_import_capacity?(destination_system)` — WRONG, destination has no wormhole
- **NEW**: Check origin system (Sol) wormhole stability and capacity
- **Call**: `wormhole_import_capacity?(solar_system: sol, destination_system: discovered)`

---

## 📂 Files to Edit

### Primary (You Will Edit)
- [galaxy_game/app/services/wormhole_expansion_service.rb](galaxy_game/app/services/wormhole_expansion_service.rb)
  - Method: `#find_expansion_opportunities(solar_system)` — rewrite
  - Method: `#infrastructure_free_deployment_possible?` — DELETE
  - Methods: `#evaluate_construction_options`, `#suitable_asteroid?`, `#tug_available?`, `#suitable_moon?`, `#wormhole_import_capacity?` — ADD
- [galaxy_game/spec/services/wormhole_expansion_service_spec.rb](galaxy_game/spec/services/wormhole_expansion_service_spec.rb)
  - Rewrite test suite for new asset-based logic

### Reference (Read Only)
- [galaxy_game/app/services/ai_manager/station_cost_benefit_analyzer.rb](galaxy_game/app/services/ai_manager/station_cost_benefit_analyzer.rb)
  - Interface: `#select_optimal_strategy(options_array) → { type:, body:, ...}`
- [docs/wormhole_expansion/wh-expansion.md](docs/wormhole_expansion/wh-expansion.md)
  - Full wormhole expansion protocol, ROI thresholds, Hammer Protocol
- [galaxy_game/app/models/solar_system.rb](galaxy_game/app/models/solar_system.rb)
  - Associations: `has_many :celestial_bodies`, `has_many :wormholes`
- [galaxy_game/app/models/celestial_bodies/celestial_body.rb](galaxy_game/app/models/celestial_bodies/celestial_body.rb)
  - Attributes: `type`, `mass`, `stable_orbit?`, associations to planet/moon
- [galaxy_game/app/models/craft/base_craft.rb](galaxy_game/app/models/craft/base_craft.rb)
  - Attribute access: `operational_data['performance']['nominal_thrust_kn']`

---

## ✅ Acceptance Criteria

- [ ] **No settlement queries** in expansion logic (remove colony traversal entirely)
- [ ] **Asset detection** correctly identifies asteroids, moons, tugs by celestial body properties
- [ ] **Tug detection** reads `operational_data[:performance][:nominal_thrust_kn]` or equivalent
- [ ] **`StationCostBenefitAnalyzer` integrated** — passes construction options, receives optimal strategy
- [ ] **Option C check** verifies origin (Sol) wormhole capacity, NOT destination wormhole (destination may not have one)
- [ ] **Spec rewritten** with tests for all 5 construction options
- [ ] **Spec passes** with 0 failures, 0 regressions in related services
- [ ] **Code comments** explain EM-at-exit assumption for future maintainers

---

## 🚧 Implementation Checklist

### Phase 1: Research (READ ONLY)
- [ ] Read `wh-expansion.md` for ROI thresholds, Hammer Protocol rules
- [ ] Inspect `StationCostBenefitAnalyzer#select_optimal_strategy` interface
- [ ] Check `SolarSystem` associations (`has_many :celestial_bodies`)
- [ ] Check `CelestialBody` model for mass/type attributes
- [ ] Verify tug thrust query pattern in `BaseAircraft`

### Phase 2: Implementation
- [ ] Rewrite `find_expansion_opportunities` to evaluate celestial bodies (not settlements)
- [ ] Implement `evaluate_construction_options` with 5-option decision tree
- [ ] Implement asset detection helpers: `suitable_asteroid?`, `suitable_moon?`, `tug_available?`, `wormhole_import_capacity?`
- [ ] **FLAG**: `wormhole_import_capacity?` should check origin system capacity, NOT destination
- [ ] DELETE `infrastructure_free_deployment_possible?` entirely
- [ ] Integrate `StationCostBenefitAnalyzer#select_optimal_strategy` call
- [ ] Add comments explaining EM availability assumptions

### Phase 3: Testing
- [ ] Rewrite spec suite: test each option type (asteroid, moon, L1, harvest, hammer)
- [ ] Test empty options case (no viable construction)
- [ ] Test with multiple celestial bodies (returns multiple opportunities)
- [ ] Verify no regressions in related specs

### Phase 4: Commit
- [ ] Commit: `refactor: wormhole_expansion_service — AWS construction option evaluation`

---

## 🔗 Dependencies

- **Blocked By**: None
- **Blocks**: Portal network deployment logic (Phase 15+)
- **Related**: `2026-03-26-HIGH-FEATURE-SEISMIC-SURVEY-SCOUT-SHIPS.md`

---

## 📌 Stop Conditions

- `StationCostBenefitAnalyzer#select_optimal_strategy` interface significantly differs from task description
- `SolarSystem` or `CelestialBody` associations missing
- Tug thrust query pattern unclear from `BaseAircraft` model
- Architectural decision needed beyond what `wh-expansion.md` covers

---

## 📝 Completion Template

*Agent completes this section upon implementation*

**Status**: [NOT STARTED / IN PROGRESS / COMPLETED / BLOCKED]
**Completion Date**: [YYYY-MM-DD]
**Git Commit**: [commit hash or "not yet pushed"]
**Test Results**: [X passed, 0 failed] or [blocked: reason]
**Notes**: [Any blockers, assumptions verified, changes to approach]
