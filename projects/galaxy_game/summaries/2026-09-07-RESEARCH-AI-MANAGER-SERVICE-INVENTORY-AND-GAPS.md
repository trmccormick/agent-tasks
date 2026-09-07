# AI Manager Acquisition Surface Inventory & Gaps
Date: 2026-09-07
Author: Implementation Agent

## 1. Service Snapshot (paths + rough size)

| Service | Path | Lines | Spec? | Role summary |
|---------|------|-------|-------|--------------|
| EscalationService | `app/services/ai_manager/escalation_service.rb` | 627 | ✅ `escalation_service_spec.rb` + `escalation_integration_spec.rb` | The real spine. `handle_resource_shortage`, `handle_expired_buy_orders`, `normalize_material`, `can_harvest_locally?` (line 457), strategy routing (`automated_harvesting` / `deploy_manufacturing_unit` / `scheduled_import`) |
| ProcurementService | `app/services/ai_manager/procurement_service.rb` | 112 | ✅ `procurement_service_can_produce_locally_spec.rb` (partial) | ISRU → market → emergency chain. Contains hardcoded `base_prices` placeholder in `check_market_price`. |
| ResourceAcquisitionService | `app/services/ai_manager/resource_acquisition_service.rb` | 148 | ❌ none | Local-GCC vs external-USD fork. Delegates expired orders to `EscalationService.handle_expired_buy_orders`. No specs. |
| ResourceFulfillmentService | `app/services/ai_manager/resource_fulfillment_service.rb` | 33 | ❌ none | Thin wrapper delegating to `MaterialRequestService.request_materials`. No specs. |
| Resource::Acquisition | `app/services/resource/acquisition.rb` | ~148 (est.) | ❌ none | Second `can_harvest_locally?` — instance method, lunar-hardware-aware. Not under `ai_manager/`. |

## 2. Does vs. Delegates

### EscalationService
**Decides:**
- Whether a settlement can fund an emergency purchase (`settlement_can_fund_shortage?`)
- Whether a shortage is `emergency_required?` (time-to-critical vs time-to-next-resupply)
- Which escalation strategy to deploy: `automated_harvesting`, `deploy_manufacturing_unit`, or `scheduled_import`
- Material normalization (display name → chemical formula)
- HLTSkimmer supply manifest (`hlt_mission_manifest`)

**Delegates:**
- `EmergencyMissionService.create_emergency_mission` — actual mission creation
- `Market::NpcPriceCalculator.calculate_bid` — price lookup
- `Financial::Account` — funding checks
- `HarvesterCompletionJob` / manufacturing scheduling
- `ResupplyManifest` — **STUB** (not yet implemented)

### ProcurementService
**Decides:**
- Whether to produce locally (`can_produce_locally?`) via `PrecursorCapabilityService` + equipment check
- Whether the settlement can afford market price (`settlement_can_afford?`, includes corporate debt conservatism at >30% debt-to-assets)
- Which path: local production → market purchase → fail

**Delegates:**
- `PrecursorCapabilityService.can_produce_locally?` — location resource availability
- `EmergencyMissionService` — **not directly**, but the caller may trigger it on failure
- Market purchase — **STUB** (`purchase_from_market` is a no-op)

### ResourceAcquisitionService
**Decides:**
- Local-GCC vs external-USD fork via `is_local_resource?` (hardcoded material list)
- Whether to create GCC contracts (`ContractCreationService`) or USD import orders (`ContractCreationService.create_import_order`)
- EAP ceiling check against player sell orders (returns **false** — placeholder, not implemented)
- Financial gate: `settlement.can_afford?` for GCC; `settlement.financials.can_afford_fiat_import?` for USD

**Delegates:**
- `EscalationService.handle_expired_buy_orders` — expired order handling
- `Market::NpcPriceCalculator.calculate_ask` — GCC price lookup
- `Lookup::MaterialLookupService` — USD anchor price lookup
- `ContractCreationService` — contract/order creation

### ResourceFulfillmentService
**Decides:**
- Whether inventory already meets the need (trivial check)

**Delegates:**
- Everything to `MaterialRequestService.request_materials` — market-first procurement pipeline

## 3. Overlaps & Conflicts

### 3a. Two `can_harvest_locally?` methods — divergence, not duplication

| | EscalationService (class method) | Resource::Acquisition (instance method) |
|---|---|---|
| **Signature** | `self.can_harvest_locally?(settlement, material)` | `can_harvest_locally?(resource_name)` |
| **Scope** | Celestial body composition (atmosphere + hydrosphere + geosphere) | Hardcoded lunar resource list + harvester availability check |
| **O2 handling** | Checks `celestial_body.atmosphere.gases.any? { |g| g.name == 'O2' }` | Not in lunar_resources list (would return false unless harvester present) |
| **Equipment gate** | None — checks body composition only | Yes — `has_suitable_harvester?(resource_name)` |
| **Used by** | `determine_escalation_strategy` in EscalationService | `try_local_harvesting` in Resource::Acquisition |

