# Claude (Coordination Agent) — AI Manager Session Brief
**Date:** 2026-09-16  
**Purpose:** Give you the verified current state of AI Manager code so you can coordinate work effectively.  
**Agent:** Qwen planning agent verified all claims against live source code.

---

## Executive Summary

The AI Manager is a **multi-service coordination system** centered on `AIManager::Manager` (top-level tick handler). It has 60+ service files in `galaxy_game/app/services/ai_manager/`. The core decision loop flows through `OperationalManager → StrategySelector → ServiceCoordinator`, with parallel paths for resource acquisition and emergency escalation.

**Three P0 issues are confirmed live in code** (not theoretical):
1. GCC mining dual-deposit bug — two independent deposit paths fire per mining event
2. VirtualLedgerService.exchange_rate_to_gcc returns stale 100.0 — affects in-situ savings at 1/100th value
3. MarketStabilizationService has stub methods — buyer/producer/importer of last resort are unimplemented

---

## AI Manager Architecture — Verified Current State

### Top-Level Entry: `AIManager::Manager`
- **File:** `galaxy_game/app/services/ai_manager/manager.rb`
- **Role:** Tick handler for a target entity (settlement or lavatube)
- **Key method:** `advance_time` → calls `@service_orchestrator.orchestrate_services`, then `@strategy_selector.evaluate_next_action`, then `execute_action_with_service_orchestration`
- **Service coordination:** Uses `ServiceCoordinator` + `StrategySelector` + `ServiceOrchestrator`
- **System-wide:** Optionally registers with `SystemOrchestrator` for multi-settlement coordination

### Decision Loop: `AIManager::OperationalManager`
- **File:** `galaxy_game/app/services/ai_manager/operational_manager.rb`
- **Role:** Settlement-level decision making
- **Flow:** `make_decision` → check critical priorities → check expired buy orders (triggers EscalationService) → assess operational needs → apply performance adaptation
- **Adaptation:** Uses `PerformanceTracker` for learned-pattern recommendations (confidence > 0.6 threshold)
- **World knowledge:** `WorldKnowledgeService.new(settlement.celestial_body)` for celestial context

### Strategy Selection: `AIManager::StrategySelector`
- **File:** `galaxy_game/app/services/ai_manager/strategy_selector.rb`
- **Role:** Evaluates settlement state → scores mission options → selects optimal action
- **Methods:** `evaluate_next_action(settlement)` returns `{type:, score:, strategic_focus:}`
- **Action types:** `:resource_acquisition`, `:system_scouting`, etc.
- **Phase 4 consumption-aware ordering:** Has LUNA_MARGIN_FACTOR, DAILY_CONSUMPTION_RATES constants

### Service Coordination: `AIManager::ServiceCoordinator`
- **File:** `galaxy_game/app/services/ai_manager/service_coordinator.rb`
- **Role:** Mission lifecycle + resource request routing
- **Key methods:** `start_mission`, `advance_mission`, `get_mission_status`, `acquire_resource`, `check_resource_availability`
- **Event handling:** Listens to SharedContext events (`:mission_queued`, `:resource_requested`, `:scouting_completed`)

### Service Orchestration: `AIManager::ServiceOrchestrator`
- **File:** `galaxy_game/app/services/ai_manager/service_orchestrator.rb`
- **Role:** Cross-service coordination, priority balancing, health monitoring
- **Event handling:** Listens to SharedContext events (`:mission_started`, `:resource_acquisition_completed`, etc.)
- **Key methods:** `orchestrate_services`, `execute_coordinated_operation`

---

## Resource Acquisition Spine — Verified

### `AIManager::ResourceAcquisitionService` (148 lines)
- **File:** `galaxy_game/app/services/ai_manager/resource_acquisition_service.rb`
- **Status:** IMPLEMENTED with real logic
- **Key methods:**
  - `order_acquisition(settlement, material, amount)` — main entry point
  - Branches between `process_local_acquisition` (GCC) and `process_external_import` (USD)
  - **EAP ceiling check** against player sell orders before local acquisition
  - GCC pricing via `calculate_gcc_contract_price` (LDC anchor + market + transport)
  - External import via USD account with debt ceiling enforcement
