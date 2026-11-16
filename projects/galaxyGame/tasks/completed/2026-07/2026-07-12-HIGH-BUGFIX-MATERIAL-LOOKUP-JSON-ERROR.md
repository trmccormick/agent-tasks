---
status: completed
completed: 2026-07-18
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-12-HIGH-BUGFIX-MATERIAL-LOOKUP-JSON-ERROR.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-12-HIGH-BUGFIX-MATERIAL-LOOKUP-JSON-ERROR.md \
         projects/galaxy_game/tasks/active/2026-07-12-HIGH-BUGFIX-MATERIAL-LOOKUP-JSON-ERROR.md
  Then open the moved file and change: status: backlog → status: active

READ FIRST: Task file contains all details.
```

---

# TASK: MaterialLookupService JSON error handling spec failure
**Status**: BACKLOG
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-07-12

---

## Completion Notes (2026-07-18)
The bugfix was already resolved in a prior session. Verified:
- Line 251 spec: 1 example, 0 failures
- Full suite: 37 examples, 0 failures
- Logger error handling for corrupted JSON works correctly
- No regressions in related specs

---

## Context
`MaterialLookupServiceSpec` expects the service to log an error with "Invalid JSON in file:" when given corrupted JSON, but the logger never receives this message. Either:
1. The error handling code path isn't being triggered by the test setup
2. The logger is configured differently in tests (silenced)
3. The exception is raised before the rescue block

---

## Problem Statement
```
Failure/Error: expect(Rails.logger).to have_received(:error).with(/Invalid JSON in file:/)
  expected: 1 time with arguments: (/Invalid JSON in file:/)
  received: 0 times
```

**Current behavior**: Logger doesn't receive the expected error message
**Expected behavior**: Service logs "Invalid JSON in file:" when parsing corrupted JSON

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|------|---------|-------------------|
| `galaxy_game/spec/services/lookup/material_lookup_service_spec.rb` | Spec (line 251) | Mock logger expectations |
| `galaxy_game/app/services/lookup/material_lookup_service.rb` | Service | JSON parsing + error handling |

### Reference Files
| File | Why You Need It |
|------|-----------------|
| `spec/rails_helper.rb` | Logger test configuration |
| `config/environments/test.rb` | Rails logger config in test env |

---

## Implementation Steps

### Step 1 — Investigate the spec setup
```bash
# Check how the mock logger is configured
grep -n "Rails.logger\|allow.*logger\|have_received" galaxy_game/spec/services/lookup/material_lookup_service_spec.rb | head -20

# Check the corrupted JSON test setup
sed -n '240,270p' galaxy_game/spec/services/lookup/material_lookup_service_spec.rb
```

### Step 2 — Determine root cause
Common causes:
- **Logger silenced in tests**: `Rails.logger` may be a `ActiveSupport::LoggerSilence` in test env
- **Exception raised before rescue**: JSON parse error may propagate up instead of being caught
- **Mock not set up correctly**: `allow(Rails.logger).to receive(:error)` may need to be in `before` block
- **File path issue**: Corrupted file may not be found, so parsing never happens

### Step 3 — Apply fix and verify
```bash
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rspec \
  spec/services/lookup/material_lookup_service_spec.rb:251 \
  --format documentation 2>&1 | tail -20
```

Expected result: 1 example, 0 failures

---

## Acceptance Criteria
- [ ] Logger receives error message with corrupted JSON
- [ ] Service handles error gracefully (doesn't crash)
- [ ] No regressions in related lookup specs

---

## Stop Conditions — escalate to user immediately if:
- Error handling architecture needs rethinking
- Fix requires changing more than 2 files

---

## Dependencies
**Blocked by**: none
**Blocks**: none
**Related tasks**: GameDataGenerator spec failure, harvester name mismatch
