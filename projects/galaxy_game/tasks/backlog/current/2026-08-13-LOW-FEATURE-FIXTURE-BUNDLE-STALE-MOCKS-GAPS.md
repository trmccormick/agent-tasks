---
status: backlog
priority: LOW
type: test-fixture-bundle
system_domain: Test Infrastructure
mvp_alignment: TEST_RELIABILITY
local_worker_safe: true
created: 2026-08-13
last_updated: 2026-08-14
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-13-LOW-FEATURE-FIXTURE-BUNDLE-STALE-MOCKS-GAPS.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-13-LOW-FEATURE-FIXTURE-BUNDLE-STALE-MOCKS-GAPS.md \
         projects/galaxy_game/tasks/active/2026-08-13-LOW-FEATURE-FIXTURE-BUNDLE-STALE-MOCKS-GAPS.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-13-LOW-FEATURE-FIXTURE-BUNDLE-STALE-MOCKS-GAPS.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-TEST-FIXTURE-BUNDLE.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Fixture Bundle — 9 Stale/Incomplete Test Fixtures
**Status**: BACKLOG
**Priority**: LOW
**Type**: test-fixture-bundle
**Created**: 2026-08-13
**Last Updated**: 2026-08-14

---

## Context
Bundle of 9 low-priority test fixture gaps and stale expectations across the spec suite. None are blockers; all are maintenance items to align tests with current implementation. Items span multiple files — best handled as a single focused session rather than scattered individual fixes.

**Relevant Architecture Docs**:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules

---

## Problem Statement
The RSpec suite contains 9 items where test fixtures, expectations, or setup don't match current implementation. These are all test-side issues (not code bugs), but they cause failures or misleading assertions that obscure real problems.

**Current behavior**: Tests fail with fixture gaps, stale expectations, or hook ordering issues
**Expected behavior**: All 9 items resolved — tests pass with correct fixtures/expectations

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: These are test-side fixes, not code bugs**
- ❌ Wrong: Modify application code to match stale test expectations
- ✅ Right: Fix the fixture/test to match current implementation
- Why: The codebase is the source of truth; tests must align to it

⚠️ **GOTCHA 2: Item #9 (HarvesterCompletionJob) is a fixture/seeding gap, not a real bug**
- ❌ Wrong: Modify HarvesterCompletionJob or job queue logic
- ✅ Right: Fix test setup — ensure harvester/order fixtures are seeded correctly and `travel_to` block advances the job queue before assertion
- Why: The job works in production; the test just doesn't set up the scenario correctly

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Section |
|---|---|---|
| `spec/controllers/admin/catalog_controller_spec.rb` | Item #1 — missing category assignments | line ~44 |
| `spec/models/celestial_bodies/material_spec.rb` | Items #2/#3 — missing boiling/melting_point fixtures | lines ~43, ~59 |
| `spec/models/geosphere_concern_spec.rb` | Item #4 — same root cause as #2/#3 | line ~344 |
| `spec/models/material_management_concern_spec.rb` | Item #5 — stale expectation for `"Fe"` vs `"iron"` | line ~193 |
| `spec/models/base_unit_spec.rb` | Item #6 — stale `source_unit:` keyword argument | line ~236 |
| `spec/services/game_data_generator_spec.rb` | Item #7 — test/hook ordering issue | line ~13 |
| `spec/services/lookup/material_lookup_service_spec.rb` | Item #8 — mock expectation mismatch | line ~248 |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `spec/factories/material.rb` | Material factory structure for items #2/#3 |
| `app/models/concerns/geosphere_concern.rb` | physical_state implementation for item #4 |
| `app/services/lookup/material_lookup_service.rb` | Normalization logic for item #5 |

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/current/2026-08-13-LOW-FEATURE-FIXTURE-BUNDLE-STALE-MOCKS-GAPS.md \
       projects/galaxy_game/tasks/active/2026-08-13-LOW-FEATURE-FIXTURE-BUNDLE-STALE-MOCKS-GAPS.md
