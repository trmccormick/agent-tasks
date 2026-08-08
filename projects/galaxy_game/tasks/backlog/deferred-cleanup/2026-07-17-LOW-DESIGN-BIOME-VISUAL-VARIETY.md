---
title: "Design Note — Biome visual variety via deterministic tile variants"
priority: LOW
status: backlog
phase: phase1
owner: Design Agent (Gemini/ChatGPT)
type: architecture
system_domain: BIOME_RENDERING
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
created: 2026-07-17
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Design Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/drafts/2026-07-17/2026-07-17-LOW-DESIGN-BIOME-VISUAL-VARIETY.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/drafts/2026-07-17/2026-07-17-LOW-DESIGN-BIOME-VISUAL-VARIETY.md \
         projects/galaxy_game/tasks/active/2026-07-17-LOW-DESIGN-BIOME-VISUAL-VARIETY.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-17-LOW-DESIGN-BIOME-VISUAL-VARIETY.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-DESIGN-BIOME-VISUAL-VARIETY.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# DESIGN NOTE: Biome visual variety via deterministic tile variants

**Status**: DRAFT (not active — awaiting human review)  
**Priority**: LOW (deferred until CanonicalMapService + elevation research complete)  
**Type**: architecture/design note  
**Created**: 2026-07-17  

---

## Purpose

Capture the ChatGPT proposal for biome visual variety without scoping the asset generation project tonight. This is a **design note**, not an implementation task. It captures the idea, its constraints, and its dependency order so it can be revisited later.

---

## Proposal Summary

**Problem**: Each biome currently maps to a single tile image (e.g. `forest.png`). On large worlds this creates visible repetition — identical tiles appearing in adjacent cells with no variation.

**Solution**: Canonical biome name → N art variants selected deterministically via hash(world_seed, x, y). The same world always renders identically without per-tile storage.

### How It Works

```
1. Biome classification produces canonical key: "boreal_forest"
2. CanonicalMapService resolves to tile group: "forest.png" (group of 12 variants)
3. Deterministic hash selects variant index:
   variant_index = hash(world_seed, x, y) % N_VARIANTS
4. Final tile: "forest_01.png" through "forest_12.png" (or similar naming)
5. Same world + same coordinates → always same variant (deterministic)
6. Different worlds → different variant distributions (varied)
```

### Key Constraints

- **Deterministic**: `hash(world_seed, x, y)` ensures reproducibility — no per-tile storage needed
- **No visible repetition**: N variants (e.g. 12) means adjacent cells rarely show the same variant
- **Asset generation is separate**: This design note captures the idea; actual asset creation is a coordinated project with ChatGPT

---

## Dependency Order (CRITICAL — do not violate)

### (a) CanonicalMapService bug fixes MUST land first

Before any visual variety work, CanonicalMapService must be fixed:

1. **Ocean/mountains missing from CANONICAL_TILE_MAP** — these Layer 0 assets need entries
2. **Broken jungle alias** — `jungle` → `tropical_jungle.png` not resolving correctly
3. **Canonical value should resolve to a biome KEY, not a literal filename** — the current design returns `{ canonical: 'forest.png', layer: :biome }` but it should return `{ canonical: 'boreal_forest', layer: :biome }` so the caller can look up the variant group

See `2026-07-17-HIGH-PHASE1-CANONICALMAPSERVICE.md` for specifics.

### (b) Elevation-in-classification research should resolve first

Item 1 above must complete before this design note is actionable, because:

- If elevation-based biome classification is added, the **biome taxonomy may change**
- New biome categories (e.g. alpine tundra at high elevation) may need art variants
- The canonical tile set may need expansion before visual variety work begins

### (c) Actual asset generation is a separate coordinated project

Do NOT scope the asset generation project tonight. This design note captures:
- The idea (deterministic variant selection)
- Its dependency order (CanonicalMapService fixes → elevation research → asset generation)
- The constraint that asset creation requires ChatGPT coordination

---

## Design Details

### Variant Naming Convention

```
forest_01.png, forest_02.png, ... forest_12.png
grasslands_01.png, grasslands_02.png, ... grasslands_12.png
tropical_jungle_01.png, tropical_jungle_02.png, ... tropical_jungle_12.png
... etc.
```

### Hash Function

Pseudocode for variant selection:

```javascript
function selectVariant(canonicalKey, worldSeed, x, y) {
  // Deterministic hash of seed + coordinates
  const hash = murmurHash3(`${worldSeed}:${x}:${y}`);
  const variantCount = getVariantCount(canonicalKey); // e.g. 12 for forest group
  const index = Math.abs(hash) % variantCount;
  return `${canonicalKey}_${String(index + 1).padStart(2, '0')}.png`;
}
```

### CanonicalMapService Integration

After fixes land, `CanonicalMapService` should expose:

```ruby
class CanonicalMapService
  # Returns the tile GROUP for a biome (not a single file)
  def tile_group(canonical_key)
    # Returns array of variant filenames: ['forest_01.png', 'forest_02.png', ...]
  end

  def variant_count(canonical_key)
    tile_group(canonical_key).length
  end
end
```

### Surface View Integration

In `surface_view.js`, the rendering loop would change from:

```javascript
// Before: single tile per biome
const tile = biomeRenderer.getTile(biomeKey);
ctx.drawImage(tile, x * TILE_SIZE, y * TILE_SIZE);
```

To:

```javascript
// After: deterministic variant selection
const group = CanonicalMapService.tile_group(canonicalKey);
const variantIndex = selectVariant(canonicalKey, worldSeed, x, y);
const tile = biomeRenderer.getTile(group[variantIndex]);
ctx.drawImage(tile, x * TILE_SIZE, y * TILE_SIZE);
```

---

## Open Questions (for future design session)

1. **How many variants per biome?** 8? 12? 16? Depends on art budget and repetition threshold
2. **Should terrain tiles also have variants?** (desert_01...desert_12, mountains_01...mountains_12)
3. **Should hydrosphere tiles have variants?** (ocean_01...ocean_12 for different wave patterns)
4. **Asset generation pipeline**: Who creates the variants? ChatGPT coordinated project?
5. **Performance impact**: Does variant selection add measurable overhead to rendering loop?

---

## Stop Conditions

- This is a design note, not an implementation task
- Do NOT create any code, assets, or configurations tonight
- Wait for human review and prioritization before any follow-up work

---

## Dependencies

**Blocked by**: 
- CanonicalMapService bug fixes (item 3a above)
- Elevation-in-classification research (item 1 above)

**Blocks**: Asset generation project (separate coordinated effort with ChatGPT)  
**Related tasks**: 
- `2026-07-17-HIGH-PHASE1-CANONICALMAPSERVICE.md` (CanonicalMapService — Phase 1 Task 1)
- Elevation-in-classification research (item 1 above)

---

## Handoff Summary
HANDOFF SUMMARY: [design note captured] | [dependencies documented] | [awaiting human review]