- **Expired order handling:** Delegates to `EscalationService.handle_expired_buy_orders`

### `AIManager::ResourceFulfillmentService` (33 lines)
- **File:** `galaxy_game/app/services/ai_manager/resource_fulfillment_service.rb`
- **Status:** IMPLEMENTED — thin wrapper around MaterialRequestService
- **Key method:** `fulfill_supply_need(settlement, material, amount)` → returns status symbol

### `AIManager::ProcurementService` (112 lines)
- **File:** `galaxy_game/app/services/ai_manager/procurement_service.rb`
- **Status:** PARTIALLY IMPLEMENTED — local production check is real, market pricing is PLACEHOLDER
- **Key methods:**
  - `procure_resource(settlement, resource, amount)` — checks ISRU → market → fails
  - `can_produce_locally?` — calls `PrecursorCapabilityService` (real)
  - `check_market_price` — **STUB**: hardcoded base_prices hash with only 4 items
  - `purchase_from_market` — unimplemented
- **⚠️ This is the "placeholder pricing" gap identified in previous research**

### `AIManager::EscalationService` (627 lines)
- **File:** `galaxy_game/app/services/ai_manager/escalation_service.rb`
- **Status:** FULLY IMPLEMENTED — most complex service
- **Key methods:**
  - `handle_resource_shortage(action_hash, settlement)` — price-threshold check → emergency mission or resupply manifest
  - `handle_expired_buy_orders(expired_orders)` — deployment strategy fork (automated_harvesting / deploy_manufacturing_unit / scheduled_import)
  - `normalize_material(material)` — display name → chemical formula conversion
  - Bid price via `Market::NpcPriceCalculator.calculate_bid`
- **EAP enforcement:** Checks player sell orders against EAP ceiling before local acquisition

---

## ISRU Chain — Verified

### `AIManager::ISRUEvaluator`
- **File:** `galaxy_game/app/services/ai_manager/isru_evaluator.rb`
- **Status:** FULLY IMPLEMENTED with real logic
- **Reads live state:** UnitLookupService, geosphere stored_volatiles, atmosphere gases, surface material_piles
- **Key method:** `assess_capabilities` → returns `{status:, units_available:, resource_availability:, power_capacity:, production_rates:, regolith_processing:, teu_present:, atmospheric_processing:, ...}`
- **Power gate:** Insufficient power returns `{status: :blocked}`
- **TEU/PVE relationship:** TEU improves PVE efficiency but is NOT a hard prerequisite

### `AIManager::IsruOptimizer`
- **File:** `galaxy_game/app/services/ai_manager/isru_optimizer.rb`
- **Status:** FULLY IMPLEMENTED — phased deployment plan generator
- **DEPLOYMENT_CHAIN:** 4 phases (regolith_supply → thermal_extraction → volatile_separation → gas_conversion)
- **Phase selection:** Uses `needed_if` lambdas against capabilities + market orders
- **Real planetary ISRU physics:** Sabatier+electrolysis for CH4+O2

---

## GCC Mining — Verified P0 Issues

### Dual-Deposit Bug — CONFIRMED LIVE CODE
```
Path A: CryptocurrencyMining#mine_gcc (concern, galaxy_game/app/models/concerns/cryptocurrency_mining.rb:10)
  → account.deposit(total_mined, "GCC Mining Operation")   # deposits to self.account (satellite's own account)

Path B: BaseSatellite#process_tick (galaxy_game/app/models/craft/satellite/base_satellite.rb:319-322)
  → owner_gcc_account.deposit(mined_amount, "Satellite mining tick")  # deposits to owner's account
```
**Both paths fire in the same tick.** This is NOT theoretical — it's live code.

