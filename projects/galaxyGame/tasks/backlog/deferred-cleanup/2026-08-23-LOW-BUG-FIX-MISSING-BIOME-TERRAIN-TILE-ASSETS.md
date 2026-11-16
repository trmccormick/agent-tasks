---
status: backlog
priority: LOW
type: bug-fix
system_domain: FRONTEND
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

You are Implementation Agent.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/deferred-cleanup/2026-08-23-LOW-BUG-FIX-MISSING-BIOME-TERRAIN-TILE-ASSETS.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
git mv projects/galaxy_game/tasks/backlog/deferred-cleanup/2026-08-23-LOW-BUG-FIX-MISSING-BIOME-TERRAIN-TILE-ASSETS.md
projects/galaxy_game/tasks/active/2026-08-23-LOW-BUG-FIX-MISSING-BIOME-TERRAIN-TILE-ASSETS.md
Then open the moved file and change: status: backlog → status: active
Paste the output of the find command below before proceeding:
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks
-name "2026-08-23-LOW-BUG-FIX-MISSING-BIOME-TERRAIN-TILE-ASSETS.md"

READ FIRST (after Step 0): Task file contains all diagnostic steps below.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
Filename pattern: 2026-08-23-BUG-FIX-BIOME-TERRAIN-ASSETS.md
Chat is for questions only — never paste synthesis into chat.

This is LOW priority / non-MVP. Do not pick this up ahead of anything MVP-relevant.


---

# TASK: Missing biome and terrain tile PNG assets

**Status**: BACKLOG
**Priority**: LOW
**Type**: bug-fix
**Created**: 2026-08-23
**Last Updated**: 2026-08-23

---

## Context

The full RSpec suite has a long-standing, pre-existing (not a new regression) block of failures in `biome_renderer_config_spec.rb` and `terrain_tile_renderer_spec.rb`: missing biome PNG files under `public/assets/biomes/`, missing terrain tile variant files/directories under the expected terrain-family structure, and a filename mismatch between `biomes.json` and what's actually on disk. Confirmed 2026-08-23 via backlog/completed search: no existing task covers this gap. Closest related work (`2026-07-15 Session10 Biome Tilesheet Architecture` — analysis only, `2026-07-17 Biome Visual Variety` — tile-variant design enrichment, `2026-07-12` and `2026-08-08` — unrelated JS/require-path fixes) does not address the missing assets themselves.