**Classification:** Different concepts. EscalationService checks *whether the body has the resource*. Resource::Acquisition checks *whether the settlement has equipment to harvest it*. They serve different layers of the decision tree. **Not a duplication risk — they answer different questions.** However, neither checks ISRU capability (deployed processing units) for bodies without atmospheric resources. The 2026-08-27 `can_harvest_locally?` fix added ISRU gate for O2 in EscalationService but only for the CO2 case; the O2 gate was confirmed as fixed per status.md.

### 3b. ProcurementService vs ResourceAcquisitionService — overlapping acquisition paths

Both services can acquire resources for a settlement, but through different mechanisms:
- **ProcurementService**: ISRU check → market price (placeholder) → purchase (STUB). Used by `OperationalManager.handle_resource_procurement`.
- **ResourceAcquisitionService**: Local-GCC vs external-USD fork → GCC contract or USD import order. Used by `ResourcePlanner.determine_procurement_method` + `initiate_fulfillment`, and by rake tasks (`solar_system_mission_pipeline.rake`, `solar_system_mission_belt.rake`).

**Conflict:** ProcurementService's market path uses **placeholder pricing** (oxygen: 500, water: 300, food: 800, structural_carbon: 2000) and the purchase method is a no-op. ResourceAcquisitionService uses **real NPC price calculator** (`Market::NpcPriceCalculator.calculate_ask`) for GCC and **anchor prices** for USD. These two paths could produce wildly different costs for the same resource.

### 3c. Expired order handling — single delegation point

`ResourceAcquisitionService.check_expired_orders` is the only caller of `EscalationService.handle_expired_buy_orders`. This is a clean delegation, no overlap.

### 3d. ResourceFulfillmentService — thin wrapper, no conflict

Delegates entirely to `MaterialRequestService`. No independent logic to conflict with other services.

## 4. Canonical Path in Manager Loop

**Primary entry point: `OperationalManager`** (app/services/ai_manager/operational_manager.rb)

The manager loop calls:
1. `OperationalManager.handle_resource_procurement` → `ProcurementService.procure_resource` (ISRU → market → fail)
2. `OperationalManager.check_expired_orders` → `ResourceAcquisitionService.check_expired_orders` → `EscalationService.handle_expired_buy_orders`

**Secondary entry point: `ResourcePlanner`** (app/services/ai_manager/resource_planner.rb)

The planner calls:
1. `ResourcePlanner.determine_procurement_method` → `ResourceAcquisitionService.acquisition_method_for` (local-GCC vs external-USD)
2. `ResourcePlanner.initiate_fulfillment` → `ResourceFulfillmentService.fulfill_supply_need` → `MaterialRequestService.request_materials`

**Tertiary entry point: Rake tasks** (`solar_system_mission_pipeline.rake`, `solar_system_mission_belt.rake`)

Direct calls to `ResourceAcquisitionService.order_acquisition` and `acquisition_method_for`.

**Ambiguity:** There is no single canonical acquisition path. The manager loop uses **two parallel paths**:
- `OperationalManager` → `ProcurementService` (ISRU-first, placeholder pricing)
- `ResourcePlanner` → `ResourceAcquisitionService` → `ResourceFulfillmentService` (GCC/USD fork, real pricing)

A runtime trace is required to determine which path dominates in a live Super-Mars run. The rake tasks are likely used for setup/bootstrap rather than the live loop.

## 5. Placeholder Pricing Status

**Location:** `ProcurementService.check_market_price` (line ~40-45)

```ruby
base_prices = {
  oxygen: 500,
  water: 300,
  food: 800,
  structural_carbon: 2000
}
```

**Status: PARTIALLY EXERCISED but functionally dead.**

- The method is called from `procure_resource` when local production fails.
- If `settlement_can_afford?` returns true, `purchase_from_market` is called — **but this method is a no-op** (only logs). No actual market transaction occurs.
- Therefore: the placeholder pricing is **reachable** but **non-functional**. It can be exercised by a live call path but produces no side effects.
- Only 4 materials have prices; all others return nil and fall through to fail.

**Recommendation:** This is not a production risk (no real transactions), but it should be flagged for the next design session. Either replace with real NPC pricing or remove if ProcurementService's market path is deprecated in favor of ResourceAcquisitionService.

## 6. can_harvest_locally? Classification

