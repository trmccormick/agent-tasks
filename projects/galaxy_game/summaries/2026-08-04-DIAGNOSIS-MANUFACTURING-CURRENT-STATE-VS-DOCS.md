# 2026-07-26-MEDIUM-RESEARCH-MANUFACTURING-CURRENT-STATE-VS-DOCS — Investigation Findings

**Date**: 2026-08-04
**Type**: Architecture Diagnosis (Research)
**Status**: Complete

---

## Executive Summary

The manufacturing chain's documented architecture is **partially stale**. The high-level flow (Raw Materials → Processed Materials → Components → Assembly → Units/Craft) described in `MANUFACTURING_SYSTEM_OVERVIEW.md` and `isru/README.md` matches the *intent* of the code, but several specific claims in the docs are outdated or describe services that no longer match their current implementation state. Additionally, a second duplicate pair was discovered beyond the known `ManufacturingService` / `Manufacturing::Service` issue.

---

## Step 1 — Live Luna MVP Manufacturing Call Chain

### Entry Points (Confirmed Live)

| Caller | File | How It Calls |
|---|---|---|
| `MarketStabilizationService.produce_item_for_market` | `ai_manager/market_stabilization_service.rb:248` | `ManufacturingService.manufacture(item, settlement.owner, settlement, count: amount)` — NPC producer of last resort |
| `ResourcePlanner.determine_procurement_method` | `ai_manager/resource_planner.rb:178` | Delegates to `ResourceFulfillmentService` for trade; calls `Manufacturing::ByproductManufacturingService.process_mining_byproducts` for local mining byproducts |
| `GameService.process_jobs` | `game_service.rb:93` | `Manufacturing::ComponentProductionService.new(settlement).complete_job(job)` — game loop processes component production jobs |
| `Craft::BaseCraft.build_units_and_modules` | `app/models/craft/base_craft.rb:475` | `UnitModuleAssemblyService.build_units_and_modules(...)` — craft deployment path |
| `rake gcc_mining_sat` | `lib/tasks/gcc_mining_sat.rake:91` | `Manufacturing::CraftFactory.build_from_blueprint(...)` — one-off satellite build task |

### Forward Trace from ManufacturingService

```
ManufacturingService.manufacture(blueprint, owner, settlement)
  → Lookup::BlueprintLookupService.find_blueprint(blueprint_name)
  → Market::NpcPriceCalculator.calculate_ask(settlement, material_name)
  → settlement.calculate_construction_cost(bom_cost)
  → Job.create!(job_type: :unit_assembly, ...)
  → check_materials(settlement, required_materials, count)
  → consume_materials(settlement, required_materials, count)
  → add_completed_items(settlement, item_name, count)
```

**Key finding**: `ManufacturingService` is a **self-contained top-level orchestrator** — it does NOT delegate to any of the 17 services in `manufacturing/`. It handles blueprint lookup, pricing, job creation, material validation/consumption, and inventory addition all internally. The 17 `Manufacturing::*` services are used by *other* callers (game loop, ResourcePlanner, Craft model), not by `ManufacturingService`.

### Forward Trace from Manufacturing::ComponentProductionService

```
GameService.process_jobs (job_type: :component_production)
  → Manufacturing::ComponentProductionService.new(settlement).complete_job(job)
    → load_blueprint(component_id)
    → add_component_to_inventory(job, blueprint)
    → add_waste_products(job, blueprint)
    → job.update!(status: :ready_to_claim)
```

### Forward Trace from Manufacturing::AssemblyService

```
Craft::BaseModel.build_units_and_modules (deprecated path)
  → Manufacturing::AssemblyService.start_assembly(blueprint:, settlement:, requester:)
    → check_material_availability(blueprint_data, settlement, requester)
    → create_assembly_job(blueprint, blueprint_data, settlement, requester)
```

### Forward Trace from Manufacturing::UnitModuleAssembly

```
(No production callers found — only its spec references it)
  → build_units_and_modules(target:, settlement_inventory:)
    → process_units / process_modules / process_rigs
```

### Full Call Chain Summary for Luna MVP

```
MarketStabilizationService (NPC producer of last resort)
  └→ ManufacturingService.manufacture() [self-contained, no delegation]

ResourcePlanner (resource procurement planning)
  ├→ ResourceFulfillmentService.fulfill_supply_need() [trade path]
  └→ Manufacturing::ByproductManufacturingService.process_mining_byproducts() [byproduct O2]

GameService (game loop job processing)
  ├→ Manufacturing::ComponentProductionService.complete_job() [component production]
  └→ Manufacturing::ShellPrintingService.complete_job() [shell printing]

Craft::BaseCraft (craft deployment)
  └→ UnitModuleAssemblyService.build_units_and_modules() [unit/module fitting]

Rake task (one-off satellite build)
  └→ Manufacturing::CraftFactory.build_from_blueprint() [craft creation]
```

