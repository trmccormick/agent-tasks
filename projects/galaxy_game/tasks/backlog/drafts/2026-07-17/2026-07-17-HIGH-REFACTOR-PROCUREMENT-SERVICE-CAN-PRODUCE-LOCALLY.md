---
title: "Refactor — Wire ProcurementService.can_produce_locally? to PrecursorCapabilityService"
priority: HIGH
status: backlog
phase: phase1
owner: Implementation Agent (Qwen)
type: refactor
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
created: 2026-07-17
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-17-HIGH-REFACTOR-PROCUREMENT-SERVICE-CAN-PRODUCE-LOCALLY.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-17-HIGH-REFACTOR-PROCUREMENT-SERVICE-CAN-PRODUCE-LOCALLY.md \
         projects/galaxy_game/tasks/active/2026-07-17-HIGH-REFACTOR-PROCUREMENT-SERVICE-CAN-PRODUCE-LOCALLY.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-17-HIGH-REFACTOR-PROCUREMENT-SERVICE-CAN-PRODUCE-LOCALLY.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-REFACTOR-PROCUREMENT-SERVICE-CAN-PRODUCE-LOCALLY.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# REFACTOR: Wire ProcurementService.can_produce_locally? to PrecursorCapabilityService

**Status**: DRAFT (not active — awaiting human review)  
**Priority**: HIGH  
**Type**: refactor  
**Created**: 2026-07-17  

---

## Objective

Replace the stub `ProcurementService.can_produce_locally?` with a real delegation to `PrecursorCapabilityService`. The data-driven capability service already exists and works — it's just not wired into ProcurementService.

**Verified gap (2026-07-17)**: `ProcurementService.can_produce_locally?` still returns hardcoded `true` via `settlement_has_facility?`. `PrecursorCapabilityService.can_produce_locally?` exists and queries celestial body sphere data correctly, but is never called from ProcurementService.

---

## Current State (Verified)

### The Stub — Still Broken

```ruby
# galaxy_game/app/services/ai_manager/procurement_service.rb lines 35-43
def self.can_produce_locally?(settlement, resource, amount)
  case resource
  when :oxygen
    settlement_has_facility?(settlement, :atmospheric_processor)  # ❌ stub
  when :water
    settlement_has_facility?(settlement, :isru_processor)          # ❌ stub
  when :structural_carbon
    settlement_has_facility?(settlement, :cnt_fabricator)          # ❌ stub
  else
    false
  end
end

def self.settlement_has_facility?(settlement, facility_type)
  true  # ❌ ALWAYS returns true — no actual check
end
```

### The Working Service — Already Built

```ruby
# galaxy_game/app/services/ai_manager/precursor_capability_service.rb line 20
def can_produce_locally?(resource)
  return false unless celestial_body
  local_resources.any? do |available_resource|
    available_resource.to_s.downcase == resource.to_s.downcase
  end
end

# local_resources extracts from:
# - celestial_body.atmosphere.gas_percentage() for atmospheric gases
# - celestial_body.geosphere.crust_composition for surface resources
# - celestial_body.hydrosphere.water_bodies for water
# Returns string chemical formulas: 'CO2', 'N2', 'CH4', 'H2O', 'O2', etc.
```

### Where It's Used — Already Wired in MissionPlannerService

```ruby
# galaxy_game/app/services/ai_manager/mission_planner_service.rb line 488
can_produce = @capability_service.can_produce_locally?(chemical_formula)
# ✅ This works correctly — delegates to PrecursorCapabilityService
```

### Where It's NOT Wired — ProcurementService

```ruby
# galaxy_game/app/services/ai_manager/procurement_service.rb line 10
if can_produce_locally?(settlement, resource, amount)
  produce_locally(settlement, resource, amount)
  return { status: :success, method: :local_production }
end
# ❌ This always returns true — bypasses real capability check
```

---

## Problem Statement

`ProcurementService.procure_resource?` calls `can_produce_locally?` which always returns `true`. This means procurement **always** attempts local production first, even when the settlement has no ISRU capabilities. The correct data-driven service (`PrecursorCapabilityService`) exists and is wired into `MissionPlannerService`, but ProcurementService was never updated to use it.

**Current behavior**: `can_produce_locally?` returns `true` for all inputs → procurement always tries local production first (even when impossible)
**Expected behavior**: `can_produce_locally?` delegates to `PrecursorCapabilityService.can_produce_locally?` which queries actual celestial body sphere data

---

## Implementation Requirements

### Target File
`galaxy_game/app/services/ai_manager/procurement_service.rb`

### Reference Files (READ — do not edit)
| File | Why |
|------|-----|
| `galaxy_game/app/services/ai_manager/precursor_capability_service.rb` | Working capability service to delegate to |
| `galaxy_game/app/services/ai_manager/mission_planner_service.rb` | Example of correct wiring (line 488) |

