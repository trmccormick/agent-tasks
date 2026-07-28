---
status: backlog
priority: LOW
type: data
system_domain: UNITS
mvp_alignment: OTHER
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/2026-07/2026-07-27-LOW-DATA-RETIRE-COMPOSITE-SPRITE-EXTRACTION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/2026-07/2026-07-27-LOW-DATA-RETIRE-COMPOSITE-SPRITE-EXTRACTION.md \
         projects/galaxy_game/tasks/active/2026-07-27-LOW-DATA-RETIRE-COMPOSITE-SPRITE-EXTRACTION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-27-LOW-DATA-RETIRE-COMPOSITE-SPRITE-EXTRACTION.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Retire composite-sheet sprite extraction; archive early test sprites
**Status**: BACKLOG
**Priority**: LOW
**Type**: data
**Created**: 2026-07-27
**Last Updated**: 2026-07-27

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*

- **Template Conformance**: [fill during triage]
- **Docker Wrapper Check**: N/A — no RSpec involved in this task
- **MVP Alignment**: [fill during triage]
- **MVP Impact Note**: Unblocks a cleaner path to Layer 5 (unit sprites) becoming production-ready, but is not itself required for any MVP milestone.
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Pure grep/find/archive housekeeping — no design judgment, no code logic changes, low risk.
**Local attempts before cloud**: N/A
**Supervision Level**: standard

> **Primary executor is always local Qwen via the GitHub Copilot custom agent config.**
> Cloud/paid agents are fallback only.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context
Unit sprites for the Surface View (Layer 5, `showUnits: false` in `surface_view.js`) have been gated off since early testing. The existing sprite set (`sprite_00.png`–`sprite_15.png`, referenced by direct index lookup with no rotation/direction logic) originated from two abandoned attempts: a full composite icon/tile sheet generated 2026-07-12, and a 2026-07-18 attempt to extract individual per-unit sprites from that sheet, which did not work reliably. The resulting sprites are inconsistent, rough hand-painted quality and were never intended as production assets.

Separately, the asset-generation pipeline has since been substantially clarified: camera projection is confirmed top-down square-grid with no rotation (surface_view.js investigation, commit d1125bd6), unit sprites need only one static image per unit type (no directional set), and a locked visual style (`precision_industrial_v1`) now exists as a style reference. Given this, direct single-unit generation under the corrected pipeline is expected to be more reliable than continuing to invest in fixing sheet-extraction.

This task does not attempt new generation — it only confirms the old sprites are safely disposable and gets the record straight for whoever picks up Layer 5 next.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `docs/architecture/three_layer_views.md` — Surface View layer spec, includes current sprite/Layer 5 status
- `NEEDS_REVIEW.md` / `status.md` — existing entries referencing gated-off unit sprites (2026-07-19)

> If a doc doesn't exist for this area, do not create one during this task.
> Flag the gap in your completion report instead.

---

## Critical Information for This Task

### Credentials (if needed)
N/A — no credentials required for this task.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Do not delete anything outright.
- ❌ Wrong: `rm` the old sprite sheets or extracted files.
- ✅ Right: Move them into a clearly-labeled archive/deprecated location, git-tracked, so history is preserved.
- Why: Time Machine covers recovery, but a clean git history of "this was archived, here's why" is more useful than relying on backup recovery, and avoids surprise if something unexpectedly still references these files.

⚠️ **GOTCHA 2**: Do not assume file paths — confirm them first.
- ❌ Wrong: Guessing the sprite sheet / `sprite_00`–`15` file locations from memory or this task description.
- ✅ Right: `find` / `grep` the actual repo to locate every reference before moving anything.
- Why: Exact current paths were not confirmed as of this task's creation — only that the files exist and are referenced by index in `surface_view.js`'s gated-off unit rendering code.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before running any commands or modifying any files, post a synthesis report per the standard template (see `TASK_TEMPLATE.md` for the exact format). Save as MD file to the summaries folder — do not paste into chat.

---

## Problem Statement
The current `sprite_00.png`–`sprite_15.png` set (and the composite sheets they came from) are low-quality, inconsistent test artifacts from an extraction process that never worked correctly. They are still sitting in the codebase in a way that could be mistaken for real assets or a style reference by a future task or agent, and the actual blocker on Layer 5 (unit sprites) is now understood to be "no direct per-unit generation has been done yet under the corrected pipeline," not "extraction is broken and needs fixing."

**Current behavior**: Old, low-quality sprite files exist in the repo with no clear labeling that they are deprecated test artifacts; `NEEDS_REVIEW.md`/`status.md` entries describe the Layer 5 blocker in terms of the old sprites' quality issues rather than the corrected plan.

**Expected behavior**: Old sprite sheets and extracted files are relocated to a clearly-labeled archive location (not deleted), confirmed to have zero live code dependencies beyond the already-gated-off array lookup, and the relevant status docs are updated to describe the actual forward plan (direct single-unit generation under the corrected pipeline, not sheet-extraction repair).

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `NEEDS_REVIEW.md` or `status.md` (galaxy_game entry) | Tracks the Layer 5 / unit sprite blocker | Locate the 2026-07-19 entry referenced in `three_layer_views.md`'s "Known Issues" section |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/app/assets/javascripts/surface_view.js` | Confirm the exact `sprite_00`–`15` array/lookup code (around the `loadUnitSprites`/`drawUnits` functions) before touching any files it references |
| `docs/architecture/three_layer_views.md` | Current documented state of Layer 5 / unit sprites |

