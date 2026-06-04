---
status: backlog
priority: HIGH
type: bug-fix
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

# TASK: ImportRequestGenerator — Remove Broken Plural API
**Status**: BACKLOG
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-06-03
**Last Updated**: 2026-06-03

---

## Local Worker Triage Report
- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS
- **MVP Alignment**: VALID — ambiguous dual API causes runtime errors and spec confusion
- **MVP Impact Note**: Two conflicting public methods on ImportRequestGenerator creates unpredictable call paths from AI Manager
- **Action Line**: READY FOR CLOUD HANDOFF

---

## Agent Assignment

**Assigned To**: Qwen3.5-9B (Local Copilot)
**Why This Agent**: Single file cleanup, no migration, no architectural judgment — mechanical removal
**Supervision Level**: Watched carefully

---

## Context

`Logistics::ImportRequestGenerator` currently has two public methods that do similar things with incompatible implementations. `generate_import_request` (singular) is tested by specs and uses the correct `CostAnalyzer` interface. `generate_import_requests` (plural) uses the wrong `CostAnalyzer` interface and is not spec-tested. Having both creates ambiguity for any calling code including the AI Manager integration.

**Relevant Architecture Docs**:
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions

---

## Problem Statement

`generate_import_requests` (plural) and `generate_request` (a wrapper alias for it) are dead weight — broken interface, no specs, conflict with the canonical singular API. They should be removed.

**Note**: This task depends on Task 1 (calculate_cost fix) being completed first, but since we are removing this method entirely, Task 1's fix to this method is moot if this task runs second. Coordinate order with the Strategist.

**Current behavior**: Two public methods exist with conflicting implementations. Callers have no clear canonical entry point.

**Expected behavior**: One canonical public method — `generate_import_request` (singular). The plural method and its `generate_request` alias are removed entirely.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/logistics/import_request_generator.rb` | Generates import requests | `#generate_import_requests`, `#generate_request` |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `spec/services/logistics/import_request_generator_spec.rb` | Confirm specs only test singular method |

### Migration (if needed)
- [x] No migration needed

---

## Implementation Steps

### Step 1 — Read the file and spec first
Read:
- `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/services/logistics/import_request_generator.rb`
- `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/spec/services/logistics/import_request_generator_spec.rb` (if it exists)

Confirm:
- Specs only reference `generate_import_request` (singular)
- No other file in `app/` calls `generate_import_requests` (plural) or `generate_request`

Run this grep on host to check for callers before removing:
```bash
grep -rn "generate_import_requests\|\.generate_request" /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/ --include="*.rb"
```

If any callers exist outside the file itself, stop and report. Do not remove the method.

### Step 2 — Remove the plural method and alias

Remove the following two methods entirely from `import_request_generator.rb`:
- `self.generate_import_requests` (the plural method)
- `self.generate_request` (the alias wrapper)

Do not modify `self.generate_import_request` (singular) — leave it exactly as is.

### Step 3 — Verify targeted spec

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/logistics/import_request_generator_spec.rb 2>&1 | tail -30'
```

Expected result: 0 failures. If spec file does not exist, report that — do not create it.

### Step 4 — Synthesis Report format (produce before applying)

```
SYNTHESIS REPORT
File: app/services/logistics/import_request_generator.rb
Action: Remove generate_import_requests and generate_request methods

GREP RESULTS
Callers found outside this file: [yes/no — paste grep output]

METHODS TO REMOVE
generate_import_requests: lines [X-Y]
generate_request: lines [X-Y]

RISK
[Low if no outside callers / High if callers found]

READY TO APPLY? — waiting for approval
```

Do not apply until user approves.

---

## Acceptance Criteria
- [ ] `generate_import_requests` method removed
- [ ] `generate_request` alias removed
- [ ] No callers exist outside the file
- [ ] `generate_import_request` (singular) untouched
- [ ] Targeted spec run: 0 failures

---

## Stop Conditions — escalate to user immediately if:
- Grep finds callers of `generate_import_requests` or `generate_request` outside this file
- Removing the methods causes spec failures
- Spec file does not exist (report, do not create)

---

## Commit Instructions
Run on **host**, not inside container:
```bash
git add app/services/logistics/import_request_generator.rb
git commit -m "bug-fix: import_request_generator — remove broken plural API and generate_request alias"
git push
```

---

## Dependencies
**Blocked by**: 2026-06-03-HIGH-BUG-FIX-IMPORT-REQUEST-GENERATOR-CALCULATE-COST.md (run after or instead of Task 1)
**Blocks**: none
**Related tasks**: 2026-06-03-HIGH-BUG-FIX-IMPORT-REQUEST-GENERATOR-CALCULATE-COST.md

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
