---
name: "Implement Settlement View Phase 3"
priority: MEDIUM
phase: phase7+
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Implement Settlement View Phase 3 — High-Resolution Canvas Rendering

**Priority**: MEDIUM  
**Phase**: phase7+ (Late Tutorial)  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

Settlement view needs high-resolution canvas rendering at 65536x32768 pixels (10m/pixel scale) with sprite atlas integration for domes, factories, and panels. This is the final phase of the monitor UI upgrade chain (Phase 1: Planetary → Phase 2: Regional → Phase 3: Settlement).

**Current**: No settlement view implementation exists  
**Expected**: High-resolution canvas rendering with sprite atlas, worldhouse placement grid, and construction preview

## Evidence of Incompleteness

```bash
find galaxy_game/app/javascript/admin -name "*settlement*" 2>/dev/null | head -3
# Likely returns no results — settlement view JS doesn't exist
```

## Files to Create/Modify

| File | Purpose | Key Section |
|------|---------|-------------|
| `galaxy_game/app/javascript/admin/settlement_view.js` (new) | Settlement view controller | 65K canvas rendering |
| `galaxy_game/app/views/admin/celestial_bodies/settlement_view.html.erb` (new) | Settlement view UI | Sprite atlas integration |
| `public/tilesets/settlement_sprites.png` (new) | Settlement sprite atlas | Domes, factories, panels |

## Acceptance Criteria

- [ ] Canvas dimensions set to 65536x32768 at 10m/pixel scale
- [ ] Sprite atlas with domes, factories, panels implemented (640x64 sprite atlas)
- [ ] Worldhouse placement grid overlay functional
- [ ] Construction preview for settlement structures working
- [ ] Interactive grid and preview features implemented
- [ ] Documentation of user interactions and APIs complete

## Implementation Steps

1. **High-Resolution Canvas Rendering**
   - Implement 65536x32768 canvas at 10m/pixel scale
   - Optimize viewport culling for massive canvas size
   - Implement level-of-detail rendering for performance
2. **Sprite Atlas Integration**
   - Create settlement sprite atlas (640x64) with domes, factories, panels
   - Implement sprite coordinate mapping for settlement structures
3. **Worldhouse Placement Grid Overlay**
   - Add interactive grid overlay for worldhouse placement
   - Implement snap-to-grid functionality
4. **Construction Preview**
   - Enable construction preview for settlement structures
   - Implement hover/preview state for structures before placement

## Commit Instructions

```bash
git add galaxy_game/app/javascript/admin/settlement_view.js public/tilesets/settlement_sprites.png
git commit -m "feat: implement Settlement View Phase 3 with 65K canvas and sprite atlas"
```
