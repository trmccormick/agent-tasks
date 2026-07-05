# TASK: Fix Biosphere Guard in Terrain Generation
**Status**: ARCHIVED / SUPERSEDED  
**Priority**: CRITICAL  
**Type**: feature  
**Created**: 2026-02-15

---

## Problem Statement
Airless bodies (Luna, Mercury, bare Mars) are assigned Earth-like biomes due to missing biosphere check in `generate_hybrid_biomes`. This causes wasted computation and incorrect terrain data.

## Goals
- Add biosphere presence guard to `generate_hybrid_biomes`
- Prevent biome generation for airless bodies
- Ensure correct biomes for Earth and biosphere worlds

## Acceptance Criteria
- [ ] Airless bodies return `nil` for biomes
- [ ] Earth and biosphere worlds generate biomes
- [ ] No performance regression

## Implementation Notes
- Add `return nil unless celestial_body.biosphere.present?` at start of `generate_hybrid_biomes`
- Test with Luna/Mercury and Earth

## Diagnostic/Debugging
- Confirm no biomes for airless bodies
- Verify correct biomes for Earth

## Related Files/Paths
- app/services/automatic_terrain_generator.rb (generate_hybrid_biomes)

## References
- Archive task (2026-02-15)

---

## Critical Findings

**Archived on 2026-05-18.** This task is obsolete. The active simulation loop lives in `BiosphereSimulationService`, which dynamically balances biomes using global water availability and climate ranges, bypassing legacy procedural generator assumptions.

The discovery of the comprehensive biome simulation system (lines 348-429 in `app/services/terra_sim/biosphere_simulation_service.rb`) revealed that:
- Climate adjustments are driven by physical variables (gravity, atmospheric density, axial tilt)
- Biome suitability is calculated from actual temperature/humidity ranges, not hardcoded planet names
- The entire atmospheric composition system feeds into the dynamic feedback loop, rendering name-based shortcuts completely irrelevant

The legacy `AutomaticTerrainGenerator` and name-based checks are entirely bypassed by the active simulation layer.
