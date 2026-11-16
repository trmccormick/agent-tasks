[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase6+/regional-view-phase2.md]
[RATIONALE: UI/dashboard cosmetic, investigation task, or obsolete code cleanup — not Luna sim prerequisite]
---
name: "Implement Regional View Phase 2"
priority: MEDIUM
phase: phase6+
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Implement Regional View Phase 2 — Civ4-Style Regional View

**Priority**: MEDIUM  
**Phase**: phase6+ (Tutorial Arc)  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

Galaxy Game planetary rendering system needs transition from Phase 1 planetary overview (4K canvas) to Phase 2 regional gameplay view (16K canvas) with sprite-based terrain rendering. Current planetary view is static overview only; need Civ4-style regional view with unit movement layer preview and city placement zones.

**Current**: Planetary view only (4K canvas), no regional gameplay view  
**Expected**: Regional view with 16384x8192 canvas, sprite atlas integration, and gameplay layers

## Evidence of Incompleteness

```bash
find galaxy_game/app/javascript/admin -name "*regional*" 2>/dev/null | head -3
# Likely returns no results — regional view JS doesn't exist
```

## Files to Create/Modify

| File | Purpose | Key Section |
|------|---------|-------------|
| `galaxy_game/app/javascript/admin/regional_view.js` (new) | Regional view controller | 16K canvas rendering |
| `galaxy_game/app/views/admin/celestial_bodies/regional_view.html.erb` (new) | Regional view UI | Sprite atlas integration |
| `public/tilesets/galaxy_surface.png` (new) | Terrain sprite atlas | 288x32, 9 terrain sprites |

## Acceptance Criteria

- [ ] Canvas dimensions set to 16384x8192 (100m/pixel resolution)
- [ ] Sprite atlas galaxy_surface.png created with 9 terrain sprites
- [ ] NASA biome → sprite coordinate mapping implemented
- [ ] Unit movement preview layer functional
- [ ] City placement zone visualization (worldhouses) working
- [ ] Viewport culling optimized for 16K canvas performance

## Implementation Steps

1. **Canvas Scaling (16384x8192)**
   - Update canvas dimensions in regional view JavaScript
   - Adjust viewport calculations for 100m/pixel resolution
   - Modify coordinate systems for regional scale
2. **Sprite Atlas Integration**
   - Create galaxy_surface.png (288x32, 9 terrain sprites)
   - Update JSON tileset configuration for regional view
   - Implement NASA biome → sprite coordinate mapping logic
3. **Layer System Enhancement**
   - Add unit movement preview layer
   - Implement city placement zone visualization (worldhouses)
   - Ensure layer toggling works at regional scale
4. **Performance Optimization**
   - Implement viewport culling for 16K canvas
   - Optimize sprite rendering for large areas
   - Add level-of-detail rendering if needed

## Commit Instructions

```bash
git add galaxy_game/app/javascript/admin/regional_view.js public/tilesets/galaxy_surface.png
git commit -m "feat: implement Regional View Phase 2 with 16K canvas and sprite atlas"
```
