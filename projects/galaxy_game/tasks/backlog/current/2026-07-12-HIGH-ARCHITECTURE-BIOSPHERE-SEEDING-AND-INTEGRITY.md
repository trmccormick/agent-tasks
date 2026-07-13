---
status: backlog
priority: HIGH
type: architecture
system_domain: CELESTIAL_BODIES | DATA_INTEGRITY
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
---

## ⚡ Minimal Handoff

```
Task: 2026-07-12-HIGH-ARCHITECTURE-BIOSPHERE-SEEDING-AND-INTEGRITY.md

BACKGROUND: Today's debug session found that Earth (ID 2493) had biome grid data 
in terrain_map but NO biosphere database record. A temporary workaround was applied 
to monitor.js to check for actual grid data instead of the has_biosphere flag, but 
this masks a data integrity issue.

ROOT PROBLEM: Planets with biome terrain_map data should ALWAYS have a corresponding 
biosphere record. The current setup allows misalignment.

THIS TASK: Determine where biosphere records should be created (during terrain_map 
generation? during seeding? via migration?) and implement automatic creation to 
prevent this from happening again.

Read task file for full context, gotchas, and implementation approach.
```

---

## TASK: Biosphere Record Seeding & Data Integrity

**Status**: BACKLOG
**Priority**: HIGH
**Type**: ARCHITECTURE
**Created**: 2026-07-12
**Last Updated**: 2026-07-12

---

## Context

During today's debug session (2026-07-12), the planetary view biome rendering was not working because:
- Earth (ID 2493) had complete biome grid data in `terrain_map` (180x90 grid, 10 biome types)
- BUT Earth had **no biosphere record** in the database
- The monitor view was checking `planetData.has_biosphere` flag (which depends on DB record existing)
- This caused biome rendering to be skipped despite data being present

**Temporary Fix Applied**: Changed monitor.js to check actual grid data instead of the server flag (commit `3f6078b3`). This works but masks a deeper issue.

**Root Cause**: Data integrity gap. Planets should not be allowed to have biome grids without a biosphere record. This is an architectural problem, not a rendering bug.

---

## Critical Information

### Architecture Gotchas

⚠️ **GOTCHA 1: Unclear biosphere creation trigger**
- ❌ Wrong: Assuming biosphere records are created when terrain_map is generated
- ✅ Right: Determine the actual intended lifecycle — when SHOULD biosphere records exist?
- Why: Currently there's no automatic creation path defined

