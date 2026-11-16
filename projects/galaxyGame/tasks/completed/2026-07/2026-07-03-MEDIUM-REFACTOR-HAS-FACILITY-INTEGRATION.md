---
status: completed
priority: MEDIUM
type: refactor
system_domain: AI_MANAGER | MARKET
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
completed_date: 2026-07-20
---

## 🔴 BLOCKED: Design Issues Require Research

**Issue**: The proposed "After" code in this task has fundamental errors:

1. **Unit types don't exist**: `atmospheric_processor` (Luna has no atmosphere — uses `regolith_oxygen_extractor`), `isru_processor` (doesn't exist — uses `ice_processor`, `planetary_volatiles_extractor_mk1`), `cnt_fabricator` (doesn't exist)
2. **Resource naming mixed**: Code uses display names (`:oxygen`, `:water`) but backend standard is chemical formulas (`O2`, `H2O`)
3. **Function design unclear**: Why hardcode only 3 resources? Multiple units can produce oxygen (greenhouse, regolith extractor, atmospheric processor on Venus). Why is this function needed at all?

**Before proceeding**: Need to answer:
- [ ] What is the actual intent of `can_produce_locally?` — efficiency check? availability check? something else?
- [ ] Should this be generic (any unit that produces resource X) or location/unit-type specific?
- [ ] What's the canonical mapping of resources → unit types for each location (Luna/Mars/Venus/Titan)?
- [ ] Should this function even exist or should procurement query units directly by `facility_required` JSON field?

**Recommended next step**: Create a RESEARCH task to clarify the architecture before this refactor proceeds.

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/phase6+/2026-07-03-MEDIUM-REFACTOR-HAS-FACILITY-INTEGRATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv docs/new_agent/projects/galaxy_game/tasks/backlog/phase6+/2026-07-03-MEDIUM-REFACTOR-HAS-FACILITY-INTEGRATION.md \
         docs/new_agent/projects/galaxy_game/tasks/active/2026-07-03-MEDIUM-REFACTOR-HAS-FACILITY-INTEGRATION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find docs/new_agent/projects/galaxy_game/tasks -name "2026-07-03-MEDIUM-REFACTOR-HAS-FACILITY-INTEGRATION.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Replace has_facility? with direct unit checks on BaseSettlement
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: refactor
**Created**: 2026-07-03
**Phase**: phase6+ (post-precursor settlement expansion)

---

## Context

The `has_facility?` method was designed as an abstraction layer to check whether a settlement has specific facilities, but it was never implemented. It exists in two production services where it's guarded by `respond_to?`, which means lunar production pricing and local procurement are effectively disabled — they always return `false`.

**The reality**: "Facilities" ARE units installed at the settlement. There is no separate facility entity. The JSON data's `facility_required` field (e.g., `"sabatier_reactor"`, `"atmospheric_processor"`) maps directly to a unit type string.

**Why this matters for phase6+**: After the precursor setup, when ISRU becomes operational and the settlement expands, lunar production pricing and local procurement need to work correctly. They depend on `has_facility?` being implemented.

---

## Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1: "facility" is not a real entity**
- ❌ Wrong: Create a new Facility model or add `has_facility?` as a separate abstraction
- ✅ Right: Check units directly — `settlement.units.where(unit_type: facility).any?`
- Why: Facilities ARE units. The settlement already has `has_many: units`. No extra layer needed.

⚠️ **GOTCHA 2: JSON field name is misleading**
- ❌ Wrong: Rename `facility_required` to `unit_required` in JSON data (out of scope)
- ✅ Right: Keep the JSON field as-is; just interpret it correctly in code
- Why: This task is a service-level fix only. Changing JSON data affects other systems.

⚠️ **GOTCHA 3: Not every settlement needs every facility**
- ✅ Correct: The check should return `false` when no matching unit exists — this is the desired behavior
- Why: Modular settlements pick what they need. This is by design, not a bug.

---

## Problem Statement

Two production services use `has_facility?` which doesn't exist on `BaseSettlement`:

1. **NpcPriceCalculator** (line ~213) — checks if settlement has facility required for lunar production pricing
2. **ProcurementService** (lines ~30-34) — checks if settlement has ISRU capabilities for local resource production

Both guard with `respond_to?` so they silently return `false`. This means:
- Lunar production pricing always reports "not available" regardless of what units the settlement actually has
- Local procurement always reports "can't produce locally" even when the right units are installed

---

## Files Involved

### Primary Files — edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/app/services/market/npc_price_calculator.rb` | Lunar production pricing | `settlement.respond_to?(:has_facility?)` line 213 |
| `galaxy_game/app/services/ai_manager/procurement_service.rb` | Local resource procurement | `can_produce_locally?` line 27, `settlement_has_facility?` line 83 |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/app/models/settlement/base_settlement.rb` | Verify `has_many :units` association exists |
| `data/json-data/blueprints/units/production/refineries/sabatier_reactor_bp.json` | See `facility_required` field in JSON data |
| `galaxy_game/spec/services/market/npc_price_calculator_spec.rb` | Existing tests for pricing (may need updates) |

---

## Implementation Steps

### Step 1 — Verify BaseSettlement has units association

```bash
grep -n "has_many.*units" galaxy_game/app/models/settlement/base_settlement.rb
```

Confirm the association exists. If it doesn't, stop and report — this is a prerequisite.

### Step 2 — Fix NpcPriceCalculator (line 213)

**Before:**
```ruby
settlement.respond_to?(:has_facility?) && settlement.has_facility?(facility)
```

**After:**
```ruby
settlement.units.where(unit_type: facility).any?
```

### Step 3 — Fix ProcurementService (lines 27-35, 83)

**Before:**
```ruby
def self.can_produce_locally?(settlement, resource, amount)
  case resource
  when :oxygen
    settlement_has_facility?(settlement, :atmospheric_processor)
  when :water
    settlement_has_facility?(settlement, :isru_processor)
  when :structural_carbon
    settlement_has_facility?(settlement, :cnt_fabricator)
  else
    false
  end
end

def self.settlement_has_facility?(settlement, facility_type)
  # Check if settlement has required facility
  # Placeholder
  true
end
```

**After:**
```ruby
def self.can_produce_locally?(settlement, resource, amount)
  case resource
  when :oxygen
    settlement.units.where(unit_type: 'atmospheric_processor').any?
  when :water
    settlement.units.where(unit_type: 'isru_processor').any?
  when :structural_carbon
    settlement.units.where(unit_type: 'cnt_fabricator').any?
  else
    false
  end
end

def self.settlement_has_facility?(settlement, facility_type)
  settlement.units.where(unit_type: facility_type.to_s).any?
end
```

### Step 4 — Verify syntax

```bash
ruby -c galaxy_game/app/services/market/npc_price_calculator.rb 2>&1
ruby -c galaxy_game/app/services/ai_manager/procurement_service.rb 2>&1
```

Both must return: `Syntax OK`

### Step 5 — Run related specs (if any exist)

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/market/npc_price_calculator_spec.rb --format progress 2>&1 | tail -20'
```

Report results. If there are no existing specs for these methods, note that in the completion report.

### Step 6 — Commit

```bash
git add galaxy_game/app/services/market/npc_price_calculator.rb galaxy_game/app/services/ai_manager/procurement_service.rb
git commit -m "refactor: replace has_facility? with direct unit checks on BaseSettlement"
git push
```

---

## Acceptance Criteria
- [ ] `has_facility?` calls replaced with `settlement.units.where(unit_type: ...).any?` in both services
- [ ] Syntax OK for both files
- [ ] No regressions in related specs (if any exist)
- [ ] Lunar production pricing will now correctly check for installed units instead of returning false

---

## Stop Conditions — escalate to user immediately if:
- `BaseSettlement` does not have `has_many :units` association
- Fix causes new failures in specs that were previously passing
- The `facility_required` field in JSON data uses values that don't match any known unit types (report the mismatch)

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add galaxy_game/app/services/market/npc_price_calculator.rb galaxy_game/app/services/ai_manager/procurement_service.rb
git commit -m "refactor: replace has_facility? with direct unit checks on BaseSettlement"
git push
```

---

## Dependencies
**Blocked by**: none
**Blocks**: phase6+ settlement expansion (lunar production pricing, local procurement)
**Related tasks**: None currently in backlog

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
[Any new backlog items identified]

---

## Handoff Summary
HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
