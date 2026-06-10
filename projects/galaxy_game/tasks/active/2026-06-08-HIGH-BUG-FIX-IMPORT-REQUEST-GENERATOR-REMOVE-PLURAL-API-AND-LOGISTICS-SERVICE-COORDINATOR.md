---
status: active
priority: HIGH
type: bug-fix
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

# TASK: Remove Orphaned Plural API and Logistics::ServiceCoordinator
**Status**: ACTIVE
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-06-08
**Last Updated**: 2026-06-08

---

## Local Worker Triage Report
- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS
- **MVP Alignment**: VALID — dead code removal, no architectural change
- **MVP Impact Note**: Orphaned plural path uses wrong ManifestGenerator API and wrong CostAnalyzer
  interface. Leaving it risks confusion for future callers. AIManager path (singular keyword-args)
  is the confirmed canonical path from Phases 1-3.
- **Action Line**: READY FOR DISPATCH

---

## Agent Assignment

**Assigned To**: Qwen3.5-27B (M4 Copilot)
**Why This Agent**: Multi-file removal with spec verification — needs terminal access
**Supervision Level**: Watched carefully

---

## Context

Phases 1-3 established `Logistics::ImportRequestGenerator.generate_import_request` (singular,
keyword args) as the canonical API, called by both `AIManager::StrategySelector` and
`AIManager::ServiceCoordinator`. A parallel legacy path exists that is fully orphaned:

- `Logistics::ServiceCoordinator#detect_and_request_imports` — no external callers, uses
  wrong plural API
- `ImportRequestGenerator.generate_import_requests` (plural) — only called by the orphaned
  Logistics::ServiceCoordinator, uses wrong `ManifestGenerator.generate_manifest(cost_data)`
  interface (correct API is `create_manifest(source, destination, items)`)
- `ImportRequestGenerator.generate_request` alias — only calls the plural method internally

This was confirmed by grep on 2026-06-08. Nothing in `app/` calls these outside themselves.

**Relevant Architecture Docs**:
- `docs/new_agent/rules/GUARDRAILS.md`
- `docs/new_agent/rules/DECISIONS.md`

---

## Problem Statement

Three dead-code items need removal:

1. `app/services/logistics/service_coordinator.rb` — entire file. No external callers confirmed.
2. `ImportRequestGenerator.generate_import_requests` (plural, lines ~7-40)
3. `ImportRequestGenerator.generate_request` alias (lines ~44-50)

**Current behavior**: Two conflicting public APIs on ImportRequestGenerator. Dead
Logistics::ServiceCoordinator references the wrong one.

**Expected behavior**: One canonical public method — `generate_import_request` (singular,
keyword args). Logistics::ServiceCoordinator removed entirely.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Action |
|---|---|---|
| `app/services/logistics/import_request_generator.rb` | Generates import requests | Remove plural method + alias |
| `app/services/logistics/service_coordinator.rb` | Orphaned orchestrator | Delete entire file |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `spec/services/logistics/import_request_generator_spec.rb` | Confirm specs only test singular method |
| `spec/services/ai_manager/service_coordinator_spec.rb` | Confirm this tests AIManager::ServiceCoordinator, not Logistics:: |

### Migration (if needed)
- [x] No migration needed

---

## Implementation Steps

### Step 1 — Verify no new callers before touching anything

Run these greps on host. If any results appear outside the files listed in Problem Statement,
STOP and report — do not proceed.

```bash
grep -rn "generate_import_requests\|\.generate_request" \
  ~/Documents/git/galaxyGame/galaxy_game/app/ --include="*.rb"
```

```bash
grep -rn "Logistics::ServiceCoordinator\|detect_and_request_imports" \
  ~/Documents/git/galaxyGame/galaxy_game/app/ --include="*.rb"
```

```bash
grep -rn "Logistics::ServiceCoordinator\|detect_and_request_imports\|generate_import_requests\|\.generate_request" \
  ~/Documents/git/galaxyGame/galaxy_game/spec/ --include="*.rb"
```

