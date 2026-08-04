---
status: backlog
priority: MEDIUM
type: feature
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
created: 2026-08-04
updated: 2026-08-04
estimated_effort: 8 hours
blocker_for:
  - 2026-08-04-MEDIUM-FEATURE-GGMAP-MAP-STUDIO-INTEGRATION
---

# Task: GGMap Generation Services — Ruby Reader/Writer + Layer Generators

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/phase8+/2026-08-04-MEDIUM-FEATURE-GGMAP-GENERATION-SERVICES.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv docs/new_agent/projects/galaxy_game/tasks/backlog/phase8+/2026-08-04-MEDIUM-FEATURE-GGMAP-GENERATION-SERVICES.md \
         docs/new_agent/projects/galaxy_game/tasks/active/2026-08-04-MEDIUM-FEATURE-GGMAP-GENERATION-SERVICES.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find docs/new_agent/projects/galaxy_game/tasks -name "2026-08-04-MEDIUM-FEATURE-GGMAP-GENERATION-SERVICES.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.
```

## Prerequisites

- Complete Phase 1 first: `2026-08-04-MEDIUM-FEATURE-GGMAP-FORMAT-DEFINITION.md` (schema + sample + spec)
- Read `docs/archive/task_archives/GGMAP_FORMAT_DESIGN.md` sections on Ruby class design and generation services
- Understand the sol-complete.json structure (magnetosphere data added in recent work)

## Context

**Problem**: The .ggmap format is defined but has no Ruby implementation. This task builds the core Ruby classes that read/write .ggmap files and generate each layer programmatically from planetary data.

**Scope for this task**:
- `lib/ggmap.rb` — Main reader/writer class (load, save, validate)
- `app/services/ggmap_generator.rb` — Generate complete .ggmap from celestial body data
- `app/services/ggmap/scientific_layer_generator.rb` — Generate geological features, resources, hazards
- `app/services/ggmap/strategic_layer_generator.rb` — Generate settlement sites, expansion zones, infrastructure recommendations
- `app/services/ggmap/terraforming_layer_generator.rb` — Generate current/target state, worldhouse sites

**Out of scope**: Map Studio UI integration, game system integration (handled in later tasks).

## Architecture Gotchas

1. **Layer independence**: Each layer generator must work independently — layers can be regenerated without regenerating others
2. **Non-destructive editing**: The writer should preserve existing layer data when updating only one layer
3. **Celestial body agnostic**: Generation must work for any planet (Mars, Luna, Venus, exoplanets) — no hardcoded Mars values
4. **Schema validation**: All generated .ggmap files must validate against the Phase 1 schema
5. **Integration with existing services**: Use `StarSim::ProceduralGenerator` and `StarSim::AtmosphereGeneratorService` for base terrain data where applicable

## Files to Create/Modify

| File | Purpose |
|---|---|
| `galaxy_game/lib/ggmap.rb` | Main reader/writer: load, save, validate against schema |
| `galaxy_game/app/services/ggmap_generator.rb` | Orchestrates layer generation for a celestial body |
| `galaxy_game/app/services/ggmap/scientific_layer_generator.rb` | Geological features, resources, hazards |
| `galaxy_game/app/services/ggmap/strategic_layer_generator.rb` | Settlement sites, expansion zones, infrastructure |
| `galaxy_game/app/services/ggmap/terraforming_layer_generator.rb` | Current/target state, worldhouse sites |
| `spec/lib/ggmap_spec.rb` | Reader/writer tests |
| `spec/services/ggmap_generator_spec.rb` | Generation service tests |

## Implementation Steps

### Step 0: Move Task to Active & Verify Synthesis
**PREREQUISITE — Do NOT skip:**
1. Move task from `backlog/phase8+/` → `active/`
2. Update YAML header: `status: backlog` → `status: active`
3. Commit move before writing any code
4. Read Phase 1 deliverables (schema, sample file, format spec)

### Step 1: Create GGMap Reader/Writer (`lib/ggmap.rb`)

```ruby
# galaxy_game/lib/ggmap.rb
class Ggmap
  def self.load(path)
    # Read JSON file, validate against schema, return parsed hash
  end

  def self.save(ggmap_data, path)
    # Validate data against schema, write JSON to path
  end

  def self.validate(ggmap_data)
    # Return [valid?, errors[]]
  end

  def self.sample_for_body(celestial_body_id)
    # Load sample file and substitute body-specific metadata
  end