```

Then open the moved file and change: `status: backlog` → `status: active`

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-13-LOW-FEATURE-FIXTURE-BUNDLE-STALE-MOCKS-GAPS.md"
```

**Paste the output in chat before proceeding.** Expected: exactly one result at `active/`.

### Step 1 — Fix Items #1 (catalog_controller_spec.rb:44)
- **Issue**: Controller's `#index` only assigns `categories: ['crafts']`. Test expects `['units', 'modules', 'crafts']`.
- **Root cause**: Controller is incomplete (missing units/modules category logic), not a test bug.
- **Fix**: Update test expectation to match current controller behavior (`['crafts']`) OR add the missing category logic to the controller if it's needed now.

### Step 2 — Fix Items #2/#3/#4 (material/geosphere fixtures)
- **Issue**: Missing `boiling_point` and `melting_point` in material factory causes `state_at(2000, 1.0)` to return `"gas"` instead of expected `"liquid"`.
- **Fix**: Add `boiling_point` and `melting_point` to the material factory/fixture with values that make sense for test materials (e.g., boiling_point: 3000, melting_point: 1500 for a solid-at-room-temp material).

### Step 3 — Fix Item #5 (material_management_concern_spec.rb:193)
- **Issue**: Test expects `remove_material` to receive raw symbol `"Fe"`, but implementation correctly normalizes via `MaterialLookupService` to `"iron"`.
- **Fix**: Update test expectation from `"Fe"` to `"iron"`.

### Step 4 — Fix Item #6 (base_unit_spec.rb:236)
- **Issue**: Test calls `surface_store.add_pile(material_name:, amount:, source_unit:)` but `Storage::SurfaceStorage#add_pile` doesn't accept a `source_unit:` keyword.
- **Fix**: Remove `source_unit:` from the test call.

### Step 5 — Fix Item #7 (game_data_generator_spec.rb:13)
- **Issue**: Test asserts a file exists at `tmp/generated_item.json`, but the `after` hook runs `FileUtils.rm_rf(Rails.root.join('tmp'))` which deletes it before the assertion can verify.
- **Fix**: Capture the file content in the test before the `after` hook runs (e.g., read the file in the test body, store in a variable, then assert on the variable).

### Step 6 — Fix Item #8 (material_lookup_service_spec.rb:248)
- **Issue**: Mock expects `Rails.logger.error` to receive message matching `/Invalid JSON in file:/`, but actual code logs `"Error loading #{file}: #{e.message}"`.
- **Fix**: Update mock expectation to match real log format: `/Error loading .*: /`.

### Step 7 — Fix Item #9 (HarvesterCompletionJob)
- **Issue**: `expect(...).to be > 0, got 0.0` on oxygen after job completion.
- **Root cause**: Test likely doesn't seed the harvester/order fixtures correctly, or the `travel_to` block doesn't properly advance the job queue for the job to actually run before the assertion.
- **Fix**: Verify fixture seeding and job queue advancement in the test setup (ensure `travel_to` advances past the job's scheduled execution time).

### Step 8 — Verify

> CRITICAL EXECUTION MANDATE: All RSpec commands must use the Docker wrapper below.
> The container working directory is already /home/galaxy_game — do NOT add cd /home/galaxy_game.
> Never run bare local test commands. Never fabricate test results. Actually run the specs.

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [SPEC_PATHS] 2>&1 | tail -20'
```

Expected result: All affected spec files pass with 0 failures.

---

## Acceptance Criteria
- [ ] All 9 items resolved or documented as known limitations
- [ ] No regressions in existing passing specs
- [ ] Test suite remains green after fixes
- [ ] Each fix is minimal — only touch the specific fixture/expectation that needs changing

---

## Stop Conditions — escalate to user immediately if:
- Fix causes new failures in specs you did not touch
- Same failure persists after two attempts
- Root cause is in a shared concern, base class, or factory used across many specs
- Any architectural decision is required (e.g., should the controller actually support multiple categories?)

---

## Dependencies
**Blocked by**: none
**Blocks**: None — this is standalone test maintenance
**Related tasks**: None

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