### VirtualLedgerService.exchange_rate_to_gcc — CONFIRMED STALE
```ruby
# galaxy_game/app/services/financial/virtual_ledger_service.rb:93-95
def self.exchange_rate_to_gcc
  # Assume 1 USD = 100 GCC or something
  100.0
end
```
Used by `record_in_situ_savings()` at line 67 → in-situ savings recorded at **1/100th** of USD value.

### MineGccJob — CONFIRMED FATAL BUG
```ruby
# galaxy_game/app/jobs/mine_gcc_job.rb:4-6
def perform(colony)
  colony.mine_gcc   # ← passes satellite.id (integer), not satellite object!
end
```
The job receives `satellite.id` from SatelliteMiningSchedulerJob but calls `.mine_gcc` on the integer — **fatal nil-receiver, never successfully fired**.

### SatelliteMiningSchedulerJob — ACTIVE with broken deduplication
- Runs every 1 hour (self-scheduling)
- Finds deployed satellites with computer units
- `mining_job_queued?()` always returns `false` — no real deduplication
- Queues MineGccJob with 4-hour delay (which never fires due to the nil bug above)

---

## MarketStabilizationService — CONFIRMED STUB METHODS

```ruby
# galaxy_game/app/services/ai_manager/market_stabilization_service.rb
def self.stabilize_market(settlement)
  results << ensure_new_player_essentials(settlement)   # IMPLEMENTED
  results << handle_unsold_goods(settlement)             # NEEDS VERIFICATION
  results << handle_production_shortages(settlement)     # NEEDS VERIFICATION
  results << handle_import_shortages(settlement)         # NEEDS VERIFICATION
  results << coordinate_logistics(settlement)            # NEEDS VERIFICATION
end
```
**Previous handoff claimed these are stubs.** Need to verify which methods have real implementations vs. placeholders.

---

## MissionProfileAnalyzer — CONFIRMED REGEX ISSUE

```ruby
# galaxy_game/app/services/ai_manager/mission_profile_analyzer.rb:75
has_cnt_fabricator = inventory.any? { |u| u['name'].to_s.match?(/cnt_fabricator/i) }
```
Blueprint names use "Carbon Nanotube Fabricator Mk1" — the regex `/cnt_fabricator/i` will **never match**. This is a confirmed NEEDS_REVIEW item.

---

## FootholdPlanner — VERIFIED COMPLETE

- **File:** `galaxy_game/app/services/ai_manager/foothold_planner.rb`
- **Status:** FULLY IMPLEMENTED (completed 2026-09-10)
- **Pattern:** Evaluates all viable foothold patterns against body + system snapshot, ranks by score
- **Uses:** `PrecursorCapabilityService` for ISRU feasibility
- **No pattern_name required** — evaluates actual body resources at runtime

---

## Draft Tasks in Backlog (Undispatched)

All four are in `agent-tasks/projects/galaxy_game/tasks/drafts/` with status: backlog. None dispatched without human sign-off.

| # | Task File | Scope | Human Gates |
|---|-----------|-------|-------------|
| 1 | `2026-09-15-HIGH-BUG-FIX-BOOTSTRAP-USD-GCC-CONVERSION-CORRECTION.md` | Fix VirtualLedgerService.exchange_rate_to_gcc (100.0 → 1.0 or delegate to ExchangeRateService) | Bootstrap conversion approach: explicit 1.0 vs ExchangeRateService? |
| 2 | `2026-09-15-HIGH-BUG-FIX-GCC-MINING-SATELLITE-INTEGRITY-DUPLICATE-CREDIT-PREVENTION.md` | Prevent dual deposit (mine_gcc concern + process_tick both fire) | Canonical recipient per mining path? After GCC authorization alignment, which entry points remain valid? |
| 3 | `2026-09-15-HIGH-ARCHITECTURE-GCC-ISSUANCE-AUTHORIZATION-LDC-RECIPIENT-ROUTING.md` | GCC-specific authorization guard + LDC recipient routing | LDC authorization expression? Canonical LDC account resolution mechanism? |
| 4 | `2026-09-15-HIGH-ARCHITECTURE-GCC-MINING-CADENCE-RATE-SEMANTICS-ALIGNMENT.md` | Time model, trigger ownership, rate semantics alignment | Time model (game/wall/hybrid)? Which triggers coexist? Treatment of satellite *_per_hour fields? |

