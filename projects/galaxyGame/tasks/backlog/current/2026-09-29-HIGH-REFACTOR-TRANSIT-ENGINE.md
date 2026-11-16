---
status: backlog
priority: HIGH
type: refactor
system_domain: CONTROLLERS | UNITS | OTHER
mvp_alignment: SPEC_HEALTH | OTHER
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [x] All Step 0-N instructions are clear and actionable (not vague)
- [x] Synthesis report template is provided (copy/paste ready, not as example)
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-09-29-HIGH-REFACTOR-TRANSIT-ENGINE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-09-29-HIGH-REFACTOR-TRANSIT-ENGINE.md \
         projects/galaxy_game/tasks/active/2026-09-29-HIGH-REFACTOR-TRANSIT-ENGINE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-29-HIGH-REFACTOR-TRANSIT-ENGINE.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.

---

# TASK: Transit Engine Refactor and Spec Cleanup
**Status**: BACKLOG
**Priority**: HIGH
**Type**: refactor
**Created**: 2026-09-29
**Last Updated**: 2026-09-29

---

## Local Worker Triage Report (Optional — for backlog review only)
- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS — Uses standard RSpec docker wrapper execution.
- **MVP Alignment**: VALID — Directly unblocks 8 failing specs currently preventing a clean test suite run.
- **MVP Impact Note**: Cleans up legacy transit engine logic and resolves spec regressions.
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Primary local worker for routine code refactoring and spec fixes.
**Local attempts before cloud**: N/A
**Supervision Level**: Watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/path/to/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/path/to/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context
The `Mission::TransitEngine` computes interplanetary transit windows using two parallel paths:
- **Hardcoded constants** (`compute_transit_days`) — returns fixed values (Earth→Luna=7, Earth→Venus=146, etc.)
- **Dynamic Hohmann computation** (`compute_transit_days_dynamic`) — calculates transfer time via `π * sqrt((r₁+r₂)³ / 8μ)` from orbital data

The 8 failing specs assert the hardcoded constant values, but when the test DB contains orbital data for celestial bodies, `calculate_transfer_window` takes the dynamic path which produces different numbers. This is a **contract mismatch**, not outdated method calls.

**Repair path**: update spec assertions to match dynamic computation output using spec-local deterministic orbital fixtures and a fixed launch date. Do not remove or strip fixture data to select the legacy route-table fallback.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Direct Database Modifications
- ❌ Wrong: Modifying production schemas or running migrations without checking schema version rules.
- ✅ Right: Rely strictly on existing model methods and services for transit calculations.
- Why: Transit parameters are tightly coupled with active simulation loops.

⚠️️ **GOTCHA 2**: Test Execution Environment
- ❌ Wrong: Running local test commands directly on the host machine.
- ✅ Right: Always use the official Docker RSpec wrapper execution string.
- Why: Ensures clean test database state and proper environment configuration.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: 2026-09-29-HIGH-REFACTOR-TRANSIT-ENGINE.md
**Status**: backlog → active
**Date**: 2026-09-29

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `galaxy_game/app/services/mission/transit_engine.rb` | Transit calculation logic | not started |
| `galaxy_game/spec/services/mission/transit_engine_spec.rb` | Transit specs | not started |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
All transit engine specs pass successfully with zero failures.

### Critical Gotchas I Will Avoid
- ❌ Running bare local test commands — instead ✅ Use Docker RSpec wrapper.
- ❌ Modifying TransitEngine to match spec constants — the dynamic computation is correct; update specs to exercise the dynamic calculation using spec-local deterministic orbital fixtures and a fixed launch date.

---

**SYNTHESIS COMPLETE.** Ready to proceed with implementation.
```

---

## Problem Statement
The test suite has 8 failing specs in `galaxy_game/spec/services/mission/transit_engine_spec.rb`. The root cause is a **contract mismatch**: the spec asserts hardcoded transit-day constants (7, 146, 1388, 259), but when the test DB contains orbital data for celestial bodies, `Mission::TransitEngine#calculate_transfer_window` takes the dynamic Hohmann computation path which produces different values. This cascades into incorrect `arrival_date`, wrong `has_arrived?` results, and wrong `days_remaining`.