### Services Actually Reached by Live Call Chain

| Service | Reached? | By Whom |
|---|---|---|
| `ManufacturingService` | ✅ YES | MarketStabilizationService |
| `Manufacturing::ProductionService` | ❌ NO (only rake task + test_sanity.rb) | `isru_production_validation.rake`, `test_sanity.rb` — not production game loop |
| `Manufacturing::AssemblyService` | ⚠️ PARTIAL | Only referenced in docs/deployment_refinement.md as a planned integration target; no live callers found |
| `Manufacturing::ComponentProductionService` | ✅ YES | GameService (game loop) |
| `Manufacturing::ShellPrintingService` | ✅ YES | GameService (game loop) |
| `Manufacturing::MaterialProcessingService` | ❌ NO production callers | Only its spec; creates jobs but no caller invokes it |
| `Manufacturing::RegolithProcessingService` | ❌ NO production callers | Only its spec |
| `Manufacturing::ByproductManufacturingService` | ✅ YES | ResourcePlanner (local mining byproducts) |
| `Manufacturing::CraftFactory` | ⚠️ ONE-OFF | One rake task only |
| `Manufacturing::UnitModuleAssembly` | ❌ NO production callers | Only its spec; dead code |
| `Manufacturing::Service` | ❌ DEAD | Only its spec (known duplicate) |
| `Manufacturing::CostCalculator` | ⚠️ PARTIAL | `e2e_economic_simulation_spec.rb` only — no production callers |
| `Manufacturing::EquipmentRequest` | ✅ YES | Construction services (hangar_service, dome_service, covering_service, shell concern, coverable concern) |
| `Manufacturing::MaterialRequestSystem` | ❌ NO production callers | Only its file exists; no callers found |
| `Manufacturing::ConstructionManager` | ⚠️ STUB | Inventory lists it as stub; no callers found |

---

## Step 2 — Doc vs. Code Comparison

### MANUFACTURING_SYSTEM_OVERVIEW.md

**Status**: Draft/Stub (marked 2026-04-27) — **STALE**

| Claim in Doc | Reality | Verdict |
|---|---|---|
| "Chain: Raw Materials → Processed Materials → Components → Blueprints → Assembly → Units/Craft" | High-level intent matches; actual code has diverged into parallel paths (ManufacturingService self-contained, ComponentProductionService via game loop, ShellPrintingService via game loop) | PARTIALLY ACCURATE — intent is right, architecture description is too abstract to be useful |
| "Each stage references canonical JSON/data files" | Blueprint lookup goes through `Lookup::BlueprintLookupService` which reads from `data/json-data/blueprints/` | ACCURATE |
| "Use v1.3-compliant templates for all new blueprints" | Cannot verify without checking template versioning code | N/A (doc is stub) |
| All To-Do items unchecked | Doc has 5 unchecked To-Dos, none addressed | STALE — doc never completed |

**Verdict**: This doc is a skeleton that was never fleshed out. It doesn't contain enough detail to be wrong, but it's not useful for understanding the current architecture.

### ISRU README.md

**Status**: More detailed than MANUFACTURING_SYSTEM_OVERVIEW.md — **MOSTLY ACCURATE but with gaps**

| Claim in Doc | Reality | Verdict |
|---|---|---|
| "Precursor robotic mission deploys Mk1 fabricator" | Intent matches; no code enforces this sequence | INTENT MATCHES (design doc, not code) |
| "Regolith composition varies by location" | `RegolithProcessingService` handles composition-based extraction | ACCURATE |
| "Mk1→Mk2→Mk3 fabricator upgrades" | No Mk1/Mk2/Mk3 unit chain found in production code; only `CNT Fabricator` series exists in blueprints | PARTIALLY STALE — specific upgrade chain not implemented as described |
| "Panel/I-Beam assembly via 3D printing" | ShellPrintingService handles regolith shell enclosure; no direct I-beam panel production service found | PARTIALLY STALE |
| "CNT Fabricator series (Mk1-Mk3)" | CNT fabricator blueprints exist in `data/json-data/blueprints/units/production/fabricators/` | ACCURATE (blueprint data exists) |
| "Canonicalization and Data Integrity" section | Audit claims all mk1-mk3 units reviewed; but `Manufacturing::Service` dead code still present | PARTIALLY STALE — canonicalization incomplete |

