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

## DESIGN CONSTRAINT ⚠️

**Biospheres should ONLY be created for Earth (currently)**

- ❌ Do NOT automatically create biospheres for Mars, Venus, or other planets yet
- ✅ Only Earth should have a biosphere record (current game state)
- ℹ️ Reason: Other worlds do not have life. Biospheres should only exist on habitable/terraformed worlds
- ℹ️ Future: When terraforming mechanics are implemented, other worlds can have biospheres created

**The Gating Logic: "Can biomes exist on the surface without supporting tech?"**

The core question drives the biosphere creation:
- **Can liquid water exist on the surface?** This is the primary trigger.
- ✅ **Earth**: YES (liquid water oceans) → Biomes can exist naturally → Has biosphere record
- ❌ **Mars**: NO (too cold, water frozen) → Biomes need worldhouse/tech → NO biosphere record
- ❌ **Venus**: NO (too hot, water vaporized) → Biomes need worldhouse/tech → NO biosphere record
- ❌ **Titan**: NO (too cold, liquid methane) → Biomes need worldhouse/tech → NO biosphere record
- 🔄 **Terraformed Mars (future)**: YES (after terraforming) → Liquid water becomes viable → Gets biosphere record

**Implementation Impact:**
- Gate on surface habitability: presence of liquid water on surface
- Check planet's atmosphere, temperature, and pressure for water viability
- When terraforming reaches target state (liquid water possible), biosphere creation auto-triggers
- This prevents data model inconsistency while respecting game design

**UI Pattern: Biosphere = nil means no biomes**

- **If `celestial_body.biosphere` is nil** → Biomes button DISABLED, no biomes rendered
- **If `celestial_body.biosphere` exists** → Biomes button ENABLED, biomes render when clicked
- Apply this check in surface_view.js and monitor.js views
- This is the clean, canonical check (not defensive data checks)

**The underlying logic**: Biosphere exists ↔ Liquid water viable on surface
- Surface with liquid water = biosphere possible = biomes can render
- Surface without liquid water = no biosphere = biomes require worldhouse tech only

**Exception: Worldhouse structures can have constructed biomes**
- ℹ️ Worldhouse biomes are engineered/artificial, not natural (greenhouse, biolab, terrarium)
- ✅ Worldhouses CAN have biomes even on planets with nil biosphere (Mars, Venus - no liquid water)
- ✅ Worldhouse biomes are a separate rendering layer (not tied to celestial_body.biosphere)
- 🎮 Game design: Players can build artificial environments before terraforming

---

## Context

During today's debug session (2026-07-12), the planetary view biome rendering was not working because:
- Earth (ID 2493) had complete biome grid data in `terrain_map` (180x90 grid, 10 biome types)
- BUT Earth had **no biosphere record** in the database
- The monitor view was checking `planetData.has_biosphere` flag (which depends on DB record existing)
- This caused biome rendering to be skipped despite data being present

**Temporary Fix Applied**: Changed monitor.js to check actual grid data instead of the server flag (commit `3f6078b3`). This works but masks a deeper issue.

**Root Cause**: Data integrity gap. Earth has biome grids and SHOULD have a biosphere record. This is an architectural problem, not a rendering bug.

---

## Scope Note

**This task addresses PLANETARY biospheres only.** Worldhouse structures have their own biome system:
- Worldhouses can have constructed biomes regardless of their planet's biosphere status
- Worldhouse biomes are engineered/artificial, not natural planetary biospheres
- Worldhouse rendering is independent and not part of this task
- This task focuses on: CelestialBody → Geosphere → BiomeGrid, and CelestialBody → Biosphere

### Architecture Gotchas

⚠️ **GOTCHA 1: Biosphere gating is based on liquid water availability, not planet type**
- ❌ Wrong: Hard-coding logic for Earth specifically (planet ID or `.earth?` check)
- ✅ Right: Gate on liquid water surface availability (`can_support_surface_life?` or similar)
- Why: This allows future terraforming to expand to Mars/Venus by making liquid water viable
- Implementation: Check planet's atmosphere, temperature, pressure for water surface viability

⚠️ **GOTCHA 2: Unclear biosphere creation trigger**
- ❌ Wrong: Assuming biosphere records are created when terrain_map is generated (might be correct)
- ✅ Right: Determine the actual intended lifecycle — when SHOULD biosphere records exist?
- Why: Currently there's no automatic creation path defined

