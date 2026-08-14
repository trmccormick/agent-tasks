---
status: blocked
priority: LOW
type: refactor
system_domain: MANUFACTURING
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
requires_architecture_review: true
---

# TASK: Purge Deprecated Manufacturing Services — Dead Code Removal

---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase05-luna-calibration/2026-08-08-LOW-REFACTOR-PURGE-DEPRECATED-MANUFACTURING-SERVICES.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/phase05-luna-calibration/2026-08-08-LOW-REFACTOR-PURGE-DEPRECATED-MANUFACTURING-SERVICES.md \
         projects/galaxy_game/tasks/active/2026-08-08-LOW-REFACTOR-PURGE-DEPRECATED-MANUFACTURING-SERVICES.md
  Then open the moved file and change: status: active → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-08-LOW-REFACTOR-PURGE-DEPRECATED-MANUFACTURING-SERVICES.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

## Context

**PRE-FLIGHT VERIFICATION FAILED (2026-08-14)** — The original premise that these services are dead code was incorrect.

### Finding 1: ConstructionManager — NOT DEAD (BLOCKS PURGE)

`Manufacturing::Construction::ConstructionManager` has **4 live app-layer callers**:

| Caller | File | Line | Method |
|--------|------|------|--------|
| `covering_service.rb` | `app/services/manufacturing/construction/` | 82 | `assign_builders` |
| `covering_service.rb` | `app/services/manufacturing/construction/` | 97 | `complete?` |
| `hangar_service.rb` | `app/services/manufacturing/construction/` | 81 | `assign_builders` |
| `hangar_service.rb` | `app/services/manufacturing/construction/` | 97 | `complete?` |

These are production callers in the app layer — dome/hangar construction workflows actively use this service. **Cannot purge without migrating these callers first.**

### Finding 2: ProductionService — rake-only callers (no app-layer)

`Manufacturing::ProductionService` has callers only in `lib/tasks/` rake files:

| Caller | File | Lines |
|--------|------|-------|
| `isru_production_validation.rake` | `lib/tasks/` | 10, 122, 198, 200, 231 |
| `lunar_base_with_isru_pipeline.rake` | `lib/tasks/` | 6, 7, 366, 401 |

**Confidence level: lower than ConstructionManager.** No app-layer callers found — rake-only usage is test/validation scaffolding. Could be safe to purge but needs review to confirm these rake tasks aren't part of a live pipeline.

### AssemblyService — still on hold (not yet reviewed)

`deployment_refinement.md` integration plan not yet assessed.

**Relevant Architecture Docs** — read before any future action:
- `docs/developer/deployment_refinement.md` — AssemblyService hold condition (3 references)
- `galaxy_game/app/services/manufacturing/construction/construction_manager.rb` — **NOT dead, 4 callers**
- `galaxy_game/app/services/manufacturing/production_service.rb` — rake-only callers
- `galaxy_game/app/services/manufacturing/assembly_service.rb` — pending review

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: AssemblyService hold condition**
- ❌ Wrong: Delete AssemblyService along with the other two services
- ✅ Right: Read `deployment_refinement.md` first. If integration plan is still current → leave intact. If superseded → delete it too and note in commit message.
- Why: `deployment_refinement.md` references `Manufacturing::AssemblyService` 3 times as a planned integration target (lines 20, 31, 45).

⚠️ **GOTCHA 2: DO NOT fix failures**
- ❌ Wrong: Attempt to fix any spec failures after deletion
- ✅ Right: Report failures and stop. This is a dead-code removal task, not a debugging task.
- Why: Failures indicate the pre-flight verification was wrong — something unexpected depends on these services.

### Current Status After Pre-Flight (2026-08-14)

| Service | File Path | Callers | Status |
|---------|-----------|---------|--------|
| `Manufacturing::Construction::ConstructionManager` | `app/services/manufacturing/construction/construction_manager.rb` | **4 app-layer callers** | ❌ NOT DEAD — blocks purge |
| `Manufacturing::ProductionService` | `app/services/manufacturing/production_service.rb` | rake-only (lib/tasks/) | ⚠️ Lower confidence — needs review |
| `Manufacturing::AssemblyService` | `app/services/manufacturing/assembly_service.rb` | none (unconfirmed) | ⚠️ HOLD — deployment_refinement.md not yet reviewed |

---

## Problem Statement

Three Manufacturing services were suspected dead (zero live callers). Pre-flight verification on 2026-08-14 disproved this for ConstructionManager (4 app-layer callers found) and ProductionService (rake-only callers). Task is blocked pending architecture review.

---

## Files Involved

### Primary Files — you will delete these (if safe)
| File | Purpose |
|---|---|
| `galaxy_game/app/services/manufacturing/construction/construction_manager.rb` | Dead code — zero callers |
| `galaxy_game/spec/services/manufacturing/construction/construction_manager_spec.rb` | Spec for dead code |
| `galaxy_game/app/services/manufacturing/production_service.rb` | Dead code — zero callers |
| `galaxy_game/spec/services/manufacturing/production_service_spec.rb` | Spec for dead code |

