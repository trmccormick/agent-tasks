---
status: backlog
priority: MEDIUM
type: feature
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
created: 2026-08-04
updated: 2026-08-04
estimated_effort: 6 hours
blocker_for: []
---

# Task: GGMap Game Integration — AI Manager Strategic Layer + Terraforming System + Mission System

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/phase8+/2026-08-04-MEDIUM-FEATURE-GGMAP-GAME-INTEGRATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv docs/new_agent/projects/galaxy_game/tasks/backlog/phase8+/2026-08-04-MEDIUM-FEATURE-GGMAP-GAME-INTEGRATION.md \
         docs/new_agent/projects/galaxy_game/tasks/active/2026-08-04-MEDIUM-FEATURE-GGMAP-GAME-INTEGRATION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find docs/new_agent/projects/galaxy_game/tasks -name "2026-08-04-MEDIUM-FEATURE-GGMAP-GAME-INTEGRATION.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.
```

## Prerequisites

- Complete Phases 1-3 first: format definition, generation services, Map Studio integration
- Understand how AI Manager currently makes settlement/infrastructure decisions
- Understand how terraforming system currently works (TerraformingManager)

## Context

**Problem**: The .ggmap format, generation services, and Map Studio editor exist but the game systems don't use them yet. This task integrates .ggmap into the actual gameplay: AI Manager reads strategic layer for decisions, terraforming system uses terraforming layer, mission system uses scenario layer, monitor view renders all layers.

**Scope for this task**:
- AI Manager integration: Read strategic layer settlement sites, expansion zones, infrastructure recommendations
- Terraforming system integration: Use terraforming layer worldhouse sites and current/target state
- Mission system integration: Use scenario layer mission objectives and tutorial markers
- Monitor view rendering: Display all .ggmap layers in the game's monitor/interface

**Out of scope**: New gameplay mechanics beyond what the .ggmap layers already define. This is an integration task, not a design task.

## Architecture Gotchas

1. **AI Manager should use .ggmap as primary data source**, not recalculate strategic layer — the generation service already did the analysis
2. **Terraforming system needs to track progress** through the terraforming timeline (current_state → target_state)
3. **Mission system needs to discover scenario layer content dynamically** — don't hardcode mission objectives
4. **Monitor view rendering should be performant** — .ggmap layers can be large, use LOD/caching where appropriate
5. **Backward compatibility**: Existing game systems that don't have .ggmap data should still work (graceful degradation)

## Files to Create/Modify

| File | Purpose |
|---|---|
| `app/services/ggmap_ai_manager_integration.rb` | AI Manager reads strategic layer for decisions |
| `app/services/ggmap_terraforming_integration.rb` | Terraforming system uses terraforming layer |
| `app/services/ggmap_mission_integration.rb` | Mission system uses scenario layer |
| `app/javascript/monitor/ggmap_layer_renderer.js` | Monitor view renders .ggmap layers |

## Implementation Steps

### Step 0: Move Task to Active & Verify Synthesis
**PREREQUISITE — Do NOT skip:**
1. Move task from `backlog/phase8+/` → `active/`
2. Update YAML header: `status: backlog` → `status: active`
3. Commit move before writing any code
4. Read Phases 1-3 deliverables to understand the data model

### Step 1: AI Manager Strategic Layer Integration

Modify `AIManager::LogisticsCoordinator` (or create integration service):
- Load .ggmap for the current celestial body on startup
- Extract settlement sites from strategic layer and use as primary settlement candidates
- Use expansion zones to guide AI expansion decisions
- Use infrastructure recommendations to prioritize construction missions
- Update strategic layer confidence score based on actual AI outcomes (learning loop)

### Step 2: Terraforming System Integration

Modify `TerraformingManager` (or create integration service):
- Load terraforming layer from .ggmap on initialization
- Use current_state as the starting point for terraforming simulation
- Use target_state to set long-term terraforming goals
- Use worldhouse_sites as priority targets for construction missions
- Track progress: update current_state values as terraforming progresses
- Update estimated_years based on actual construction pace

### Step 3: Mission System Integration

Modify mission generation system:
- Load scenario layer from .ggmap
- Extract mission_objectives and tutorial_markers as available missions
- Use points_of_interest for discovery-based missions
- Allow AI Manager to generate additional missions based on strategic layer gaps
- Support "custom scenario" mode where player-created scenario layer content is used

### Step 4: Monitor View Rendering

Update monitor/interface JavaScript:
- Render all .ggmap layers in the monitor view
- Layer toggle controls (same as Map Studio)
- Click features to show details (same as Map Studio property panel)
- Performance optimization: LOD rendering for large grids, feature clustering at zoom levels

### Step 5: Write Integration Tests

```ruby
# spec/services/ggmap_ai_manager_integration_spec.rb
describe GgmapAiManagerIntegration do
  it 'uses strategic layer settlement sites as primary candidates'
  it 'updates confidence score based on AI outcomes'
end

# spec/services/ggmap_terraforming_integration_spec.rb
describe GgmapTerraformingIntegration do
  it 'uses terraforming layer current_state as starting point'
  it 'tracks progress toward target_state'
  it 'prioritizes worldhouse_sites for construction missions'
end

# spec/services/ggmap_mission_integration_spec.rb
describe GgmapMissionIntegration do
  it 'extracts mission_objectives from scenario layer'
  it 'generates discovery missions from points_of_interest'
end
```

## Acceptance Criteria
- [ ] AI Manager uses .ggmap strategic layer for settlement/infrastructure decisions
- [ ] Terraforming system uses .ggmap terraforming layer for current/target state
- [ ] Mission system extracts content from .ggmap scenario layer
- [ ] Monitor view renders all .ggmap layers with toggle controls
- [ ] Backward compatibility: existing systems work without .ggmap data
- [ ] Tests pass (RSpec) — 0 failures

## Dependencies
**Blocked by**: `2026-08-04-MEDIUM-FEATURE-GGMAP-FORMAT-DEFINITION.md` (Phase 1), `2026-08-04-MEDIUM-FEATURE-GGMAP-GENERATION-SERVICES.md` (Phase 2), `2026-08-04-MEDIUM-FEATURE-GGMAP-MAP-STUDIO-INTEGRATION.md` (Phase 3)
**Blocks**: None — this is the final phase of GGMap implementation
**Related**: `docs/archive/task_archives/GGMAP_FORMAT_DESIGN.md` (source design document)

## Completion Report
**Completed by**:
**Completion date**:
**Final test result**:

### What was integrated
### Issues discovered
### Follow-up tasks needed
### Lessons learned

## Handoff Summary
HANDOFF SUMMARY: GGMap game integration complete | AI Manager + Terraforming + Mission systems use .ggmap | Monitor view renders all layers | Phase 4 of 4 phases — GGMap implementation complete