**Current behavior**: 8 failing specs in transit-related test suites.
**Expected behavior**: 100% passing test suite for transit components.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/app/services/mission/transit_engine.rb` | Transit computation logic | `compute_transit_days_dynamic`, `compute_transit_days`, `calculate_transfer_window` |
| `galaxy_game/spec/services/mission/transit_engine_spec.rb` | Transit specs | `.calculate_transfer_window`, `.has_arrived?`, `.days_remaining` blocks |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/app/models/celestial_bodies/celestial_body.rb` | Understand orbital_elements schema (JSONB column) |
| `spec/factories/celestial_bodies.rb` | Check if test DB seeds orbital data for bodies |

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/current/2026-09-29-HIGH-REFACTOR-TRANSIT-ENGINE.md \
       projects/galaxy_game/tasks/active/2026-09-29-HIGH-REFACTOR-TRANSIT-ENGINE.md
```

Then open the moved file and change: `status: backlog` → `status: active`
Verify with:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-09-29-HIGH-REFACTOR-TRANSIT-ENGINE.md"
```
Expected: exactly one result at the `active/` path.

### Step 1 — Inspect failing specs and confirm root cause

Run the transit specs to see which assertions fail:
```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/mission/transit_engine_spec.rb 2>&1 | tail -30'
```

Confirm that failures are all about `transit_days` values diverging from hardcoded constants (not missing methods or nil errors).

### Step 2 — Apply repair: update spec assertions to match dynamic computation

Dynamic orbital calculation via `compute_transit_days_dynamic` is the canonical transit-planning contract when valid orbital data exists. Legacy route-name duration tables (`compute_transit_days`) are not the normal planning contract.

Tests must validate the dynamic path using deterministic orbital fixtures and a fixed launch/simulation time. **The spec must create explicit origin and destination celestial-body fixtures with documented valid `orbital_elements` and a fixed launch date — do not depend on ambient test-DB bodies or seeded identifiers without locally controlled orbital inputs.**

```ruby
# Before (spec asserts hardcoded constant):
it 'returns correct window for Earth→Venus' do
  result = described_class.calculate_transfer_window('EARTH-01', 'VENUS-01', launch_date)
  expect(result[:transit_days]).to eq(146)  # hardcoded constant
end

# After (spec asserts dynamically derived value from local fixtures):
it 'returns correct window for Earth→Venus' do
  # Create explicit local fixtures with documented orbital_elements
  let(:earth_orbitals) { { semi_major_axis: 149_598_000_000, eccentricity: 0.0167, inclination: 0.0, mean_anomaly: 100.0, orbital_period_days: 365.25 } }
  let(:venus_orbitals) { { semi_major_axis: 108_200_000_000, eccentricity: 0.0068, inclination: 3.39, mean_anomaly: 50.0, orbital_period_days: 224.7 } }
  let(:launch_date) { Date.new(2030, 1, 15) }

  result = described_class.calculate_transfer_window('EARTH-01', 'VENUS-01', launch_date)
  # Derive expected transit_days from the fixture values: ceil of Hohmann computation (π * sqrt((r₁+r₂)³ / 8μ))
  # plus any phase/synodic wait if phase angle > 15° from optimal
  expect(result[:transit_days]).to eq(derived_expected_transit_days)  # replace with computed value
end
```

**Rounding behavior**: `compute_transit_days_dynamic` uses `.ceil` on the final transfer-days value. Spec assertions must match this rounding exactly.

**Downstream helper guidance**: `has_arrived?` and `days_remaining` examples must derive boundary values from the returned/explicit transit record's `transit_days`, not hardcode legacy route values:

