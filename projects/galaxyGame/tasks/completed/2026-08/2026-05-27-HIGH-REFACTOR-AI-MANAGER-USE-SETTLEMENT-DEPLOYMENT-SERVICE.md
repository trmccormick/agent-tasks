---
status: completed
priority: HIGH
type: refactor
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
created: 2026-05-27
updated: 2026-08-24
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase05-luna-calibration/2026-05-27-HIGH-REFACTOR-AI-MANAGER-USE-SETTLEMENT-DEPLOYMENT-SERVICE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/phase05-luna-calibration/2026-05-27-HIGH-REFACTOR-AI-MANAGER-USE-SETTLEMENT-DEPLOYMENT-SERVICE.md \
         projects/galaxy_game/tasks/active/2026-05-27-HIGH-REFACTOR-AI-MANAGER-USE-SETTLEMENT-DEPLOYMENT-SERVICE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-05-27-HIGH-REFACTOR-AI-MANAGER-USE-SETTLEMENT-DEPLOYMENT-SERVICE.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Migrate Direct BaseSettlement.create! to SettlementDeploymentService

**Status**: BACKLOG
**Priority**: HIGH
**Type**: refactor
**Created**: 2026-05-27
**Last Updated**: 2026-08-15

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model during backlog review*

- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS
- **MVP Alignment**: VALID — AI Manager must use shared deployment service for Luna settlement creation to ensure consistent manifest-driven deployment logic
- **MVP Impact Note**: Direct BaseSettlement.create! in AI Manager bypasses cargo verification, unit deployment, and cargo transfer — Luna precursor deployment will be incomplete without this fix
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: [TBD]
**Why This Agent**: Targeted migration, 3 files, clear replacement pattern
**Local attempts before cloud**: N/A
**Supervision Level**: Watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/TASK_TEMPLATE.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

## Context

`SettlementDeploymentService.establish_from_craft` is the canonical settlement creation primitive. It encapsulates manifest-driven cargo verification, settlement creation, unit deployment, and cargo transfer. The AI Manager currently bypasses this service in 3 files, creating settlements directly via `BaseSettlement.create!`. This violates the architecture and means AI Manager deployments skip cargo verification and unit deployment steps.

`task_execution_engine_v2.rb` is the current active engine — v2 replaced v1. It should be a pure task runner with no direct model creation.

**Service interface:**
```ruby
# SettlementDeploymentService — line 2
def self.establish_from_craft(craft, location, manifest_name: 'precursor_craft_deployment_cargo')
```

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1: All 3 call sites still present as of 2026-08-15 verification**
- ❌ Wrong: Assume the migration was done by an intervening task or commit
- ✅ Right: Verify each call site with grep before starting — all 3 confirmed present at manager.rb:235, system_architect.rb:377, task_execution_engine_v2.rb:107
- Why: This task has been in backlog since May 27; intervening commits modified the files but did NOT touch the settlement creation call sites

⚠️ **GOTCHA 2: `craft` variable availability varies by call site**
- ❌ Wrong: Invent a craft object where none exists in scope
- ✅ Right: At each call site, check what variables are available. If `craft` is not directly available, flag the gap and stop — do not fabricate.
- Why: `manager.rb` creates from a lavatube plan (no craft), `system_architect.rb` deploys habitats (no craft), only `task_execution_engine_v2.rb` may have craft context

---

## Problem Statement

**Current behavior**: Three AI Manager files create settlements directly:
- `app/services/ai_manager/manager.rb:235` — `Settlement::BaseSettlement.create!`
- `app/services/ai_manager/system_architect.rb:377` — `Settlement::BaseSettlement.create!`
- `app/services/ai_manager/task_execution_engine_v2.rb:104` — `Settlement::BaseSettlement.create!`

**Expected behavior**: All three use `SettlementDeploymentService.establish_from_craft` with appropriate `craft`, `location`, and `manifest_name` parameters.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Line |
|---|---|---|
| `app/services/ai_manager/manager.rb` | AI Manager core | line 235 |
| `app/services/ai_manager/system_architect.rb` | System architecture service | line 377 |
| `app/services/ai_manager/task_execution_engine_v2.rb` | Current task runner | line 104 |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/services/settlement_deployment_service.rb` | Service interface and parameters |
| `app/models/settlement/base_settlement.rb` | Current model for context |

---

## Implementation Steps

### Step 1 — Read all three create! call sites in full context

```bash
sed -n '225,250p' galaxy_game/app/services/ai_manager/manager.rb
sed -n '367,392p' galaxy_game/app/services/ai_manager/system_architect.rb
sed -n '94,119p' galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb
```

Read the full `establish_from_craft` signature:
```bash
sed -n '1,25p' galaxy_game/app/services/settlement_deployment_service.rb
```

### Step 2 — Synthesis Report

```
SYNTHESIS REPORT — BaseSettlement.create! Migration
=====================================================
SITE 1 — manager.rb:235
Current code: [paste]
Available craft variable: [what craft object is in scope]
Available location variable: [what location is in scope]
Proposed replacement: [exact call]

