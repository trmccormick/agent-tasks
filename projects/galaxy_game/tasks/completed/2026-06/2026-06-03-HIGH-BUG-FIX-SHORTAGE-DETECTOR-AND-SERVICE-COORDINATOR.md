---
status: completed
priority: HIGH
type: bug-fix
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

# TASK: Fix ShortageDetector + ServiceCoordinator Interface Mismatch
**Status**: ACTIVE
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-06-03
**Last Updated**: 2026-06-03

---

## Local Worker Triage Report
- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS
- **MVP Alignment**: VALID — two bugs block all shortage detection and import request generation for Luna
- **MVP Impact Note**: Bug 1 causes shortages to never be detected (silent nil). Bug 2 causes ServiceCoordinator to pass malformed data to ImportRequestGenerator.
- **Action Line**: READY FOR CLOUD HANDOFF

---

## Agent Assignment

**Assigned To**: Qwen3.5-27B (Cloud)
**Why This Agent**: Two related bugs across three files requiring schema verification before fixing
**Supervision Level**: Watched carefully

---

## Context

There are two bugs in the shortage detection pipeline that must be fixed together. They are in different files but are tightly coupled — fixing one without the other leaves the pipeline broken.

**Bug 1 — ShortageDetector silent nil** (`shortage_detector.rb`):
`detect_shortages` accesses `settlement.operational_data[:survival_targets]` using symbol keys. If `operational_data` is a JSONB column (string keys), this silently returns `{}` and no shortages are ever detected.

**Bug 2 — ServiceCoordinator map mismatch** (`service_coordinator.rb`):
`detect_and_request_imports` calls `.map` directly on the hash returned by `ShortageDetector.detect_shortages`. The return value is:
```ruby
{ viable: true, survival_shortages: [...], expansion_shortages: [...], total_shortages: [...] }
```
Calling `.map` on a hash yields `[key, value]` pairs, not shortage hashes. This passes malformed data into `ImportRequestGenerator.generate_import_request`.

**Relevant Architecture Docs**:
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions (check Virtual Ledger rules — import requests may be real GCC transactions)

---

## Problem Statement

**Bug 1:**
```ruby
# shortage_detector.rb — current (broken if JSONB)
survival_targets = settlement.operational_data[:survival_targets] || {}
expansion_targets = settlement.operational_data[:expansion_targets] || {}
```
Symbol keys on a JSONB hash always return nil. Shortages are never detected.

**Bug 2:**
```ruby
# service_coordinator.rb — current (broken)
shortages = Logistics::ShortageDetector.detect_shortages(settlement)
results = shortages.map do |shortage|
  Logistics::ImportRequestGenerator.generate_import_request(settlement, shortage)
end
```
`shortages` is a hash with keys `:viable`, `:survival_shortages`, etc. Mapping over it yields `[:viable, true]`, `[:survival_shortages, [...]]` — not individual shortage hashes.

