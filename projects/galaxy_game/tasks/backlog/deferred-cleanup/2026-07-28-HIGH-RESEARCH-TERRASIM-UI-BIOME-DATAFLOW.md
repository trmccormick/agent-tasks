---
status: backlog
priority: HIGH
type: research
system_domain: TERRA_SIM
mvp_alignment: OTHER
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Research Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/research/2026-07-28-HIGH-RESEARCH-TERRASIM-UI-BIOME-DATAFLOW.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/research/2026-07-28-HIGH-RESEARCH-TERRASIM-UI-BIOME-DATAFLOW.md \
         projects/galaxy_game/tasks/active/2026-07-28-HIGH-RESEARCH-TERRASIM-UI-BIOME-DATAFLOW.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start research until this is done.
  If the file is not found at that exact path, run `find` for it across the whole tasks/
  tree first (it may be filed under a different backlog subfolder), paste that output,
  then git mv from wherever it actually is.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-28-HIGH-RESEARCH-TERRASIM-UI-BIOME-DATAFLOW.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-07-28-RESEARCH-TERRASIM-UI-BIOME-DATAFLOW.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Does TerraSim own biome/terrain classification, or does the UI compute it independently?
**Status**: BACKLOG
**Priority**: HIGH
**Type**: research
**Created**: 2026-07-28
**Last Updated**: 2026-07-28

---

## Context
A prior research task (2026-07-17-MEDIUM-RESEARCH-ELEVATION-IN-BIOME-CLASSIFICATION, completed 2026-07-28) found that `surface_view.js` computes biome classification client-side from raw tile data (latitude, elevation, precipitation) via `_selectTerrainFamily()`, `_selectTerrainType()`, and `_biomeTileKey()`, with no elevation/lapse-rate adjustment. That task assumed the fix was "add elevation to the existing classification logic" — but the deeper question, raised in review, is whether that JS-side classification logic should exist independently at all.

The architectural intent is: **TerraSim is the simulation authority for planetary state (atmosphere, hydrosphere, geosphere, biosphere) — the UI/render layer should display what TerraSim decided, not recompute biome/climate classification itself.** If `surface_view.js` is independently deriving biome from raw tile data rather than rendering a biome value TerraSim already computed and sent, that's a duplicate-source-of-truth bug, not a missing-feature gap — and the elevation issue is a symptom of it, not the root problem.

This task does NOT fix anything. It traces the actual data flow and reports which of two situations exists, so a correctly-scoped follow-up task can be written.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- Prior research doc: `summaries/2026-07-17-RESEARCH-ELEVATION-IN-BIOME-CLASSIFICATION.md` — read this first, it's the starting point for this task

> If a doc doesn't exist for this area, do not create one during this task.
> Flag the gap in your completion report instead.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: This is a read-only tracing task. Do not modify any code, do not "fix" the elevation gap, do not implement lapse rate.
- ❌ Wrong: noticing the gap and patching `_biomeTileKey()` to add elevation
- ✅ Right: document exactly what data crosses the Ruby→JS boundary today and whether biome/terrain fields are part of it
- Why: whether the fix belongs in TerraSim-only, JS-only, or both depends entirely on what this investigation finds. Fixing before tracing risks entrenching the wrong architecture further.

⚠️ **GOTCHA 2**: Don't trust doc claims about what TerraSim exports — verify against the actual controller/serializer/API response, not just service method names.
- ❌ Wrong: "TerraSim has a biome field on the model, so it must be sent to the client"
- ✅ Right: trace the actual JSON payload the client receives (controller action → serializer/view → what surface_view.js actually parses) and confirm whether a biome/terrain-classification field is present in it
- Why: this project has a recurring pattern of docs/model fields existing without actually being wired through to where they're consumed (see: symlink/path drift findings, camera/projection doc mismatch).

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before running any commands, post a synthesis report per the standard template (task name, what you're about to do, files you'll reference, expected outcome). Save as MD to summaries/, do not paste into chat.

---

## Problem Statement
Unknown whether biome/terrain classification is computed once (in TerraSim/Ruby) and passed to the client, or computed independently in the JS render layer from raw tile data. This determines whether elevation-aware biome classification should be fixed in one place (TerraSim only, if JS already consumes its output) or requires a larger architectural change (TerraSim needs to start exporting biome classification, and JS needs to stop computing its own).

**Current behavior**: `surface_view.js` calls `_selectTerrainFamily()`, `_selectTerrainType()`, `_biomeTileKey()` using latitude-derived temperature and raw tile data. Unknown whether these are recomputing something TerraSim already decided, or are the only place this decision is made at all.

