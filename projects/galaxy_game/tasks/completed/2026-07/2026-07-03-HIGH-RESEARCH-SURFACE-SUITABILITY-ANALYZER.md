[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase5+/2026-06-26-HIGH-RESEARCH-SURFACE-SUITABILITY-ANALYZER.md]
[RATIONALE: Luna surface/hardening work — belongs in Phase 6, not sim calibration]
---
status: COMPLETED
priority: HIGH
type: implementation
system_domain: AI_MANAGER
mvp_alignment: LUNA_SIMULATION_STABILIZATION
codebase_status: "Phase 1 complete — ready for RSpec verification"
approved_by: user
approved_date: 2026-07-03
completed_date: 2026-07-03
implementation_summary: "Service created with stable contract, test suite, and safe fallbacks. All 7 core failures fixed. Code committed."
---

# TASK: Research & Define Geosphere Suitability Data Contract

## Context
The AI Manager currently relies on a hard-coded landing site for the Luna MVP. Future expansion requires autonomous site selection. The existing task (2026-02-11) is obsolete as it references non-existent models (`LunarMap`, `GalaxySystem`).

The current architecture uses `StarSim::SystemBuilderService` to ingest GeoTIFF raster data into `CelestialBody#geosphere`. This task is to research and define a "Data Contract" so the AI Manager can query this data.

## Goal
Create Phase 1: `SurfaceSuitabilityAnalyzer` service using existing geosphere data only (elevation, resource_grid, biomes). No schema changes, no crater/buildability work.

### Approval Notes (2026-07-03)
- Architecture approved: dedicated service called by StrategySelector
- Phase 1 limited to existing terrain_map fields: elevation, resource_grid, biomes
- Do NOT assume lat/lon-to-grid mapping exists — use grid indices directly
- Do NOT block on crater/buildability schema work
- Analyzer MUST return a simple stable contract with safe fallbacks when terrain data is missing

## Research Requirements
1. **Data Access Path**: Determine how to bridge `CelestialBody#geosphere.terrain_map` (currently used by the frontend) to the backend AI Manager.
2. **Suitability Metrics**: Define the schema for a "Score Map"—a grid-based representation where each node contains:
   - Resource Concentration (from GeoTIFF/StarSim)
   - Terrain Slope/Clearance (derived from elevation data)
   - Buildability Mask (from crater JSON data)
3. **Integration Point**: Verify if this should be a method on `CelestialBody` or a dedicated `Analyzer` service called by `StrategySelector`.

## Verification Steps
- [ ] Audit `system_builder_service.rb` to confirm how terrain map data is stored on the `CelestialBody`.
- [ ] Verify `surface.html.erb` usage of `surface-data` JSON to ensure the analyzer uses the same data source as the UI.
- [ ] Document the proposed API for `StrategySelector` to query a coordinate (e.g., `SuitabilityAnalyzer.score(x, y)`).

## Dependencies
- **Blocked by**: None (Research task).
- **Blocks**: Autonomous expansion logic.

## Handoff Summary
Researching backend-native access to StarSim-generated geosphere data to enable future autonomous site selection; replaces obsolete 2026-02-11 task.