⚠️ **GOTCHA 2: Seeding vs. Runtime Creation**
- ❌ Wrong: Creating biosphere records via seed data alone (doesn't help planets created programmatically)
- ✅ Right: Implement automatic creation in the code path that generates/assigns terrain_map data
- Why: Need to cover both seeded and dynamically generated planets

⚠️ **GOTCHA 3: Existing planets without biosphere**
- ❌ Wrong: Ignoring Earth and other planets that already lack biosphere records
- ✅ Right: Create a migration to backfill biosphere records for planets with terrain_map data
- Why: Data must be consistent across the entire database

---

## Problem Statement

**Current State:**
- Planets CAN have `geosphere.terrain_map` with biome grid data
- BUT planets may NOT have a `biosphere` record
- The system works around this with defensive checks in JavaScript
- Data model allows inconsistent state

**Expected State:**
- Any planet with biome grid data MUST have a biosphere record
- Biosphere records should be created automatically alongside terrain_map data
- Existing planets should be migrated to restore consistency
- Views should be able to trust `has_biosphere` flag

**Impact:**
- Data integrity issue (misaligned records)
- Fragile rendering logic (must check data, not flags)
- Risk that other systems also have defensive workarounds

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Consideration |
|---|---|---|
| `app/models/celestial_bodies/spheres/biosphere.rb` | Biosphere model | Check associations, validations |
| `app/models/celestial_bodies/celestial_body.rb` | CelestialBody model | May need `after_create` hook to generate biosphere |
| `app/services/[terrain_service].rb` | (find the service that generates terrain_map) | This is where biosphere should be auto-created |
| `db/migrate/[new_migration].rb` | Migration to backfill biosphere records | Required for existing planets |
| `spec/models/biosphere_spec.rb` | Biosphere tests | Add spec for auto-creation on terrain_map assignment |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `db/migrate/20240919010729_create_biospheres.rb` | See biosphere schema and what fields exist |
| `db/seeds.rb` | How seeding currently works (or doesn't) for biospheres |
| `app/services/ai_manager/automatic_terrain_generator.rb` | Likely creates terrain_map — check if it calls biosphere creation |

---

## REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat.

**Synthesis Report Template** (save as MD file in `/memories/session/`, do NOT paste in chat):

```markdown
## STATUS SYNTHESIS REPORT — Biosphere Seeding & Data Integrity

**Task**: 2026-07-12 HIGH ARCHITECTURE — Biosphere Record Seeding & Integrity
**Status**: BACKLOG → ACTIVE
**Date**: 2026-07-12

### What I'm About to Do
1. Find where terrain_map is created/assigned (likely AutomaticTerrainGenerator or similar service)
2. Identify current biosphere creation path (if any)
3. Create migration to backfill missing biosphere records
4. Add automatic biosphere creation to terrain_map assignment
5. Update specs to verify biosphere records are created with terrain data

### Files I'll Examine
| File | Purpose | Status |
|---|---|---|
| `app/services/ai_manager/automatic_terrain_generator.rb` | Generates terrain_map | not started |
| `app/models/celestial_bodies/celestial_body.rb` | Find assignment point for terrain_map | not started |
| `app/models/celestial_bodies/spheres/biosphere.rb` | Check model structure | not started |
| `db/migrate/20240919010729_create_biospheres.rb` | Schema reference | not started |

### Prerequisites Completed
- ✅ Read today's context (Earth missing biosphere record)
- ✅ Understand current workaround (monitor.js data-driven check)
- ✅ Understand root cause (no auto-creation of biosphere with terrain_map)

### Expected Outcomes
1. Migration created to add biosphere records for all planets with terrain_map data
2. Service/model hook added to auto-create biosphere when terrain_map is assigned
3. RSpec spec added to verify auto-creation
4. All existing planets now have biosphere records
5. Monitor.js workaround can be removed (but leave for now as defensive)

### Critical Gotchas I Will Avoid
- ❌ Only fixing Earth in seed data — instead ✅ implement automatic creation
- ❌ Ignoring existing planets — instead ✅ create migration to backfill
- ❌ Creating biosphere without proper attributes — instead ✅ set sensible defaults (habitable_ratio, biodiversity_index, etc.)

---

**SYNTHESIS COMPLETE.** Ready to proceed with [PRIORITY 1: Find terrain_map creation code].
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until approved.

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete synthesis report first.

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP)

```bash
git mv docs/new_agent/projects/galaxy_game/tasks/backlog/current/2026-07-12-HIGH-ARCHITECTURE-BIOSPHERE-SEEDING-AND-INTEGRITY.md \
       docs/new_agent/projects/galaxy_game/tasks/active/2026-07-12-HIGH-ARCHITECTURE-BIOSPHERE-SEEDING-AND-INTEGRITY.md
```

Then update YAML status:
```
status: backlog  →  status: active
```

### Step 1 — Research where terrain_map is created

Find the service/method that generates or assigns `geosphere.terrain_map`. Likely candidates:
- `AutomaticTerrainGenerator` service
- `Geosphere` model after_create hook
- Migration that initially populates data

**Deliverable**: Paste the relevant code section in chat showing how terrain_map is created.

### Step 2 — Check if biosphere creation already happens

Search for any existing code that creates biosphere records:

```bash
grep -r "create_biosphere\|Biosphere.create" galaxy_game/app --include="*.rb"
```

**Deliverable**: Report what you find (should be minimal if this bug exists).

### Step 3 — Create migration to backfill biosphere records

Create a migration that:
1. Finds all planets with `geosphere.terrain_map` but no `biosphere`
2. Creates biosphere records with sensible defaults:
   - `habitable_ratio: 0.5`
   - `biodiversity_index: 0.5`
   - `vegetation_cover: 0.5`
   - `biome_count: (count from terrain_map['biomes'])`
   - `soil_health: 50`
   - `soil_organic_content: 0.05`
   - `soil_microbial_activity: 0.5`

**File**: `db/migrate/20260712_create_missing_biosphere_records.rb`

### Step 4 — Add automatic biosphere creation to terrain_map assignment

Identify where `geosphere.terrain_map =` is called, and ensure a biosphere record is created if one doesn't exist:

```ruby
# In the service/method that assigns terrain_map:
after_terrain_map_assignment do
  unless celestial_body.biosphere.present?
    celestial_body.create_biosphere(
      habitable_ratio: 0.5,
      biodiversity_index: 0.5,
      # ... other defaults
    )
  end
end
```

**Deliverable**: Paste the hook location and implementation.

### Step 5 — Add spec to verify auto-creation

Create a spec that:
1. Creates a celestial body with terrain_map data
2. Verifies that a biosphere record is automatically created
3. Confirms biosphere has correct attributes

**File**: `spec/models/celestial_bodies/spheres/biosphere_spec.rb` (add to existing or create new)

### Step 6 — Run migration and verify

```bash
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rake db:migrate
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rails console -e development << 'EOF'
require 'celestial_bodies/celestial_body'
earth = CelestialBodies::CelestialBody.find(2493)
puts "Earth biosphere present? #{earth.biosphere.present?}"
puts "Earth biosphere ID: #{earth.biosphere.id}"
EOF
```

### Step 7 — Run RSpec to confirm no breakage

```bash
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rspec \
  spec/models/celestial_bodies/spheres/biosphere_spec.rb
```

Expected: all specs pass.

---

## Success Criteria

✅ **Data Integrity Restored**
- All planets with terrain_map now have biosphere records
- Earth (ID 2493) now has a biosphere record with appropriate attributes
- Existing code that checks `has_biosphere` flag will now work correctly

✅ **Automatic Creation Implemented**
- New planets with terrain_map automatically get biosphere records
- No manual workarounds needed

✅ **Specs Pass**
- RSpec: biosphere auto-creation spec added and passing
- No existing tests broken

✅ **Code Quality**
- Follows existing model patterns
- Sensible default values for new biosphere records
- Comments explain the auto-creation logic
