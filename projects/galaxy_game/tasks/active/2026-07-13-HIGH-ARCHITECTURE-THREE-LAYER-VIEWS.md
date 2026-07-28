---
title: "Three-Layer View Architecture & Integration"
date: 2026-07-13
priority: HIGH
type: ARCHITECTURE
status: active
phase: UI
assigned_to: qwen
depends_on: null
---

## Summary
Define the complete rendering and interaction architecture for celestial bodies: Planetary View (SimEarth), Surface View (Civ4), and TerrainForge Detail View (SimCity). Establish data flow, zoom hierarchy, and layer separation rules.

## Context
Galaxy Game UI needs **three distinct operational levels** rather than monolithic surface map:

1. **Planetary View** — See entire world, macro weather, biome distribution
2. **Surface View** — Tactical settlement management (Civ4-style map)
   - Example: See Valles Marineris has a settlement tile (marker icon)
3. **TerrainForge Layer** — Same Surface View rendering, zoomed in on one settlement tile
   - Example: Click settlement on Valles Marineris → zoom in → see worldhouse structure, buildings inside/around it, roads, power infrastructure at readable scale
   - **NOT a separate rendering system** — same surface_view.js, just camera zoomed in

Current work focuses exclusively on **Surface View foundation**. This task defines how all three layers fit together and avoid scope creep into planetary concerns.

## Scope

### Phase 1: Architecture Definition
1. **Planetary View Specification**
   - Render planet as 3D globe or 2D flattened projection
   - Display atmospheric layers, cloud formations, weather systems
   - Show biome distribution heatmap
   - Allow rotation, zoom trigger to Surface View
   - Integration point: Click region → zoom to Surface View at that location
   - **Out of scope for Surface View work**

2. **Surface View Specification (PRIMARY FOCUS)**
   - Isometric or orthographic grid map (Civ4 standard)
   - Tactical-level settlement management
   - Resource/improvement display and interaction
   - Unit movement and orders
   - City overlay and yield management
   - Layer toggles for clarity
   - **IN SCOPE** — Current tasks define this layer

3. **TerrainForge Detail View Specification**
   - **Same rendering system as Surface View, but zoomed in on one settlement tile**
   - Zoom in 10-100x on a single settlement tile to show buildings at full scale
   - Shows all buildings/structures contained on that settlement tile (same sprites, larger)
   - Click building to configure operational parameters (production rate, staffing, inventory)
   - Drag-place new structures or rearrange on the tile
   - Connect pipelines/roads between buildings on this tile (same improvement system)
   - Uses same layer rendering (terrain, biomes, improvements, units) just zoomed
   - Double-click settlement tile in Surface View → TerrainForge at that location
   - Scroll out/zoom out → return to Surface View
   - **Not a separate rendering engine; just a camera zoom into Surface View**

### Phase 2: Data Flow & Zoom Hierarchy
1. **Zoom Navigation**
   ```
   Planetary View (overview)
     ├─ Rotation/pan on globe
     └─ Click region / "drill down" button
          ↓
     Surface View (settlement region)
        ├─ Pan/zoom within region
        ├─ Layer toggles (terrain, improvements, units, settlements)
        └─ Double-click SETTLEMENT TILE / "enter settlement" button
             ↓
          TerrainForge View (detail of that settlement tile)
             ├─ View all buildings on this tile
             ├─ Click building to configure
             └─ Close / "back to map" button
                  ↑
             Surface View (return)
   ```

2. **Context Preservation**
   - When zooming from Planetary → Surface, remember clicked location
   - Center Surface View camera on that region
   - When entering TerrainForge, pause Surface View time (or freeze camera)
   - When exiting TerrainForge, resume Surface View with same camera position

### Phase 3: Layer Separation Rules
1. **What Each Layer IS Responsible For**
   - **Planetary:** Global atmospheric effects, biome heatmap, weather visualization, macro events
   - **Surface/TerrainForge (same rendering):** Terrain rendering, improvements (roads/farms/mines), units/vehicles, settlement tiles, Civ4 gameplay
   - **Note:** TerrainForge IS Surface View zoomed in on one settlement tile — same rendering pipeline, different camera

2. **What Each Layer IS NOT Responsible For**
   - **Planetary:** Do NOT include settlement-level detail (tiles, improvements, units, settlement tiles)
   - **Surface/TerrainForge:** Do NOT include planetary atmosphere