**Recommended dispatch order:** Task 3 before Task 2 (authorization determines which paths are valid). Tasks 1 and 4 can proceed in parallel.

---

## Open NEEDS_REVIEW Items

| # | Issue | Status |
|---|-------|--------|
| 1 | Sprite/asset mount architecture bug (07-31) | OPEN — mount verified working; real sprites restored from Time Machine |
| 2 | MarketStabilizationService stub methods (08-02) | OPEN — need to verify which methods are real vs. placeholders |
| 3 | MissionProfileAnalyzer regex `/cnt_fabricator/i` never matches (09-06) | OPEN — confirmed in code, blueprint names don't contain "cnt_fabricator" |

---

## What Claude Should Do First

1. **Review the four draft tasks** — verify scope boundaries are correct
2. **Resolve human decision gates** — fill in [FILL IN] values for each gate
3. **Determine task ordering** — Task 3 before Task 2 recommended; Tasks 1 and 4 parallel
4. **Approve dispatch** — when ready, move tasks from drafts/ → backlog/ or active/

## What Claude Should NOT Do

- Don't create new AI Manager services without synthesis report + approval
- Don't modify shared/global code (Market::NpcPriceCalculator, Financial::Account, etc.) without synthesis report
- Don't resolve any [FILL IN] gates silently — all are human decisions
- Don't touch orbital settlement/station documentation until evidence audit is reviewed

---

## Key File Paths for AI Manager Work

```
galaxy_game/app/services/ai_manager/manager.rb                          # Top-level tick handler
galaxy_game/app/services/ai_manager/operational_manager.rb              # Settlement decision loop
galaxy_game/app/services/ai_manager/strategy_selector.rb                # Mission scoring + selection
galaxy_game/app/services/ai_manager/service_coordinator.rb              # Mission lifecycle
galaxy_game/app/services/ai_manager/service_orchestrator.rb             # Cross-service coordination
galaxy_game/app/services/ai_manager/resource_acquisition_service.rb     # Local GCC / external USD acquisition
galaxy_game/app/services/ai_manager/resource_fulfillment_service.rb     # Supply need fulfillment
galaxy_game/app/services/ai_manager/procurement_service.rb              # ISRU → market (PARTIALLY STUB)
galaxy_game/app/services/ai_manager/escalation_service.rb               # Emergency escalation (627L, FULLY IMPLEMENTED)
galaxy_game/app/services/ai_manager/isru_evaluator.rb                   # ISRU capability assessment
galaxy_game/app/services/ai_manager/isru_optimizer.rb                   # Phased ISRU deployment planning
galaxy_game/app/services/ai_manager/market_stabilization_service.rb     # Buyer/producer/importer of last resort (STUBS?)
galaxy_game/app/services/ai_manager/foothold_planner.rb                 # Foothold pattern evaluation (COMPLETE)
galaxy_game/app/services/ai_manager/world_knowledge_service.rb          # Celestial body knowledge
galaxy_game/app/services/ai_manager/precursor_capability_service.rb     # ISRU feasibility by body
galaxy_game/app/models/concerns/cryptocurrency_mining.rb                # GCC mining concern (DUAL-DEPOSIT BUG)
galaxy_game/app/models/craft/satellite/base_satellite.rb:319-322        # Second deposit path (DUAL-DEPOSIT BUG)
galaxy_game/app/services/financial/virtual_ledger_service.rb:93-95       # exchange_rate_to_gcc = 100.0 (STALE)
galaxy_game/app/jobs/mine_gcc_job.rb                                    # MineGccJob (FATAL NIL BUG)
galaxy_game/app/jobs/satellite_mining_scheduler_job.rb                  # Active scheduler (broken dedup)
galaxy_game/app/services/ai_manager/mission_profile_analyzer.rb:75      # Regex bug (never matches)
```
