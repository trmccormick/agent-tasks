---
status: backlog
priority: MEDIUM
type: bug-fix
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/2026-07-04-MEDIUM-FIX-ESCALATION-SERVICE-HARVESTER-COMPLETION-JOB-MATCHER.md

READ FIRST: Task file contains all prerequisites, credentials, gotchas, and verification steps.
```

---

# TASK: Fix escalation_service_spec.rb — `a_value_within` matcher vs keyword-arg hash
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: bug-fix
**Created**: 2026-07-04

---

## Context

`escalation_service_spec.rb:429` fails because the spec uses `a_value_within(60)` matcher to check a keyword-arg hash `{wait_until: time}`, but RSpec's `a_value_within` expects a bare value, not a hash. The actual code passes `{:wait_until => completion_time}` as a single argument to `.set()`.

This is a simple spec fix — no service code changes needed.

---

## Problem Statement

**Error**:
```
#<HarvesterCompletionJob (class)> received :set with unexpected arguments
  expected: (a value within 60 of 2026-07-04 14:10:18.678590396 +0000)
       got: ({:wait_until => 2026-07-04 14:10:18.361737000 +0000})
```

**Root cause**: The spec uses `a_value_within(60)` to match the hash argument, but that matcher only works on bare values. The actual code passes a keyword-arg hash.

---

## Files Involved

### Primary — edit this
| File | Purpose | Key Section |
|---|---|---|
| `galaxy_game/spec/services/ai_manager/escalation_service_spec.rb:429` | Fix matcher to match hash argument correctly | line ~429 |

### Reference — read only
| File | Why You Need It |
|---|---|
| `galaxy_game/app/services/ai_manager/escalation_service.rb:395-396` | See actual `.set(wait_until: ...)` call |

---

## Implementation Steps

### Step 1 — Fix the matcher

**Before** (approximate line 429):
```ruby
expect(HarvesterCompletionJob).to receive(:set)
  .with(a_value_within(60).of(completion_time))
  .and_call_original
```

**After**:
```ruby
expect(HarvesterCompletionJob).to receive(:set)
  .with(hash_including(wait_until: a_value_within(60).of(completion_time)))
  .and_call_original
```

Or alternatively, if the test just needs to verify timing accuracy:
```ruby
expect(HarvesterCompletionJob).to receive(:set) do |args|
  expect(args[:wait_until]).to be_within(60).of(completion_time)
  true
end.and_call_original
```

### Step 2 — Verify syntax

```bash
ruby -c galaxy_game/spec/services/ai_manager/escalation_service_spec.rb 2>&1
```

Must return: `Syntax OK`

### Step 3 — Run targeted spec

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/escalation_service_spec.rb:429 --format progress 2>&1' | tail -5
```

Target: 0 failures.

### Step 4 — Commit

```bash
git add galaxy_game/spec/services/ai_manager/escalation_service_spec.rb
git commit -m "fix: correct matcher in escalation_service_spec for keyword-arg hash"
git push
```

---

## Acceptance Criteria
- [ ] `a_value_within` replaced with `hash_including(wait_until: a_value_within(...))` or equivalent
- [ ] Syntax OK
- [ ] 0 failures on targeted spec run
- [ ] No regressions in related specs (run full escalation_service_spec.rb)

---

## Stop Conditions
- Same failure persists after two attempts — stop and report
- Any service file touched — stop immediately, this is a spec-only fix

---

## Commit Instructions
```bash
git add galaxy_game/spec/services/ai_manager/escalation_service_spec.rb
git commit -m "fix: correct matcher in escalation_service_spec for keyword-arg hash"
git push
```

---

## Completion Report
**Completed by**: [agent]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `[file]` — [description]

---

## Handoff Summary
HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