SITE 2 — system_architect.rb:377
Current code: [paste]
Available craft variable: [what craft object is in scope]
Available location variable: [what location is in scope]
Proposed replacement: [exact call]

SITE 3 — task_execution_engine_v2.rb:104
Current code: [paste]
Available craft variable: [what craft object is in scope]
Available location variable: [what location is in scope]
Proposed replacement: [exact call]

RISK:
[any downstream code expecting BaseSettlement instance]
[any specs affected]

READY TO APPLY? — waiting for approval
```

Stop here and wait for approval before making any changes.

### Step 3 — Apply migration (after approval)

Replace each `BaseSettlement.create!` call with `SettlementDeploymentService.establish_from_craft(craft, location, manifest_name: manifest_name)` using the variables identified in Step 2.

If `craft` or `location` is not directly available at the call site — do not invent them. Flag the gap and stop.

### Step 4 — Run targeted specs

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/ 2>&1 | tail -20'
```

Expected: 0 new failures.

### Step 5 — Full suite check

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec 2>&1 | tail -15'
```

---

## Acceptance Criteria
- [ ] All 3 `BaseSettlement.create!` calls in AI Manager removed
- [ ] All 3 replaced with `SettlementDeploymentService.establish_from_craft`
- [ ] `task_execution_engine_v2.rb` contains no direct model creation
- [ ] AI Manager specs pass — 0 new failures
- [ ] Full suite — 0 new failures

---

## Stop Conditions — escalate immediately if:
- `craft` or `location` not available at call site — do not fabricate, flag and stop
- `establish_from_craft` requires a cargo manifest that doesn't exist for AI Manager context — flag before proceeding
- More than 3 `BaseSettlement.create!` calls found — map all before touching any
- Any call site is inside a rescue block or fallback path — flag, these need special handling

---

## Commit Instructions
```bash
git add galaxy_game/app/services/ai_manager/manager.rb
git add galaxy_game/app/services/ai_manager/system_architect.rb
git add galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb
git commit -m "refactor: migrate AI Manager BaseSettlement.create! to SettlementDeploymentService.establish_from_craft"
git push
```

---

## Documentation
- [ ] No doc changes needed for this task
- [ ] Flag gap: `docs/architecture/ai_manager/` directory does not exist — consider creating AI Manager architecture docs in a future documentation task

---

## Dependencies
**Blocked by**: none
**Blocks**: AI Manager Luna settlement deployment correctness
**Supersedes**: `docs/agent/archive/backlog_april_2026/2026-04-16-HIGH-REFACTOR-AI_MANAGER-USE-SETTLEMENT_DEPLOYMENT_SERVICE.md`
**Related**: `2026-05-27-CRITICAL-REFACTOR-EXTRACT-ORBITAL-RESUPPLY-CYCLE.md`

---

## Completion Report
**Completed by**: Implementation Agent (synthesis analysis)
**Completion date**: 2026-08-24
**Final test result**: N/A — no code changes made

### What was changed
None. The task premise was architecturally incorrect.

### Issues discovered
The original task assumed all 3 `BaseSettlement.create!` call sites should use `SettlementDeploymentService.establish_from_craft`. Analysis confirmed:

1. **manager.rb** — lava tube exploration plan creates settlement from lavatube + plan (no craft in scope). This is a distinct pre-deployment flow.
2. **system_architect.rb** — direct habitat deployment on celestial body (no craft in scope). This is a bootstrap operation.
3. **task_execution_engine_v2.rb** — temporary settlement + location creation from scratch (no craft in scope). This is a test/bootstrap pattern.

`SettlementDeploymentService.establish_from_craft` is correctly scoped to craft-based deployment only (landing craft arrives with cargo → verify → create settlement → deploy units → transfer cargo). None of the 3 sites have a craft object in scope.

The task's acceptance criteria ("All 3 replaced with establish_from_craft") cannot be met because it would require fabricating craft objects where none exist.

### Follow-up tasks needed
None filed per instructions. If additional settlement creation primitives are needed (e.g., `establish_from_plan`, `establish_direct`), they should be scoped and dispatched separately.

### Lessons learned
- Architecture docs can define a "canonical" primitive that doesn't cover all use cases — always verify variable availability at each call site before assuming replacement is possible.
- The stop condition "craft or location not available at call site — do not fabricate, flag and stop" was the correct guardrail. This task should have been closed during synthesis rather than proceeding to implementation.

Full evidence: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-08-24-SYNTHESIS-AI-MANAGER-SETTLEMENT-DEPLOYMENT.md`

---

## Handoff Summary
HANDOFF SUMMARY: BaseSettlement.create! removed from AI Manager | SettlementDeploymentService used for all settlement creation | task_execution_engine_v2 model-creation free | Luna deployment logic consistent
