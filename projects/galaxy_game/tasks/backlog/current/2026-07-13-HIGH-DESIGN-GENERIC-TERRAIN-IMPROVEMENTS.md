---
status: backlog
priority: HIGH
type: feature
system_domain: UI
mvp_alignment: OTHER
local_worker_safe: true
created: 2026-07-13
last_updated: 2026-07-29
---

## ⚡ Minimal Handoff (Copy this to send to agent)
```text
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-13-HIGH-DESIGN-GENERIC-TERRAIN-IMPROVEMENTS.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-13-HIGH-DESIGN-GENERIC-TERRAIN-IMPROVEMENTS.md \
         projects/galaxy_game/tasks/active/2026-07-13-HIGH-DESIGN-GENERIC-TERRAIN-IMPROVEMENTS.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-13-HIGH-DESIGN-GENERIC-TERRAIN-IMPROVEMENTS.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-07-13-FEATURE-GENERIC-TERRAIN-IMPROVEMENTS.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Generic Terrain Improvements & Infrastructure Sprites
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-07-13
**Last Updated**: 2026-07-29

## Context
Define generic terrain improvement sprites (roads, farms, mines, power plants, docks) and infrastructure design language. These assets are **not planet-specific** and must render cleanly over any terrain type.

## Context
Civ4 gameplay requires visual indicators for terrain improvements (farms, mines, roads, irrigation). Current spritesheet has building section but lacks **generic improvement overlays** that work across all planet types.

Current Challenge:
- ❌ "Martian Mine" only makes sense on Mars
- ✅ Generic "Mine" works on any world (sprite is a pickaxe or mining symbol, not terrain-specific)
- Infrastructure must be small overlays that don't obscure terrain/biome sprites below

## Scope

### Phase 1: Design Language & Reference
1. **Improvement Types** (Civ4 and Galaxy Game standard set)
   - **Production:**
     - Farm (food)
     - Mine (production from regolith/minerals)
     - Irrigation (food boost)
     - Logging Camp (forest clearance)
   - **Resources:**
     - Resource Extraction Node (generic, specialized per material)
     - Harvester (mobile resource collector)
   - **Movement/Trade:**
     - Road (basic movement)
     - Rail (fast transport)
     - Pipeline (fluid transport — O2, H2O, propellant)
     - Harbor/Dock (water/liquid handling)
   - **Power:**
     - Solar Panel Field
     - Geothermal Vent (Titan-style)
     - Wind Farm (atmospheric worlds)
     - Reactor (nuclear)
   - **Command/Control:**
     - Outpost (visibility/control)
     - Communication Relay (signal)
     - Defense Tower (military)

2. **Visual Hierarchy Rules**
   - Improvement icons should occupy ~25% of tile space (not dominate)
   - Neutral colors (greys, industrial blues) that work over any biome
   - Icons must be recognizable at 32×32px tile size
   - Use symbolic rather than photorealistic style to match terrain sprite aesthetic

3. **Placement Rules**
   - Improvements render ABOVE biome/terrain sprites but BELOW units
   - Can stack up to 2-3 improvements per tile (e.g., road + farm, rail + mine)
   - Priority stacking: base improvement (farm) + modifier (irrigation) + connection (road)

### Phase 2: Asset Definitions
1. **Per-Improvement Asset Definition**
   - Icon sprite(s): base (small, 16×16px) + variants
   - Rotation variants if applicable (roads: N-S, E-W, corner, T-junction)
   - Color scheme: grayscale base + faction coloring overlay option
   - Canvas: transparent PNG, same 8-bit sRGB as terrain tiles

2. **Improvement Sprite Directory Structure**
   ```
   /data/images/improvements/
     farm/
       farm_01.png (field icon)
       farm_irrigated_01.png (wet field)
     mine/
       mine_01.png (pickaxe / mining symbol)
       mine_regolith_01.png
     road/
       road_straight.png (horizontal segment)
       road_vert.png (vertical segment)
       road_corner.png
       road_tjunction.png
       road_cross.png
     rail/
       rail_straight.png
       rail_vert.png
       rail_corner.png
     pipeline/
       pipeline_liquid.png (water/methane)
       pipeline_gas.png (oxygen/nitrogen)
       pipeline_energy.png (power conduit)
     power/
       solar_panel.png
       geothermal.png
       windmill.png
       reactor.png
     extraction/
       harvester_generic.png
       resource_node_marker.png
     dock/
       harbor_liquid.png
     command/
       outpost.png
       relay.png
       defense_tower.png
   ```

### Phase 3: Integration Rules
1. **Rendering Layer**
   - New Layer 3.5 (between biomes/resources and units)
   - Called during main render loop AFTER resource grid, BEFORE unit grid
   - Viewport-culled like terrain (no rendering off-screen)

2. **Data Structure**
   ```ruby
   # terrain_data.improvements array
   [
     {
       col: 42,
       row: 50,
       type: "farm",
       variant: "01",
       rotated: false
     },
     {
       col: 43,
       row: 50,
       type: "road",
       variant: "straight_horizontal",
       rotated: false
     }
   ]
   ```

3. **Asset Lookup Path**
   `/assets/improvements/{type}/{variant}.png`
   Fallback: `/assets/improvements/{type}/01.png`

### Phase 4: Faction Coloring (Optional Enhancement)
1. **Color Overlay System**
   - Base sprites: neutral grey/blue
   - Apply faction color tint in JS (blue for Earth, red for Mars faction, etc.)
   - Allows single sprite asset + runtime coloring

## Acceptance Criteria
- [ ] 10+ improvement types defined with design language rules
- [ ] All improvement sprites extracted/created and organized
- [ ] Sprites render cleanly over terrain without obscuring biome
- [ ] Up to 3 improvements stack per tile without visual conflict
- [ ] Rotation variants work for road/rail/pipeline (N-S, E-W, corners)
- [ ] Generic naming — no planet-specific suffixes
- [ ] Asset directory matches defined structure
- [ ] Render layer integration in surface_view.js working
- [ ] Performance: no measurable FPS impact from improvement rendering
- [ ] Faction coloring tint system implemented (if used)

## Blockers
- Depends on having final ChatGPT-derived or custom infrastructure sprites
- May need designer review for visual consistency

## Dependencies
- **Related**: 2026-07-13-HIGH-FEATURE-CIV4-SURFACE-VIEW-GAMEPLAY (uses these sprites)
- **Related**: Unit/Building model definitions (determines which improvements are renderable)

## Notes
- Improvements are **visual indicators only** at this phase — actual mechanics (farm producing food, mine extracting resources) handled elsewhere
- Consider directional rendering for roads/rails (no diagonal movement in Civ4-style grids)
- Each improvement type should have 3-5 visual variants to reduce repetitiveness
- Fence/border rendering optional — may add too much visual clutter

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: This is a DESIGN/ASSET task, NOT a code implementation task.
- ❌ Wrong: Try to implement mechanics (farm producing food, mine extracting resources) — those are handled elsewhere
- ✅ Right: Focus on sprite design, directory structure, rendering layer integration, and data schema for improvement placement
- Why: The scope is visual indicators only; mechanics belong in separate tasks

⚠️ **GOTCHA 2**: Sprite assets must be extracted from existing composite sheets or created fresh — do not assume they exist.
- ❌ Wrong: Reference sprite paths that don't exist yet
- ✅ Right: Either extract from existing spritesheets (check `tilesets/`, `images/` directories) or create new ones matching the defined directory structure
- Why: The task depends on having actual sprite files, not just design docs

⚠️ **GOTCHA 3**: Rendering layer must integrate with existing `surface_view.js` without breaking current terrain/biome rendering.
- ❌ Wrong: Add a new canvas element that overlaps incorrectly
- ✅ Right: Use the existing tile rendering pipeline; improvements are drawn as overlays on existing tiles, not as separate layers
- Why: The surface view already has complex viewport culling and tile management

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Generic Terrain Improvements & Infrastructure Sprites
**Status**: [backlog → active → completed]
**Date**: YYYY-MM-DD

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| [existing spritesheet paths] | Check for extractable assets | [not started / pending / done] |
| `public/` or `assets/` directories | Create improvement sprite directory | [not started / pending / done] |
| `surface_view.js` (if exists) | Integration point for rendering layer | [not started / pending / done] |

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
- ❌ Implementing mechanics (farm production, mine extraction) — instead ✅ Focus on visual sprites + rendering layer only
- ❌ Creating planet-specific assets — instead ✅ Keep all improvements generic/symbolic
- ❌ Breaking existing surface_view.js rendering — instead ✅ Integrate as overlay in existing tile pipeline

---

**SYNTHESIS COMPLETE.** Ready to proceed with [PRIORITY 1 / PRIORITY 2 / etc].
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Stop Conditions — escalate to user immediately if:
- No existing spritesheets found to extract from AND no design tools available to create new ones
- `surface_view.js` (or equivalent rendering code) has changed significantly since this task was created and the integration approach is unclear
- The Civ4 surface view gameplay task (dependency) has been deferred or cancelled, making this task orphaned
- Any decision about asset creation tooling (AI-generated vs hand-drawn vs extracted) requires design input

---

## Dependencies
**Blocked by**: `2026-07-13-HIGH-FEATURE-CIV4-SURFACE-VIEW-GAMEPLAY` (uses these sprites) — verify this task is still active
**Blocks**: Surface view gameplay polish (improvements are a prerequisite for full Civ4-style surface interaction)
**Related**: Asset generation architecture docs, tileset pipeline

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
