[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase7+/implement_protoplanet_terrain_generation.md]
[RATIONALE: Terrain/UI cosmetic or investigation task — not Luna sim prerequisite]
---
name: "Implement Protoplanet Terrain Generation"
priority: MEDIUM
phase: 7
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Implement Protoplanet Terrain Generation System

**Priority**: MEDIUM  
**Phase**: 7  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

Protoplanet terrain generation (asteroid belt, Kuiper belt, dwarf planets) pending implementation. Needs realistic terrain using Vesta GeoTIFF data as reference template.

**Current**: Protoplanet terrain unimplemented  
**Expected**: Physics-based terrain with Vesta reference and procedural enhancement

## Acceptance Criteria

- [ ] Vesta GeoTIFF data loaded and processed
- [ ] Protoplanet terrain templates created
- [ ] Procedural generation with AI learning
- [ ] Composition-based terrain variation (C/S/M types)
- [ ] Realistic crater density algorithms
- [ ] Applied to asteroid belt, Kuiper, and dwarf planets

## Implementation Steps

1. Load and process Vesta GeoTIFF elevation data
2. Create protoplanet terrain template system
3. Implement GeotiffLoader for Vesta data
4. Build pattern extraction for feature learning
5. Create composition-based terrain variation
6. Implement crater formation algorithms
7. Apply to asteroid/Kuiper/dwarf planet objects
8. Integrate with StarSim generation pipeline

## Commit Instructions

```bash
git add galaxy_game/app/services/terrain/ galaxy_game/spec/services/terrain/
git commit -m "feat: implement protoplanet terrain generation with Vesta reference"
```