**Expected outcome of this research**: A clear answer to one of these two scenarios, with file/line evidence:
- **Scenario A**: TerraSim (Ruby) already computes and exports a biome/terrain classification field per tile/location, and the JS functions above ignore it and recompute independently — duplicate logic, one is dead weight.
- **Scenario B**: TerraSim has no biome/terrain classification output at all today — JS has always been the only place this decision is made, and TerraSim needs to be extended to own it.

---

## Files Involved

### Primary Files — read only, do not edit
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/assets/javascripts/surface_view.js` | Client-side biome/terrain rendering | `_selectTerrainFamily()`, `_selectTerrainType()`, `_biomeTileKey()` |
| `app/services/terra_sim/` (whole directory, grep) | Planetary simulation services | any biome/terrain classification method |
| Controller/serializer that feeds tile data to the client | API boundary | whatever endpoint `surface_view.js` fetches tile/map data from |
| Tile JSON asset (`galaxy_game_tileset.json`) or equivalent API response | Data shape reaching the client | confirm whether a biome/classification field is present per tile, vs. only raw physical data (lat, elevation, precipitation) |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `summaries/2026-07-17-RESEARCH-ELEVATION-IN-BIOME-CLASSIFICATION.md` | Starting point — prior findings on the JS-side classification logic |

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
(See handoff block above — same procedure.)

### Step 1 — Trace the API/data boundary
Find the actual controller action or endpoint that supplies map/tile data to the client. Identify every field present in that payload. Confirm whether any field represents a computed biome/terrain classification, versus only raw physical inputs (latitude, elevation, precipitation, etc.).

### Step 2 — Grep TerraSim for biome/terrain classification logic
Search `app/services/terra_sim/` and any related model (e.g. a `Biome` or `Biosphere` model) for methods that classify biome/terrain from physical state. Determine whether such logic exists in Ruby at all today, and if so, whether its output ever reaches the serializer/API payload identified in Step 1.

### Step 3 — Compare against the JS classification logic
Confirm (or update, if changed since 2026-07-17) exactly what `_selectTerrainFamily()`, `_selectTerrainType()`, `_biomeTileKey()` consume as input — raw tile fields only, or a pre-computed classification field from the payload.

### Step 4 — Synthesis Report
State plainly which scenario (A or B, as defined in Problem Statement) the evidence supports, with specific file/line citations. Do not propose an implementation fix — that is out of scope for this task and will be a separate follow-up task file.

---

## Acceptance Criteria
- [ ] Clear determination of Scenario A vs. Scenario B, with file/line evidence for each claim
- [ ] Confirmation of what fields are actually present in the client-facing map/tile payload today
- [ ] Confirmation of whether Ruby TerraSim has any biome/terrain classification logic at all, and if so, whether it is currently unused/dead relative to the client
- [ ] No code changes made
- [ ] Follow-up task(s) needed are named in the Completion Report, not created as files

---

## Stop Conditions — escalate to user immediately if:
- The API boundary is unclear or there are multiple candidate endpoints supplying tile data (don't guess which one is live)
- Evidence conflicts with the 2026-07-17 research doc's findings (report the discrepancy, don't silently resolve it)
- Any architectural decision is required (this task is trace-only)

---

## Commit Instructions
No code changes expected. If the synthesis report is the only artifact, commit that:
```bash
git add projects/galaxy_game/summaries/2026-07-28-RESEARCH-TERRASIM-UI-BIOME-DATAFLOW.md
git commit -m "research: TerraSim vs UI biome classification data flow — [scenario A/B] found"
```

**Task file move on completion:**
```bash
git mv projects/galaxy_game/tasks/active/2026-07-28-HIGH-RESEARCH-TERRASIM-UI-BIOME-DATAFLOW.md \
       projects/galaxy_game/tasks/completed/2026-07/2026-07-28-HIGH-RESEARCH-TERRASIM-UI-BIOME-DATAFLOW.md
git commit -m "chore: move 2026-07-28-HIGH-RESEARCH-TERRASIM-UI-BIOME-DATAFLOW.md to completed/"
```

---

## Documentation
- [ ] No doc changes needed during this task
- [ ] Flag doc gap: if TerraSim/UI ownership was assumed correct in any existing architecture doc, name which doc and what it claims — do not fix the doc here, add to backlog instead

---

## Dependencies
**Blocked by**: none
**Blocks**: any future elevation-in-biome-classification implementation task (do not write that task until this one resolves which scenario applies)
**Related tasks**: 2026-07-17-MEDIUM-RESEARCH-ELEVATION-IN-BIOME-CLASSIFICATION (completed — this task follows up on it)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: 
**Completion date**: 
**Final test result**: N/A (research task, no specs run unless discovered as relevant)

### What was changed
- None (research only)

### Issues discovered


### Follow-up tasks needed


### Lessons learned


---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: 
