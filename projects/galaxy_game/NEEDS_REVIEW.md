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

**Status**: OPEN — needs triage before updating status.md baseline further

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

**Status**: OPEN — needs handoff decision on whether to: (a) keep as placeholder/gate, (b) commit to proper asset regen, or (c) investigate alternative source material

---

(more entries go here as needed)