### Known location
- `galaxyGame/galaxy_game/app/assets/images/unit_sprites/` — confirmed location of `sprite_00.png`–`sprite_15.png`. Still confirm exact contents with `ls -la` before moving anything (this folder may also contain other files not part of this cleanup — do not move anything you can't account for).

### Files to locate (paths not yet confirmed — find before moving)
- Any extraction script/tooling used on 2026-07-18, if it still exists in the repo (the original ChatGPT-generated composite sheets themselves are being cleaned up manually by Tracy — out of scope for this task, do not search for or move those)

### Migration (if needed)
- [x] No migration needed

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
```bash
git mv projects/galaxy_game/tasks/backlog/2026-07/2026-07-27-LOW-DATA-RETIRE-COMPOSITE-SPRITE-EXTRACTION.md \
       projects/galaxy_game/tasks/active/2026-07-27-LOW-DATA-RETIRE-COMPOSITE-SPRITE-EXTRACTION.md
```
Then update `status: backlog` → `status: active` in the file, then:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-07-27-LOW-DATA-RETIRE-COMPOSITE-SPRITE-EXTRACTION.md"
```
**Paste the output in chat before proceeding.** Expected: exactly one result, at the `active/` path.

### Step 1 — Locate all sprite-extraction artifacts
- Confirmed location: `galaxyGame/galaxy_game/app/assets/images/unit_sprites/`. Run `ls -la` on it and report full contents — do not assume it's only `sprite_00`–`15`.
- `find` the repo for any 2026-07-18 extraction script/tooling, if it still exists (the original ChatGPT-generated composite sheets are out of scope — Tracy is handling that cleanup manually).
- `grep` the codebase for every reference to `sprite_00` through `sprite_15` and to the `unit_sprites` folder path (expected: only the gated-off unit-sprite array/loader in `surface_view.js`).
- Report every file path and every reference found, with file:line for code references, before moving anything.

### Step 2 — Confirm zero live dependency
- Confirm `showUnits: false` is still the current state (i.e., this code path is not live in production).
- Confirm no other file, spec, or config references these sprites outside the gated-off array.
- If anything unexpected references them, STOP and escalate (see Stop Conditions) rather than proceeding.

### Step 3 — Archive, don't delete
- Move the source composite sheet(s) and the extracted `sprite_00`–`15` files into a clearly-labeled location, e.g. `docs/architecture/deprecated-assets/2026-07-sprite-extraction-attempt/` (or the project's existing convention for deprecated artifacts, if one exists — check first, do not invent a new convention without checking).
- Use `git mv` for tracked files; `mv` + `git add` for untracked files.
- Do not touch `surface_view.js`'s code in this task — the array/lookup logic stays as-is (still gated off) unless a separate task addresses it.

### Step 4 — Update status docs
- Update the relevant `NEEDS_REVIEW.md`/`status.md` entry (the one referenced in `three_layer_views.md`'s "Known Issues" section, dated 2026-07-19) to reflect the corrected understanding: the blocker is that no direct per-unit generation has happened yet under the corrected pipeline (top-down, `precision_industrial_v1` style, no directional set needed) — not that extraction needs fixing.
- Do not create new architecture docs in this task — flag any doc gap instead.

### Step 5 — Verify
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game -iname "sprite_0*"
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game -iname "sprite_1*"
```
Expected: no results outside the new archive location.

### Step 6 — Synthesis Report (before committing anything)
Standard format — confirm what was found, what was moved, what was updated, and explicit confirmation that nothing was deleted.

Do not commit until the user explicitly approves.

---

## Acceptance Criteria
- [ ] All sprite-extraction-attempt files located and reported with paths
- [ ] Zero live code dependency confirmed (or escalated if found)
- [ ] Old sprite sheets and extracted files moved (not deleted) to a clearly-labeled archive location
- [ ] Relevant status doc entry updated to describe the corrected forward plan
- [ ] `surface_view.js` code untouched (out of scope for this task)
- [ ] No new generation attempted in this task

---

## Stop Conditions — escalate to user immediately if:
- Any live (non-gated-off) code path references these sprite files
- The task requires deciding on a new "deprecated assets" folder convention that doesn't already exist
- Any architectural decision is required beyond archiving
- More files than expected are found and their disposition is unclear

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add [specific files only — never git add .]
git commit -m "chore: archive abandoned composite-sheet sprite extraction attempt (2026-07-12/18)"
git push
```

**Task file move on completion:**
```bash
git mv projects/galaxy_game/tasks/active/2026-07-27-LOW-DATA-RETIRE-COMPOSITE-SPRITE-EXTRACTION.md \
       projects/galaxy_game/tasks/completed/2026-07/2026-07-27-LOW-DATA-RETIRE-COMPOSITE-SPRITE-EXTRACTION.md
git commit -m "chore: move 2026-07-27-LOW-DATA-RETIRE-COMPOSITE-SPRITE-EXTRACTION.md to completed/"
```

---

## Documentation
- [ ] Update `NEEDS_REVIEW.md`/`status.md` unit-sprite entry (see Step 4)
- [ ] Flag doc gap: no existing "deprecated assets" convention found (if true) — do not create one, add to backlog instead

---

## Dependencies
**Blocked by**: none
**Blocks**: none (Layer 5 direct-generation work can proceed independently, but this cleanup removes a source of confusion for whoever picks it up)
**Related tasks**: 2026-07-26-MEDIUM-DATA-VISUAL-DEFINITION-V1-RH400.md (unrelated unit, same broader asset pipeline)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Final test result**: N/A — no specs involved

### What was changed


### Issues discovered


### Follow-up tasks needed


### Lessons learned


---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: