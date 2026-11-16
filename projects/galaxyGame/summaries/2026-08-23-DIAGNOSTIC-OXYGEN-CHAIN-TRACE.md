# Diagnostic: Oxygen Chain Trace — Harvester → TEU → PVE → Inventory

**Date:** 2026-08-23  
**Type:** READ-ONLY diagnostic — production code trace only  
**Scope:** `app/jobs/`, `app/services/` related to harvesting, ISRU, manufacturing  

---

## Summary

**The harvester completion path does NOT route through the ISRU chain (TEU → PVE). It short-circuits directly from "harvester deployed" to crediting the order.resource to inventory.** There are no TEU or PVE job classes in the codebase. The ISRU chain exists only as assessment/evaluation logic and a manual manufacturing orchestration service — neither is invoked by the harvester completion flow.

---

## 1. Does HarvesterCompletionJob trigger a real, separate TEU job?

**No. No such trigger exists.**

`HarvesterCompletionJob` (app/jobs/harvester_completion_job.rb, lines 1–56) performs exactly these steps in its `perform` method:

1. **Calculate harvested amount** from harvester's `extraction_rate * operational_hours` (line 23)
2. **Credit inventory directly** via `add_to_settlement_inventory(order.base_settlement, order.resource, harvested_amount)` (line 26) — this calls `inventory.add_item(material, amount)` where `material = order.resource` (e.g., `"O2"`)
3. **Fulfill the order** via `order.fulfill!` (line 29)
4. **Deactivate the harvester** (line 32)

There is zero code in this job that references TEU, PVE, ISRU, volatiles, or any processing step. The method `add_to_settlement_inventory` at line 45-50 is:

```ruby
def add_to_settlement_inventory(settlement, material, amount)
  inventory = settlement.inventory || settlement.create_inventory
  inventory.add_item(material, amount)
end
```

It credits `order.resource` (whatever the order was for) directly. No intermediate processing exists.

---

## 2. Does a TEU job's completion trigger a real, separate PVE job?

**No. No TEU or PVE job classes exist anywhere in the codebase.**

Searched all of `app/jobs/` — only these job classes exist:
- `HarvesterCompletionJob` (harvesting)
- `ConcurrentTaskWorkerJob` (task execution engine, generates trace O2/H2O via `generate_regolith_volatiles` at line 73 — only 0.001kg O2 per print task)
- `SafetyNetLogisticsJob`
- `MineGccJob`
- `ApplicationJob`

Searched for class names: `TeuService`, `PveService`, `TeuJob`, `PveJob`, `TeuProcessingJob`, `PveProcessingJob` — **zero matches**.

The ISRU chain (TEU → PVE) exists only in these locations as **assessment/evaluation/simulation logic**:

| File | Purpose | Invoked by HarvesterCompletionJob? |
|------|---------|-----------------------------------|
| `app/services/ai_manager/isru_evaluator.rb` | Assesses ISRU capabilities (TEU present?, PVE capable?) — returns assessment hash | No |
| `app/services/ai_manager/isru_optimizer.rb` | Optimizes ISRU deployment strategy — planning only | No |
| `app/services/manufacturing/production_service.rb` | `manufacture_component()` orchestrates TEU→PVE cycles manually (lines 30-95) | No — this is a separate manual API |
| `app/services/luna_operations_simulation_service.rb` | Simulates TEU/PVE production for Luna ops (lines 174-240) | No — simulation only |

None of these are job classes. None are triggered by the harvester completion flow.

---

## 3. Does O2 only get written to inventory after PVE completes?

**No. O2 is written directly to inventory by HarvesterCompletionJob, bypassing any processing step.**

The path for an `O2` order:

1. **EscalationService.determine_escalation_strategy** (line 406) checks `can_harvest_locally?`
2. **can_harvest_locally?** (line 457-473) for `"O2"` checks: `celestial_body.atmosphere&.gases&.any? { |g| g.name == 'O2' }` — this checks if O2 exists in the atmosphere, NOT if PVE can produce it
3. **EscalationService.schedule_harvester_completion** (line 375-386) schedules `HarvesterCompletionJob` with `(harvester.id, order.id)`
4. **HarvesterCompletionJob.perform** credits `order.resource` (i.e., `"O2"`) directly to inventory via `inventory.add_item("O2", harvested_amount)`

The O2 is treated as if it were a directly harvestable raw material from the atmosphere/geosphere. No PVE processing step exists in this path.

**Secondary O2 source:** `ConcurrentTaskWorkerJob.generate_regolith_volatiles` (line 73) adds 0.001kg O2 per print task — this is trace byproduct, not the main production path.

---

## 4. Missing hops in the chain

All three hops are missing:

| Hop | Expected Behavior | Actual State |
|-----|------------------|--------------|
| Harvester → TEU job | Deploy TEU unit, process raw_regolith → mixed volatiles | **No trigger exists.** HarvesterCompletionJob credits order.resource directly to inventory |
| TEU → PVE job | Process mixed volatiles/processed_regolith → O2 + depleted regolith | **No TEU or PVE job classes exist** in the entire codebase |
| PVE completion → inventory credit | Credit O2 to inventory after PVE cycle completes | **O2 is credited before any processing** — HarvesterCompletionJob writes order.resource directly |

The ISRU chain (raw_regolith → TEU → mixed volatiles → PVE → O2 + depleted_regolith) exists only as:
- **Assessment logic** (`ISRUEvaluator.assess_capabilities`): evaluates whether TEU/PVE units are present and capable
- **Manufacturing orchestration** (`ProductionService.manufacture_component`): a manual API that consumes raw_regolith from surface pile, runs PVE cycles, produces water/gases/inert waste — but this is never called by the harvester/escalation flow
- **Simulation logic** (`LunaOperationsSimulationService`): simulates production for planning purposes

The escalation service's `can_harvest_locally?` method (line 457) determines harvestability by checking atmospheric composition, not ISRU capability. For O2 specifically, it checks if the celestial body's atmosphere contains O2 gas — if yes, a harvester is deployed that directly credits O2 to inventory upon completion.

---

## Conclusion

The production code treats oxygen as a **directly harvestable resource** (like raw_regolith or ice), not as a processed product of the ISRU chain. The ISRU chain (TEU → PVE) exists only as assessment/evaluation/simulation infrastructure and a manual manufacturing API — it is never wired into the order fulfillment pipeline.

This means:
- A settlement with no TEU/PVE units can still receive O2 via harvester orders
- No raw_regolith is consumed in the process
- No depleted regolith byproduct is produced
- The ISRU chain is purely informational/planning infrastructure, not operational
