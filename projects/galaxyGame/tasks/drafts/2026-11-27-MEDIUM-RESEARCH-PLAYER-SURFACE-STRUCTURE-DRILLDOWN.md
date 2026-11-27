---
status: draft
resume: false
priority: MEDIUM
type: architecture
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
last_updated: 2026-11-27
---

# Player Surface — structure selection and equipment drill-down research

## Agent Dispatch Interface

**DRAFT ONLY — NOT DISPATCHABLE.** Suggested Qwen research task. Tracy must review and approve before dispatch. This is research only; no UI implementation is authorized.

## Objective

Research how the existing main GalaxyGame Player Surface and Settlement experiences should let a player select a surface installation or structure, inspect its properties, and drill down through its installed units, modules, rigs, and equipment.

The goal is to establish how the reusable structure/equipment hierarchy should be presented in the main application, without confusing Surface's regional/civilization-level role with Settlement's management of one developed settlement.

## Read and reconcile

- docs/wiki/ui/02_player_view_architecture.md — canonical Player view boundaries and Surface/Settlement responsibilities.
- docs/wiki/ui/04_current_ui_state_and_known_issues.md — dated observations about existing Surface UI; do not assume they are still current without checking.
- docs/wiki/player/space_and_surface_operations.md — distinction between spacecraft-based Space Operations and planetary Surface Operations.
- Current main-app Surface and Settlement routes, controllers, views, JavaScript, selection behavior, and data sources.
- Existing structure, settlement, unit, and equipment relationships in code and JSON.
- Relevant existing task and review records, including the already-existing prototype Surface-view port task.

## Scope

1. Inventory the current main-app Surface and Settlement interaction paths and identify what already works, is partial, disconnected, or absent. Distinguish source inspection from runtime verification.
2. Establish the Surface-level selection context: how a player identifies a settlement, installation, structure, or other surface asset on the map or in supporting lists.
3. Establish the handoff from Surface to the selected settlement/structure detail experience. Determine whether the player opens a Settlement workspace, a structure detail pane, or another existing context; recommend only what is supported by the canonical view boundaries.
4. Define the information hierarchy for selecting a structure and drilling down to:
   - structure properties and status;
   - installed equipment and aggregate properties;
   - individual units, modules, rigs, and component details.
5. Keep structure-level aggregation separate from settlement-wide aggregation. A structure is a container for installed equipment; it is not the entire settlement.
6. Consider how the same structure detail information can be reached from Surface and Settlement without making Surface a second copy of the Settlement management screen.
7. Identify data-source, selection-state, location, permissions, stale-data, and unavailable-data requirements. Do not imply that inspecting a remote object changes player location or grants live access.
8. Recommend the smallest coherent screen/interaction model and identify any genuinely missing implementation tasks.

## Constraints

- This is a research and design-reconciliation task only. Do not change routes, controllers, views, JavaScript, CSS, fixtures, or game code.
- Do not redesign the Planetary view or Space Operations.
- Do not duplicate or replace the already-approved Surface-view port task in projects/galaxy-game-ui/tasks/hold_for_review/ or its current lifecycle location.
- Do not treat a surface structure as a new game-domain entity if the existing model already represents it.
- Do not conflate Player, Admin, and AI Manager permissions or workflows.
- The main GalaxyGame wiki and current code govern intent and implementation respectively. Record conflicts rather than silently resolving them.

## Deliverable

Create a report in projects/galaxyGame/summaries/ containing:

- current Surface and Settlement selection/inspection paths with verified source references;
- a proposed navigation and drill-down flow;
- a clear boundary between Surface, Settlement, Structure Detail, and equipment detail;
- data and state needed at each level;
- existing capabilities versus gaps;
- recommended narrowly scoped follow-up tasks, if any.

## Acceptance criteria

- [ ] Existing Surface and Settlement behavior is inspected before proposing new UI.
- [ ] Surface and Settlement responsibilities remain distinct.
- [ ] Structure and equipment drill-down is reusable and does not duplicate the settlement readout.
- [ ] The report accounts for the existing Surface-view port work and avoids duplication.
- [ ] No UI or application changes are made.

## Dependencies

**Blocked by:** None for source inventory; final data requirements should be reconciled with the engineering aggregation research when available.  
**Blocks:** A later, separately reviewed main-app Player UI implementation task, if Tracy approves one.

## Progress / Resume

- **Done so far:** Scope drafted from the canonical Player view architecture and owner-confirmed structure hierarchy.
- **Next step:** Human review; dispatch only after approval.
- **Status:** Draft; not approved for dispatch.