**Verdict**: The ISRU README is a design intent document that's mostly aligned with the codebase vision, but specific implementation details (Mk1-Mk3 upgrade chain, panel/I-beam assembly) are not fully realized in code.

### CORE_CONCEPT_MAP.md (2026-07-16)

**Status**: Confirmed-stale lead — **INACCURATE on key claims**

| Claim | Reality | Verdict |
|---|---|---|
| "Likely owner: Manufacturing::Service + Manufacturing::ProductionService + Manufacturing::AssemblyService" | `Manufacturing::Service` is dead code (zero production callers); `Manufacturing::ProductionService` is only used by rake tasks/tests; `Manufacturing::AssemblyService` has no live callers | **WRONG** — none of these three are the actual manufacturing entry point. The real entry point is `ManufacturingService` (top-level, no namespace) |
| "17 services in manufacturing/" | Confirmed: 17 files in `app/services/manufacturing/` | ACCURATE count |
| "Chain: Raw Materials → Processed Materials → Components → Blueprints → Assembly → Units/Craft" | Same as MANUFACTURING_SYSTEM_OVERVIEW.md — high-level intent matches | PARTIALLY ACCURATE |

**Verdict**: CORE_CONCEPT_MAP.md is the most problematic doc. Its "Likely owner" claim for the manufacturing chain is wrong and would mislead anyone trying to understand where manufacturing actually starts.

---

## Step 3 — Additional Duplicate/Orphaned Services Found

### NEW FINDING: UnitModuleAssemblyService vs Manufacturing::UnitModuleAssembly

**This is a second duplicate pair**, identical pattern to `ManufacturingService` / `Manufacturing::Service`:

| Service | File | Status | Callers |
|---|---|---|---|
| `UnitModuleAssemblyService` (top-level) | `app/services/unit_module_assembly_service.rb` | ✅ LIVE | `Craft::BaseCraft.build_units_and_modules` (line 475) — production caller |
| `Manufacturing::UnitModuleAssembly` | `app/services/manufacturing/unit_module_assembly.rb` | ❌ DEAD | Only its spec (`spec/services/manufacturing/unit_module_assembly_spec.rb`) |

**Evidence**: Both classes have identical `build_units_and_modules` class method signatures and nearly identical instance method implementations. The top-level `UnitModuleAssemblyService` is the one actually called from production code (`base_craft.rb`).

### Other Orphaned Services (No Production Callers)

| Service | File | Notes |
|---|---|---|
| `Manufacturing::ProductionService` | `manufacturing/production_service.rb` | Partially implemented (PVE metrics work; logistics/consumption are TODOs). Only called by rake task + test_sanity.rb. Listed as "PARTIAL STUB" in inventory. |
| `Manufacturing::AssemblyService` | `manufacturing/assembly_service.rb` | Fully implemented but no production callers. Referenced in deployment_refinement.md as a *planned* integration target. |
| `Manufacturing::MaterialProcessingService` | `manufacturing/material_processing_service.rb` | Creates jobs but no caller invokes it. Has working `process` and `complete_job` methods. |
| `Manufacturing::RegolithProcessingService` | `manufacturing/regolith_processing_service.rb` | Fully implemented (temperature-based extraction) but no production callers. |
| `Manufacturing::CostCalculator` | `manufacturing/cost_calculator.rb` | EAP-COGS/LAP-COGS calculations implemented but only used in e2e spec. |
| `Manufacturing::MaterialRequestSystem` | `manufacturing/material_request_system.rb` | Appears to be a legacy/abandoned system — no callers found. References non-existent `MiningOperation`, `Refinery`, `ImportSystem` classes. |
| `Manufacturing::ConstructionManager` | `manufacturing/construction/construction_manager.rb` | Stub (both methods are TODOs). Listed in inventory as STUB. |

---

## Step 4 — Findings Summary

### What's Live (Actually Exercised for Luna MVP)

1. **`ManufacturingService`** — NPC producer of last resort (via MarketStabilizationService). Self-contained, no delegation to other manufacturing services.
2. **`Manufacturing::ComponentProductionService`** — Game loop component production jobs.
3. **`Manufacturing::ShellPrintingService`** — Game loop shell printing jobs.
4. **`Manufacturing::ByproductManufacturingService`** — ResourcePlanner byproduct generation (O2 from silicon mining).
5. **`UnitModuleAssemblyService`** — Craft deployment unit/module fitting (via Craft::BaseCraft).
6. **`Manufacturing::EquipmentRequest`** — Construction services (dome, hangar, covering) equipment requests.
7. **`Manufacturing::CraftFactory`** — One-off rake task for satellite builds.

