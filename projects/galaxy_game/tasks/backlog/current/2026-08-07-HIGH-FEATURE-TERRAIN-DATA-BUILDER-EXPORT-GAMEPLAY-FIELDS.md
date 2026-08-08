---
status: backlog
priority: HIGH
type: feature
system_domain: CONTROLLERS | BIOME_RENDERING
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
created: 2026-08-07
last_updated: 2026-08-07
---

## ⚡ Minimal Handoff (Copy this to send to agent)
```text
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-07-HIGH-FEATURE-TERRAIN-DATA-BUILDER-EXPORT-GAMEPLAY-FIELDS.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-07-HIGH-FEATURE-TERRAIN-DATA-BUILDER-EXPORT-GAMEPLAY-FIELDS.md \
         projects/galaxy_game/tasks/active/2026-08-07-HIGH-FEATURE-TERRAIN-DATA-BUILDER-EXPORT-GAMEPLAY-FIELDS.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-07-HIGH-FEATURE-TERRAIN-DATA-BUILDER-EXPORT-GAMEPLAY-FIELDS.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-08-07-TERRAIN-DATA-BUILDER-EXPORT-GAMEPLAY-FIELDS.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Terrain Data Builder Export Gameplay Fields
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-08-07
**Last Updated**: 2026-08-07

## Context

CIV4-SURFACE-VIEW-GAMEPLAY (task `2026-07-13-HIGH-FEATURE-CIV4-SURFACE-VIEW-GAMEPLAY.md`) needs `terrain_data_builder.rb` to export four gameplay fields it currently doesn't. This task adds those exports to the Ruby builder — NOT the JS interaction layer (that's a separate, larger task).

**Architecture context**: TerrainForge and Surface View share the same underlying tile data at different zoom/resolution levels. Design this export with that in mind — not as a Surface-View-only shape.

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: `yield_grid` has NO existing game-balance foundation anywhere in the codebase.
- ❌ Wrong: Invent food/production/science/culture values per tile and hardcode them
- ✅ Right: Report this as a design blocker — yield_grid needs its own design task before implementation
- Why: No model, service, or config anywhere defines per-tile yields. This is a game-balance concept that doesn't exist yet.

⚠️ **GOTCHA 2**: Units/Structures/Settlements do NOT have `tile_col`/`tile_row` fields. They use `Location::CelestialLocation` with lat/long coordinates.
- ❌ Wrong: Assume grid position is stored directly on unit/structure/settlement models
- ✅ Right: Use the existing `get_grid_position(entity)` method in TerrainDataBuilder (already used for unit_grid) to map lat/long → grid col/row
- Why: The builder already has this mapping logic; reuse it.

⚠️ **GOTCHA 3**: Unit "orders" don't exist as a tracked concept on units.
- ❌ Wrong: Assume `unit.current_order` or similar field exists
- ✅ Right: Units have `operational_data` JSONB (used for manufacturing, construction_cost_percentage, etc.) and `ConstructionJob` records track work but aren't unit-bound. Report this gap — unit_orders needs a design decision on how orders are tracked.
- Why: No `current_order`, `assigned_task`, `mission_type`, or `work_assignment` field exists on Units::BaseUnit or Craft::BaseCraft.

⚠️ **GOTCHA 4**: Settlements don't have control_radius or worked_tiles fields.
- ❌ Wrong: Assume city radius is stored on BaseSettlement
- ✅ Right: Colony has settlements; each settlement has a location with lat/long. Control radius and worked tiles need to be designed as new data (likely stored in `operational_data` JSONB on Colony or as a separate model).

---

## Current State of terrain_data_builder.rb

**File**: `galaxy_game/app/services/terrain_data_builder.rb`
**Class**: `TerrainDataBuilder`
**Method**: `build(terrain_map_data = nil, planet_data = nil)`

### Currently exported fields:
```ruby
{
  elevation: ...,           # from terrain_map_data['elevation'] or ['grid']
  biomes: ...,              # from terrain_map_data['biomes']
  resources: ...,           # from terrain_map_data['resource_grid']
  width: ...,               # from terrain_map_data['width'] or derived from elevation
  height: ...,              # from terrain_map_data['height'] or derived from elevation
  quality_score: ...,       # from terrain_map_data.dig('quality_score')
  generation_method: ...,   # from terrain_map_data.dig('generation_method')
  unit_grid: ...            # extracted via extract_unit_grid (places craft/locations on grid)
}
```

### Missing fields needed by CIV4-SURFACE-VIEW-GAMEPLAY:
| Field | Status | What it needs |
|---|---|---|
| `city_overlays` | **MISSING** | Derive from Colony → settlements; needs control_radius + worked_tiles (neither exists) |
| `improvements` | **MISSING** | Derive from Structures::BaseStructure records on this celestial body; needs improvement_type field |
| `yield_grid` | **BLOCKER** | No game-balance concept exists anywhere — food/production/science/culture per tile is entirely new |
| `unit_orders` | **MISSING** | No order tracking exists on units — needs design decision |

---

## Data Source Investigation (Completed)

### city_overlays — What data exists:
- **Colony model** (`galaxy_game/app/models/colony.rb`): Has `has_many :settlements, class_name: 'Settlement::BaseSettlement'`, `total_population`, `calculate_resource_requirements`
- **Settlement::BaseSettlement** (`galaxy_game/app/models/settlement/base_settlement.rb`): Has `location` (polymorphic → Location::CelestialLocation), `colony_id`, `settlement_type` enum (base/outpost/settlement/city/station), `current_population`, `operational_data` JSONB
- **Location::CelestialLocation** (`galaxy_game/app/models/location/celestial_location.rb`): Has `latitude` and `longitude` (computed from lat/lon strings like "45.0°N 120.0°E")
- **NO control_radius field exists** on Colony or Settlement
- **NO worked_tiles field exists** anywhere
- **Design needed**: How to store city control radius and which tiles are being worked

### improvements — What data exists:
- **Structures::BaseStructure** (`galaxy_game/app/models/structures/base_structure.rb`): Has `structure_name`, `structure_type`, `settlement` (belongs_to Settlement::BaseSettlement), `location` (polymorphic), `operational_data` JSONB, `modules`, `units`
- **NO improvement_type field exists** on structures — `structure_type` is the closest thing but it describes the structure category (habitat, manufacturing, etc.), not an "improvement" type (road/farm/mine)
- **Design needed**: How to represent road/farm/mine as improvements — are these new structure types? Or a separate concept?

### yield_grid — What data exists:
- **NO per-tile yield concept exists anywhere** in the codebase
- `LunaOperationsSimulationService` has resource production/consumption tracking but it's colony-level, not tile-level
- `BaseStructure#input_resources` and `output_resources` exist (from operational_data) but are structure-level, not tile-level
- **BLOCKER**: This is a completely new game-balance system. Needs its own design task.

