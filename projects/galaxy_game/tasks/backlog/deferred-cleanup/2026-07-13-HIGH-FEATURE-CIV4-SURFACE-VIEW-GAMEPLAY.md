---
status: active
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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-13-HIGH-FEATURE-CIV4-SURFACE-VIEW-GAMEPLAY.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-13-HIGH-FEATURE-CIV4-SURFACE-VIEW-GAMEPLAY.md \
         projects/galaxy_game/tasks/active/2026-07-13-HIGH-FEATURE-CIV4-SURFACE-VIEW-GAMEPLAY.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-13-HIGH-FEATURE-CIV4-SURFACE-VIEW-GAMEPLAY.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-07-13-FEATURE-CIV4-SURFACE-VIEW-GAMEPLAY.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Civ4-Style Surface View Gameplay Layer
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-07-13
**Last Updated**: 2026-07-30

## Context

Current surface_view.js is **pure rendering** (read-only visualization). Civ4 gameplay requires **interactive mechanics**:
- Click tiles to inspect elevation/biome/resources/yields
- See city control radius and citizen allocation
- Toggle terrain improvement layers (roads, farms, mines)
- Drag-select units for movement
- Place buildings/infrastructure with visual feedback
- Display production/yield information per tile

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: This task is a FEATURE implementation, NOT a design task — unlike the settlement tiles task which was design-only.
- ❌ Wrong: Treat this as a design document and defer implementation
- ✅ Right: Implement the interactive mechanics in surface_view.js (tile click detection, overlay rendering, unit selection)
- Why: This task describes gameplay mechanics that need actual code; the design decisions are already made

⚠️ **GOTCHA 2**: surface_view.js currently has NO interaction layer — all rendering is read-only. Adding interactivity requires a complete architectural addition.
- ❌ Wrong: Assume existing rendering pipeline handles click detection or tile selection
- ✅ Right: Add an entirely new interaction layer on top of the existing render loop (click detection, state management, overlay rendering)
- Why: The current codebase has no precedent for interactive tiles — this is greenfield work

⚠️ **GOTCHA 3**: `yield_grid` and `improvements` arrays don't exist in terrain_data JSON yet.
- ❌ Wrong: Assume yield/improvement data is already exported by the Ruby terrain_data builder
- ✅ Right: Verify what terrain_data currently exports; design the Ruby-side export if needed before implementing JS rendering
- Why: The JS layer depends on correct server-side data; don't implement rendering for data that doesn't exist

⚠️ **BLOCKER CORRECTION (2026-08-02)** — Do NOT re-investigate whether City/Improvement/Yield models exist.
Tracy confirmed the underlying game concepts already exist under different names:
  - "City" → `Settlement::BaseSettlement` (tile-level) + `Colony` (governance layer over multiple settlements). No new model needed.
  - "Farms" (improvement) → existing `Structures` with functions (e.g., greenhouse structure behaving like a farm). No new model needed.
  - "Mines" (improvement) → excavated geological feature (`CelestialBodies::Features::ExcavatedCavity` / `BaseFeature`) or a Unit doing excavation (e.g., `hollowing_equipment.json`). No new model needed — a mine is interaction with an existing feature/unit, not a distinct game concept.
  - "Roads" (improvement) → **genuinely does NOT exist** anywhere in the game yet. This is a design question (does Galaxy Game want tile-connectivity/logistics-network mechanics at all?), not an implementation task. Parked as an open design question, not scoping this further.
  - The real blocker for terrain_data_builder.rb is **exposing existing Structure/Unit/Feature per-tile data** through the builder's export — a data-export task, much smaller than originally scoped. NOT missing game models.

⚠️ **DEFERRED (not blockers)**:
  - Geological features (`BaseFeature`) have a `coordinates` accessor returning lat/long format (`/\A\d+\.\d+°[NS]\s+\d+\.\d+°[EW]\z/`). A lat/long-to-tile-grid mapping helper is expected at this stage but not needed yet — game hasn't progressed far enough. Deferred, not a gap.
  - Planetary/Surface/TerrainForge views are the same underlying data at different scales/zoom, not three separate systems needing translation.

---

## REQUIRED Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Civ4-Style Surface View Gameplay Layer
**Status**: [backlog → active → completed]
**Date**: YYYY-MM-DD

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| surface_view.js | Add interaction layer (click detection, overlays) | [not started / pending / done] |
| terrain_data builder (Ruby) | Verify yield_grid/improvements export | [not started / pending / done] |
| spec files | Add specs for new gameplay mechanics | [not started / pending / done] |

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
- ❌ Assuming yield_grid/improvements data exists — instead ✅ Verify terrain_data export first
- ❌ Modifying existing rendering pipeline — instead ✅ Add interaction layer on top
- ❌ Implementing without specs — instead ✅ Write RSpec specs for click detection, overlay rendering, selection logic

---

**SYNTHESIS COMPLETE.** Ready to proceed with [PRIORITY 1 / PRIORITY 2 / etc].
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Scope

### Phase 1: City Overlay Layer
1. **Settlement Radius Visualization**
   - Draw semi-transparent overlay showing city control area (typically 2-3 tiles radius)
   - Different colors per faction/settlement type
   - Toggle on/off with layer button

2. **Tile Yield Display**
   - Show food/production/science/culture per tile (from yield_data JSON)
   - Overlay small numbers or icons on each tile
   - Highlight high-yield tiles during city planning

3. **Citizen Allocation Indicator**
   - Visual marker showing which tiles are being worked by settlement
   - Different icon/color for allocated vs. available tiles

