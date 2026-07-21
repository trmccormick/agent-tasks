# Needs Review

Short-list of items flagged for Claude (planning review) or human decision.
Qwen: add an entry here instead of just noting something in status.md when
an escalation trigger below fires. Remove/archive entries once resolved —
this file should stay small. Full history stays in status.md.

---

## Escalation triggers — add an entry here when:

- Any file operation touches data/json-data/ or another gitignored path
  (wrong location, git add -f, revert of a move+tracking commit — this
  class of bug has hit 3 times, always needs a second check)
- A task is marked "complete" but the completion claim was not independently
  re-verified in the SAME session (e.g. you fixed something and inferred it
  works, but didn't re-run the actual test/rake/grep after the fix)
- A task's fix touches a system another already-completed task built
  assumptions on top of (cross-task dependency risk)
- Two research/design documents disagree about the same system
- You catch yourself repeating an identical action/output 3+ times without
  progress — stop, write an entry here, don't keep retrying

---

## Entry template

### [DATE] — [task name or file]
**What happened**: 
**What I already checked**: 
**What needs a second opinion**: 
**Status**: OPEN / RESOLVED (date + how)

---

## Current entries

### 2026-07-19 — CanonicalMapService 'jungle' Alias Bug
**What happened**: During verification of canonical_map_service.rb, found that 'jungle' alias resolves to 'tropical_jungle' canonical name, but 'tropical_jungle' is NOT a direct key in CANONICAL_TILE_MAP — only referenced as canonical value from other aliases (tropical_rainforest, rainforest, tropical_forest)

**What I already checked**: 
- Confirmed line 105 maps 'jungle' → 'tropical_rainforest'
- Confirmed lines 45-47 map tropical_* aliases → 'tropical_jungle' with layer :biome
- Confirmed CANONICAL_TILE_MAP lines 32-72 do NOT include direct 'tropical_jungle' entry
- Confirmed direct entries exist for: 'forest', 'swamp', 'grasslands', 'plains', 'mountains_snow_covered', 'mountains', 'rocky_plains', 'desert', 'ocean', 'savanna', 'agricultural_fields'

**What needs a second opinion**: 
- Is 'tropical_jungle' intentionally missing (bug) or should 'jungle' alias map to 'forest' instead?
- Should direct entry be added to CANONICAL_TILE_MAP for 'tropical_jungle', or should all tropical_* aliases be consolidated to 'forest'?
- Impact: normalize_biome('jungle') returns valid canonical name but subsequent lookup fails

**Status**: RESOLVED (2026-07-19) — Not a bug. The canonical value in CANONICAL_TILE_MAP is a terminal filename (e.g., 'tropical_jungle.png'), not a further lookup key. Alias resolution stops at the first mapping; no recursive lookup occurs. Added clarifying comment above CANONICAL_TILE_MAP to prevent future re-flagging.

---

### 2026-07-19 — RSpec Baseline Growth Unexplained
**What happened**: Full RSpec suite shows 4343 examples (201 new since 2026-07-13), 4 failures. Only 2 confirmed flaky baseline + 2 regressions identified.

**What I already checked**: 
- Confirmed latest run: 4343 examples, 4 failures, 56 pending
- Identified 2 flaky baseline: game_data_generator_spec.rb:13, material_lookup_service_spec.rb:251
- Identified 2 regressions: planetary_monitor_spec.rb:171 (surface view), orbital_shipyard_service_spec.rb:129 (NEW — distributes materials across projects)
- Did not investigate root cause of either regression

**What needs a second opinion**: 
- Are the 2 regressions related to recent changes or pre-existing?
- Should baseline be updated to 4343/4 with details on what caused growth?
- orbital_shipyard_service_spec.rb:129 is NEW — did something change in that service recently?

**Status**: RESOLVED (2026-07-19) — Root cause investigation complete.
- **planetary_monitor_spec.rb:171**: Test data bug. Line 165 creates `'elevation' => [1.0]` (1D array with single Float). `extract_width` in TerrainDataBuilder line 88 falls through to elevation, calls `.first` → gets Float `1.0`, then `.length` on Float → NoMethodError. **Fix:** Change line 165 to `'elevation' => [[1.0]]` (2D grid) to match code's expectation that elevation is always a grid of rows.
- **orbital_shipyard_service_spec.rb:129**: NOT a current failure. Ran spec individually — 25 examples, 0 failures. Was never a regression or has already been fixed by a prior commit. The "4 failures" baseline in this entry is outdated.

---

### 2026-07-19 — Unit Sprite Extraction Visual Validation Gap (Task 2026-07-13-HIGH-FEATURE-UNIT-LAYER-RENDERING)
**What happened**: Unit sprite extraction completed with 18/18 RSpec tests passing, sprites committed to git (galaxyGame `9ee37c6b`). Post-completion visual review revealed sprites are **misaligned and malformed**: sprite_00.png (row 0, col 0) properly framed ✓, but sprite_15.png (row 3, col 3) badly misaligned — rover shoved into corner with large blank margin, partially cut off ✗

**What I already checked**: 
- Confirmed extraction math correct (`x = col*256, y = row*256` for 4×4 grid on 1024×1024 image)
- Confirmed RSpec suite validates only structure (sprite_index_for(), UNIT_SPRITE_MAP entries, JSON export shape)
- Confirmed RSpec does NOT validate PNG visual content, framing, or completeness
- Confirmed source image is AI-generated collage (`test-images/ChatGPT Image Mar 4 2026...`), not precision-gridded sprite sheet
- Confirmed cumulative grid drift: cells near origin (0,0) look fine, cells far from origin show worst misalignment — signature of non-uniform source image

**What needs a second opinion**: 
- **Are sprites production-ready or placeholder?** Current status: committed as game assets, not flagged as temporary/mock
- **Should Layer 5 rendering be gated behind feature flag** until proper sprite assets exist?
- **Re-crop same source with adjusted math (bad idea) vs. pause production use and plan proper asset generation?**

**Recommendation (from async review)**:
- Option 1 (Recommended): Mark sprites as placeholder, gate Layer 5 rendering
- Option 2 (Better long-term): Plan proper asset generation as Phase 2 Asset Registry work
- Option 3 (Not recommended): Re-crop with adjusted offsets — treats symptom, not cause

**Status**: RESOLVED (2026-07-20) — Layer 5 rendering gated behind feature flag. `showUnits` set to `false` in surface_view.js with quality note comment linking back to this entry. Sprites NOT re-cropped. Asset regeneration remains separate, unscoped work.

---

(more entries go here as needed)

---

### 2026-07-21 — InfrastructureCostCalculator calls non-existent method
**What happened**: infrastructure_cost_calculator.rb:152 calls `AIManager::PrecursorCapabilityService.can_produce?(destination, material.chemical_formula)` — a two-arg method that does not exist on PrecursorCapabilityService. Only `can_produce_locally?(resource)` (one-arg) exists.

**What I already checked**:
- Confirmed via grep: only `can_produce_locally?(resource)` exists in precursor_capability_service.rb
- Confirmed the one call site at infrastructure_cost_calculator.rb:152
- Confirmed `can_produce_locally?` resolves celestial_body internally via settlement.location.celestial_body — doesn't accept a destination param even if renamed
- No specs exist for InfrastructureCostCalculator (grep spec/ returned zero matches)
- One caller in test script only: galaxy_game/test_realistic_costs.rb (not in spec/)

**What needs a second opinion**:
- Is this code path ever actually exercised? (calculate_local_production_discount is called from calculate_cost, need to confirm whether calculate_cost has any live callers/specs, or if this is dead/untested code)
- If live: fix is to change the call to `can_produce_locally?(material.chemical_formula)` and remove the destination arg — but confirm InfrastructureCostCalculator has access to the right settlement context for that method to resolve celestial_body correctly, since it currently operates on a destination CelestialBody directly, not a settlement

**Status**: OPEN