### Key Design Constraints

1. **Resource naming mismatch** — ProcurementService uses symbols (`:oxygen`, `:water`) while PrecursorCapabilityService uses string chemical formulas (`'O2'`, `'H2O'`). The delegation must handle this mapping.

2. **Settlement → celestial body mapping** — ProcurementService receives a settlement; PrecursorCapabilityService needs a celestial_body. Must resolve via `settlement.location.celestial_body`.

3. **No breaking changes to method signature** — `can_produce_locally?(settlement, resource, amount)` must keep its existing 3-parameter signature for callers.

---

## Implementation Steps

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/current/2026-07-17-HIGH-REFACTOR-PROCUREMENT-SERVICE-CAN-PRODUCE-LOCALLY.md \
       projects/galaxy_game/tasks/active/2026-07-17-HIGH-REFACTOR-PROCUREMENT-SERVICE-CAN-PRODUCE-LOCALLY.md
```

Then update YAML status: `status: backlog → status: active`

### Step 1 — Read reference implementations

Read these files to understand the existing patterns (do NOT edit):
- `galaxy_game/app/services/ai_manager/precursor_capability_service.rb` — capability service to delegate to
- `galaxy_game/app/services/ai_manager/mission_planner_service.rb` — correct wiring example at line 488

### Step 2 — Add resource name mapping

Add a constant mapping ProcurementService symbols to PrecursorCapabilityService string formulas:

```ruby
# At top of ProcurementService class, after module declaration
RESOURCE_NAME_MAP = {
  :oxygen => 'O2',
  :water => 'H2O',
  :methane => 'CH4',
  :nitrogen => 'N2',
  :co2 => 'CO2',
  :structural_carbon => 'C',
  :regolith => 'regolith'
}.freeze
```

### Step 3 — Replace can_produce_locally? with delegation

Replace the stub method:

```ruby
# BEFORE (lines 35-43):
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

# AFTER:
def self.can_produce_locally?(settlement, resource, amount)
  celestial_body = resolve_celestial_body(settlement)
  return false unless celestial_body
  
  capability_service = AIManager::PrecursorCapabilityService.new(celestial_body)
  
  # Map symbol to chemical formula
  resource_key = RESOURCE_NAME_MAP[resource] || resource.to_s
  
  capability_service.can_produce_locally?(resource_key)
end

private

def self.resolve_celestial_body(settlement)
  settlement&.location&.celestial_body
end
```

### Step 4 — Remove obsolete stub method

Delete `settlement_has_facility?` (no longer needed):

```ruby
# DELETE this method:
def self.settlement_has_facility?(settlement, facility_type)
  true
end
```

### Step 5 — Verify

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/procurement_service_spec.rb'
```

Expected: All existing tests pass (may need updates if they relied on the stub always returning true)

---

## Acceptance Criteria

- [ ] `ProcurementService.can_produce_locally?` delegates to `PrecursorCapabilityService.can_produce_locally?`
- [ ] Resource name mapping handles symbol → chemical formula conversion
- [ ] Settlement → celestial body resolution works correctly
- [ ] Method signature unchanged (3 parameters)
- [ ] `settlement_has_facility?` stub removed
- [ ] All existing ProcurementService tests pass
- [ ] No regressions in MissionPlannerService or NpcPriceCalculator

---

## Stop Conditions — escalate to user immediately if:
- PrecursorCapabilityService API has changed since last reference file was read
- Settlement model doesn't have `location.celestial_body` chain (edge case, not blocker)
- Existing ProcurementService tests fail for unrelated reasons (flag before proceeding)
- Resource name mapping is ambiguous (some symbols don't map cleanly to formulas)

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add galaxy_game/app/services/ai_manager/procurement_service.rb
git commit -m "refactor: wire ProcurementService.can_produce_locally? to PrecursorCapabilityService

- Replace stub case statement with delegation to PrecursorCapabilityService
- Add RESOURCE_NAME_MAP for symbol → chemical formula conversion
- Resolve settlement → celestial_body via location chain
- Remove obsolete settlement_has_facility? stub method
- Follows same pattern as MissionPlannerService wiring (line 488)"
git push
```

---

## Documentation
- [ ] No doc changes needed
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies

**Blocked by**: none (standalone refactor task)  
**Blocks**: None — unblocks ProcurementService from working correctly  
**Related tasks**: 
- `2026-07-11-HIGH-RESEARCH-PROCUREMENT-SERVICE-DESIGN.md` (Gemini research — superseded by actual implementation)
- `2026-07-17-HIGH-FEATURE-PRECURSOR-MISSION-PROFILE.md` (Phase 5 — independent)

---

## Completion Report Template
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]  
**Completion date**: YYYY-MM-DD  

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
HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