This is confirmed **non-MVP-blocking** — the current MVP target is the Luna rake build sequence, not tileset/biome rendering. This task exists to track and eventually close the gap, not to be prioritized ahead of MVP work.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md`
- `docs/new_agent/rules/GUARDRAILS.md`
- `2026-07-15-HIGH-DATA-SESSION10-BIOME-TILESHEET-ARCHITECTURE.md` (completed) — read for the intended tilesheet architecture/naming convention before regenerating anything, so replacement assets match the documented structure rather than guessing a new one

---

## Problem Statement

**Current behavior**: `biome_renderer_config_spec.rb` and `terrain_tile_renderer_spec.rb` fail — biome PNGs referenced in `biomes.json` are missing from `public/assets/biomes/`, terrain tile variant directories/files (5 families × 9 variants expected, per `terrain_tile_renderer_spec.rb`) are missing or incomplete under the expected terrain path, and at least one PNG filename in `biomes.json` doesn't match what's on disk.

**Unknown / to be determined by this task**:
1. Are these assets missing entirely (never generated) or generated-but-misplaced (e.g. the same `data/json-data/` vs `<app>/data/json-data/` gitignore-boundary bug flagged as a recurring pattern in this project)? Check the actual expected paths against what exists on disk before assuming a full regeneration is needed.
2. Does the docker volume mount (`data/images/terrain` → `public/assets/terrain`, referenced in `terrain_tile_renderer_spec.rb:50`) actually work as configured, or is that itself part of the gap?
3. Is asset regeneration needed at all here, or just path/mount/naming correction? These are different fixes with very different cost — don't assume regeneration without checking that the assets don't already exist somewhere on disk first.

**Expected behavior**: `biome_renderer_config_spec.rb` and `terrain_tile_renderer_spec.rb` pass — all expected biome PNGs and 45 terrain tile files (5 families × 9 variants) exist at their expected paths, `biomes.json` filenames match disk exactly, tiles are the expected 150×150px dimensions.

---

## Files Involved

### Primary Files — likely relevant, confirm before editing
| File | Purpose | Key Method/Section |
|---|---|---|
| `spec/services/tileset/biome_renderer_config_spec.rb` | Failing spec — biome PNG existence checks | `[FILL IN]` |
| `spec/services/tileset/terrain_tile_renderer_spec.rb` | Failing spec — terrain tile family/variant existence + dimension checks | `[FILL IN]` |
| `[FILL IN]` — likely `data/json-data/.../biomes.json` | Source of truth for expected biome PNG filenames | `[FILL IN]` |
| `[FILL IN]` — expected biome/terrain asset directories | Where PNGs should actually live | `[FILL IN]` |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `2026-07-15-HIGH-DATA-SESSION10-BIOME-TILESHEET-ARCHITECTURE.md` | Documents the intended tilesheet naming/architecture — check any regenerated assets match this rather than inventing a new convention |
| ChatGPT image-gen session output (if any exists for these assets) | If these were meant to come from the ChatGPT asset-gen pipeline (per [[asset-pipeline]] tracking), check whether they were ever actually generated and just not placed correctly |

### Migration (if needed)
- [x] No migration needed — asset files and/or path/mount fix only

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
[Standard — see dispatch interface above]

### Step 1 — Diagnose before regenerating anything
Check whether the missing assets are (a) genuinely never-created, (b) misplaced due to a path/gitignore-boundary issue, or (c) blocked by a broken docker volume mount. Report which of these it is with file/path evidence before proceeding to a fix.

### Step 2 — Fix per diagnosis
- If (b) or (c): fix the path/mount/naming issue — no asset generation needed.
- If (a): this needs actual PNG assets generated. Given [[asset-pipeline]]'s ChatGPT image-gen capacity constraint (capped/limited daily availability), flag this back to Tracy rather than assuming generation capacity is available — do not consume image-gen budget on this LOW-priority task without explicit confirmation it's the right day to spend it.

### Step 3 — Verify
Run both specs in isolation, then the full file, confirm 0 failures, confirm no regressions elsewhere.

### Step 4 — Synthesis Report

SYNTHESIS REPORT
Diagnosis: [missing entirely / misplaced / mount broken / mixed]
Fix applied: [path fix / mount fix / asset generation needed — flagged back, not done]
Verification: [pass/fail counts, isolation + full file]
RISK: [any other spec/feature relying on these same asset paths]


---

## Acceptance Criteria
- [ ] Root cause diagnosed (missing vs misplaced vs mount) with evidence, not assumed
- [ ] If asset generation is required, this is flagged back rather than silently consuming image-gen budget
- [ ] `biome_renderer_config_spec.rb` and `terrain_tile_renderer_spec.rb` both pass, 0 failures
- [ ] No regressions in the rest of the suite

---

## Stop Conditions — escalate to user immediately if:
- Fixing this requires generating new image assets (per the image-gen capacity constraint, this needs Tracy's explicit go-ahead on timing, not silent consumption of that day's budget)
- The docker volume mount configuration itself needs changing (infrastructure-level, wider blast radius than this task's scope)

---

## Dependencies
**Blocked by**: none
**Blocks**: none
**Related tasks**: `2026-07-15-HIGH-DATA-SESSION10-BIOME-TILESHEET-ARCHITECTURE.md` (completed, architecture reference), `2026-07-17-LOW-DESIGN-BIOME-VISUAL-VARIETY.md` (backlog, separate enrichment work — do not conflate)

---

## Completion Report
*Filled in by the implementing agent after completion*

## Handoff Summary
*Filled in at end of session*