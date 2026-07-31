---
status: completed
priority: HIGH
type: feature
system_domain: UI
mvp_alignment: OTHER
local_worker_safe: true
created: 2026-07-13
last_updated: 2026-07-31
completed: 2026-07-31
---

## ⚡ Minimal Handoff (Copy this to send to agent)
```text
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-13-HIGH-FEATURE-SPRITE-TILES-SURFACE-VIEW-INTEGRATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-13-HIGH-FEATURE-SPRITE-TILES-SURFACE-VIEW-INTEGRATION.md \
         projects/galaxy_game/tasks/active/2026-07-13-HIGH-FEATURE-SPRITE-TILES-SURFACE-VIEW-INTEGRATION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-13-HIGH-FEATURE-SPRITE-TILES-SURFACE-VIEW-INTEGRATION.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-07-13-FEATURE-SPRITE-TILES-SURFACE-VIEW-INTEGRATION.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Sprite Tiles → Surface View Integration
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-07-13
**Last Updated**: 2026-07-30

## Context

45 sprite tiles (138×145px) have been extracted from the original sprite sheet and organized by terrain family. This task integrates them into the surface view rendering system:
- **Terrain families**: dust, frozen, regolith, temperate, volcanic
- **9 variants per terrain type**: variant_01.png through variant_09.png
- **Location**: `/Users/tam0013/Documents/git/galaxyGame/data/images/terrain/`
- **Status**: Ready for integration; not git-tracked (data/ in .gitignore)

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Sprite tiles live in `data/images/terrain/` which is `.gitignore`d — they are NOT part of the Rails asset pipeline by default.
- ❌ Wrong: Put tiles in `app/assets/images/` or `public/images/` without considering that data files should stay in data/
- ✅ Right: Use a Rails middleware or controller action to serve tiles from data/ at runtime, or symlink/copy during build
- Why: The data/ directory is intentionally excluded from git and asset pipeline; serving them requires explicit routing

⚠️ **GOTCHA 2**: surface_view.js currently renders with canvas (not DOM elements). Tile integration may require switching to DOM-based rendering or using canvas `drawImage` with preloaded sprites.
- ❌ Wrong: Assume existing canvas rendering can directly use PNG file paths without loading them into Image objects first
- ✅ Right: Determine whether the current surface view uses canvas or DOM; if canvas, preload all 45 tiles as Image objects and cache them
- Why: Canvas `drawImage` requires preloaded Image objects, not file URLs — this affects the asset loading strategy

⚠️ **GOTCHA 3**: Terrain type mapping between Ruby (server-side) and JS (client-side) must be consistent.
- ❌ Wrong: Use different terrain enum names in Ruby vs. JS tile selector logic
- ✅ Right: Verify the Ruby terrain enum values match the directory names under `data/images/terrain/` exactly
- Why: Mismatched naming will cause 404s when the JS layer requests tiles that don't exist

---

## REQUIRED Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Sprite Tiles → Surface View Integration
**Status**: [backlog → active → completed]
**Date**: YYYY-MM-DD

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| data/images/terrain/ | Verify 45 tiles exist and are named correctly | [not started / pending / done] |
| surface_view.js (or equivalent) | Determine rendering approach (canvas vs DOM) | [not started / pending / done] |
| Rails routes/config | Design tile serving strategy | [not started / pending / done] |

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
- ❌ Assuming tiles can be served via Rails asset pipeline — instead ✅ Design explicit tile serving route or middleware
- ❌ Assuming canvas rendering works with file URLs — instead ✅ Preload all 45 tiles as Image objects
- ❌ Using different terrain names in Ruby vs JS — instead ✅ Verify enum-to-directory name mapping

---

**SYNTHESIS COMPLETE.** Ready to proceed with [PRIORITY 1 / PRIORITY 2 / etc].
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Scope

### Phase 1: Tile Inventory Verification
1. Verify all 45 tiles exist in `data/images/terrain/` (5 terrain types × 9 variants)
2. Confirm file naming convention: `<terrain_type>/variant_XX.png`
3. Validate image dimensions are 138×145px for all tiles

### Phase 2: Tile Serving Strategy
1. **Option A**: Rails controller action to serve tiles from data/ (recommended for non-git-tracked assets)
   - `GET /tiles/:terrain_type/variant_XX.png` → serves from data/images/terrain/
   - Add cache headers for performance
2. **Option B**: Symlink/copy tiles into public/assets/ during build/deploy
3. Document chosen approach and rationale

### Phase 3: Surface View Tile Integration
1. Determine current surface view rendering approach (canvas vs DOM)
2. If canvas: preload all 45 tiles as Image objects, cache in a tile map
3. If DOM: update partial to render `<img>` tags with correct tile URLs
4. Map terrain type + variant selection to tile files (seeded random or coordinate-based deterministic)

### Phase 4: Testing
1. Verify all 45 tiles load without errors in surface view
2. Test terrain-to-tile mapping (all 5 types × 9 variants)
3. Ensure no broken image references in rendered HTML
4. Add RSpec specs for tile loading and view rendering

## Acceptance Criteria
- [ ] All 45 tiles accessible via chosen serving strategy
- [ ] Surface view displays tiles by terrain type with correct variant selection
- [ ] Tile variant selection working (seeded random or coordinate-based deterministic)
- [ ] No broken image references in rendered HTML
- [ ] RSpec specs added for tile loading and view rendering
- [ ] Surface view UI updates documented

## Blockers
- Tiles are in data/ (.gitignore) — serving strategy must account for this
- Current surface_view.js rendering approach (canvas vs DOM) unknown until investigation
- Terrain enum values in Ruby must match directory names exactly

## Dependencies
- **Upstream**: 2026-07-13-HIGH-FEATURE-CIV4-SURFACE-VIEW-GAMEPLAY.md (gameplay layer may depend on tile rendering)
- **Related**: Sprite extraction pipeline (already completed — tiles exist in data/images/terrain/)

## Notes
- UI phase is undefined — this task establishes tile integration pattern for future UI work
- Sprite tiles are world-agnostic (color/texture only, no world-specific identifiers)
- Variant naming is simple (variant_01–09) to enable straightforward selection logic
- No git-tracking of tile data files (correct behavior — data/ in .gitignore)

## Next Steps
1. Move task to active/ and begin investigation
2. Verify all 45 tiles exist with correct dimensions
3. Document current surface view rendering approach
4. Design tile serving strategy (controller action vs symlink)
5. Implement and test with subset of tiles first
6. Report findings and blockers

## Stop Conditions
- Stop if fewer than 45 tiles exist in data/images/terrain/ — report missing files before proceeding
- Stop if surface_view.js rendering approach is unclear after investigation — document blocker before implementing
- Stop if Ruby terrain enum values don't match directory names under data/images/terrain/ — report mismatch before implementing

## Completion Report

When done, provide:
1. **Files modified**: List all files changed with brief description
2. **New files created**: List any new files (tile serving controller, etc.)
3. **Tile serving strategy**: Which approach was chosen and why
4. **Test coverage**: Which RSpec specs were added and what they cover
5. **Known limitations**: Any tiles or features deferred

## Handoff Summary

**Task**: Sprite Tiles → Surface View Integration
**Status**: backlog → active → completed
**Type**: feature (asset pipeline integration + rendering)
**Key Risk**: data/ directory is .gitignore'd — serving strategy must explicitly route to non-pipeline assets
**Approach**: Verify tile inventory → design serving strategy → integrate into surface view → test all 45 tiles

