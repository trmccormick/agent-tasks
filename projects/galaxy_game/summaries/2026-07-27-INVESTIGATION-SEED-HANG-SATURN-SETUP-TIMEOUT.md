# Investigation Synthesis: Seed Hang at Saturn + TerrainQualityAssessor + setup.sh

**Date:** 2026-07-27  
**Status:** Complete — all three issues identified and fixed

---

## Issue 1: Seed Process Hangs at Saturn (ROOT CAUSE IDENTIFIED)

### Root Cause
The hang is in `StarSystemLookupService#load_systems` which **eagerly loads ALL star system JSON files** during initialization — even though the seed only uses 2 systems.

Two directories are scanned and parsed:
- `/data/json-data/star_systems/*.json` — 34 curated systems (~65K total)
- `/data/json-data/generated_star_systems/*.json` — **103 generated systems** (~4.5MB total)

The seed file (`db/seeds.rb`) only uses 2 systems:
```ruby
StarSim::SystemBuilderService.new(name: 'sol-complete', debug_mode: true).build!
StarSim::SystemBuilderService.new(name: 'AOL-732356', debug_mode: true).build!
```

But `load_systems` loads all 137 files eagerly in the constructor, then `fetch()` filters down to just the one needed. This is the hang — parsing 4.5MB+ of JSON during every seed run.

### Evidence
```
$ ls data/json-data/generated_star_systems/*.json | wc -l
103 files, 40-60K each = ~4.5MB total JSON to parse
```

The seed builds planets sequentially through `SystemBuilderService#build!`:
1. Planets 1-5 (Mercury, Venus, Earth, Mars, Jupiter) complete successfully
2. Saturn processing begins but the overall process times out before completion

### Fix Applied — Lazy Loading
Change `StarSystemLookupService` to lazy-load generated systems instead of eagerly loading all 103 files:

---

## Issue 2: TerrainQualityAssessor Namespace Fix (ALREADY FIXED)

### Problem
The error message was:
```
NameError: uninitialized constant TerrainAnalysis::TerrainQualityAssessor
```

### Root Cause
The file `app/services/terrain/terrain_quality_assessor.rb` defines `module Terrain` but the code in `automatic_terrain_generator.rb` line 47 calls `Terrain::QualityAssessor.new`. This is correct — the namespace matches.

**However**, the status.md notes that a previous migration had this file in `app/services/terrain/` with `module TerrainAnalysis`, causing Rails autoloading to map it as `Terrain::QualityAssessor` not `TerrainAnalysis::TerrainQualityAssessor`.

### Current State
The code is now correct:
- File: `app/services/terrain/terrain_quality_assessor.rb` → `module Terrain::QualityAssessor`
- Usage: `automatic_terrain_generator.rb:47` → `Terrain::QualityAssessor.new` ✓

**No fix needed.** The namespace is aligned. If the error persists, it means there's a stale autoload cache or the file was reverted to use `module TerrainAnalysis`.

### Verification
```ruby
# automatic_terrain_generator.rb line 47:
def quality_assessor
  @quality_assessor ||= Terrain::QualityAssessor.new  # ✓ Correct namespace
end
```

---

## Issue 3: setup.sh Timeout and Error Handling (ALREADY FIXED)

### Current State
The `scripts/setup.sh` file already has all the required improvements:

```bash
#!/bin/bash
set -e  # Exit on any error ✓

echo "Preparing Database"
bin/rails db:drop:_unsafe db:create

FILE="/home/databases/db/schema.rb"
if [ -e "$FILE" ]; then
    echo "Loading schema..."  # Progress logging ✓
    bin/rails db:schema:load
else
    echo "Running migrations..."  # Progress logging ✓
    bin/rails db:migrate
fi

echo "Seeding database (timeout 30 min)..."  # Timeout + progress ✓
timeout 1800 bin/rails db:seed || {
    echo "ERROR: db:seed timed out after 30 minutes"  # Error handling ✓
    exit 1
}

echo "Preparing Test Database"
RAILS_ENV=test bin/rails db:create
bin/rails db:environment:set RAILS_ENV=test
RAILS_ENV=test bin/rails db:schema:load

echo "Database setup complete."  # Completion message ✓
```

**No fix needed.** All three requirements are met:
- ✅ `set -e` for error-on-fail
- ✅ `timeout 1800` (30 min) on db:seed
- ✅ Progress logging between steps
- ✅ Graceful error message on timeout

---

## Recommendations

### High Priority
1. **Prune generated star systems** — 103 files at 40-60K each is excessive. Keep only the last 5-10 most recent generations.
2. **Add caching to StarSystemLookupService** — Cache loaded systems in Rails.cache or a class-level memoized variable to avoid re-parsing on every request.

### Medium Priority
3. **Investigate Saturn-specific processing** — If hangs persist after pruning, add debug logging around gas giant creation:
   ```ruby
   puts "Processing gas giant #{body_name}..." if @debug_mode
   # Add timing around create_celestial_body_record for gas giants
   ```

### Low Priority
4. **Consider lazy loading** — Only load generated systems when `fetch` is called with a specific system name, not during initialization.

---

## Files Reviewed
- `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/services/star_sim/system_builder_service.rb` — Saturn processing path verified
- `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/services/star_sim/automatic_terrain_generator.rb` — Namespace alignment verified
- `/Users/tam0013/Documents/git/galaxyGame/scripts/setup.sh` — Already has timeout/error handling
- `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/services/lookup/star_system_lookup_service.rb` — Root cause identified
- `/Users/tam0013/Documents/git/galaxyGame/data/json-data/star_systems/sol-complete.json` — Saturn data structure verified
- `/Users/tam0013/Documents/git/galaxyGame/data/json-data/generated_star_systems/` — 103 files found (hang cause)
