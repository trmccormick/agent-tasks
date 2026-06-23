---
status: not-started
priority: HIGH
type: feature
created: 2026-06-22
last-updated: 2026-06-22
phase: 5+
---

# GGMap Scientific Data Display Layer

## Context
GGMap (Galaxy Game Map) is the planetary surface visualization system. The scientific layer displays geological and environmental data: terrain elevation, biome distribution, atmospheric composition, temperature, radiation, seismic activity, water presence. This layer is essential for player understanding of planetary conditions before settlement placement.

Players use the scientific layer to scout locations, understand environmental challenges, and plan settlements accordingly.

## Problem Statement
Without scientific data visualization, players cannot:
- Identify biome boundaries and types
- Assess environmental hazards (radiation, extreme temperatures)
- Locate water resources and geological features
- Plan settlement placement based on conditions
- Understand why certain locations are suitable/unsuitable

## Scope
- Implement scientific data layer rendering (biome colors, elevation heatmap, hazard zones)
- Support multiple data overlays (selectable by player)
- Integrate with existing terrain/world data
- Responsive to world state changes (as world develops)
- Performance optimized for large planetary maps
- Full test coverage

## Target Files
- `galaxy_game/app/views/game/planetary_view/scientific_layer.html.erb` (NEW)
- `galaxy_game/app/assets/javascripts/map/scientific_layer.js` (NEW)
- `galaxy_game/app/assets/stylesheets/map/scientific_layer.scss` (NEW)
- `galaxy_game/app/controllers/game/planetary_view_controller.rb` (modify to expose scientific data)
- `spec/views/game/planetary_view/scientific_layer_spec.rb` (NEW)

## Acceptance Criteria
- [ ] Scientific layer renders terrain elevation as heatmap
- [ ] Biome distribution visible with distinct colors
- [ ] Hazard zones highlighted (radiation, temperature extremes)
- [ ] Water resources/aquifers marked
- [ ] Layer toggleable on/off
- [ ] Performance acceptable for 2048x2048 maps (< 500ms render)
- [ ] Updates reactively as world state changes
- [ ] RSpec: Full coverage for data exposure and rendering

## Implementation Steps
1. Design data structure for scientific overlays (elevation, biome, hazards, resources)
2. Expose scientificData endpoint in PlanetaryViewController
3. Implement GGMap scientific layer rendering in JavaScript
4. Create toggle UI and styling
5. Optimize rendering for large maps (tile-based or WebGL if needed)
6. Write comprehensive RSpec for all data exposure paths

## Dependencies
- Requires: Existing terrain/world model with biome and hazard data
- Requires: Planetary view controller and map display system already working
- Requires: Material/resource system for water resource rendering

## Notes
- Layer should complement strategic layer (not overlap)
- Consider accessibility (colorblind-friendly hazard colors)
- Elevation heatmap should be configurable (logarithmic vs linear scale)
- Future: Add historical layer (how conditions have changed over time)
