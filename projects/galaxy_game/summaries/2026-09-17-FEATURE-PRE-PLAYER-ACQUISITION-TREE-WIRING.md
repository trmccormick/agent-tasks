# STATUS SYNTHESIS REPORT

**Task**: 2026-09-17-HIGH-FEATURE-PRE-PLAYER-ACQUISITION-TREE-WIRING
**Status**: backlog → active
**Date**: 2026-09-17

---

## What I'm About to Do

Wire the locked 5-step pre-player acquisition tree onto `EscalationService` as decision spine, delegating execution to `ResourceAcquisitionService`. Import cost uses `Market::NpcPriceCalculator.evaluate_strategy` only. No player-first branches. No new service class.

---

## Files Referenced (Step 1 — Read-Only Mapping Confirmed)

| File | Purpose | Status |
|---|---|---|
| `galaxy_game/app/services/ai_manager/escalation_service.rb` | Decision spine — extend entry point | ✅ confirmed, 627 lines |
| `galaxy_game/app/services/ai_manager/resource_acquisition_service.rb` | Execution owner | ✅ confirmed, methods at lines 6/47/98 |
| `galaxy_game/app/services/market/npc_price_calculator.rb` | evaluate_strategy (read-only API) | ✅ confirmed, class method line 67 |
| `summaries/2026-09-16-ARCHITECTURE-PRE-PLAYER-ACQUISITION-DECISION-TREE.md` | Locked design | ✅ read |

---

## Existing Method Inventory (Step 1 Output)

### EscalationService (galaxy_game/app/services/ai_manager/escalation_service.rb)
| Method | Line | Notes |
|---|---|---|
| `self.handle_resource_shortage(action_hash, settlement)` | 10 | **Has real implementation** — extend/wrap, do NOT delete |
| `self.emergency_required?(settlement, resource)` | 165 | Depends on stub ETAs |
| `self.time_to_critical(settlement, resource)` | 81 | Stub (≈72h) |
| `self.time_to_next_resupply(settlement)` | 90 | Stub (≈7 days) |
| `self.can_harvest_locally?(settlement, material)` | 457 | Existing capability check |
| `self.can_manufacture_locally?(settlement, material)` | 475 | Existing capability check |
| `self.settlement_can_fund_emergency?(settlement, resource)` | 97 | Funding check |
| `self.settlement_can_fund_shortage?(settlement, cost_estimate)` | 114 | Funding check |
| `self.create_emergency_mission_for_shortage(...)` | 128 | Existing behavior to preserve |
| `self.add_shortage_to_resupply_manifest(...)` | 154 | Existing behavior to preserve |
| `self.deploy_automated_harvesters(order)` | 184 | Local production helper |
| `self.deploy_manufacturing_unit(order)` | 199 | Local production helper |

### ResourceAcquisitionService (galaxy_game/app/services/ai_manager/resource_acquisition_service.rb)
| Method | Line | Notes |
|---|---|---|
| `self.order_acquisition(settlement, material, amount)` | 6 | Primary entry |
| `self.process_local_acquisition(settlement, material, amount)` | 47 | Local fulfillment |
| `self.process_external_import(settlement, material, amount, delivery_method)` | 98 | Import execution |
| `self.acquisition_method_for(material)` | 28 | Routing helper |
| `self.player_sell_orders_exceed_eap?(settlement, material)` | 139 | **Stub always false** — do not depend on it |

### Market::NpcPriceCalculator (galaxy_game/app/services/market/npc_price_calculator.rb)
| Method | Line | Notes |
|---|---|---|
| `self.evaluate_strategy(material:, location:, context:)` | 67 | Class method — sole pricing source |
| `evaluate_strategy` | 403 | Instance method variant |

---

## Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted)
- ✅ Step 0: YAML status updated backlog → active
- ✅ Read architecture synthesis (2026-09-16) and this task
- ✅ Read-only mapping confirmed all method names and line numbers
- ✅ Understand pre-player vs post-player split

