---
status: backlog
priority: LOW
type: bug-fix
system_domain: MANUFACTURING
mvp_alignment: OTHER
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)
You are Implementation Agent.
Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase05-luna-calibration/2026-08-04-LOW-BUGFIX-REMOVE-MANUFACTURING-SERVICE-DEAD-CODE.md
STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
git mv projects/galaxy_game/tasks/backlog/phase05-luna-calibration/2026-08-04-LOW-BUGFIX-REMOVE-MANUFACTURING-SERVICE-DEAD-CODE.md 
projects/galaxy_game/tasks/active/2026-08-04-LOW-BUGFIX-REMOVE-MANUFACTURING-SERVICE-DEAD-CODE.md
Then open the moved file and change: status: backlog → status: active
Paste the output of both commands in chat before proceeding.
Do NOT read the task file content, run any commands, or start synthesis until this is done.
LIFECYCLE: backlog → active → completed
Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-04-LOW-BUGFIX-REMOVE-MANUFACTURING-SERVICE-DEAD-CODE.md"
Only ONE result should exist. Paste this output before committing.
READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.
CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
Filename pattern: YYYY-MM-DD-BUGFIX-REMOVE-MANUFACTURING-SERVICE-DEAD-CODE.md
Chat is for questions only — never paste synthesis into chat.

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Remove Manufacturing::Service dead code
**Status**: BACKLOG
**Priority**: LOW
**Type**: bug-fix
**Created**: 2026-08-04

---

## Prerequisites — READ FIRST (Sequential Order)

1. Workflow README for the executor role.
2. Project guide for galaxy_game.
3. This task file.

---

## Context
Follow-up from the 2026-08-04 git-history investigation (closing out `2026-07-26-LOW-REFACTOR-MANUFACTURING-SERVICE-DUPLICATE.md`). Confirmed via git history: `ManufacturingService` (live, production callers, actively maintained through 2026-05-10) and `Manufacturing::Service` (dead, zero production callers) were created in the SAME commit (53eeac63, 2025-06-21) — parallel same-era development, not an incomplete migration. `Manufacturing::Service`'s only reference is its own spec file. `CORE_CONCEPT_MAP.md`'s mention of it as "likely owner" was confirmed to be a stale red herring from the parallel-dev era, not an intended replacement.

**Relevant docs**:
- `CORE_CONCEPT_MAP.md` — contains the stale "likely owner" reference that should be corrected or removed as part of this task

---

## Critical Information for This Task

### Gotchas

⚠️ **GOTCHA 1: Re-confirm zero callers before deleting, don't just trust the prior investigation.**
- Run a fresh grep for `Manufacturing::Service` references across `app/`, `lib/`, `config/` before removing — confirm nothing was added since the 2026-08-04 investigation.

⚠️ **GOTCHA 2: Decide the spec's fate explicitly, don't leave it dangling.**
- `Manufacturing::Service`'s spec currently only tests dead code. Either delete it alongside the class, or if any of its test cases cover behavior `ManufacturingService` doesn't yet have equivalent coverage for, migrate those specific cases — don't just delete the spec file wholesale without checking whether it has unique coverage worth preserving.

---

## Problem Statement
**Current state**: `Manufacturing::Service` exists as dead code — zero production callers, confirmed parallel-origin (not an abandoned migration), only referenced by its own spec.

**Expected state**: `Manufacturing::Service` and its spec are removed (or the spec is migrated to test `ManufacturingService` if it has unique coverage), and `CORE_CONCEPT_MAP.md`'s stale "likely owner" reference is corrected to point to `ManufacturingService` or removed.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose |
|---|---|
| `manufacturing/service.rb` (Manufacturing::Service) | Dead code to remove |
| Its corresponding spec file | Remove or migrate coverage |
| `CORE_CONCEPT_MAP.md` | Correct the stale "likely owner" reference |

---

## Implementation Steps

1. Re-confirm zero callers via fresh grep (see Gotcha 1).
2. Review `Manufacturing::Service`'s spec — identify any test cases not already covered by `ManufacturingService`'s spec.
3. If unique coverage exists, migrate those specific cases to `ManufacturingService`'s spec.
4. Delete `Manufacturing::Service` and its spec file.
5. Correct `CORE_CONCEPT_MAP.md`'s reference.
6. Run the full manufacturing spec suite to confirm no regressions.
7. Synthesis report noting what (if anything) was migrated vs simply deleted.

---

## Acceptance Criteria
- [ ] `Manufacturing::Service` and its spec removed
- [ ] Any unique spec coverage migrated to `ManufacturingService`'s spec first, if applicable
- [ ] `CORE_CONCEPT_MAP.md` corrected
- [ ] Manufacturing spec suite passes with no regressions

---

## Stop Conditions
- Stop if fresh grep finds a caller that wasn't present during the original 2026-08-04 investigation — re-escalate as a live-usage question, not a simple dead-code removal.

---

## Dependencies
**Blocked by**: none
**Blocks**: none
**Related**: `2026-07-26-MEDIUM-RESEARCH-MANUFACTURING-CURRENT-STATE-VS-DOCS.md` (broader manufacturing-chain-vs-docs research, still untouched — this task is narrower and doesn't require that one first)

---

## Completion Report
*Filled in by the implementing agent after completion*

---

## Handoff Summary
HANDOFF SUMMARY: [what removed] | [spec fate] | [doc correction made]