⚠️ **GOTCHA 3: Seeding vs. Runtime Creation**
- ❌ Wrong: Creating biosphere records via seed data alone (doesn't help planets created programmatically)
- ✅ Right: Implement automatic creation in the code path that generates/assigns terrain_map data
- Why: Need to cover both seeded and dynamically generated planets

⚠️ **GOTCHA 4: Existing planets without biosphere**
- ❌ Wrong: Ignoring all planets and assuming all need biosphere records
- ✅ Right: Create a migration to backfill biosphere records only for planets where liquid water can exist on surface
- Why: Data must be consistent for viable planets, but non-viable planets should remain empty

---

## Problem Statement

**Current State:**
- Planets CAN have `geosphere.terrain_map` with biome grid data
- BUT planets may NOT have a `biosphere` record
- The system works around this with defensive checks in JavaScript
- Data model allows inconsistent state

**Expected State:**
- Planets where liquid water can exist on surface MUST have biosphere records
- Planets where liquid water CANNOT exist on surface should NOT have biosphere records
- Biosphere records should be created automatically when terrain_map is assigned AND liquid water is viable on surface
- Migration should backfill biosphere records for all planets where liquid water can exist on surface
- Views can trust that biospheres only exist where surface life is viable
- Future: When terraforming is implemented, achieving liquid water viability on surface will trigger automatic biosphere creation

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
1. Finds planets where `can_support_surface_life?` is true BUT no biosphere record exists
2. Creates biosphere records with sensible defaults
3. For Earth specifically:
   - `habitable_ratio: 0.95` (Earth is fully habitable)
   - `biodiversity_index: 0.95` (Earth has high biodiversity)
   - `vegetation_cover: 0.75` (most of Earth is vegetated)
   - `biome_count: 10` (Earth has 10 biome types)
   - `soil_health: 80` (Earth has good soil)
   - `soil_organic_content: 0.08` (Earth has organic matter)
   - `soil_microbial_activity: 0.8` (Earth has active microbes)

**File**: `db/migrate/20260712_create_missing_biosphere_records.rb`

**Important**: Only create biosphere records for planets where surface life is viable (liquid water exists).
- ✅ Create for: Earth (has liquid water), (future) terraformed Mars/Venus (after water becomes viable)
- ❌ Skip: Mars (frozen water), Venus (vaporized water), Titan (liquid methane, not water)

### Step 4 — Add automatic biosphere creation to terrain_map assignment

Identify where `geosphere.terrain_map =` is called, and add logic to create biosphere for planets where surface biomes are viable:

```ruby
# In the service/method that assigns terrain_map:
# After terrain_map is assigned, check if this planet can support surface biomes
# (Primary check: Can liquid water exist on the surface?)
if celestial_body.can_support_surface_life?  # or similar flag/method
  unless celestial_body.biosphere.present?
    celestial_body.create_biosphere(
      habitable_ratio: 0.95,
      biodiversity_index: 0.95,
      vegetation_cover: 0.75,
      biome_count: 10,
      soil_health: 80,
      soil_organic_content: 0.08,
      soil_microbial_activity: 0.8
    )
  end
# Planets that cannot support surface biomes (Mars, Venus) skip biosphere creation
# They can have worldhouse biomes, but those are independent
end
```

**CRITICAL**: Gate the biosphere creation on surface life support viability (especially liquid water availability).
- **Earth**: Has liquid water oceans → `can_support_surface_life? = true` → Creates biosphere
- **Mars**: No liquid water (frozen) → `can_support_surface_life? = false` → Skips biosphere
- **Venus**: No liquid water (vaporized) → `can_support_surface_life? = false` → Skips biosphere
- **Terraformed planets (future)**: Liquid water becomes available → `can_support_surface_life? = true` → Creates biosphere

**Find or Implement**: Check if `can_support_surface_life?` or similar method exists. If not, you will need to add it based on:
- Planet atmosphere composition
- Surface temperature range (can water remain liquid?)
- Atmospheric pressure (can water remain liquid?)
- These factors should already exist in the planet data model

**ALSO REQUIRED**: After biosphere records exist, update JavaScript views to use the canonical check:
- **Old workaround** (temporary, in current code): `const hasBiosphere = layers.biomes && layers.biomes.grid`
- **New canonical** (after this task): Pass `has_biosphere: @celestial_body.biosphere.present?` from ERB
- **View behavior**: If `has_biosphere` is false, DISABLE biomes button and skip rendering
- **Why**: Cleaner pattern—biosphere existence is the source of truth, not data presence

**Deliverable**: Paste the hook location, implementation, and the condition check (can_support_surface_life).

### Step 5 — Update views to use canonical biosphere check

After biosphere records are created, update JavaScript views to check `has_biosphere` flag instead of defensive grid data check:

**In ERB templates** (surface_view.html.erb, planetary.html.erb):
```erb
<% has_biosphere = @celestial_body.biosphere.present? %>
terrain_data: terrain_map_data,
has_biosphere: <%= has_biosphere %>,
```

**In monitor.js** (replace temporary workaround with canonical check):
```javascript
// OLD WORKAROUND (remove after this task):
// const hasBiosphere = layers.biomes && layers.biomes.grid ? true : false;

// NEW CANONICAL (add after this task):
const hasBiosphere = planetData?.has_biosphere || false;

// UI BEHAVIOR:
if (!hasBiosphere) {
  // Disable biomes button
  document.querySelector('[data-layer="biomes"]').disabled = true;
  // Skip rendering
  return; // early exit from biome render
}
```

**In surface_view.js** (verify it's using the canonical check):
```javascript
// Should already have this pattern:
const hasBiosphere = planetData?.has_biosphere || false;
if (!hasBiosphere) {
  // Biomes button disabled, no rendering
}
```

**Deliverable**: Screenshots showing biomes button is disabled for Mars (no biosphere), enabled for Earth (has biosphere).

### Step 6 — Add spec to verify auto-creation

Create a spec that:
1. Creates a celestial body with terrain_map data
2. Verifies that a biosphere record is automatically created
3. Confirms biosphere has correct attributes

**File**: `spec/models/celestial_bodies/spheres/biosphere_spec.rb` (add to existing or create new)

### Step 7 — Run migration and verify

```bash
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rake db:migrate
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rails console -e development << 'EOF'
require 'celestial_bodies/celestial_body'
earth = CelestialBodies::CelestialBody.find(2493)
puts "Earth biosphere present? #{earth.biosphere.present?}"
puts "Earth biosphere ID: #{earth.biosphere.id}"
EOF
```

### Step 8 — Run RSpec to confirm no breakage

```bash
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rspec \
  spec/models/celestial_bodies/spheres/biosphere_spec.rb
```

Expected: all specs pass.

---

## Success Criteria

✅ **Data Integrity Restored (Earth Only)**
- Earth (ID 2493) now has a biosphere record with appropriate attributes
- Other planets (Mars, Venus, etc.) remain WITHOUT biosphere records
- Migration backfills Earth if biosphere record was missing

✅ **Automatic Creation Implemented (Earth Only)**
- When Earth's terrain_map is assigned/generated, biosphere record is automatically created
- Other planets do NOT get automatic biosphere creation (gated by planet type check)
- No manual workarounds needed for Earth

✅ **UI Pattern Enforced (Canonical Check)**
- Views use `has_biosphere` flag (not defensive grid data check)
- Biomes button DISABLED when `has_biosphere` is false (Mars, Venus, etc.)
- Biomes button ENABLED when `has_biosphere` is true (Earth only)
- No biomes render for planets with nil biosphere record

✅ **Design Constraints Enforced**
- Biosphere creation ONLY for Earth (habitable world)
- Other planets must have biospheres created via terraforming feature (not yet implemented)
- Code is structured to make it easy to add other worlds later

✅ **Specs Pass**
- RSpec: biosphere auto-creation spec added and passing (Earth scenario)
- RSpec: verify Mars/Venus do NOT get biospheres (negative test)
- No existing tests broken

✅ **Code Quality**
- Follows existing model patterns
- Sensible default values for Earth's biosphere
- Comments explain why only Earth gets automatic creation
- Gating logic clear and maintainable for future expansion

---

## Out of Scope

❌ **Worldhouse biomes** — Do NOT touch worldhouse structure biome system
- Worldhouses can have constructed/engineered biomes on any planet (Mars, Venus, etc.)
- Worldhouse biomes are independent from celestial_body.biosphere
- Worldhouse rendering has its own data model and view layer
- That system is handled separately (NOT part of this task)

---

## Future Work & Potential Overlap

⚠️ **When terraforming is implemented** (future feature):
- Terraformed worlds will eventually support natural biomes without enclosures
- Mars/Venus will get biosphere records when terraformed
- Those planets will have introduced biomes (not native, but now capable of supporting life)
- **Potential Overlap**: Worldhouse biomes could coexist with introduced natural biomes on terraformed worlds

**Design Pattern: Worldhouses as Terraforming Seeds**
- **Phase 1 (Current)**: Players build worldhouse structures with engineered biomes on bare planets (Mars, Venus — no liquid water on surface)
- **Phase 2 (Future)**: Terraforming initiates from worldhouse biomes as seed source
- **Phase 3 (Future)**: Biomes spread from worldhouse to planet surface as terraforming progresses
- **Phase 4 (Future)**: Terraforming achieves goal: liquid water becomes viable on surface → `can_support_surface_life? = true` → Create biosphere record
- **Result**: Planet graduates from tech-dependent to surface-viable biomes (liquid water exists)

**Integration Point for This Task:**
- The gating condition (`can_support_surface_life?`) checks for liquid water viability on surface
- When terraforming reaches completion, `can_support_surface_life?` becomes true (liquid water viable)
- This triggers automatic biosphere record creation via the same pattern in this task
- Biosphere record creation = "graduation" from worldhouse-dependent to planet-native biomes
- The migration/auto-creation logic will be reused for terraformed worlds