### unit_orders — What data exists:
- **Units::BaseUnit** (`galaxy_game/app/models/units/base_unit.rb`): Has `operational_data` JSONB (used for manufacturing, capacity, etc.), `job_types`, `max_concurrent_jobs`, `processing_type`, `attachable` (polymorphic), `location` (polymorphic → CelestialLocation)
- **Craft::BaseCraft**: No order/command fields found
- **ConstructionJob** (`galaxy_game/app/models/construction_job.rb`): Has `job_type` enum, `status` enum, but is settlement-bound, not unit-bound
- **NO current_order, assigned_task, mission_type, or work_assignment field exists** on any unit/craft model
- **Design needed**: How to track what each unit is currently doing (mining/moving/idle/constructing)

---

## REQUIRED Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Terrain Data Builder Export Gameplay Fields
**Status**: [backlog → active → completed]
**Date**: YYYY-MM-DD

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| terrain_data_builder.rb | Add city_overlays, improvements exports (yield_grid/unit_orders blocked) | [not started / pending / done] |
| Colony model | Confirm settlement relationship for city_overlays | [done] |
| BaseStructure model | Confirm structure data available for improvements | [done] |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
[Exact description of what "done" looks like]

### Critical Gotchas I Will Avoid
- ❌ Inventing yield_grid values — instead ✅ Report as design blocker, implement stub with nil/null
- ❌ Assuming unit_orders field exists — instead ✅ Report gap, implement stub
- ❌ Assuming control_radius/worked_tiles exist — instead ✅ Design minimal storage (operational_data JSONB)

---

**SYNTHESIS COMPLETE.** Ready to proceed with [PRIORITY 1 / PRIORITY 2 / etc].
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Scope — What to Implement vs. Block

### ✅ IMPLEMENT (data exists, just needs export):

