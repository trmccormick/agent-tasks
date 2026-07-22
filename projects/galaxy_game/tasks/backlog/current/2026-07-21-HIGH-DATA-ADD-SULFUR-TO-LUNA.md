markdown---
status: backlog
priority: HIGH
type: data
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT | ISRU_PRODUCTION
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-21-HIGH-DATA-ADD-SULFUR-TO-LUNA.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-21-HIGH-DATA-ADD-SULFUR-TO-LUNA.md \
         projects/galaxy_game/tasks/active/2026-07-21-HIGH-DATA-ADD-SULFUR-TO-LUNA.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-21-HIGH-DATA-ADD-SULFUR-TO-LUNA.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-07-21-DATA-ADD-SULFUR-TO-LUNA.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Add Sulfur as a Producible Resource on Luna (data-only)
**Status**: BACKLOG
**Priority**: HIGH
**Type**: data
**Created**: 2026-07-21
**Last Updated**: 2026-07-21

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot
**Why This Agent**: Data lookup + JSON edit + service verification — no architectural decisions required
**Local attempts before cloud**: N/A
**Supervision Level**: Standard

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context

Luna is a **known celestial body** with real geological data already defined (`stored_volatiles`, `materials`, `crust_composition`) — this is distinct from procedurally-generated worlds, which lack this data and require the full resource-spawning/deposit system (see `2026-05-18-DESIGN-Resource-Deposit-Model-And-Persistence.md` and related — that system is correctly scoped as phase7+ and blocked on separate design work; it is NOT needed here).

This task adds sulfur to Luna's existing data profile so `AIManager::PrecursorCapabilityService` recognizes it as locally producible — same mechanism already used for Luna's other materials (regolith, O2, H2O, etc.). No new architecture, no spawner, no equipment tiers, no triggers.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules

> If a doc doesn't exist for this area, do not create one during this task. Flag the gap in your completion report instead.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Do not touch the resource-spawning/deposit system.
- ❌ Wrong: Creating a `ResourceDeposit`, `DepositSpawner`, or `PlausibilityEngine` entry for sulfur
- ✅ Right: Adding sulfur directly to Luna's existing JSON data fields
- Why: That system (phase7+, files under `backlog/phase7+/`) exists for procedurally-generated worlds with no real data. Luna already has real data — this task only extends it.

⚠️ **GOTCHA 2**: Confirm the field `PrecursorCapabilityService.local_resources` actually reads before editing.
- ❌ Wrong: Assuming `materials` array is the only relevant field and editing blind
- ✅ Right: Read `app/services/ai_manager/precursor_capability_service.rb` first, confirm exactly which CelestialBody fields feed `local_resources`, then edit only those
- Why: The service pulls from `atmosphere.gas_percentage()`, `geosphere.crust_composition`, and `hydrosphere.water_bodies` per prior research — confirm sulfur's correct home (likely `crust_composition`, since it's a mineral/troilite-sourced solid, not a gas or water body) before writing.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Post the standard synthesis report (see TASK_TEMPLATE.md format) to chat before starting. Must confirm: which file(s) hold Luna's data, which field you'll edit, and what chemical formula key you'll use — before making any edit.

---

## Problem Statement

**Current behavior**: Luna's data profile does not include sulfur. `PrecursorCapabilityService.can_produce_locally?('S')` (or whatever the correct formula key turns out to be) returns `false`.

