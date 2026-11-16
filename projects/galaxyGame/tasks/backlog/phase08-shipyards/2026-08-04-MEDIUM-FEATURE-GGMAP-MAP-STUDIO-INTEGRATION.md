---
status: backlog
priority: MEDIUM
type: feature
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
created: 2026-08-04
updated: 2026-08-04
estimated_effort: 12 hours
blocker_for:
  - 2026-08-04-MEDIUM-FEATURE-GGMAP-GAME-INTEGRATION
---

# Task: GGMap Map Studio Integration — Layer Editor UI + Feature Placement Tools

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/phase8+/2026-08-04-MEDIUM-FEATURE-GGMAP-MAP-STUDIO-INTEGRATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv docs/new_agent/projects/galaxy_game/tasks/backlog/phase8+/2026-08-04-MEDIUM-FEATURE-GGMAP-MAP-STUDIO-INTEGRATION.md \
         docs/new_agent/projects/galaxy_game/tasks/active/2026-08-04-MEDIUM-FEATURE-GGMAP-MAP-STUDIO-INTEGRATION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave copies in the source folder
  - Verify with: find docs/new_agent/projects/galaxy_game/tasks -name "2026-08-04-MEDIUM-FEATURE-GGMAP-MAP-STUDIO-INTEGRATION.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.
```

## Prerequisites

- Complete Phase 1 first: `2026-08-04-MEDIUM-FEATURE-GGMAP-FORMAT-DEFINITION.md` (schema + sample + spec)
- Complete Phase 2 first: `2026-08-04-MEDIUM-FEATURE-GGMAP-GENERATION-SERVICES.md` (Ruby reader/writer + generators)
- Understand existing Map Studio codebase — find where map editing currently happens

## Context

**Problem**: The .ggmap format and generation services exist but there's no way for humans to edit maps visually. This task builds the Map Studio layer editor UI with feature placement tools.

**Scope for this task**:
- Map Studio integration: Load/display .ggmap files in existing map editor
- Layer-based editor UI: Toggle layers on/off, switch between base/scientific/strategic/terraform/scenario
- Feature placement tools: Add/edit/remove lava tubes, aquifers, settlement sites, worldhouse sites, etc.
- Property editors: Edit feature properties (stability, concentration, priority, etc.)
- Save/load .ggmap files through the UI

**Out of scope**: Game system integration (AI Manager reading strategic layer, terraforming system using terraforming layer — handled in Phase 4).

## Architecture Gotchas

1. **Layer independence in UI**: Each layer should be independently toggleable and editable — editing one layer shouldn't affect others
2. **Non-destructive editing**: Manual edits in the scenario layer should survive regeneration of other layers
3. **Coordinate system consistency**: All feature placement must use the same coordinate system defined in the format spec (equirectangular, grid-based)
4. **Performance with large grids**: 96x48 terrain grids are manageable, but scientific/strategic features are sparse — don't render every cell individually
5. **Integration with existing Map Studio**: Find where map editing currently happens and extend it, don't build a parallel editor

## Files to Create/Modify

| File | Purpose |
|---|---|
| `galaxy_game/app/javascript/map_studio/ggmap_layer_editor.js` | Layer toggle/display UI |
| `galaxy_game/app/javascript/map_studio/ggmap_feature_tools.js` | Feature placement/editing tools |
| `galaxy_game/app/javascript/map_studio/ggmap_property_panel.js` | Feature property editor panel |
| `galaxy_game/app/javascript/map_studio/ggmap_save_load.js` | Save/load .ggmap through UI |
| `galaxy_game/app/controllers/ggmap_controller.rb` | Backend controller for save/load/validation |

## Implementation Steps

### Step 0: Move Task to Active & Verify Synthesis
**PREREQUISITE — Do NOT skip:**
1. Move task from `backlog/phase8+/` → `active/`
2. Update YAML header: `status: backlog` → `status: active`
3. Commit move before writing any code
4. Read Phase 1 format spec and Phase 2 generation services to understand the data model

### Step 1: Map Studio Layer Editor UI

Create layer-based editor that:
- Displays all 5 layers (base, scientific, strategic, terraforming, scenario)
- Each layer has a toggle checkbox (show/hide)
- Layers are rendered in z-order (base at bottom, scenario on top)
- Clicking a feature on any layer selects it and opens the property panel
- Layer colors are configurable per-layer

### Step 2: Feature Placement Tools

Create tools for each feature type:
- **Scientific layer**: Lava tube (circle placement), aquifer (polygon placement), resource deposit (point placement), hazard zone (polygon placement)
- **Strategic layer**: Settlement site (point placement with scoring), expansion zone (polygon placement), infrastructure corridor (polyline placement)
- **Terraforming layer**: Worldhouse site (point placement), ocean basin (polygon placement), biosphere seed region (polygon placement)
- **Scenario layer**: Point of interest (point placement), mission objective (text marker), tutorial marker (icon placement)

Each tool should:
- Show a cursor preview before placing
- Snap to grid coordinates
- Validate against format schema on placement
- Add the feature to the correct layer in the .ggmap data structure

### Step 3: Property Editor Panel

When a feature is selected, show an editable property panel:
- Type-specific properties (e.g., lava tube: stability, accessibility, radiation_shielding)
- Common properties: id, priority, discovery_state, reasoning
- Validation feedback (show errors if property values are out of range)
- "Reset to defaults" button for each feature type

### Step 4: Save/Load Through UI

- **Load**: File picker → validate against schema → display in layer editor
- **Save**: Write .ggmap JSON → validate before writing → show success/error message
- **Regenerate**: Button to regenerate a specific layer (scientific/strategic/terraforming) while preserving manual edits in other layers

### Step 5: Write Tests

```ruby
# spec/controllers/ggmap_controller_spec.rb
describe GgmapController do
  it 'loads a valid .ggmap file'
  it 'saves edited .ggmap and validates round-trip'
  it 'rejects invalid edits with error details'
end
```

## Acceptance Criteria
- [ ] Map Studio can load and display .ggmap files with all 5 layers
- [ ] Each layer is independently toggleable (show/hide)
- [ ] Feature placement tools work for all feature types across all layers
- [ ] Property editor shows correct fields for each feature type
- [ ] Save/load through UI validates against schema
- [ ] Layer regeneration preserves manual edits in other layers
- [ ] Tests pass (RSpec) — 0 failures

## Dependencies
**Blocked by**: `2026-08-04-MEDIUM-FEATURE-GGMAP-FORMAT-DEFINITION.md` (Phase 1), `2026-08-04-MEDIUM-FEATURE-GGMAP-GENERATION-SERVICES.md` (Phase 2)
**Blocks**: `2026-08-04-MEDIUM-FEATURE-GGMAP-GAME-INTEGRATION` (Phase 4: game system integration)
**Related**: `docs/archive/task_archives/GGMAP_FORMAT_DESIGN.md` (source design document)

## Completion Report
**Completed by**:
**Completion date**:
**Final test result**:

### What was created
### Issues discovered
### Follow-up tasks needed
### Lessons learned

## Handoff Summary
HANDOFF SUMMARY: GGMap Map Studio integration complete | Layer editor + feature tools + property panel + save/load UI | Phase 3 of 4 phases