### Hold Condition File — review before deleting
| File | Why You Need It |
|---|---|
| `galaxy_game/app/services/manufacturing/assembly_service.rb` | Check if deployment_refinement.md integration plan is current or superseded |
| `docs/developer/deployment_refinement.md` | Contains 3 references to AssemblyService as planned integration target |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/spec/services/manufacturing/` (full suite) | Verify no broken imports after deletion |

### Migration (if needed)
- [ ] No migration needed

---

## Blocked — Architecture Review Required

This task is blocked pending architecture review. Do NOT proceed with deletion until:

### Blocker 1: ConstructionManager Migration Path
- **4 app-layer callers** found in `covering_service.rb` and `hangar_service.rb`
- Need decision: Should these callers be migrated off ConstructionManager, or is manual construction intentionally using this path?
- If migration needed: plan the caller refactoring first, then revisit purge scope

### Blocker 2: ProductionService rake-only usage
- Callers exist only in `lib/tasks/` rake files (test/validation scaffolding)
- Lower confidence than ConstructionManager — no app-layer callers
- Need review: confirm these rake tasks aren't part of a live pipeline before purging

### Blocker 3: AssemblyService hold condition
- `deployment_refinement.md` integration plan not yet reviewed
- Pending separate assessment

---

## Acceptance Criteria
- [ ] Pre-flight verification confirms zero live callers for ConstructionManager and ProductionService
- [ ] 4 dead-code files deleted (2 services + specs)
- [ ] Manufacturing spec suite runs with no failures
- [ ] AssemblyService hold condition resolved (kept or deleted with documented reason)
- [ ] Commit message accurately describes what was removed and why

---

## Stop Conditions — escalate to user immediately if:
- Any production caller is found for ConstructionManager or ProductionService — report the caller and stop ✅ DONE (4 callers found)
- Specs fail after deletion — report failures and stop. Do not attempt to fix
- AssemblyService hold condition cannot be resolved from deployment_refinement.md alone — flag for manual review

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: Implementation Agent (pre-flight only)
**Completion date**: 2026-08-14
**Final spec result**: N/A — task blocked before implementation

### What was changed
- Pre-flight verification disproved dead-code premise for ConstructionManager (4 app-layer callers found)
- ProductionService confirmed rake-only usage (no app-layer callers)
- Task moved to active/deferred-cleanup/ with blocked status

### Issues discovered
- **ConstructionManager is NOT dead**: 4 live callers in covering_service.rb and hangar_service.rb
- **ProductionService has rake-only callers**: lower confidence for purge, needs pipeline review
- Original task file's core assumption (zero live callers) was incorrect

### Follow-up tasks needed
- Architecture decision on ConstructionManager migration path (should dome/hangar services migrate off it?)
- Review ProductionService rake callers to confirm they're not part of a live pipeline
- Assess AssemblyService hold condition in deployment_refinement.md
- Consider splitting this into separate tasks per service with different confidence levels

### Lessons learned
- Pre-flight grep must use fully-qualified class names (e.g., `Manufacturing::Construction::ConstructionManager`) to avoid false negatives from bare class name matches
- "Zero callers" claims need verification on every task — assumptions about dead code can be stale
- rake-only usage is a distinct case from app-layer production callers and should be treated separately

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add -A
git commit -m "refactor: purge dead Manufacturing services (zero live callers confirmed)

- Remove Manufacturing::ConstructionManager + spec (dead code)
- Remove Manufacturing::ProductionService + spec (dead code)
- AssemblyService: [kept/deleted — reason]"
git push
```

**Task file move on completion:**
```bash
mv projects/galaxy_game/tasks/active/2026-08-08-LOW-REFACTOR-PURGE-DEPRECATED-MANUFACTURING-SERVICES.md \
   projects/galaxy_game/tasks/completed/2026-08/2026-08-08-LOW-REFACTOR-PURGE-DEPRECATED-MANUFACTURING-SERVICES.md
git add projects/galaxy_game/tasks/completed/2026-08/2026-08-08-LOW-REFACTOR-PURGE-DEPRECATED-MANUFACTURING-SERVICES.md
git commit -m "chore: move 2026-08-08-LOW-REFACTOR-PURGE-DEPRECATED-MANUFACTURING-SERVICES.md to completed/"
```

---

## Documentation
- [ ] No doc changes needed

---

## Dependencies
**Blocked by**: none — requires only test environment access
**Blocks**: none
**Related tasks**: none

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Final spec result**: [X examples, Y failures]

### What was changed
-

### Issues discovered
-

### Follow-up tasks needed
-

### Lessons learned
-

---

## Handoff Summary
HANDOFF SUMMARY: [files deleted] | AssemblyService hold resolved | next: [if applicable]