### Phase 2: Terrain Improvements & Infrastructure
1. **Improvement Sprites Layer**
   - Generic road/rail/pipeline icons (small overlays, don't obscure terrain)
   - Farm/irrigation markers
   - Mine indicators
   - Power generation markers (solar, geothermal, wind)
   - Position determined by Building model in terrain_data.improvements array (NEW)

2. **Improvement Visibility Toggle**
   - Separate layer control: "Show Improvements"
   - Can be toggled independent of units/biomes

### Phase 3: Tile Interaction & UI
1. **Click-to-Inspect**
   - Click tile → show detail panel: elevation, biome, resources, yields, current occupant
   - Display panel location: side panel or hover tooltip
   - Show resource amounts (water, minerals, rare elements)

2. **Tile Selection State**
   - Visual highlight for selected tile (border, subtle glow)
   - Persist selection until clicking another tile or ESC

3. **Building Placement Preview**
   - Drag-select building type from UI
   - Preview placement on hover (ghost sprite with transparency)
   - Click to confirm placement (if allowed)
   - Show validation feedback (OK/not allowed due to terrain/conflicts)

### Phase 4: Unit Movement & Orders
1. **Unit Selection & Movement Path**
   - Click unit → show movement range overlay (pathfinding via shortest-path algorithm)
   - Highlight tiles unit can reach with current fuel/movement
   - Drag to move or click destination (Civ4-style)

2. **Order Indicators**
   - Show current unit orders as overlay text/icons
   - "Mining" / "Moving" / "Idle" states visible on map

### Phase 5: Resource Node Interactivity
1. **Resource Detail on Hover**
   - Show resource type/amount when hovering over resource node
   - Click to filter map view (show only water, only minerals, etc.)

2. **Harvestable Resource Queue**
   - Visual indicator when a resource is queued for extraction
   - Countdown or progress bar overlay

## Data Requirements

**New terrain_data fields:**
```json
{
  "city_overlays": [
    {
      "settlement_id": 1,
      "center_col": 50,
      "center_row": 40,
      "radius": 3,
      "faction": "Earth",
      "worked_tiles": [[50,40], [51,40], [50,41]]
    }
  ],
  "improvements": [
    {
      "col": 51,
      "row": 40,
      "type": "road",
      "direction": "horizontal"
    },
    {
      "col": 52,
      "row": 41,
      "type": "mine",
      "material": "regolith"
    }
  ],
  "yield_grid": [[{food: 2, prod: 1}, {food: 1, prod: 3}]],  // or null per tile
  "unit_orders": [
    {
      "unit_id": 5,
      "order": "mining",
      "target_col": 55,
      "target_row": 42,
      "time_remaining": 120
    }
  ]
}
```

## Acceptance Criteria
- [ ] City overlay renders with faction-specific colors
- [ ] Tile yields (food/prod/science) display when improvement layer enabled
- [ ] Click tile → detail panel shows elevation, biome, resources, yields
- [ ] Building/improvement icons render without obscuring terrain
- [ ] Improvements toggle on/off via layer control
- [ ] Unit movement range visible on unit selection
- [ ] Resource nodes show type/amount on hover
- [ ] Tile selection state persists and visually highlights
- [ ] Building placement preview (ghost sprite) works on hover
- [ ] Unit order status ("Mining", "Moving") visible as overlay
- [ ] No performance regression from additional overlays
- [ ] RSpec specs cover city overlay, improvement rendering, selection logic

## Blockers
- Depends on terrain + unit sprite rendering working first
- Requires yield_data builder in Ruby (may not exist yet)
- Unit/Building data must be exported in terrain_data JSON
- Requires pathfinding algorithm for movement range calculation

## Dependencies
- **Upstream**: 2026-07-13-HIGH-FEATURE-SPRITE-TILES-SURFACE-VIEW-INTEGRATION
- **Upstream**: 2026-07-13-HIGH-FEATURE-UNIT-LAYER-RENDERING
- **Related**: Civ4 UI reference (see docs/reference/civ4_gameplay_mechanics.md if it exists)

## Notes
- Civ4 uses isometric view; confirm tile selection logic works with current orthographic map
- Resource node harvesting may require separate Civ4-style "right-click → queue action" workflow
- Consider accessibility: ensure all overlays can be toggled for clarity
- This is the "gameplay feel" layer — transforms surface_view from static map to interactive strategic interface

## Next Steps
1. Design city overlay data structure (settlement radius, faction colors, worked tiles)
2. Implement yield_grid builder in terrain_data generator
3. Add improvements array to terrain_data export
4. Implement city overlay rendering pass
5. Add tile click detection and detail panel
6. Test with sample settlement + improvements

## Stop Conditions
- Stop if terrain_data JSON doesn't export any of: `city_overlays`, `improvements`, `yield_grid`, `unit_orders` — report blocker before implementing JS layer
- Stop if surface_view.js rendering pipeline has no canvas event listeners for click/hover — report blocker before adding interaction code
- Stop if upstream dependencies (sprite tiles, unit layer) are not yet merged — do not proceed

## Completion Report

When done, provide:
1. **Files modified**: List all files changed with brief description
2. **New files created**: List any new files
3. **Data contract changes**: What terrain_data fields were added/modified (Ruby side)
4. **Test coverage**: Which RSpec specs were added and what they cover
5. **Performance notes**: Any performance impact from overlay rendering
6. **Known limitations**: What was NOT implemented or deferred

## Handoff Summary

**Task**: Civ4-Style Surface View Gameplay Layer
**Status**: backlog → active → completed
**Type**: feature (greenfield interaction layer on top of read-only renderer)
**Key Risk**: terrain_data Ruby builder may not export required fields (`yield_grid`, `improvements`, `city_overlays`, `unit_orders`) — verify first, report blocker if missing
**Approach**: Add new interaction layer (not modify existing renderer), implement phases 1-5 sequentially, write specs alongside code
