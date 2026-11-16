[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase5+/2026-06-22-HIGH-FEATURE-GGMAP-STRATEGIC-DATA-DISPLAY.md]
[RATIONALE: UI/monitoring or investigation task — not Luna sim prerequisite, cosmetic/post-MVP]
---
status: not-started
priority: HIGH
type: feature
created: 2026-06-22
last-updated: 2026-06-22
phase: 5+
---

# GGMap Strategic Data Display Layer

## Context
GGMap strategic layer displays player-relevant planning information: settlement locations, resource deposits, claimed territories, trade routes, AI expansion patterns, infrastructure networks. This layer helps players strategically manage their settlements and understand geopolitical conditions.

Strategic data overlays on top of terrain to show "the game state from a planning perspective."

## Problem Statement
Without strategic visualization, players cannot:
- See their settlement network and expansion footprint
- Identify resource deposits and trade opportunities
- Monitor rival AI settlements and expansions
- Plan logistics routes (depot locations, trade routes)
- Visualize territorial control and competition zones

## Scope
- Implement strategic data layer rendering (settlement markers, resource deposits, claimed zones)
- Support multiple data overlays (settlements, resources, trade routes, control zones)
- Integrate with game state (updates as settlements change)
- Interactive elements (click settlement for details, show/hide layers)
- Performance optimized
- Full test coverage

## Target Files
- `galaxy_game/app/views/game/planetary_view/strategic_layer.html.erb` (NEW)
- `galaxy_game/app/assets/javascripts/map/strategic_layer.js` (NEW)
- `galaxy_game/app/assets/stylesheets/map/strategic_layer.scss` (NEW)
- `galaxy_game/app/controllers/game/planetary_view_controller.rb` (modify to expose strategic data)
- `spec/views/game/planetary_view/strategic_layer_spec.rb` (NEW)

## Acceptance Criteria
- [ ] Strategic layer renders settlements with owner distinction (player vs AI)
- [ ] Resource deposits visible and distinguishable by type
- [ ] Territory control zones shown (colored by faction)
- [ ] Trade routes displayable with flow indicators
- [ ] Infrastructure networks (tugs, cyclers, orbital) visible
- [ ] Layer toggleable on/off, with sublayer granularity
- [ ] Interactive (click for settlement details)
- [ ] Performance acceptable for large scale maps
- [ ] RSpec: Full coverage for data exposure and interaction

## Implementation Steps
1. Design data structure for strategic overlays (settlements, resources, zones, routes)
2. Expose strategicData endpoint in PlanetaryViewController
3. Implement GGMap strategic layer rendering in JavaScript
4. Add interactive click handlers (settlement popups)
5. Create layer toggle UI with sublayer options
6. Optimize rendering for large maps
7. Write comprehensive RSpec for all data exposure paths

## Dependencies
- Requires: Settlement model with location and owner data
- Requires: Resource deposit spawning system already implemented
- Requires: Trade route/logistics system defined
- Requires: Planetary view controller and map display system

## Notes
- Strategic layer should use distinct color scheme from scientific layer
- Settlement markers should scale with settlement size
- Control zones can use alpha blending to avoid obscuring terrain
- Interactive popups should show key settlement metrics (population, production)
- Future: Add historical layer (territorial changes over time)