### What's Stale / Not Matched by Code

1. **CORE_CONCEPT_MAP.md "Likely owner" claim** — Lists `Manufacturing::Service` + `ProductionService` + `AssemblyService` as the manufacturing chain owners. All three are dead or stub. The real entry point is `ManufacturingService` (top-level).
2. **MANUFACTURING_SYSTEM_OVERVIEW.md** — Draft/Stub from 2026-04-27 with 5 unchecked To-Dos. High-level flow is correct but too abstract to be useful. Never completed.
3. **ISRU README.md** — Design intent mostly matches, but specific implementation details (Mk1-Mk3 upgrade chain, panel/I-beam assembly) are not fully realized in code.
4. **`Manufacturing::ProductionService`** — Partially implemented (PVE metrics work). Logistics/consumption steps are TODOs. Only used by rake tasks and tests, not the game loop.

### New Duplicate Pair Found

- **`UnitModuleAssemblyService`** (live) vs **`Manufacturing::UnitModuleAssembly`** (dead code) — identical pattern to the known `ManufacturingService` / `Manufacturing::Service` duplicate. Both created in same era, parallel development, one abandoned.

### Services That Are Implemented But Not Called

- `Manufacturing::AssemblyService` — Fully implemented, no callers. Planned integration target per deployment_refinement.md.
- `Manufacturing::MaterialProcessingService` — Working implementation, no callers. Creates jobs but nothing triggers them.
- `Manufacturing::RegolithProcessingService` — Working temperature-based extraction, no callers.
- `Manufacturing::CostCalculator` — Working EAP/LAP-COGS calculations, only in specs.

### Services That Are Stubs/Incomplete

- `Manufacturing::ConstructionManager` — Both methods are TODOs.
- `Manufacturing::MaterialRequestSystem` — References non-existent classes (`MiningOperation`, `Refinery`, `ImportSystem`). Appears abandoned.

---

## Follow-Up Tasks Needed (Listed, Not Created)

1. **Remove dead code**: `Manufacturing::Service` (manufacturing/service.rb) — zero production callers, duplicate of ManufacturingService
2. **Remove dead code**: `Manufacturing::UnitModuleAssembly` (manufacturing/unit_module_assembly.rb) — zero production callers, duplicate of UnitModuleAssemblyService
3. **Remove abandoned code**: `Manufacturing::MaterialRequestSystem` (manufacturing/material_request_system.rb) — references non-existent classes, no callers
4. **Resolve stub services**: `Manufacturing::ConstructionManager` and `Manufacturing::ProductionService` — either complete them or remove if superseded
5. **Investigate AssemblyService integration**: deployment_refinement.md lists it as a planned target — is this still the intent?
6. **Correct CORE_CONCEPT_MAP.md**: Update "Likely owner" claim to reflect actual live entry points (ManufacturingService, not Manufacturing::Service)
7. **Complete or archive MANUFACTURING_SYSTEM_OVERVIEW.md**: Either flesh out with real architecture or mark as superseded by current state

---

## Lessons Learned

1. **Doc staleness accumulates silently** — MANUFACTURING_SYSTEM_OVERVIEW.md has been a stub since 2026-04-27 with no one flagging it. Architecture docs need periodic "is this still accurate?" checks.
2. **Parallel development creates hidden duplicates** — Both `ManufacturingService`/`Manufacturing::Service` and `UnitModuleAssemblyService`/`Manufacturing::UnitModuleAssembly` were created in the same commit era, suggesting two separate developers working on similar functionality without awareness of each other.
3. **"Partial stub" services are dangerous** — `Manufacturing::ProductionService` has working PVE metrics but broken logistics. It's not obviously dead (has a spec) but isn't functional for its intended purpose.
4. **The inventory audit is the ground truth** — The 121-service inventory (already finalized) was essential for this task. Without it, I would have been recounting services and falling into the same reconciliation loop that cost previous sessions.

---

## Handoff Summary

Live chain: ManufacturingService (NPC producer) + ComponentProductionService (game loop) + ShellPrintingService (game loop) + ByproductManufacturingService (ResourcePlanner) + UnitModuleAssemblyService (craft deployment). Two duplicate pairs found: ManufacturingService/Manufacturing::Service (known) and UnitModuleAssemblyService/Manufacturing::UnitModuleAssembly (new). CORE_CONCEPT_MAP.md "Likely owner" claim is wrong. MANUFACTURING_SYSTEM_OVERVIEW.md is an incomplete stub. 6+ services implemented but not called. 2 services are abandoned stubs.
