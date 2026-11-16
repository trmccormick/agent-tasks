---
status: completed
priority: HIGH
type: bug-fix
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

# TASK: Fix TaskExecutionEngineV2 — `load_settlement` Find-or-Create Pattern
**Status**: ACTIVE
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-06-03
**Last Updated**: 2026-06-03

---

## Local Worker Triage Report
- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS
- **MVP Alignment**: VALID — creates duplicate settlement records for Luna on every AI Manager invocation
- **MVP Impact Note**: Luna already has a settlement. Unconditional create! produces orphaned duplicate records and breaks all settlement-scoped queries
- **Action Line**: READY FOR CLOUD HANDOFF

---

## Agent Assignment

**Assigned To**: Qwen3.5-27B (Cloud)
**Why This Agent**: Requires reading related models to understand settlement-to-celestial-body relationship before writing find logic
**Supervision Level**: Watched carefully

---

## Context

`AIManager::TaskExecutionEngineV2#load_settlement` is called during initialization to find the settlement associated with the target celestial body. Currently it calls `create_temporary_settlement` unconditionally whenever a celestial body is found. For Luna, which already has an established settlement, this creates a duplicate record every time the engine is initialized. The `AIManager::Manager` already has a find-before-create pattern in `run_initial_construction` — this engine needs the same.

**Relevant Architecture Docs**:
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions

---

## Problem Statement

```ruby
# current — always creates a new settlement
def load_settlement(target_body)
  body = CelestialBodies::CelestialBody.find_by(identifier: target_body)
  return nil unless body
  create_temporary_settlement(body)
end
```

Every call to `TaskExecutionEngineV2.new('luna')` creates a new `Settlement::BaseSettlement` record and a new `Location::CelestialLocation` record, even when Luna already has a settlement.

**Current behavior**: Duplicate settlement records created on every engine initialization
**Expected behavior**: Existing settlement found and returned. New settlement created only if none exists.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/ai_manager/task_execution_engine_v2.rb` | V2 task execution engine | `#load_settlement` |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/models/settlement/base_settlement.rb` | Understand how settlements link to celestial bodies |
| `app/models/location/celestial_location.rb` | Understand the locationable/celestial_body join |
| `app/services/ai_manager/manager.rb` | Reference pattern — see `settlement_established?` and `run_initial_construction` |

### Migration (if needed)
- [x] No migration needed

---

## Implementation Steps

### Step 1 — Read the relationship models first

Read:
- `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/models/settlement/base_settlement.rb`
- `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/models/location/celestial_location.rb`
- `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb`

Determine:
- How does a `BaseSettlement` link to a `CelestialBody`? (via `CelestialLocation`? direct association?)
- What is the correct query to find an existing settlement for a given celestial body?

### Step 2 — Replace `load_settlement` with find-or-create pattern

Reference the pattern in `manager.rb`:
```ruby
Settlement::BaseSettlement.find_by(location: lavatube.location) ||
  establish_settlement_from_plan(lavatube, initial_plan)
```

Apply the same logic to `load_settlement`. The exact query depends on Step 1 findings, but the shape should be:

```ruby
# after
def load_settlement(target_body)
  body = CelestialBodies::CelestialBody.find_by(identifier: target_body)
  return nil unless body

  # Find existing settlement linked to this celestial body
  existing = find_existing_settlement(body)
  return existing if existing

  # Only create if none exists
  create_temporary_settlement(body)
end

def find_existing_settlement(body)
  # Use the correct association based on model structure found in Step 1
  # Example — adjust based on actual model:
  location = Location::CelestialLocation.find_by(celestial_body: body)
  return nil unless location
  Settlement::BaseSettlement.find_by(location: location)
end
```

**Do not guess the association** — confirm it from the model files in Step 1 before writing the query.

### Step 3 — Run targeted spec

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/task_execution_engine_v2_spec.rb 2>&1 | tail -30'
```

If spec file does not exist, report that — do not create it. Run a broader smoke check instead:

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/ 2>&1 | tail -30'
```

Expected result: 0 failures introduced by this change.

### Step 4 — Synthesis Report format (produce before applying)

```
SYNTHESIS REPORT
File: app/services/ai_manager/task_execution_engine_v2.rb
Method: load_settlement

MODEL FINDINGS
Settlement-to-CelestialBody link: [describe the association]
Correct find query: [exact ActiveRecord query]

ROOT CAUSE
load_settlement calls create_temporary_settlement unconditionally.
For Luna (existing settlement), this creates duplicate records every initialization.

PROPOSED FIX
[exact before/after code for load_settlement and new find_existing_settlement method]

RISK
Medium — change affects engine initialization. If find query is wrong, engine gets nil settlement.
Mitigation: find_by returns nil safely, falls through to create_temporary_settlement as before.

READY TO APPLY? — waiting for approval
```

Do not apply until user approves.

---

## Acceptance Criteria
- [ ] `load_settlement` uses find-or-create pattern
- [ ] Existing settlement returned when one exists for the celestial body
- [ ] New settlement only created when none exists
- [ ] Association query confirmed from model files, not assumed
- [ ] Targeted spec run: 0 failures introduced

---

## Stop Conditions — escalate to user immediately if:
- Settlement-to-CelestialBody association is ambiguous or polymorphic in unexpected way
- Multiple settlements can exist for one celestial body (changes the find logic significantly)
- Spec file does not exist and broader spec run shows failures
- Fix causes failures in specs you did not touch

---

## Commit Instructions
Run on **host**, not inside container:
```bash
git add app/services/ai_manager/task_execution_engine_v2.rb
git commit -m "bug-fix: task_execution_engine_v2 — load_settlement find-or-create to prevent duplicate settlement records"
git push
```

---

## Dependencies
**Blocked by**: none
**Blocks**: 2026-06-03-HIGH-FEATURE-LUNA-PHASE1-COST-ANALYZER-AI-MANAGER-INTEGRATION.md (future)
**Related tasks**: none

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: Qwen3.5-27B (Cloud)
**Completion date**: 2026-06-04
**Final test result**: 722 examples, 0 failures, 4 pending

### What was changed
- Modified `load_settlement` method to use find-or-create pattern
- Added private `find_existing_settlement(body)` helper method
- Settlement-to-CelestialBody association confirmed: BaseSettlement → CelestialLocation (polymorphic) → CelestialBody

### Issues discovered
- None — fix applied cleanly without breaking existing tests

### Follow-up tasks needed
- None — task fully completed

### Lessons learned
- Two-step find query (location then settlement) is clearer and safer than join queries for this use case
- `find_by` returns nil safely, allowing graceful fallback to create logic

---

## Handoff Summary
HANDOFF SUMMARY: [app/services/ai_manager/task_execution_engine_v2.rb updated] | [load_settlement now uses find-or-create pattern] | [task completed, ready for next task in backlog]
