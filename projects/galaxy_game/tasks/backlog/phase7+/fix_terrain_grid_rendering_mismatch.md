---
name: "Fix Terrain Grid Rendering Pixelation"
priority: MEDIUM
phase: 7
created: 2026-06-21
status: backlog
type: bugfix
---

# TASK: Fix Terrain Grid Pixelation for Small Bodies

**Priority**: MEDIUM  
**Phase**: 7  
**Type**: bugfix  
**Created**: 2026-06-21  

## Problem Statement

Terrain rendering for small bodies like Luna doesn't display surface features (craters) due to pixelation. Grid generation doesn't match rendering parameters, causing canvas sizing mismatches.

**Current**: Grid mismatch causes pixelation and feature loss  
**Expected**: Small bodies show detail (Luna craters visible)

## Files to Edit

| File | Purpose |
|---|---|
| `galaxy_game/app/assets/javascripts/monitor.js` | Fix calculateAdaptiveGrid() |
| `galaxy_game/app/services/planetary_map_generator.rb` | Verify grid calculation |

## Acceptance Criteria

- [ ] Luna craters clearly visible in monitor
- [ ] Canvas sizes consistent across body types
- [ ] Grid resolution scales appropriately
- [ ] No performance degradation
- [ ] All celestial bodies render properly

## Implementation Steps

1. Update calculateAdaptiveGrid() to use actual terrain dimensions
2. Prioritize terrainData.width over diameter calculations
3. Add special case adjustments for moons
4. Test Luna terrain rendering
5. Verify Mars and other bodies
6. Check performance
7. Validate all body types

## Commit Instructions

```bash
git add galaxy_game/app/assets/javascripts/monitor.js
git commit -m "fix: terrain grid rendering for small bodies with proper detail"
```