end
```

### Step 2: Create GGMapGenerator (`app/services/ggmap_generator.rb`)

Orchestrates layer generation for a given celestial body:
- Accepts `CelestialBody` or celestial_body_id
- Calls each layer generator in order (base → scientific → strategic → terraforming)
- Returns complete .ggmap hash ready for save
- Uses sol-complete.json data as the source of truth

### Step 3: Create Scientific Layer Generator

Generate from planetary parameters:
- **Lava tubes**: Based on volcanic history, radius, surface features (from procedural generation data)
- **Aquifers**: Based on subsurface ice probability (from atmosphere/temperature data)
- **Resource deposits**: Based on planetary composition (from sol-complete.json celestial body data)
- **Hazard zones**: Dust storm corridors (based on atmospheric data), radiation hotspots (based on magnetosphere_strength)

### Step 4: Create Strategic Layer Generator

AI Manager analysis layer:
- **Settlement sites**: Score locations by terrain flatness, resource proximity, natural shelter, radiation protection
- **Expansion zones**: Identify contiguous flat areas with resources
- **Infrastructure recommendations**: Transport corridors between key points, spaceport locations
- **Resource extraction sites**: Prioritize by concentration and accessibility

### Step 5: Create Terraforming Layer Generator

Terraforming simulation layer:
- **Current state**: From celestial body's current atmospheric/temperature data
- **Target state**: Based on terraforming goals (from terraforming_manager if available)
- **Worldhouse sites**: Based on strategic layer settlement recommendations + solar exposure
- **Ocean basin zones**: Low-elevation areas suitable for water retention

### Step 6: Write Tests

```ruby
# spec/lib/ggmap_spec.rb
describe Ggmap do
  it 'loads a valid .ggmap file'
  it 'rejects invalid .ggmap files with error details'
  it 'saves data and validates round-trip'
  it 'provides sample for any celestial body'
end

# spec/services/ggmap_generator_spec.rb
describe GgmapGenerator do
  it 'generates complete .ggmap for Mars'
  it 'generates complete .ggmap for Luna'
  it 'generates complete .ggmap for Venus'
  it 'scientific layer includes lava tubes for volcanic bodies'
  it 'strategic layer scores settlement sites by suitability'
  it 'terraforming layer reflects current atmospheric state'
end
```

## Acceptance Criteria
- [ ] `Ggmap.load` reads and validates .ggmap files
- [ ] `Ggmap.save` writes valid .ggmap files that pass schema validation
- [ ] `GgmapGenerator.generate(celestial_body)` produces complete .ggmap with all layers
- [ ] Scientific layer generator creates realistic features based on planetary data
- [ ] Strategic layer generator scores and ranks settlement sites
- [ ] Terraforming layer generator reflects current/target atmospheric state
- [ ] All generation works for Mars, Luna, Venus (no hardcoded body-specific values)
- [ ] Tests pass (RSpec) — 0 failures
- [ ] Generated .ggmap files validate against Phase 1 schema

## Dependencies
**Blocked by**: `2026-08-04-MEDIUM-FEATURE-GGMAP-FORMAT-DEFINITION.md` (Phase 1: format definition)
**Blocks**: `2026-08-04-MEDIUM-FEATURE-GGMAP-MAP-STUDIO-INTEGRATION` (Phase 3: Map Studio UI)
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
HANDOFF SUMMARY: GGMap generation services complete | Ruby reader/writer + 4 layer generators | Phase 2 of 4 phases