```ruby
# has_arrived? — test at duration, duration-1, before arrival, after arrival
let(:transit_record) { described_class.schedule_departure('test_craft', 'EARTH-01', 'VENUS-01', launch_date) }
it 'returns true when sim_day >= transit_days' do
  expect(described_class.has_arrived?(transit_record, transit_record[:transit_days])).to be true
end
it 'returns false when sim_day < transit_days' do
  expect(described_class.has_arrived?(transit_record, transit_record[:transit_days] - 1)).to be false
end

# days_remaining — test at duration, before arrival, after arrival
it 'returns zero on arrival day' do
  expect(described_class.days_remaining(transit_record, transit_record[:transit_days])).to eq(0)
end
it 'returns positive days when before arrival' do
  expect(described_class.days_remaining(transit_record, transit_record[:transit_days] - 1)).to be > 0
end
it 'returns negative days when overdue' do
  expect(described_class.days_remaining(transit_record, transit_record[:transit_days] + 1)).to be < 0
end
```

**Bounded missing-orbital-data statement**: When orbital data is missing, `fallback_transfer_window` delegates to `compute_transit_days`, a fixed route-name duration table — not a physics-based generalized estimator. Improving/replacing that fallback is explicitly out of scope for this task and requires a separate follow-up.

**Out of scope for this task**:
- No wormhole or interstellar behavior
- No multi-leg route system
- No high-fidelity trajectory solver (eccentricity-aware Kepler propagation)
- No broad data migration of orbital_elements fixtures
- No unrelated RSpec fixes

### Step 3 — Verify

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/mission/transit_engine_spec.rb --order defined 2>&1 | tail -5'
```

Expected result: `X examples, 0 failures` (where X is the full count from the spec file).

---

## Acceptance Criteria
- [ ] All 8 failing transit specs pass successfully
- [ ] Isolation run: 0 failures
- [ ] No regressions in related model specs
- [ ] Full suite run completed and logged (human runs overnight — agent does not trigger)
- [ ] Deterministic fixture inputs: specs create explicit origin/destination celestial-body fixtures with documented valid `orbital_elements` and a fixed launch date; stated rounding behavior (`.ceil` on transit_days)
- [ ] Dynamic calculation result is used for transit planning: `calculate_transfer_window` returns values from `compute_transit_days_dynamic`, not hardcoded constants, when valid orbital data exists
- [ ] `arrival_date = departure_date + transit_days`: verified in spec assertions for all tested routes
- [ ] `schedule_departure` returns a transit-record hash containing `transit_days`, `departure_date`, and `arrival_date` from the same calculated window path (no ActiveRecord persistence claimed)
- [ ] `has_arrived?` and `days_remaining` derive boundary values from the returned/explicit transit record's `transit_days`, not hardcoded legacy route values; tested at duration, duration minus one, before arrival, and after arrival as applicable
- [ ] No invented duration numbers: expected `transit_days` values are derived from the selected local fixtures, accounting for `.ceil` and any phase/synodic wait

---

## Stop Conditions
- Fix causes new failures in specs you did not touch
- Same failure persists after two attempts
- Root cause is in a shared concern, base class, or factory used across many specs
- A database migration is needed that wasn't anticipated
- Any architectural decision is required
- Fix requires changing more files than the task specifies

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add galaxy_game/app/services/mission/transit_engine.rb galaxy_game/spec/services/mission/transit_engine_spec.rb
git commit -m "refactor: transit_engine — resolve 8 failing specs by aligning spec assertions with dynamic Hohmann computation"
git push
```

**Task file move on completion:**
```bash
git mv projects/galaxy_game/tasks/active/2026-09-29-HIGH-REFACTOR-TRANSIT-ENGINE.md \
       projects/galaxy_game/tasks/completed/2026-09/2026-09-29-HIGH-REFACTOR-TRANSIT-ENGINE.md
git commit -m "chore: move 2026-09-29-HIGH-REFACTOR-TRANSIT-ENGINE.md to completed/"
```

---

## Documentation
- [ ] No doc changes needed
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: none
**Blocks**: none
**Related tasks**: none

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures
**Evidence basis:** [direct verification / review of pasted evidence / reported by agent / human assertion] — [one-line source or note when not direct verification]

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

HANDOFF SUMMARY: transit_engine.rb refactored | transit specs passing | backlog cleared