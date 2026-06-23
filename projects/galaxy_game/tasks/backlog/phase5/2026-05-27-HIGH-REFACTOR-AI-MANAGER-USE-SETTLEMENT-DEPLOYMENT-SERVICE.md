---
status: active
priority: HIGH
type: refactor
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: false
relocated_from: reorganization_attempt_3
relocated_reason: "Luna simulation blocker — task_execution_engine_v2.rb:107 actively used in rake luna_mission:execute; bypasses SettlementDeploymentService"
codebase_status_2026_06_22: "All 3 direct BaseSettlement.create! call sites confirmed present: manager.rb:235, system_architect.rb:377, task_execution_engine_v2.rb:107. All bypass SettlementDeploymentService and skip cargo verification/unit deployment. Requires refactoring to use establish_from_craft."
---

# TASK: Migrate Direct BaseSettlement.create! to SettlementDeploymentService

**Status**: ACTIVE
**Priority**: HIGH
**Type**: refactor
**Created**: 2026-05-27
**Last Updated**: 2026-05-27

---

## Local Worker Triage Report

- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS
- **MVP Alignment**: VALID — AI Manager must use shared deployment service for Luna settlement creation to ensure consistent manifest-driven deployment logic
- **MVP Impact Note**: Direct BaseSettlement.create! in AI Manager bypasses cargo verification, unit deployment, and cargo transfer — Luna precursor deployment will be incomplete without this fix
- **Action Line**: READY FOR CLOUD HANDOFF

---

## Agent Assignment

**Assigned To**: GPT-4.1 0x
**Why This Agent**: Targeted migration, 3 files, clear replacement pattern
**Supervision Level**: Watched carefully

---

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
**Completed by**:
**Completion date**:
**Final test result**:

### What was changed
### Issues discovered
### Follow-up tasks needed
### Lessons learned

---

## Handoff Summary
HANDOFF SUMMARY: BaseSettlement.create! removed from AI Manager | SettlementDeploymentService used for all settlement creation | task_execution_engine_v2 model-creation free | Luna deployment logic consistent
