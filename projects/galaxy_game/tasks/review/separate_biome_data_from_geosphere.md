[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase6+/separate_biome_data_from_geosphere.md]
[RATIONALE: UI/dashboard cosmetic, investigation task, or obsolete code cleanup — not Luna sim prerequisite]
---
name: "Separate Biome Data from Geosphere"
priority: MEDIUM
phase: 6
created: 2026-06-21
status: backlog
type: refactor
---

# TASK: Refactor Biome Data Separation from Geosphere

**Priority**: MEDIUM  
**Phase**: 6  
**Type**: refactor  
**Created**: 2026-06-21  

## Problem Statement

Biome data mixed in geosphere (terrain_map.biomes) instead of separate biosphere model. Causes simulation issues and blocks proper TerraGen and biosphere modularity.

**Current**: Biome data embedded in geosphere  
**Expected**: Biome data in biosphere, linked via coordinates

## Acceptance Criteria

- [ ] Biome grid generation only for active biospheres
- [ ] Biome data moved to biosphere model/storage
- [ ] Geosphere contains only geological data
- [ ] Monitor view and TerraGen updated for separated data
- [ ] Migration cleaning biome data from non-biosphere worlds
- [ ] Performance and memory improvements validated

## Implementation Steps

1. Update terrain generation to check for active biosphere
2. Move biome grid storage to biosphere model
3. Refactor geosphere/terrain_map to remove biomes key
4. Update monitor view to reference biosphere
5. Update TerraGen to use biosphere data
6. Create migration to clean existing data
7. Add specs for separated structure
8. Test performance impact

## Commit Instructions

```bash
git add galaxy_game/app/models/geosphere.rb galaxy_game/app/models/biosphere.rb galaxy_game/db/migrate/
git commit -m "refactor: separate biome data from geosphere into biosphere model"
```