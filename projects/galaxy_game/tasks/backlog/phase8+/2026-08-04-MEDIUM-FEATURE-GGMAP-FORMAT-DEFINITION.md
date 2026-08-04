---
status: backlog
priority: MEDIUM
type: architecture
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
created: 2026-08-04
updated: 2026-08-04
estimated_effort: 2 hours
blocker_for:
  - 2026-08-04-MEDIUM-FEATURE-GGMAP-GENERATION-SERVICES
---

# Task: GGMap Format Definition — JSON Schema, Sample File, Documentation

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/phase9+/2026-08-04-MEDIUM-FEATURE-GGMAP-FORMAT-DEFINITION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv docs/new_agent/projects/galaxy_game/tasks/backlog/phase8+/2026-08-04-MEDIUM-FEATURE-GGMAP-FORMAT-DEFINITION.md \
         docs/new_agent/projects/galaxy_game/tasks/active/2026-08-04-MEDIUM-FEATURE-GGMAP-FORMAT-DEFINITION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find docs/new_agent/projects/galaxy_game/tasks -name "2026-08-04-MEDIUM-FEATURE-GGMAP-FORMAT-DEFINITION.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.
```

## Prerequisites

- Read `docs/archive/task_archives/GGMAP_FORMAT_DESIGN.md` — the complete format specification that defines the .ggmap structure
- This task is Phase 1 of a 4-phase implementation (28 hours total across all phases)

## Context

**Problem**: Galaxy Game needs a native map format beyond FreeCiv/Civ4. The design spec (`GGMAP_FORMAT_DESIGN.md`) exists and defines the complete .ggmap structure with base/scientific/strategic/terraform/gameplay layers. This task implements Phase 1: the format definition itself — JSON schema, sample file, and documentation.

**Scope for this task**:
- Create `data/schemas/ggmap.schema.json` — validation schema for all .ggmap fields
- Create a sample `data/maps/mars_sample.ggmap` — Mars Utopia Planitia example with all layers populated
- Create `docs/architecture/ggmap_format_spec.md` — human-readable format specification

**Out of scope**: Ruby reader/writer classes, generation services, Map Studio integration, game integration (handled in later tasks).

## Architecture Gotchas

1. **Schema must match the design doc exactly** — `GGMAP_FORMAT_DESIGN.md` defines the structure; don't invent new fields
2. **Sample file should be realistic** — use Mars data from sol-complete.json for consistency
3. **Format spec is documentation, not code** — write it so a future implementer can build Ruby classes from it without reading the design doc

## Files to Create

| File | Purpose |
|---|---|
| `data/schemas/ggmap.schema.json` | JSON Schema v7 validation for .ggmap files |
| `data/maps/mars_sample.ggmap` | Sample Mars map with all layers populated |
| `docs/architecture/ggmap_format_spec.md` | Human-readable format specification |

## Implementation Steps

### Step 0: Move Task to Active & Verify Synthesis
**PREREQUISITE — Do NOT skip:**
1. Move task from `backlog/phase8+/` → `active/`
2. Update YAML header: `status: backlog` → `status: active`
3. Commit move before writing any code
4. Read `GGMAP_FORMAT_DESIGN.md` sections on format structure, JSON schema example, and layer definitions

### Step 1: Create JSON Schema (`data/schemas/ggmap.schema.json`)

Define the complete .ggmap schema based on the design doc's JSON structure:
- `format_version` (string)
- `metadata` (object): name, celestial_body_id, created_at, author, locked, description
- `dimensions` (object): width, height, resolution, coordinate_system
- `base_terrain` (object): source, generation_method, generated_at, elevation, terrain_types, biomes
- `scientific_layer` (object): generation_method, generated_at, geological_features[], resource_deposits[], hazard_zones[]
- `strategic_layer` (object): generation_method, generated_at, confidence, settlement_sites[], expansion_zones[], infrastructure_recommendations[], resource_extraction_sites[]
- `terraforming_layer` (object): generation_method, generated_at, current_state, target_state, worldhouse_sites[], ocean_basin_zones[], biosphere_seed_regions[]
- `scenario_layer` (object): generation_method, generated_at, points_of_interest[], mission_objectives[], tutorial_markers[]

Each sub-object should have proper type definitions, required fields, and descriptions.

### Step 2: Create Sample Mars Map (`data/maps/mars_sample.ggmap`)

Create a realistic Mars Utopia Plania sample with:
- Dimensions: 96x48 (standard resolution)
- Base terrain from NASA MOLA data (source: "nasa_geotiff")
- Scientific layer: 2-3 lava tubes, 1 aquifer, iron ore deposit, dust storm corridor
- Strategic layer: 2 settlement site recommendations with suitability scores
- Terraforming layer: current state (0.6 kPa, -60°C), target state (60 kPa, 10°C), 1 worldhouse site
- Scenario layer: 1 tutorial marker, 1 mission objective

Use Mars data from sol-complete.json for consistency (magnetosphere_strength: 0.0, etc.).

### Step 3: Create Format Specification (`docs/architecture/ggmap_format_spec.md`)

Write a human-readable spec covering:
- Overview and design philosophy (hierarchical, non-destructive, game-specific)
- Complete field reference with types and descriptions for every layer
- Layer dependency chain (base → scientific → strategic → terraforming → scenario)
- Validation rules (required fields, type constraints)
- Extensibility hooks (how to add new layers without breaking existing readers)
- Integration notes (how Ruby classes should read/write this format)

## Acceptance Criteria
- [ ] `data/schemas/ggmap.schema.json` validates the sample file
- [ ] `data/maps/mars_sample.ggmap` is a valid .ggmap with all 5 layers populated
- [ ] `docs/architecture/ggmap_format_spec.md` covers every field in the schema
- [ ] Format spec references GGMAP_FORMAT_DESIGN.md as the source design doc
- [ ] Sample file uses Mars data consistent with sol-complete.json

## Dependencies
**Blocked by**: none (design doc exists)
**Blocks**: `2026-08-04-MEDIUM-FEATURE-GGMAP-GENERATION-SERVICES` (Phase 2: Ruby generation services)
**Related**: `docs/archive/task_archives/GGMAP_FORMAT_DESIGN.md` (source design document)

## Completion Report
**Completed by**:
**Completion date**:
**Final verification**:

### What was created
### Issues discovered
### Follow-up tasks needed
### Lessons learned

## Handoff Summary
HANDOFF SUMMARY: GGMap format definition complete | JSON schema + sample file + spec doc created | Phase 1 of 4 phases
