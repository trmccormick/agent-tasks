## STATUS SYNTHESIS REPORT

**Task**: 2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-EVALUATE.md
**Status**: backlog → active
**Date**: 2026-09-08

### What I'm About to Do
Add a new public class method `.evaluate_strategy(material:, location:, context:)` to the EXISTING `Market::NpcPriceCalculator` that returns a structured strategy evaluation (EAP vs CapEx amortization vs extraction floor) for AI Manager Phase 3 acquisition decisions, and refactor `cost_based_bid` to branch between Earth-import (EAP) and extraction-floor pricing based on location. Success is verified by a passing RSpec suite (existing + new tests) run via the Docker wrapper, with zero regressions and zero fee-mechanism coupling.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `app/services/market/npc_price_calculator.rb` | Existing class — add `.evaluate_strategy` + refactor `cost_based_bid` | read ✅ |
| `spec/services/market/npc_price_calculator_spec.rb` | Existing spec — add tests for new method + refactored bid | read ✅ |
| `app/services/tier1_price_modeler.rb` | EAP calculation (Earth cost + transport) | read ✅ |
| `app/services/economic_config.rb` | `npc_buy_discount`, `local_production_cost`, `usd_to_gcc_peg` | read ✅ |
| `spec/factories/celestial_bodies/celestial_bodies.rb` | `:luna` trait; build Mars/Venus test bodies | read ✅ |
| `summaries/2026-09-08-RESEARCH-FEE-MECHANISM-HISTORY.md` | Confirms neither fee mechanism is live | read ✅ |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat — ONE result)
- ✅ Step 0: YAML status updated from backlog → active (committed 9992395)
- ✅ Read README.md EXECUTOR section (workflow rules in memory)
- ✅ Read project guide (galaxy_game agent guide)
- ✅ Read this task file (full)
- ✅ Understand architecture gotchas above

### Expected Outcomes
A new `.evaluate_strategy(material:, location:, context:)` class method on the EXISTING `Market::NpcPriceCalculator` that returns an OpenStruct with `strategy_type`, `reference_cost`, `breakdown`, `feasible?`, and `notes`. `cost_based_bid` branches: EAP for Earth/Luna, extraction floor for deep-space bodies. No new service classes. Existing method signatures unchanged.

### Design Decisions (from code reading)
1. **Location resolution**: `evaluate_strategy` accepts `location` as either a `Settlement` (has `.location.celestial_body`) or a `CelestialBody`/String. Resolve to a celestial body name, then classify.
2. **Location classification**:
   - EAP-viable: `earth`, `luna` (and nil/unknown → default to EAP for backward-compat, matching existing `calculate_earth_import_cost` fallback to 'luna').
   - Deep space: `mars`, `venus`, `mercury`, `phobos`, `deimos`, `europa`, `ganymede`, `titan`, `ceres`, and any other body not in the EAP set.
   - `deep_space_location?(celestial_body)` returns true for deep-space bodies.
3. **Strategy selection in `#evaluate_strategy`**:
   - EAP location → `:eap`, reference_cost = `calculate_earth_import_cost`.
   - Deep space + `can_produce_locally?` → `:extraction_floor`, reference_cost = `calculate_local_production_cost`.
   - Deep space + no local production → `:capex_amortization`, reference_cost = CapEx estimate (equipment import ÷ lifespan). Use a conservative estimate derived from EAP × amortization factor when no explicit CapEx data exists; mark `feasible?` accordingly.
   - Return all three strategies evaluated in `breakdown` even when infeasible (per Step 1.4).
4. **`cost_based_bid` refactor**: keep existing Earth/Luna behavior identical (import_cost path). For deep-space, use `calculate_extraction_floor` (local production cost, falling back to CapEx estimate) as the base cost. Preserve discount + inventory adjustments.
5. **Fee decoupling**: NO references to `SettlementFees`, `TransactionFee`, or `TransitFeeService` in the calculator. Fees remain caller-applied.

### Critical Gotchas I Will Avoid
- ❌ Creating a new `AIManager::NpcPriceCalculator` or any duplicate service — instead ✅ Work on existing `Market::NpcPriceCalculator`
- ❌ Coupling fee logic into the calculator — instead ✅ Keep fees completely decoupled
- ❌ Hardcoding Earth import floors globally — instead ✅ Support location-aware strategy branching
- ❌ Changing existing method signatures (`calculate_ask`, `calculate_bid`) — instead ✅ Add new public method only + internal refactor of `cost_based_bid`

### Verification Plan
- Run existing spec file via Docker wrapper (must stay green).
- Add new describe blocks for `.evaluate_strategy` (EAP / extraction floor / capex / infeasible) and refactored `cost_based_bid` (Earth/Luna no-regression + deep-space branching).
- Grep to confirm no fee-mechanism references in the calculator.

---

**SYNTHESIS COMPLETE.** Ready to proceed with implementation.