#### 1. `city_overlays` export
- Derive from `Colony.all` → each colony's `settlements`
- Each overlay entry: `{ settlement_id:, center_col:, center_row:, faction: nil_or_colony_name }`
- Use existing `get_grid_position()` to map lat/long → grid col/row
- **Note**: control_radius and worked_tiles don't exist yet — export them as `nil` for now, with a TODO comment

#### 2. `improvements` export
- Derive from `Structures::BaseStructure.where(celestial_body: @celestial_body)` (or via locations)
- Each improvement entry: `{ col:, row:, type: structure_type, name: structure_name }`
- Use existing `get_grid_position()` to map lat/long → grid col/row
- **Note**: No "improvement_type" concept exists — use `structure_type` as the closest proxy

### ⚠️ BLOCKED — Report in synthesis, implement as stubs:

#### 3. `yield_grid` — BLOCKER
- NO game-balance concept for per-tile yields exists anywhere
- **Action**: Export as `nil` with a clear TODO comment pointing to a design task that needs to be created
- Do NOT invent values

#### 4. `unit_orders` — BLOCKER
- NO order tracking exists on units or crafts
- **Action**: Export as `[]` (empty array) with a TODO comment noting the gap
- Do NOT invent an order system

---

## Acceptance Criteria
- [ ] `city_overlays` exports settlement positions derived from Colony → Settlements via lat/long → grid mapping
- [ ] `improvements` exports structure positions derived from Structures::BaseStructure records
- [ ] `yield_grid` exported as `nil` with TODO comment (no game-balance data exists)
- [ ] `unit_orders` exported as `[]` with TODO comment (no order tracking exists)
- [ ] No existing functionality broken (terrain_data_builder specs pass)
- [ ] New RSpec specs for each export field

## Blockers
- yield_grid has no game-balance foundation — needs separate design task
- unit_orders has no tracking mechanism — needs design decision on how orders are stored
- city_overlays needs control_radius/worked_tiles design (can stub as nil for now)
- improvements needs improvement_type design (can use structure_type as proxy for now)

## Dependencies
- **Downstream**: 2026-07-13-HIGH-FEATURE-CIV4-SURFACE-VIEW-GAMEPLAY.md (needs these fields)
- **Upstream**: 2026-07-13-HIGH-FEATURE-SPRITE-TILES-SURFACE-VIEW-INTEGRATION.md (completed — provides rendering foundation)
- **Upstream**: 2026-07-13-HIGH-FEATURE-UNIT-LAYER-RENDERING.md (completed — provides unit rendering foundation)

## Notes
- Architecture: TerrainForge = zoomed Surface View, same rendering pipeline (see `three_layer_views.md` and completed task 2026-07-13-HIGH-ARCHITECTURE-THREE-LAYER-VIEWS.md)
- This export should work for both Surface View and TerrainForge since they share the same underlying data
- The builder already has `get_grid_position(entity)` method that maps lat/long → grid col/row — reuse it

## Next Steps
1. Synthesis report in chat (before any code changes)
2. Implement city_overlays export (data exists via Colony → Settlements)
3. Implement improvements export (data exists via Structures::BaseStructure)
4. Stub yield_grid as nil with TODO (no game-balance data exists — needs design task)
5. Stub unit_orders as [] with TODO (no order tracking exists — needs design task)
6. Write RSpec specs for all four exports
7. Run existing terrain_data_builder specs to confirm no regressions

## Stop Conditions
- Stop if you find that any of the data sources investigated above don't match what's documented here — report before implementing
- Stop if yield_grid turns out to have SOME precedent I missed — verify thoroughly before deciding to stub vs implement
- Do NOT modify surface_view.js — that's a separate, larger task

## Completion Report

When done, provide:
1. **Files modified**: List all files changed with brief description
2. **New files created**: List any new files (specs)
3. **Data contract changes**: What terrain_data fields were added/modified
4. **Test coverage**: Which RSpec specs were added and what they cover
5. **Known limitations**: yield_grid and unit_orders stubbed — note what design work is needed

## Handoff Summary

**Task**: Terrain Data Builder Export Gameplay Fields
**Status**: backlog → active → completed
**Type**: feature (data export addition to existing builder)
**Key Risk**: yield_grid has no game-balance foundation; unit_orders has no tracking mechanism — both need design tasks before full implementation
**Approach**: Implement city_overlays and improvements from existing data; stub yield_grid and unit_orders with TODO comments pointing to needed design work
