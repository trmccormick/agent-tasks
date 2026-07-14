---
title: "Sprite Tiles → Surface View Integration"
date: 2026-07-13
priority: HIGH
type: FEATURE
status: backlog
phase: UI
assigned_to: qwen
---

## Summary
Integrate the 45 newly extracted sprite tiles (138×145px, organized by terrain type) into the surface view rendering system. Determine tile loading mechanism, asset pipeline integration, and procedural variant selection logic.

## Context
- **45 sprite tiles extracted** from original sprite sheet (1536×1024)
- **Organized by terrain family**: dust, frozen, regolith, temperate, volcanic
- **9 variants per terrain type**: variant_01.png through variant_09.png
- **Dimensions**: 138×145 pixels (PNG, 8-bit sRGB)
- **Location**: `/Users/tam0013/Documents/git/galaxyGame/data/images/terrain/`
- **Status**: Ready for integration; not git-tracked (in data/ .gitignore)

## Scope
1. **Asset Pipeline Integration**
   - Load sprite tiles into Rails asset pipeline or external asset system
   - Determine storage strategy (public/images vs. app/assets/images vs. config)
   - Handle tile access/loading in views

2. **Surface View Tile Selection**
   - Map terrain type + variant selection to tile files
   - Create tile selector logic (random, seeded, or deterministic based on coordinates)
   - Support dynamic variant switching on terrain change

3. **Rendering Integration**
   - Update surface view partial/component to render tiles
   - Handle tile sizing and positioning in grid layout
   - Test tile display in surface view context

4. **Testing**
   - Verify all 45 tiles load without errors
   - Test terrain-to-tile mapping (all 5 types + variants)
   - Ensure no broken image links in rendered view

## Acceptance Criteria
- [ ] All 45 tiles accessible via asset pipeline
- [ ] Surface view displays tiles by terrain type
- [ ] Tile variant selection working (at least one selector pattern)
- [ ] No broken image references in rendered HTML
- [ ] RSpec specs added for tile loading and view rendering
- [ ] Surface view UI updates documented

## Files to Investigate
- `galaxy_game/app/views/surface_views/_surface.html.erb` (or equivalent surface rendering partial)
- Asset pipeline configuration (config/initializers or config/assets.rb)
- Current terrain property/enum definitions
- Surface view controller

## Notes
- UI phase is undefined—this task establishes tile integration pattern for future UI work
- Sprite tiles are world-agnostic (color/texture only, no world-specific identifiers)
- Variant naming is simple (variant_01–09) to enable straightforward selection logic
- No git-tracking of tile data files (correct behavior—data/ in .gitignore)

## Next Steps
1. Move task to active/ and begin investigation
2. Document current surface view rendering approach
3. Design tile loading/selection strategy
4. Implement and test with subset of tiles first
5. Report findings and blockers
