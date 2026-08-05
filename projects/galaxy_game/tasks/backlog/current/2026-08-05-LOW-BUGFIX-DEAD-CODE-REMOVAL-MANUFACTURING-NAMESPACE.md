---
title: "Dead Code Removal — Manufacturing namespace cleanup"
date: 2026-08-05
type: BUGFIX
priority: LOW
status: backlog
phase: phase9+
assigned_to: Qwen
---

# ⚡ Minimal Handoff

Delete three dead `Manufacturing::*` services that have zero production callers (confirmed by live call chain audit). Same pattern as the already-completed `Manufacturing::Service` removal. One task, three files to delete, one spec file per service to delete.

---

## Context

The manufacturing diagnosis (`2026-07-26-MEDIUM-RESEARCH-MANUFACTURING-CURRENT-STATE-VS-DOCS.md`) confirmed the live call chain for Luna MVP exercises exactly 7 services. Three `Manufacturing::*` services in the namespace are dead code — identical risk profile to the already-closed `Manufacturing::Service` removal.

### Live Call Chain (confirmed)
1. `ManufacturingService` — NPC producer of last resort
2. `Manufacturing::ComponentProductionService` — game loop component jobs
3. `Manufacturing::ShellPrintingService` — game loop shell printing
4. `Manufacturing::ByproductManufacturingService` — ResourcePlanner byproducts
5. `UnitModuleAssemblyService` — craft deployment unit/module fitting
6. `Manufacturing::EquipmentRequest` — construction services
7. `Manufacturing::CraftFactory` — one-off rake task

### Dead Code to Remove (3 files + 3 spec files)

| # | Service | File | Evidence |
|---|---------|------|----------|
| 1 | `Manufacturing::Service` | `app/services/manufacturing/service.rb` | Zero callers, only its spec references it (known duplicate of top-level `ManufacturingService`) |
| 2 | `Manufacturing::UnitModuleAssembly` | `app/services/manufacturing/unit_module_assembly.rb` | Zero production callers, only its spec references it (confirmed dead, parallel development origin) |
| 3 | `Manufacturing::MaterialRequestSystem` | `app/services/manufacturing/material_request_system.rb` | No callers found; references non-existent `MiningOperation`, `Refinery`, `ImportSystem` classes — abandoned legacy |

### What to Delete
- `app/services/manufacturing/service.rb`
- `app/services/manufacturing/unit_module_assembly.rb`
- `app/services/manufacturing/material_request_system.rb`
- `spec/services/manufacturing/service_spec.rb`
- `spec/services/manufacturing/unit_module_assembly_spec.rb`
- `spec/services/manufacturing/material_request_system_spec.rb`

### What NOT to Touch (need design calls first)
- `Manufacturing::ProductionService` — partially implemented, needs "complete or delete" decision
- `Manufacturing::AssemblyService` — fully implemented but no callers; deployment_refinement.md says "planned integration target" — needs confirmation
- `Manufacturing::ConstructionManager` — stub (both methods are TODOs)

---

## Prerequisites

- None — this is a straightforward deletion with no dependencies

## Architecture Gotchas

1. **Same pattern as Manufacturing::Service removal** — that task was already completed and closed. This is the same operation repeated for two more dead services.
2. **UnitModuleAssembly duplicate** — `UnitModuleAssemblyService` (top-level, live) vs `Manufacturing::UnitModuleAssembly` (dead). The top-level one is what's actually called from `base_craft.rb:475`.
3. **MaterialRequestSystem references non-existent classes** — `MiningOperation`, `Refinery`, `ImportSystem` don't exist in the codebase. This confirms it was abandoned during development, not just unused.

## Stop Conditions

- All 6 files deleted
- No remaining references to any of the three services (grep confirms zero callers)
- RSpec suite runs clean (no broken spec imports)

## Completion Report Template

Fill in:
1. Files deleted (6 filenames)
2. Grep results confirming zero remaining references
3. RSpec run result (total examples, failures)
4. Any unexpected findings

---

**Status**: Ready for dispatch — same dead-code-removal pattern as the already-completed Manufacturing::Service task.