---

## Implementation Plan

### Step 2 — Tree on EscalationService

**Approach**: Extend `handle_resource_shortage` (line 10) with a new private helper that runs the ordered 5-step tree. Preserve existing emergency-mission / resupply-manifest behavior.

```
New entry: self.pre_player_acquisition_tree(settlement, material, needed_quantity)
  → calls ordered private helpers:
    - step_1_stockpile_sufficient?(settlement, material)
    - step_2_local_capability_exists?(settlement, material)
    - step_3_wait_for_cycler?(settlement)
    - step_4_emergency_local_standup?(settlement, material)
    - step_5_import_via_evaluate_strategy(settlement, material, quantity)
```

**Key design decisions**:
- `step_1`: Use existing stockpile data (no new subsystem)
- `step_2`: Call `can_harvest_locally?` + `can_manufacture_locally?` → delegate to `ResourceAcquisitionService.process_local_acquisition` if true
- `step_3`: Use `emergency_required?` with stub ETAs — defer if not emergency
- `step_4`: If emergency + local standup possible → force via existing deploy helpers; else continue
- `step_5`: Call `Market::NpcPriceCalculator.evaluate_strategy(material:, location: settlement.celestial_body, context: {source: :import})` → use `reference_cost` / `strategy_type` → delegate to `ResourceAcquisitionService.process_external_import`

### Step 3 — ResourceAcquisitionService Adapters (if needed)

Current methods are sufficient for delegation:
- `process_local_acquisition(settlement, material, amount)` — already exists
- `process_external_import(settlement, material, amount, delivery_method)` — already exists

No new methods required unless inventory/stockpile checks need a thin adapter.

### Step 4 — Specs

**Target files**:
- `spec/services/ai_manager/escalation_service_spec.rb` — add focused tree examples
- `spec/services/ai_manager/resource_acquisition_service_spec.rb` — verify delegation paths (if touched)

**New spec examples**:
1. Sufficient stockpile → returns early, no side effects
2. No local capability → skips to import step
3. Normal shortage + not emergency → defers (no forced import)
4. Emergency + can stand up locally → forces local production
5. Emergency + cannot stand up → imports via evaluate_strategy

**Regression**: Keep existing escalation_service_spec and npc_price_calculator_spec green.

### Step 5 — Guardrails

```bash
grep -rn "calculate_eap_ceiling\|player_sell_orders_exceed_eap" galaxy_game/app/services/ai_manager/ || true
```

Expected: `calculate_eap_ceiling` returns zero results; `player_sell_orders_exceed_eap?` may appear in ResourceAcquisitionService as stub. No resurrected dead helpers.

---

## Expected Outcomes
- EscalationService entry runs ordered pre-player checks
- Execution delegated to ResourceAcquisitionService where appropriate
- Import path uses evaluate_strategy
- Specs cover tree branches (focused examples)
- No player buy-order / mission code
- No new service class

## Critical Gotchas I Will Avoid
- ❌ Player-first default — instead ✅ pre-player tree only
- ❌ New acquisition service — instead ✅ EscalationService + ResourceAcquisitionService
- ❌ Bypass EscalationService — instead ✅ spine decides, execution delegates
- ❌ Delete handle_resource_shortage existing behavior — instead ✅ extend/wrap
- ❌ Depend on player_sell_orders_exceed_eap? stub — instead ✅ ignore for pre-player logic

---

## Commit Plan (Host Only)

```bash
cd /Users/tam0013/Documents/git/galaxyGame
git add galaxy_game/app/services/ai_manager/escalation_service.rb \
        galaxy_game/app/services/ai_manager/resource_acquisition_service.rb \
        galaxy_game/spec/services/ai_manager/
git commit -m "feature(ai-manager): wire pre-player acquisition tree on EscalationService → ResourceAcquisitionService"
```

---

**SYNTHESIS COMPLETE.** Ready to proceed.
