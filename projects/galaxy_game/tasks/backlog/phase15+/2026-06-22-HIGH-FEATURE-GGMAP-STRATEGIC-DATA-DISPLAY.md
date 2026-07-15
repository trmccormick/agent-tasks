---
status: not-started
priority: HIGH
type: feature
mvp_alignment: POST_SNAP (player-facing UI needed when players enter post-Snap)
local_worker_safe: true
created: 2026-06-22
last-updated: 2026-07-11
phase: 15+
---

# GGMap Strategic Data Display Layer

## Context
GGMap strategic layer displays player-relevant planning information: settlement locations, resource deposits, claimed territories, trade routes, AI expansion patterns, infrastructure networks. This layer helps players strategically manage their settlements and understand geopolitical conditions.

Strategic data overlays on top of terrain to show "the game state from a planning perspective."

**Why Phase 15+**: Players do not enter the game until after the Snap crisis (Phase 15+). The strategic layer is player-facing UI needed when players first see the universe — they need to see their settlement network, resource deposits, and territorial control on the planetary map. This is not cosmetic;e — they need to see their settlement network, resource deposits, and territorial control on the planetary map. This is not cosmetic; it's the primary UI for player decision-making once Eden connection is restored and multi-system scope begins.

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

---

## Handoff

### STEP 0 — Lifecycle Rules (MANDATORY)
1. **Move task from review/ → backlog/current/ BEFORE starting work**
2. Update YAML header: `status: not-started` → `status: active`
3. Commit the move to agent-tasks repo before writing any code
4. Do not skip this step — it signals work has begun

### Output Destination
- Synthesis report → `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/summaries/2026-06-22-GGMAP-STRATEGIC-DATA-DISPLAY.md`
- Save synthesis report to agent-tasks repo after implementation

### Files to Review (context only — not for editing)
- `galaxy_game/app/assets/javascripts/surface_view.js` — existing terrain rendering pipeline (4 layers: elevation, liquid, biomes, resources)
- `galaxy_game/app/models/settlement/base_settlement.rb` — settlement location/owner data source
- `data/json-data/resource_deposits/` — resource deposit definitions

### Scope Clarification
This task creates a **strategic overlay canvas** on top of the existing terrain rendering in surface_view.js. It does NOT modify terrain layers — it adds game objects (settlements, resources, zones, routes) as a separate render pass. The PlanetaryViewController needs to expose a `strategicData` JSON endpoint with settlement positions, resource deposit locations, territory control data, and trade route definitions for the JS layer to consume.
