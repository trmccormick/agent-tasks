---
status: active
priority: MEDIUM
type: bug-fix
system_domain: AI_MANAGER
mvp_alignment: ISRU_PRODUCTION
local_worker_safe: true
created: 2026-08-16
updated: 2026-08-22
estimated_effort: 30-45 min (test-only fix)
blocker_for: []
depends_on: []
---

# TASK: Luna Oxygen Fixture — Fix Test Premise (Item #9)

## ✅ Diagnosis Complete (2026-08-22)

**Status:** Ready for implementation. Diagnostic research confirmed the root cause.

### Research Findings

**Source:** `projects/galaxy_game/summaries/2026-08-15-TEST-FIXTURE-BUNDLE.md` (stops at item #8)
**Item #9 reference:** 2026-08-13 handoff — *"HarvesterCompletionJob's fixture/seeding gap as the 9th item."*

**Test file:** `galaxy_game/spec/integration/ai_manager/escalation_integration_spec.rb:251`

**Failing assertion (line 261):**
```ruby
expect(settlement.inventory.current_storage_of('oxygen')).to be > 0
```

**Order resource (line ~224):** `resource: 'oxygen'`

### Root Cause

The test's premise is **physically wrong**: it treats oxygen as a directly-harvestable deposit. On Luna, there is no free O₂ to harvest. The realistic oxygen pathway is:

1. Order for `raw_regolith` (ubiquitous on Luna)
2. Escalation service routes to `:automated_harvesting` (verified at `escalation_service.rb:399`)
3. Harvester deployed with `target_material: 'processed_regolith'`
4. PVE/TEU processes regolith → oxide reduction → O₂

The test orders `resource: 'oxygen'` which doesn't exist as a harvestable resource on Luna. The correct order is `raw_regolith`, which the escalation service already routes correctly.

### Classification

**Test-side fix (fixture/premise), NOT a production code bug.**
- No changes needed to HarvesterCompletionJob
- No changes needed to EscalationService routing logic
- Only the test's order resource and assertion need correction

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/spec/integration/ai_manager/escalation_integration_spec.rb` | The failing test | `let(:oxygen_order)` line ~224; `it 'HarvesterCompletionJob fulfills order...'` lines 251–265 |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/app/services/ai_manager/escalation_service.rb` | Confirms `raw_regolith` routes to `:automated_harvesting` (line ~399) |
| `galaxy_game/app/jobs/harvester_completion_job.rb` | Confirms job adds `order.resource` to inventory (no key transformation) |
| `galaxy_game/spec/integration/ai_manager/escalation_integration_spec.rb` line 238 | The passing `processed_regolith` test — model for the correct pattern |

### Migration
- [ ] No migration needed

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

### Step 0 — Task file is already in active/ (verify only)

The task file is already at `active/`. Verify only one copy exists:

```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-16-MEDIUM-BUG-FIX-HARVESTER-COMPLETION-JOB-OXYGEN-FIXTURE.md"
```

**Paste the output in chat before proceeding.** Expected: exactly one result at the `active/` path.

### Step 1 — Read the failing test and the passing regolith test

Read `galaxy_game/spec/integration/ai_manager/escalation_integration_spec.rb`:
- The `let(:oxygen_order)` block (line ~224) — note `resource: 'oxygen'`
- The failing `it` block (lines 251–265)
- The PASSING `processed_regolith` test (line ~238) — this is the correct pattern to mirror

Confirm the exact resource key and assertion before editing.

### Step 2 — Fix the order resource and assertion

Change the order to request `raw_regolith` (a real, harvestable Luna resource) and assert on the regolith inventory key, matching the passing test at line 238.

```ruby
# before (line ~224)
let(:oxygen_order) { ... resource: 'oxygen' ... }

# after
let(:oxygen_order) { ... resource: 'raw_regolith' ... }
```

```ruby
# before (line 261)
expect(settlement.inventory.current_storage_of('oxygen')).to be > 0

# after — assert on the key the harvester actually deposits (mirror line 238)
expect(settlement.inventory.current_storage_of('raw_regolith')).to be > 0
```

> ⚠️ **Verify the exact inventory key** the harvester deposits by reading the passing test at line 238 and `harvester_completion_job.rb#add_to_settlement_inventory`. Use the SAME key that test asserts. Do NOT guess between `'raw_regolith'` and `'processed_regolith'` — read the passing test and match it exactly.

### Step 3 — Verify

> CRITICAL EXECUTION MANDATE: All RSpec commands must use the Docker wrapper below.
> The container working directory is already /home/galaxy_game — do NOT add cd /home/galaxy_game.
> Never run bare local test commands. Never fabricate test results. Actually run the specs.

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/integration/ai_manager/escalation_integration_spec.rb 2>&1 | tail -20'
```

Expected result: all examples in this spec pass, 0 failures.

### Step 4 — Synthesis Report (before committing anything)

```
SYNTHESIS REPORT
Spec: spec/integration/ai_manager/escalation_integration_spec.rb:251
Error: current_storage_of('oxygen') returned 0.0
Expected: > 0
Got: 0.0

ROOT CAUSE
Test ordered 'oxygen' as a directly-harvestable resource, which does not exist on
Luna. The escalation service routes 'raw_regolith' to :automated_harvesting
(escalation_service.rb:399), and the passing test at line 238 confirms the
regolith pathway works. The test premise was physically wrong, not a production bug.

PROPOSED FIX
Change order resource 'oxygen' → 'raw_regolith' and assert on the regolith
inventory key (matching the passing test at line 238).

RISK
Test-only change. No production code touched. No shared concerns affected.

READY TO APPLY? — waiting for approval
```

Do not commit until the user explicitly approves.

---

## Acceptance Criteria

- [ ] Order resource changed from `'oxygen'` to `'raw_regolith'`
- [ ] Assertion changed to the regolith inventory key (matching passing test at line 238)
- [ ] Isolation run: `escalation_integration_spec.rb` — 0 failures
- [ ] No regressions in related specs
- [ ] No production code modified (test-only fix)
- [ ] Full suite run completed and logged (human runs overnight — agent does not trigger)

---

## Gotchas & Traps

1. **Trap — Guessing the inventory key:**
   - Do NOT assume the key is `'raw_regolith'` vs `'processed_regolith'`
   - **Verify:** Read the PASSING test at line 238 and `harvester_completion_job.rb#add_to_settlement_inventory`, then match that exact key

2. **Trap — "Fixing" production code to make the old assertion pass:**
   - Do NOT add an `'oxygen'`/`'O2'` alias or a fake atmospheric-harvest path to satisfy the old assertion
   - That would undo prior hardening and model a physically impossible scenario
   - **Right:** Fix the test premise to use a real harvestable resource

3. **Trap — Scope creep into Mars:**
   - Mars O2 handling (MOXIE-analog, capacity-scaling escalation) is a SEPARATE design task
   - See `backlog/design/2026-08-21-DESIGN-MARS-MOXIE-ANALOG-ATMOSPHERIC-PROCESSING-UNIT.md`
   - **Do NOT** fold Mars logic into this Luna test fix

---

## Stop Conditions — escalate to user immediately if:
- The passing test at line 238 does NOT use a regolith key (diagnosis may be incomplete)
- Changing the key still leaves the assertion failing after two attempts
- The fix requires touching production code (HarvesterCompletionJob or EscalationService)
- Any architectural decision is required

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add galaxy_game/spec/integration/ai_manager/escalation_integration_spec.rb
git commit -m "fix: escalation_integration_spec item #9 — order raw_regolith (real Luna resource) instead of non-harvestable oxygen"
git push
```

**Task file move on completion:**
```bash
git mv projects/galaxy_game/tasks/active/2026-08-16-MEDIUM-BUG-FIX-HARVESTER-COMPLETION-JOB-OXYGEN-FIXTURE.md \
       projects/galaxy_game/tasks/completed/2026-08/2026-08-16-MEDIUM-BUG-FIX-HARVESTER-COMPLETION-JOB-OXYGEN-FIXTURE.md
git add projects/galaxy_game/tasks/completed/2026-08/2026-08-16-MEDIUM-BUG-FIX-HARVESTER-COMPLETION-JOB-OXYGEN-FIXTURE.md
git commit -m "chore: move oxygen fixture task to completed/"
```

---

## Dependencies
**Blocked by**: none
**Blocks**: none
**Related tasks**: `backlog/design/2026-08-21-DESIGN-MARS-MOXIE-ANALOG-ATMOSPHERIC-PROCESSING-UNIT.md` (Mars O2 — separate, do not fold in)

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