**Expected behavior:**
- `ShortageDetector` correctly reads survival and expansion targets
- `ServiceCoordinator` iterates only the survival shortages array and passes each individual shortage hash to `generate_import_request`

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/logistics/shortage_detector.rb` | Detects shortages | `#detect_shortages` — operational_data key access |
| `app/services/ai_manager/service_coordinator.rb` | Orchestrates shortage detection and import requests | `#detect_and_request_imports` |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/models/settlement/base_settlement.rb` | Confirm operational_data column type and any accessor |
| `db/schema.rb` | Confirm operational_data column type (jsonb, json, text) |
| `spec/services/logistics/shortage_detector_spec.rb` | Check key format used in spec setup |
| `spec/services/ai_manager/service_coordinator_spec.rb` | Check if detect_and_request_imports is tested |

### Migration (if needed)
- [x] No migration needed

---

## Implementation Steps

### Step 1 — Verify operational_data column type

Read:
- `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/db/schema.rb` — search for `operational_data` on settlements table
- `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/models/settlement/base_settlement.rb` — check for `store_accessor` or `with_indifferent_access`

Report the column type before proceeding.

### Step 2 — Fix Bug 1: ShortageDetector key access

**If JSONB column with plain string keys** (most likely):
```ruby
# before
survival_targets = settlement.operational_data[:survival_targets] || {}
expansion_targets = settlement.operational_data[:expansion_targets] || {}
```
```ruby
# after
survival_targets = settlement.operational_data&.with_indifferent_access&.dig('survival_targets') || {}
expansion_targets = settlement.operational_data&.with_indifferent_access&.dig('expansion_targets') || {}
```

**If model already provides indifferent access**, use string keys directly:
```ruby
survival_targets = settlement.operational_data['survival_targets'] || {}
expansion_targets = settlement.operational_data['expansion_targets'] || {}
```

Confirm from Step 1 which applies. Do not guess.

### Step 3 — Fix Bug 2: ServiceCoordinator map mismatch

```ruby
# before
shortages = Logistics::ShortageDetector.detect_shortages(settlement)
results = shortages.map do |shortage|
  Logistics::ImportRequestGenerator.generate_import_request(settlement, shortage)
end
```
```ruby
# after
shortage_report = Logistics::ShortageDetector.detect_shortages(settlement)
return [] unless shortage_report[:viable]

results = shortage_report[:survival_shortages].map do |shortage|
  Logistics::ImportRequestGenerator.generate_import_request(settlement, shortage)
end
```

Note: The `return []` guard respects the existing `viable` flag from `ShortageDetector`. Only survival shortages are processed — expansion shortages are not actioned at this stage (Luna Phase 1 scope).

### Step 4 — Run targeted specs

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/logistics/shortage_detector_spec.rb 2>&1 | tail -30'
```

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/service_coordinator_spec.rb 2>&1 | tail -30'
```

If either spec file does not exist, report that — do not create it.

### Step 5 — Synthesis Report format (produce before applying)

```
SYNTHESIS REPORT
Files: shortage_detector.rb, service_coordinator.rb

SCHEMA FINDINGS
operational_data column type: [jsonb / json / text]
Model accessor: [store_accessor / with_indifferent_access / none]
Spec key format in shortage_detector_spec: [symbol / string / not found]

BUG 1 FIX — shortage_detector.rb
Before: [exact current lines]
After: [exact proposed lines]

BUG 2 FIX — service_coordinator.rb
Before: [exact current lines]
After: [exact proposed lines]

RISK
Bug 1: Low — isolated read access change
Bug 2: Low — detect_and_request_imports is not called in the hot path unless AI Manager ticks

READY TO APPLY? — waiting for approval
```

Do not apply until user approves.

---

## Acceptance Criteria
- [ ] operational_data key access confirmed against actual schema
- [ ] Bug 1 fix applied using correct pattern for column type
- [ ] Bug 2 fix iterates `survival_shortages` array, not raw hash
- [ ] `viable` guard in place before iterating
- [ ] Targeted spec runs: 0 failures
- [ ] No regressions in related specs

---

## Stop Conditions — escalate to user immediately if:
- Schema shows operational_data is not a JSON column
- `survival_shortages` key is missing from actual ShortageDetector output (re-read the file)
- Either spec file does not exist (report, do not create)
- Fix causes failures outside the two target spec files
- Any question arises about whether import requests are virtual ledger or real GCC — check DECISIONS.md, do not assume

---

## Commit Instructions
Run on **host**, not inside container:
```bash
git add app/services/logistics/shortage_detector.rb
git add app/services/ai_manager/service_coordinator.rb
git commit -m "bug-fix: shortage_detector operational_data keys + service_coordinator map mismatch"
git push
```

---

## Dependencies
**Blocked by**: none
**Blocks**: Luna Phase 3 integration task
**Related tasks**: 2026-06-03-HIGH-BUG-FIX-IMPORT-REQUEST-GENERATOR-CALCULATE-COST.md (completed)

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
