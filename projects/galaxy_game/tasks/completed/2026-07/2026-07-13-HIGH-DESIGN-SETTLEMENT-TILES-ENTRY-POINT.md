---
status: completed
priority: HIGH
type: feature
system_domain: UI
mvp_alignment: OTHER
local_worker_safe: true
created: 2026-07-13
last_updated: 2026-07-30
---

## ⚡ Minimal Handoff (Copy this to send to agent)
```text
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-13-HIGH-DESIGN-SETTLEMENT-TILES-ENTRY-POINT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-13-HIGH-DESIGN-SETTLEMENT-TILES-ENTRY-POINT.md \
         projects/galaxy_game/tasks/active/2026-07-13-HIGH-DESIGN-SETTLEMENT-TILES-ENTRY-POINT.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-13-HIGH-DESIGN-SETTLEMENT-TILES-ENTRY-POINT.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-07-13-FEATURE-SETTLEMENT-TILES-ENTRY-POINT.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Settlement Tiles & SimCity Entry Point Design
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-07-13
**Last Updated**: 2026-07-30

## Context

The three-layer architecture works as follows:
- **Planetary View:** Entire planet at macro level
- **Surface View:** Tactical grid map showing entire settlement region (50×50 tiles, 32px per tile on screen)
- **TerrainForge "Detail View":** Same Surface View rendering, but **zoomed in 10-100x on one settlement tile** to show buildings at full scale

TerrainForge is NOT a separate rendering system — it's the same surface_view.js, just with the camera focused on a single tile and zoomed way in. Buildings that appear as tiny sprites on a settlement tile in Surface View become full-scale buildings when zoomed in to TerrainForge.

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: This is a DESIGN/SCHEMA task, NOT an implementation task.
- ❌ Wrong: Implement settlement tile rendering or camera zoom logic
- ✅ Right: Define the data structure, visual treatment rules, and navigation flow — save implementation for a follow-up task
- Why: The scope of implementing this (rendering, camera system, UI panels) is massive; design first prevents wasted implementation effort

⚠️ **GOTCHA 2**: TerrainForge is NOT a separate rendering layer — it's surface_view.js at a different zoom level.
- ❌ Wrong: Design a new canvas or rendering pipeline for TerrainForge
- ✅ Right: Design settlement tile data that works with the EXISTING surface_view.js rendering, just at higher zoom
- Why: The architecture doc (three_layer_views.md) already established this; don't duplicate work

⚠️ **GOTCHA 3**: Settlement tiles are a NEW concept — no `settlement_tile` or `has_settlement` property exists in the codebase yet.
- ❌ Wrong: Assume settlement tile data structures already exist in terrain_data JSON
- ✅ Right: Design the schema from scratch, including how it integrates into existing terrain_data export
- Why: This is greenfield work; verify what terrain_data currently exports before designing integration

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Settlement Tiles & SimCity Entry Point Design
**Status**: [backlog → active → completed]
**Date**: YYYY-MM-DD

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| terrain_data export code | Understand current JSON structure | [not started / pending / done] |
| surface_view.js | Verify existing rendering pipeline | [not started / pending / done] |
| settlement tile design doc | Output of this task | [not started / pending / done] |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above
- ✅ Know which domain/credentials to use

### Expected Outcomes
[Exact description of what "done" looks like]

### Critical Gotchas I Will Avoid
- ❌ Implementing rendering/camera code — instead ✅ Design data structure and visual rules only
- ❌ Creating a new TerrainForge layer — instead ✅ Use existing surface_view.js at different zoom
- ❌ Assuming settlement tile schema exists — instead ✅ Design from scratch, verify terrain_data export

---

**SYNTHESIS COMPLETE.** Ready to proceed with [PRIORITY 1 / PRIORITY 2 / etc].
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: This is a DESIGN/SCHEMA task, NOT an implementation task.
- ❌ Wrong: Implement settlement tile rendering or camera zoom logic
- ✅ Right: Define the data structure, visual treatment rules, and navigation flow — save implementation for a follow-up task
- Why: The scope of implementing this (rendering, camera system, UI panels) is massive; design first prevents wasted implementation effort

⚠️ **GOTCHA 2**: TerrainForge is NOT a separate rendering layer — it's surface_view.js at a different zoom level.
- ❌ Wrong: Design a new canvas or rendering pipeline for TerrainForge
- ✅ Right: Design settlement tile data that works with the EXISTING surface_view.js rendering, just at higher zoom
- Why: The architecture doc (three_layer_views.md) already established this; don't duplicate work

⚠️ **GOTCHA 3**: Settlement tiles are a NEW concept — no `settlement_tile` or `has_settlement` property exists in the codebase yet.
- ❌ Wrong: Assume settlement tile data structures already exist in terrain_data JSON
- ✅ Right: Design the schema from scratch, including how it integrates into existing terrain_data export
- Why: This is greenfield work; verify what terrain_data currently exports before designing integration

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Settlement Tiles & SimCity Entry Point Design
**Status**: [backlog → active → completed]
**Date**: YYYY-MM-DD

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| terrain_data export code | Understand current JSON structure | [not started / pending / done] |
| surface_view.js | Verify existing rendering pipeline | [not started / pending / done] |
| settlement tile design doc | Output of this task | [not started / pending / done] |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above
- ✅ Know which domain/credentials to use

### Expected Outcomes
[Exact description of what "done" looks like]

### Critical Gotchas I Will Avoid
- ❌ Implementing rendering/camera code — instead ✅ Design data structure and visual rules only
- ❌ Creating a new TerrainForge layer — instead ✅ Use existing surface_view.js at different zoom
- ❌ Assuming settlement tile schema exists — instead ✅ Design from scratch, verify terrain_data export

---

**SYNTHESIS COMPLETE.** Ready to proceed with [PRIORITY 1 / PRIORITY 2 / etc].
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Scope

### Phase 1: Settlement Tile Definition
1. **Settlement Tile Concept**
   - A settlement tile is a Surface View grid cell (32×32px on screen)
   - Special property: `has_settlement: true` in terrain_data
   - Contains array of buildings located on that tile
   - Can have multiple structures stacked (habitat dome + mining station + power plant all on same tile)
   - Size: Not larger than one surface tile (constraint: prevents mega-structures spanning multiple tiles in Phase 5)

2. **Settlement Tile Data Structure**
   ```json
   {
     "col": 50,
     "row": 42,
     "settlement_id": 1,
     "settlement_name": "Jezero Base",
     "faction": "Earth",
     "buildings": [
       {
         "id": 101,
         "type": "habitat",
         "capacity": 50,
         "power_input": 15,
         "status": "operational"
       },
       {
         "id": 102,
         "type": "mining_station",
         "material": "regolith",
         "power_input": 20,
         "production_rate": 100,
         "status": "idle"
       },
       {
         "id": 103,
         "type": "power_plant",
         "fuel_type": "solar",
         "power_output": 50,
         "status": "operational"
       }
     ]
   }
   ```

3. **Settlement Tile Rendering on Surface View**
   - Terrain/biome sprite rendered normally (Layer 0-3)
   - Settlement overlay icon placed on tile (Layer 4)
   - Icon indicates "settlement tile present here" (small dome/tower sprite)
   - Optional: Faction color border or glow
   - Optional: Population indicator (small number badge)

### Phase 2: Navigation Flow
1. **Surface View → TerrainForge Zoom**
   - User clicks or double-clicks settlement tile in Surface View
   - Highlight settlement tile with glow/border to show selection
   - Display tooltip: "Habitat + Mining Station (2 buildings)"
   - Double-click or "zoom" button → Smoothly zoom camera in on that tile (10-100x)
   - Same surface_view.js rendering, just different camera position/zoom level

2. **TerrainForge Detail View (Zoomed In)**
   - Show buildings on this tile at full scale (what was tiny sprite is now large)
   - All layers (terrain, improvements, buildings) scale with zoom
   - Click building to configure operational parameters (production rate, staffing, etc.)
   - Drag-place new structures on the tile or rearrange existing ones
   - Same improvement system (roads, pipelines) just zoomed in

3. **Return to Surface View**
   - Zoom out / press ESC → Camera smoothly zooms back out
   - Return to Surface View with same camera position
   - Reflect any parameter changes made to buildings (production rate, staffing, inventory)

### Phase 3: Settlement Tile Markers & Overlays
1. **Visual Distinction**
   - Settlement tiles should be visually distinct from empty tiles
   - Options:
     - Colored background overlay (20% opacity faction color)
     - Small icon in corner (habitat dome, tower, etc.)
     - Glow effect around tile border
     - Text label showing settlement name (toggle-able)

2. **Information Display**
   - On hover: Show settlement name + building count
   - On click: Show detail panel (3-5 line text box):
     - Settlement name
     - Faction/owner
     - Population / capacity
     - Power status (produced vs. consumed)
     - Main production (mining regolith, generating power, etc.)

3. **Layer Toggle for Settlements**
   - Add "Show Settlements" layer toggle (can be disabled to see bare terrain)
   - Independent from unit/improvement layers

### Phase 4: Multiple Settlements on Region
1. **Handling Multiple Settlements**
   - Each Surface View region (50×50 tiles) might have 3-5 settlements
   - Each settlement tile independently accessible
   - Settlement overlays don't block neighboring tiles
   - UI makes it clear which tile is selected (glow/highlight)

2. **Settlement Navigation**
   - Double-click tile → enter SimCity for that tile
   - Right-click settlement → show context menu ("Manage", "Rename", "Delete", etc.)
   - Keyboard navigation: Tab to cycle through nearby settlements

### Phase 5: Data Export for TerrainForge
TerrainForge uses **the same terrain_data JSON as Surface View** — no separate export needed. Both views render the same data, just at different zoom levels.

1. **Settlement Tile Data in terrain_data JSON**
   ```json
   {
     "settlements": [
       {
         "settlement_id": 1,
         "col": 50,
         "row": 42,
         "name": "Jezero Base",
         "faction_id": 1,
         "buildings": [ ...building array... ]
       }
     ]
   }
   ```

2. **Building Definition Schema**
   - Each building has: type, status, power_input/output, material (if applicable), capacity, queue
   - Store all needed state that surface_view.js needs whether zoomed in or out

## Acceptance Criteria
- [ ] Settlement tile concept clearly defined (single grid cell, contains array of buildings)
- [ ] Settlement tile data structure designed and documented
- [ ] Settlement tiles render as distinct overlay on Surface View (icon, color, or glow)
- [ ] Settlement tile hover/click shows info panel (name, building count, power status)
- [ ] Double-click settlement tile → transitions to TerrainForge view
- [ ] TerrainForge shows all buildings on that tile with configuration UI
- [ ] Back button returns to Surface View at same camera position
- [ ] Multiple settlements visible on same region without visual conflict
- [ ] Layer toggle for settlements works independent of other layers
- [ ] No performance impact from settlement overlays
- [ ] settlement_grid or settlements array properly exported in terrain_data JSON

## Blockers
- Depends on Surface View rendering foundation (Layers 0-3 working)
- Requires Building model definition in Ruby
- Requires TerrainForge detail view framework (separate layer, not this task's responsibility)

## Dependencies
- **Related**: 2026-07-13-HIGH-FEATURE-CIV4-SURFACE-VIEW-GAMEPLAY (settlement tiles part of Civ4 UI)
- **Related**: 2026-07-13-HIGH-FEATURE-UNIT-LAYER-RENDERING (buildings != units, different data)
- **Future**: TerrainForge detail view (consumes this data structure)

## Notes
- Settlement tiles are localized containers; prevents sprawling mega-bases across 10+ tiles
- Buildings within a settlement tile can be connected (roads, pipelines) to each other
- Cross-tile connections (roads, pipelines between settlements) handled by Surface View improvements layer
- One settlement per tile or multiple buildings per tile? → Multiple buildings per tile (more interesting gameplay)
- **TerrainForge is NOT a separate layer** — it's surface_view.js zoomed in on one settlement tile
- Same rendering pipeline handles both Surface View and TerrainForge; just different camera position/zoom
- Implementation note: Can use same canvas + camera system, just change viewport and scale when user zooms in

## Next Steps
1. Finalize settlement tile data structure with Ruby developer
2. Design settlement tile visual treatment (which overlay style works best)
3. Implement settlement_grid or settlements array export in terrain_data builder
4. Add settlement overlay rendering to surface_view.js
5. Implement settlement tile click detection and info panel
6. Design TerrainForge entry/exit transition
7. Test with sample 3-settlement region

---

## Stop Conditions — escalate to user immediately if:
- Existing terrain_data export already has settlement tile data that this task would duplicate
- surface_view.js rendering pipeline has changed significantly since this task was created and the zoom/camera model is no longer viable
- The three-layer views architecture doc (Phase 3) has been superseded by a different approach

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was changed
- `[file/dir]` — [description of change]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