**Expected behavior**: Luna's data profile includes sulfur at a scientifically-grounded quantity/percentage. `can_produce_locally?` returns `true` for sulfur once queried against Luna's celestial body.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| Luna's celestial body JSON data (locate via how `'LUNA-01'` identifier resolves — check `data/json-data/` or wherever CelestialBody seed/fixture data lives) | Defines Luna's `materials`/`crust_composition`/`stored_volatiles` | the relevant resource-listing field |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/services/ai_manager/precursor_capability_service.rb` | Confirms which field(s) `local_resources` actually reads (do not guess) |
| Any other single-element material entries already in Luna's data (e.g. Fe, C) | Confirms existing chemical-formula-key naming convention to match |

### Migration (if needed)
- [x] No migration needed — data file edit only

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
(see Minimal Handoff block above — same steps)

### Step 1 — Locate Luna's data source
Find the JSON/data file(s) that back the `'LUNA-01'` CelestialBody identifier. Confirm this is where `materials`/`crust_composition` are defined.

### Step 2 — Confirm PrecursorCapabilityService's read path
Read `precursor_capability_service.rb` in full. Confirm exactly which CelestialBody field(s) populate `local_resources`. Do not proceed to Step 3 until this is confirmed — do not assume `materials` is correct without checking.

### Step 3 — Add sulfur to the correct field
Add sulfur using the real geological basis: sourced from troilite (FeS) in mare basalts, roughly 0.15–0.27% by weight in high-titanium mare regions. Use chemical formula key `'S'` unless the existing convention in Luna's data file uses a different format for single-element entries — match whatever convention already exists there.

### Step 4 — Verify

> CRITICAL EXECUTION MANDATE: All RSpec/runner commands must use the Docker wrapper.
> The container working directory is already `/home/galaxy_game` — do NOT add `cd /home/galaxy_game`.
> Never fabricate results. Actually run the command.

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rails runner "
body = CelestialBodies::CelestialBody.find_by(identifier: %q(LUNA-01))
service = AIManager::PrecursorCapabilityService.new(body)
puts service.can_produce_locally?(%q(S))
"'
```
Expected result: `true`

Then run existing specs touching `PrecursorCapabilityService` and Luna's celestial body data:
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/precursor_capability_service_spec.rb 2>&1 | tail -20'
```
Expected result: no new failures.

### Step 5 — Synthesis Report (before committing anything)
Standard format — root cause N/A (data addition, not a bug fix), proposed change, risk assessment (should be near-zero — additive data only).

---

## Acceptance Criteria
- [ ] Sulfur added to Luna's data profile in the correct field (confirmed via reading `PrecursorCapabilityService`, not assumed)
- [ ] Chemical formula key matches existing single-element naming convention in the same file
- [ ] `can_produce_locally?` returns `true` for sulfur when queried against Luna
- [ ] No regressions in `PrecursorCapabilityService` spec or any spec touching Luna's celestial body data
- [ ] No changes to the resource-spawning/deposit system files (phase7+)

---

## Stop Conditions — escalate to user immediately if:
- `PrecursorCapabilityService.local_resources` reads from a field this task didn't anticipate, and the fix isn't a simple data addition
- Adding sulfur requires a schema/model change (not just a data value)
- Any existing spec breaks that references Luna's material composition and depends on it NOT including sulfur

---

## Commit Instructions
```bash
git add [specific data file(s) only]
git commit -m "data: add sulfur to Luna's crust composition (troilite/mare-basalt sourced, ~0.15-0.27% by weight)"
git push
```

**Task file move on completion:**
```bash
git mv projects/galaxy_game/tasks/active/2026-07-21-HIGH-DATA-ADD-SULFUR-TO-LUNA.md \
       projects/galaxy_game/tasks/completed/2026-07/2026-07-21-HIGH-DATA-ADD-SULFUR-TO-LUNA.md
git commit -m "chore: move 2026-07-21-HIGH-DATA-ADD-SULFUR-TO-LUNA.md to completed/"
```

---

## Documentation
- [x] No doc changes needed

---

## Dependencies
**Blocked by**: none
**Blocks**: none directly — but this is a prerequisite for any future sulfur-economics/binder-material work (out of scope here)
**Related tasks**: `2026-05-18-DESIGN-*` resource-spawning design docs (phase7+, NOT touched by this task — reference only, for context on why the spawning system doesn't apply here)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Final test result**:

### What was changed


### Issues discovered


### Follow-up tasks needed


### Lessons learned


---

## Handoff Summary
HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]