Expected results:
- Plural method grep: only self-references inside `import_request_generator.rb`
- ServiceCoordinator grep in app/: only `logistics/service_coordinator.rb` itself
- Spec grep: only `ai_manager/service_coordinator_spec.rb` (which tests AIManager::, not Logistics::)

### Step 2 — Remove plural method and alias from ImportRequestGenerator

In `app/services/logistics/import_request_generator.rb`, remove these two methods entirely:

```ruby
# REMOVE THIS — generate_import_requests (plural, ~lines 7-40)
def self.generate_import_requests(settlement, shortage_report)
  ...
end

# REMOVE THIS — generate_request alias (~lines 44-50)
def self.generate_request(settlement, shortage_report)
  ...
end
```

Leave `generate_import_request` (singular, keyword args) exactly as is.

### Step 3 — Delete Logistics::ServiceCoordinator

```bash
rm ~/Documents/git/galaxyGame/galaxy_game/app/services/logistics/service_coordinator.rb
```

Confirm deletion:
```bash
ls ~/Documents/git/galaxyGame/galaxy_game/app/services/logistics/
```

### Step 4 — Run targeted spec

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/logistics/import_request_generator_spec.rb 2>&1 | tail -20'
```

Expected: 0 failures.

### Step 5 — Run ai_manager suite to confirm no regressions

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/ 2>&1 | tail -10'
```

Expected: 740 examples, 0 failures, 4 pending.

### Step 6 — Synthesis Report (produce before commit, wait for approval)

```
SYNTHESIS REPORT
Action: Remove orphaned plural API + Logistics::ServiceCoordinator

GREP VERIFICATION
Plural callers outside import_request_generator.rb: [none confirmed / list any found]
Logistics::ServiceCoordinator callers outside itself: [none confirmed / list any found]
Spec references to removed code: [none confirmed / list any found]

CHANGES MADE
import_request_generator.rb: removed generate_import_requests (lines X-Y) and generate_request (lines X-Y)
service_coordinator.rb: deleted

SPEC RESULTS
import_request_generator_spec.rb: X examples, 0 failures
ai_manager suite: 740 examples, 0 failures, 4 pending

RISK
Low — no external callers confirmed by grep

READY TO APPLY? — waiting for approval
```

---

## Acceptance Criteria
- [ ] `generate_import_requests` method removed from ImportRequestGenerator
- [ ] `generate_request` alias removed from ImportRequestGenerator
- [ ] `app/services/logistics/service_coordinator.rb` deleted
- [ ] `generate_import_request` (singular keyword args) untouched
- [ ] import_request_generator_spec: 0 failures
- [ ] ai_manager suite: 740 examples, 0 failures, 4 pending

---

## Stop Conditions — escalate to user immediately if:
- Grep finds any callers of the plural methods or Logistics::ServiceCoordinator outside the
  files listed in Problem Statement
- Removing the methods causes spec failures
- ai_manager suite drops below 740 examples or shows new failures

---

## Commit Instructions
Run on **host**, not inside container:
```bash
cd ~/Documents/git/galaxyGame/galaxy_game
git add app/services/logistics/import_request_generator.rb
git rm app/services/logistics/service_coordinator.rb
git commit -m "bug-fix: remove orphaned plural API and Logistics::ServiceCoordinator — singular keyword-args API is canonical from Phase 3"
git push
```

---

## Dependencies
**Blocked by**: none
**Blocks**: Phase 4 task (cleaner codebase for next integration)
**Related tasks**: 2026-06-08-HIGH-FEATURE-LUNA-PHASE-4-CONSUMPTION-AWARE-ORDERING.md

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: —
**Completion date**: —
**Final test result**: —

### What was changed
### Issues discovered
### Follow-up tasks needed
### Lessons learned

---

## Handoff Summary
HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