### EscalationService.can_harvest_locally?(settlement, material) — class method
- **Purpose:** Check if a celestial body has the resource (for escalation strategy selection)
- **Checks:** Atmosphere gases, hydrosphere water, geosphere materials
- **O2 gate:** Checks atmospheric O2 presence. For bodies without atmospheric O2 (Luna, Mars), the 2026-08-27 fix added ISRU capability check for deployed TEU/PVE units.
- **Used by:** `determine_escalation_strategy` in EscalationService

### Resource::Acquisition.can_harvest_locally?(resource_name) — instance method
- **Purpose:** Check if settlement has equipment to harvest a lunar resource
- **Checks:** Hardcoded lunar resource list + harvester availability
- **O2 gate:** Not in the lunar resources list (would return false unless O2 is added to the list)
- **Used by:** `try_local_harvesting` in Resource::Acquisition

**Classification: Genuinely different concerns.** EscalationService checks *body composition*; Resource::Acquisition checks *equipment availability*. They are not duplicates. However, neither method fully implements the ISRU gate for all cases — the 2026-08-27 fix addressed O2 in EscalationService but the Resource::Acquisition class has no ISRU check at all (only harvester presence).

## 7. Gaps vs. Economic Rules

### Resource-first / self-harvest preference
- **Gap:** `ProcurementService.can_produce_locally?` checks `PrecursorCapabilityService` + equipment, but this is a *location capability* check, not an ISRU deployment check. `ResourceAcquisitionService.is_local_resource?` is a hardcoded list with no ISRU gate. Neither enforces "harvest locally before buying" as a hard rule — they fall through to market/import when local fails.
- **Severity:** Medium. The preference exists as a soft ordering (check local first) but not as a hard gate.

### EAP / player-first constraints
- **Gap:** `ResourceAcquisitionService.player_sell_orders_exceed_eap?` returns **false** (placeholder). EAP ceiling is calculated but never enforced. ProcurementService has no EAP check at all.
- **Severity:** High. This means NPC acquisition can bypass player sell order price ceilings entirely.

### GCC gate
- **Status:** Partially implemented. `ResourceAcquisitionService` enforces `financials.can_afford_fiat_import?` for USD imports. GCC contract affordability uses `settlement.can_afford?`. ProcurementService uses `settlement_funds` with corporate debt conservatism (>30% debt → conservative).
- **Gap:** No unified view of what "can afford" means across services. Each service has its own funding check logic.

### Virtual ledger scope
- **Gap:** No virtual ledger mechanism exists in any of the four services. All checks use real `Financial::Account` balances. If a virtual ledger is part of the economic model, it is not implemented in the acquisition surface.
- **Severity:** Unknown — depends on whether virtual ledger is a standing rule or a future design element.

### DC continuity
- **Gap:** No explicit DC (distribution center) continuity logic in any service. Expired orders are handled by EscalationService but there is no mechanism to ensure a DC remains operational during shortages beyond emergency missions.
- **Severity:** Low-Medium. May be addressed at a higher architectural layer.

### Cycler/resupply preference (normal shortage waits; emergency is the exception)
- **Gap:** `EscalationService.emergency_required?` compares time-to-critical vs time-to-next-resupply — this is the only cycler/resupply logic. It determines whether to create an emergency mission or add to resupply manifest. However, there is no explicit "wait for cycler/resupply before escalating" rule in ProcurementService or ResourceAcquisitionService.
- **Severity:** Medium. The emergency fork exists but normal-shortage cycler preference is not enforced across the acquisition surface.

### Excess listing after self-harvest ("keep need, list excess")
- **Gap:** No excess-listing logic exists in any service. When local production meets a need, there is no mechanism to automatically list surplus on the market. ProcurementService's `produce_locally` is a no-op. ResourceAcquisitionService creates contracts but does not handle excess listing.
- **Severity:** Medium. This is a key economic rule that has no implementation path.

## 8. Recommendations for Next Design Session

1. **Extend EscalationService spine** with:
   - Explicit cycler/resupply preference for normal shortages (wait before escalating)
   - Excess-listing-after-self-harvest mechanism ("keep need, list excess")
   - Unified EAP enforcement across all acquisition paths (fix `player_sell_orders_exceed_eap?` placeholder)

2. **Clarify canonical path:** Decide whether ProcurementService or ResourceAcquisitionService is the primary acquisition path for the live manager loop. If both are needed, define their boundary clearly (e.g., ProcurementService = emergency/short-term, ResourceAcquisitionService = strategic/long-term).

3. **Resolve placeholder pricing:** Either replace `ProcurementService.check_market_price` with real NPC pricing or deprecate its market path if ResourceAcquisitionService is canonical.

4. **No new architecture.** The existing spine (EscalationService + one acquisition service) is sufficient for extension. Do not create a parallel procurement layer.

5. **Runtime trace required** to confirm which acquisition path dominates in a live Super-Mars manager loop run. Static analysis alone cannot resolve the OperationalManager vs ResourcePlanner ambiguity.
