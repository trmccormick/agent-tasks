---
status: backlog
priority: LOW
type: refactor
system_domain: MANUFACTURING
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
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
  Then open the moved file and change: status: backlog → status: active
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

Three Manufacturing services were confirmed dead (zero live callers in app/lib) during the 2026-08-07 verification session. Two are safe to purge; one requires a hold condition pending review of `deployment_refinement.md`.

**Relevant Architecture Docs** — read before starting:
- `docs/developer/deployment_refinement.md` — AssemblyService hold condition (3 references)
- `galaxy_game/app/services/manufacturing/construction/construction_manager.rb` — dead code target 1
- `galaxy_game/app/services/manufacturing/production_service.rb` — dead code target 2

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

### Confirmed Dead Code (Safe to Remove)

| Service | File Path | Live Callers | Status |
|---------|-----------|-------------|--------|
| `Manufacturing::ConstructionManager` | `galaxy_game/app/services/manufacturing/construction/construction_manager.rb` | **None** | ✅ Safe to delete |
| `Manufacturing::ProductionService` | `galaxy_game/app/services/manufacturing/production_service.rb` | **None** | ✅ Safe to delete |

### Hold Condition (Do NOT Remove Yet)

| Service | File Path | Live Callers | Status |
|---------|-----------|-------------|--------|
| `Manufacturing::AssemblyService` | `galaxy_game/app/services/manufacturing/assembly_service.rb` | **None** | ⚠️ HOLD — see deployment_refinement.md |

---

## Problem Statement

Three Manufacturing services have zero live callers in app/lib. Two are safe to purge immediately; one requires a hold condition review of `deployment_refinement.md` before removal.

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

## Implementation Steps

### Step 1 — Pre-flight Verification (MANDATORY)

Run these inside Docker to confirm zero production callers:

```bash
docker exec web bash -c 'grep -rn "ConstructionManager" /home/galaxy_game/app/ /home/galaxy_game/lib/ | grep -v "class ConstructionManager"'
docker exec web bash -c 'grep -rn "ProductionService" /home/galaxy_game/app/ /home/galaxy_game/lib/ | grep -v "class ProductionService"'
```

Both should return **no results** (excluding the class definition lines themselves). If any production caller is found, STOP and report it. Do not proceed with removal.

### Step 2 — Delete Dead Code Files

Delete these 4 files:

```bash
docker exec web bash -c 'rm /home/galaxy_game/app/services/manufacturing/construction/construction_manager.rb'
docker exec web bash -c 'rm /home/galaxy_game/spec/services/manufacturing/construction/construction_manager_spec.rb'
docker exec web bash -c 'rm /home/galaxy_game/app/services/manufacturing/production_service.rb'
docker exec web bash -c 'rm /home/galaxy_game/spec/services/manufacturing/production_service_spec.rb'
```

### Step 3 — Verify No Broken Imports

Run the manufacturing spec suite to confirm no broken imports:

> CRITICAL EXECUTION MANDATE: All RSpec commands must use the Docker wrapper below.
> The container working directory is already /home/galaxy_game — do NOT add cd /home/galaxy_game.

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/manufacturing/ --format progress' 2>&1 | tail -5
```

Report the result. If any failures, STOP and report them — do not attempt to fix.

### Step 4 — Review AssemblyService Hold Condition

Read `docs/developer/deployment_refinement.md` (on host):

```bash
grep -n "AssemblyService" /Users/tam0013/Documents/git/galaxyGame/docs/developer/deployment_refinement.md
```

Report the 3 references found. Determine:
- Is the integration plan still current? → Leave `Manufacturing::AssemblyService` intact, report why in your notes.
- Is it superseded? → Delete `assembly_service.rb` + its spec file too, note the supersession in commit message.

---

## Acceptance Criteria
- [ ] Pre-flight verification confirms zero live callers for ConstructionManager and ProductionService
- [ ] 4 dead-code files deleted (2 services + specs)
- [ ] Manufacturing spec suite runs with no failures
- [ ] AssemblyService hold condition resolved (kept or deleted with documented reason)
- [ ] Commit message accurately describes what was removed and why

---

## Stop Conditions — escalate to user immediately if:
- Any production caller is found for ConstructionManager or ProductionService — report the caller and stop
- Specs fail after deletion — report failures and stop. Do not attempt to fix
- AssemblyService hold condition cannot be resolved from deployment_refinement.md alone — flag for manual review

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