3. **Data Ownership**
   - Planetary: planet_data, atmosphere_layers, weather_grid, biome_distribution
   - Surface + TerrainForge: **SAME terrain_data** (elevation, biomes, resources, improvements, units, settlement tiles)
   - **Difference: camera zoom and viewport focus only**
1. **Planetary → Surface Zoom**
   - User clicks region on planet
   - Determine which Surface View tile region that corresponds to
   - Initialize Surface View with camera centered on region
   - Smoothly transition (fade or zoom animation)

2. **Surface → TerrainForge (Enter Settlement Tile)**
   - User double-clicks settlement tile on Surface grid
   - Settlement tile is marked with special overlay (icon, glow, color)
   - Fetch buildings on this settlement tile from terrain_data
   - Initialize TerrainForge view showing all buildings on THIS tile
   - Pause Surface View time (optional: continue or freeze)

3. **TerrainForge → Surface Return (Exit Settlement Tile)**
   - User closes TerrainForge (back button or ESC)
   - Resume Surface View at same camera position
   - Reflect any parameter changes made to buildings (production rate, staffing, etc.)
   - Settlement tile still shows same overlay/marker

### Phase 5: Rendering Technology Considerations
1. **Planetary View Rendering**
   - Options: Three.js (3D globe), Babylon.js, or 2D canvas with projection
   - Atmosphere shader for visual effect
   - Cloud layer animation
   - Biome color overlay with blending

2. **Surface View Rendering**
   - 2D canvas (existing surface_view.js)
   - Orthographic or isometric projection
   - Sprite-based rendering (terrain, improvements, units)
   - Viewport culling for performance

3. **TerrainForge Rendering**
   - 2D or 3D depending on building visual complexity
   - Interactive connection editing (drag lines between ports)
   - Real-time visualization of power/material flow

