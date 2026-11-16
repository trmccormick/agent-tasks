[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase7+/implement_geotiff_auto_detection_system.md]
[RATIONALE: Terrain/UI cosmetic or investigation task — not Luna sim prerequisite]
---
name: "Implement GeoTIFF Auto-Detection System"
priority: MEDIUM
phase: 7
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Implement GeoTIFF Auto-Detection and Update System

**Priority**: MEDIUM  
**Phase**: 7  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

Adding new NASA GeoTIFF files requires manual terrain regeneration. System doesn't detect file changes or validate data integrity before use.

**Current**: Manual process; no change detection or validation  
**Expected**: Automatic detection and update of terrain when GeoTIFF files change

## Acceptance Criteria

- [ ] File modification time checking implemented
- [ ] GeoTIFF freshness detection working
- [ ] Automatic terrain regeneration on data updates
- [ ] Data validation and error handling
- [ ] Graceful fallback to procedural generation
- [ ] Admin interface for data freshness indicators

## Implementation Steps

1. Add file modification time checking to nasa_geotiff_available?
2. Compare GeoTIFF timestamps vs terrain generation metadata
3. Create terrain update triggers with force_update parameter
4. Add GeoTIFF data validation and integrity checks
5. Implement error handling and procedural fallback
6. Add comprehensive logging and monitoring
7. Enhance admin interface for data status visibility

## Commit Instructions

```bash
git add galaxy_game/app/services/star_sim/automatic_terrain_generator.rb galaxy_game/app/controllers/admin/
git commit -m "feat: add GeoTIFF auto-detection and automatic terrain update system"
```