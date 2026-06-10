---
status: completed
priority: HIGH
type: bug-fix
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

# TASK: Fix ImportRequestGenerator — `calculate_cost` NoMethodError
**Status**: ACTIVE
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-06-03
**Last Updated**: 2026-06-03

---

## Local Worker Triage Report
- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS
- **MVP Alignment**: VALID — blocks Luna Phase 1 integration
- **MVP Impact Note**: ImportRequestGenerator will throw NoMethodError at runtime when called from AI Manager Luna integration
- **Action Line**: READY FOR CLOUD HANDOFF

---

## Agent Assignment

**Assigned To**: Qwen3.5-9B (Local Copilot)
**Why This Agent**: Single file, one method name fix, no migration, no architectural judgment needed
**Supervision Level**: Watched carefully

---

## Context

`Logistics::ImportRequestGenerator` is responsible for creating import requests when a shortage is detected at a settlement. It calls `Settlements::CostAnalyzer` to get cost data before creating an `ImportRequest` record. The `generate_import_requests` (plural) method calls a method that does not exist on `CostAnalyzer`.

**Relevant Architecture Docs**:
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions

---

## Problem Statement

`Logistics::ImportRequestGenerator#generate_import_requests` calls `Settlements::CostAnalyzer.calculate_cost(settlement.id, shortage[:material], shortage[:need])` on line ~14. This method does not exist on `CostAnalyzer`. The actual method is `Settlements::CostAnalyzer.compare_costs(resource_name, settlement)` with a different signature.

**Current behavior**: `NoMethodError: undefined method 'calculate_cost' for Settlements::CostAnalyzer`

**Expected behavior**: Cost data retrieved successfully via `compare_costs(resource_name, settlement)`

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/logistics/import_request_generator.rb` | Generates import requests from shortage data | `#generate_import_requests` |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/services/settlements/cost_analyzer.rb` | Confirms actual method signature: `compare_costs(resource_name, settlement)` |
| `spec/services/logistics/import_request_generator_spec.rb` | Confirms which methods specs test |

### Migration (if needed)
- [x] No migration needed

---

## Implementation Steps

### Step 1 — Read both files first
Read the full contents of:
- `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/services/logistics/import_request_generator.rb`
- `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/services/settlements/cost_analyzer.rb`

Confirm:
- The exact line where `calculate_cost` is called in `generate_import_requests`
- The exact signature of `compare_costs` on `CostAnalyzer`

### Step 2 — Fix the method call in `generate_import_requests`

In `generate_import_requests`, replace:
```ruby
# before
cost_data = Settlements::CostAnalyzer.calculate_cost(
  settlement.id,
  shortage[:material],
  shortage[:need]
)
```

```ruby
# after
cost_data = Settlements::CostAnalyzer.compare_costs(
  shortage[:material],
  settlement
)
```

### Step 3 — Verify spec file exists and run targeted spec

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/logistics/import_request_generator_spec.rb 2>&1 | tail -30'
```

Expected result: 0 failures. If spec file does not exist, report that in your synthesis report — do not create the spec.

### Step 4 — Synthesis Report format (produce before applying fix)

```
SYNTHESIS REPORT
File: app/services/logistics/import_request_generator.rb
Error: NoMethodError — calculate_cost does not exist on CostAnalyzer
Line: [exact line number]
Current call: [paste exact current line]
Correct call: [paste corrected line]

ROOT CAUSE
generate_import_requests uses a method signature that does not match CostAnalyzer's public API.
compare_costs takes (resource_name, settlement) not (settlement_id, material, quantity).

PROPOSED FIX
[exact code change]

RISK
Low — isolated to generate_import_requests method only. generate_import_request (singular) already uses compare_costs correctly.

READY TO APPLY? — waiting for approval
```

Do not apply the fix until the user explicitly approves.

---

## Acceptance Criteria
- [ ] `calculate_cost` call replaced with `compare_costs` using correct signature
- [ ] Targeted spec run: 0 failures
- [ ] No regressions in related specs

---

## Stop Conditions — escalate to user immediately if:
- Spec file does not exist (report path, do not create it)
- `compare_costs` signature differs from what is documented here
- Fix causes failures in specs you did not touch
- Any factory or model setup issues appear

---

## Commit Instructions
Run on **host**, not inside container:
```bash
git add app/services/logistics/import_request_generator.rb
git commit -m "bug-fix: import_request_generator — replace calculate_cost with compare_costs correct signature"
git push
```

---

## Dependencies
**Blocked by**: none
**Blocks**: 2026-06-03-HIGH-BUG-FIX-IMPORT-REQUEST-GENERATOR-REMOVE-PLURAL-API.md
**Related tasks**: 2026-06-03-MEDIUM-BUG-FIX-SHORTAGE-DETECTOR-OPERATIONAL-DATA-KEYS.md

---

## Completion Report
*Filled in by the implementing agent after completion*
*Filled in by the implementing agent after completion*

**Completed by**: Implementation Agent
**Completion date**: 2026-06-03
**Final test result**: 2 examples, 0 failures (see targeted spec run)

### What was changed
- Replaced `Settlements::CostAnalyzer.calculate_cost(...)` with `Settlements::CostAnalyzer.compare_costs(shortage[:material], settlement)` in `app/services/logistics/import_request_generator.rb`.

### Issues discovered
- `generate_import_requests` used a non-existent `calculate_cost` method causing NoMethodError. The plural API is used by `Logistics::ServiceCoordinator`, so it was corrected rather than removed.

### Follow-up tasks needed
- Consider consolidating/remove the plural API `generate_import_requests` in a separate task if the singular API is preferred.

### Lessons learned
- Keep public API usage consistent across service methods; tests cover singular API but not plural one.

---

## Handoff Summary
HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]