## Acceptance Criteria
- [ ] Complete specification document for all three layers (architecture, data flow, responsibilities)
- [ ] Zoom hierarchy flow chart defined and documented
- [ ] Layer separation rules clearly stated (what each layer does/doesn't do)
- [ ] Integration points defined (zoom transitions, data synchronization)
- [ ] Data ownership clarified (which layer owns which terrain_data fields)
- [ ] Rendering technology recommendations documented
- [ ] Scope boundaries clearly marked (prevents Surface View tasks from creeping into planetary/terrain-forge)
- [ ] Surface View layer fully documented (current state + in-progress tasks)

## Blockers
- None — this is pure architecture/specification work

## Dependencies
- None — foundational document
- **Informs**: All current and future view layer work

## Notes
- Planetary View is out of scope for current surface_view.js improvements
- **TerrainForge IS NOT separate work** — it's surface_view.js with camera zoomed on one tile
- Example: Valles Marineris worldhouse
  - Surface View: See settlement marker on Valles Marineris tile
  - Click/zoom into tile: TerrainForge shows worldhouse structure, buildings, roads, power infrastructure at readable scale
  - Manage: Place structures, assign units, configure production
  - Zoom out: Return to Surface View
- This document serves as scope boundary enforcement
- Should be referenced in all UI-related task discussions

## Next Steps
1. Create detailed specification document for each layer
2. Define exact data structures for layer transitions
3. Document camera positioning/interpolation for smooth zooming
4. Create mockups/wireframes for each layer
5. Identify any shared systems (e.g., sprite cache used by multiple layers)
6. Plan rendering performance budget for each layer

---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-13-HIGH-ARCHITECTURE-THREE-LAYER-VIEWS.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-13-HIGH-ARCHITECTURE-THREE-LAYER-VIEWS.md \
         projects/galaxy_game/tasks/active/2026-07-13-HIGH-ARCHITECTURE-THREE-LAYER-VIEWS.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-13-HIGH-ARCHITECTURE-THREE-LAYER-VIEWS.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-ARCHITECTURE-THREE-LAYER-VIEWS.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Three-Layer View Architecture & Integration
**Status**: BACKLOG
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-07-13
**Last Updated**: 2026-07-27

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context
Galaxy Game UI needs **three distinct operational levels** rather than monolithic surface map. This task defines the complete rendering and interaction architecture for Planetary View, Surface View, and TerrainForge Detail View — establishing data flow, zoom hierarchy, and layer separation rules to prevent scope creep.

**Current state**: `surface_view.js` exists at `galaxy_game/app/assets/javascripts/surface_view.js`. The Surface View Implementation Plan exists at `docs/developer/SURFACE_VIEW_IMPLEMENTATION_PLAN.md`. This task builds on that foundation.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `docs/developer/SURFACE_VIEW_IMPLEMENTATION_PLAN.md` — current Surface View state

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1: TerrainForge is NOT a separate rendering engine**
- ❌ Wrong: Build a new 3D/2D renderer for TerrainForge
- ✅ Right: Same `surface_view.js` rendering pipeline, just camera zoomed 10-100x on one settlement tile
- Why: The task explicitly states "NOT a separate rendering system" — it's a camera state change, not a new subsystem

⚠️ **GOTCHA 2: Planetary View is out of scope for current work**
- ❌ Wrong: Implement planetary globe rendering in this task
- ✅ Right: Define the interface contract (click region → zoom to Surface View) but do NOT implement it
- Why: Planetary View is a future task. This architecture doc must define boundaries, not implementations

⚠️ **GOTCHA 3: Surface + TerrainForge share terrain_data**
- ❌ Wrong: Create separate data structures for each view layer
- ✅ Right: Single `terrain_data` object — difference is camera zoom and viewport focus only
- Why: Data consistency across zoom levels is critical; duplicating data causes sync bugs

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Three-Layer View Architecture & Integration
**Status**: [backlog → active → completed]
**Date**: YYYY-MM-DD

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `galaxy_game/app/assets/javascripts/surface_view.js` | Current Surface View implementation | [not started / pending / done] |
| `docs/developer/SURFACE_VIEW_IMPLEMENTATION_PLAN.md` | Existing Surface View spec | [not started / pending / done] |
| Output: docs/new_agent/projects/galaxy_game/architecture/three_layer_views.md | New architecture doc | [pending] |

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
- ❌ Building a separate TerrainForge renderer — instead ✅ Same surface_view.js with camera zoom
- ❌ Implementing Planetary View — instead ✅ Defining interface contract only
- ❌ Creating duplicate terrain_data structures — instead ✅ Single shared data object

---

**SYNTHESIS COMPLETE.** Ready to proceed with [PRIORITY 1 / PRIORITY 2 / etc].
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement
The UI lacks a documented architecture for three operational levels (Planetary, Surface, TerrainForge). Without this spec, future tasks risk scope creep into Planetary View or building duplicate rendering systems for TerrainForge.

**Current behavior**: No architecture doc exists defining layer boundaries, data flow, or zoom hierarchy.

**Expected behavior**: A complete architecture specification that:
- Defines all three layers and their responsibilities
- Establishes zoom navigation flow
- Documents layer separation rules (what each layer IS/IS NOT responsible for)
- Clarifies data ownership
- Prevents scope creep in future tasks

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `docs/new_agent/projects/galaxy_game/architecture/three_layer_views.md` (NEW) | Output: complete architecture spec | N/A |
| `galaxy_game/app/assets/javascripts/surface_view.js` | Current Surface View implementation — read for context | All |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `docs/developer/SURFACE_VIEW_IMPLEMENTATION_PLAN.md` | Existing Surface View spec — understand current state |
| `galaxy_game/app/services/star_sim/automatic_terrain_generator.rb` | Terrain data source — understand terrain_data structure |
| `galaxy_game/app/models/celestial_body.rb` | Celestial body model — understand planet/satellite data |

### Migration (if needed)
- [x] No migration needed — this is pure architecture/documentation work

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
# From inside agent-tasks repo root:
git mv projects/galaxy_game/tasks/backlog/current/2026-07-13-HIGH-ARCHITECTURE-THREE-LAYER-VIEWS.md \
       projects/galaxy_game/tasks/active/2026-07-13-HIGH-ARCHITECTURE-THREE-LAYER-VIEWS.md
```

Then open the moved file and change the YAML status field:
```
status: backlog  →  status: active
```

Then verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-07-13-HIGH-ARCHITECTURE-THREE-LAYER-VIEWS.md"
```

**Paste the output of the find command in chat before proceeding.**

### Step 1 — Read Current Surface View Implementation

Read `galaxy_game/app/assets/javascripts/surface_view.js` to understand:
- Current rendering pipeline (canvas, sprites, camera)
- Existing zoom/pan capabilities
- Current terrain_data structure usage
- Layer toggling if any exists

Read `docs/developer/SURFACE_VIEW_IMPLEMENTATION_PLAN.md` for existing Surface View specs.

### Step 2 — Define Planetary View Contract (Out of Scope Implementation)

Document the Planetary View layer as a **specification only** — no implementation:
- Render options (3D globe vs 2D projection)
- Data needed from celestial bodies (atmosphere_layers, biome_distribution, weather_grid)
- Interface contract: click region → zoom to Surface View at that location
- Explicitly mark as OUT OF SCOPE for current work

### Step 3 — Define Surface View Architecture (PRIMARY FOCUS)

Document the Surface View layer with implementation-ready detail:
- Isometric/orthographic grid map specification
- Layer system (terrain, improvements, units, settlements)
- Camera zoom levels and viewport culling strategy
- Data flow: how terrain_data flows from backend → surface_view.js
- Integration points with existing `surface_view.js`

### Step 4 — Define TerrainForge Detail View Architecture

Document TerrainForge as **camera zoom into Surface View** (not a new renderer):
- Zoom range: 10-100x on single settlement tile
- Building display and interaction (click to configure, drag to place)
- Pipeline/road connection editing on the tile
- Data flow: same terrain_data, different camera state
- Exit flow: zoom out → return to Surface View with same camera position

### Step 5 — Define Zoom Navigation & Context Preservation

Document the complete zoom hierarchy:
```
Planetary View (overview)
  └─ Click region → Surface View (centered on region)
       └─ Double-click settlement tile → TerrainForge (zoomed in)
            └─ Close/ESC → Surface View (same camera position)
```

Document context preservation rules:
- Camera position tracking across zoom transitions
- Time pause/resume when entering/exiting TerrainForge
- Data synchronization between layers

### Step 6 — Write Architecture Document

Create `docs/new_agent/projects/galaxy_game/architecture/three_layer_views.md` with:
- Complete specification for all three layers
- Zoom hierarchy flow chart (Mermaid diagram)
- Layer separation rules (IS/IS NOT responsibility tables)
- Integration points defined
- Data ownership clarified
- Rendering technology recommendations
- Scope boundaries clearly marked

---

## Acceptance Criteria
- [ ] Complete specification document at `docs/new_agent/projects/galaxy_game/architecture/three_layer_views.md`
- [ ] Zoom hierarchy flow chart defined and documented (Mermaid diagram)
- [ ] Layer separation rules clearly stated (what each layer does/doesn't do)
- [ ] Integration points defined (zoom transitions, data synchronization)
- [ ] Data ownership clarified (which layer owns which terrain_data fields)
- [ ] Rendering technology recommendations documented
- [ ] Scope boundaries clearly marked (prevents Surface View tasks from creeping into planetary/terrain-forge)
- [ ] Surface View layer fully documented (current state + in-progress tasks)

---

## Stop Conditions — escalate to user immediately if:
- Existing `surface_view.js` structure is fundamentally incompatible with the three-layer model
- terrain_data structure needs modification that wasn't anticipated
- Any architectural decision conflicts with locked decisions in `DECISIONS.md`

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add docs/new_agent/projects/galaxy_game/architecture/three_layer_views.md
git commit -m "docs: Add Three-Layer View Architecture spec (Planetary, Surface, TerrainForge)"
```

**Task file move on completion:**
```bash
git mv projects/galaxy_game/tasks/active/2026-07-13-HIGH-ARCHITECTURE-THREE-LAYER-VIEWS.md \
       projects/galaxy_game/tasks/completed/2026-07/2026-07-13-HIGH-ARCHITECTURE-THREE-LAYER-VIEWS.md

git commit -m "chore: move 2026-07-13-HIGH-ARCHITECTURE-THREE-LAYER-VIEWS.md to completed/"
```

---

## Documentation
- [x] No doc changes needed (this task creates the architecture doc)
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: none
**Blocks**: All future UI view layer work, Surface View improvements, TerrainForge implementation
**Related tasks**: Any task touching `surface_view.js` or celestial body rendering

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was changed
- `[file]` — [description of change]

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
