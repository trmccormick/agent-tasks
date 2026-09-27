---
status: backlog
priority: HIGH
type: feature
system_domain: AI_MANAGER
mvp_alignment: SOURCING_HIERARCHY
local_worker_safe: true
---

# TASK: Mission Planner — Tier 3 Sourcing with Connectivity Gate
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-07-15
**Last Updated**: 2026-07-15

---

## Context
This task wires the Mission Planner's three-tier sourcing hierarchy to use `SolarSystem#connectivity_status` as a gate for Tier 3 (Network Import). The architecture is already documented in `planner.md`. This depends on Task 1 (wormhole connectivity status) being completed first.

**Architecture docs** (read before starting):
- `docs/architecture/services/ai_manager/planner.md` — three-tier sourcing hierarchy, orphaned system behavior
- `docs/architecture/services/ai_manager/AI_PRIORITY_SYSTEM.md` — priority mapping context
- `docs/new_agent/projects/galaxy_game/tasks/backlog/phase5+/2026-07-15-HIGH-FEATURE-WORMHOLE-CONNECTIVITY-STATUS.md` — prerequisite task

> Do not create any documentation during this task.
> Flag any gaps in your completion report.

---

## Problem Statement
`MissionPlannerService` has no tier-based sourcing logic. Every mission requirement must be resolved through the three-tier check, but Tier 3 (Network Import from Sol/Earth) is gated by `SolarSystem#connectivity_status`. If a system is orphaned, Tier 3 is skipped entirely and the project status becomes `BLOCKED: RESOURCE_UNAVAILABLE` if Tiers 1 and 2 also fail.

**Current behavior**: No sourcing hierarchy in MissionPlannerService; no connectivity gate.
**Expected behavior**: Every resource request flows through Tier 1 → Tier 2 → Tier 3, with Tier 3 gated by `connectivity_status != 'orphaned'`.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/ai_manager/mission_planner_service.rb` | Add three-tier sourcing logic | new method + integrate into existing flow |
| `spec/services/ai_manager/mission_planner_service_spec.rb` | Add tier sourcing tests | new examples |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/models/solar_system.rb` | `connectivity_status` enum (from Task 1) |
| `app/models/settlement/base_settlement.rb` | Local ISRU resources |
| `app/services/ai_manager/wormhole_navigator.rb` | Path validation for Tier 2 routing |

### Integration
- [x] MissionPlannerService needs three-tier sourcing method that gates Tier 3 on connectivity_status

---

## Implementation Steps

> Follow these steps exactly in order.

### Step 1 — Read MissionPlannerService

Open `app/services/ai_manager/mission_planner_service.rb` and understand its current structure:
- What methods exist?
- How does it currently resolve resource requests?
- Where should the tier sourcing method be added?

### Step 2 — Add three-tier sourcing method

In `MissionPlannerService`, add a new method that implements the sourcing hierarchy:

```ruby
# Resolve a resource request through the three-tier sourcing hierarchy.
# Returns a hash with :success, :source, :details keys.
def resolve_resource_request(resource_type, quantity, settlement)
  # Tier 1: Local ISRU — can the settlement harvest/process it?
  tier1_result = tier1_local_isru(resource_type, quantity, settlement)
  return tier1_result if tier1_result[:success]

  # Tier 2: System Trade — is it available at another node in-system?
  tier2_result = tier2_system_trade(resource_type, quantity, settlement)
  return tier2_result if tier2_result[:success]

  # Tier 3: Network Import — gated by connectivity_status
  tier3_result = tier3_network_import(resource_type, quantity, settlement)
  return tier3_result
end

private

def tier1_local_isru(resource_type, quantity, settlement)
  # Check if settlement has active ProcessingUnits that can produce resource_type
  # Return { success: true/false, source: 'local_isru', details: {...} }
  # Implementation: check settlement.inventory for existing stock + processing capacity
end

def tier2_system_trade(resource_type, quantity, settlement)
  # Check if another node in the same solar system has resource_type available
  # Requires active Tug (HLT) + CargoCapacity for transport
  # Return { success: true/false, source: 'system_trade', details: {...} }
  # Implementation: query other settlements/craft in same SolarSystem
end

def tier3_network_import(resource_type, quantity, settlement)
  # GATED: If solar system is orphaned, skip Tier 3 entirely
  solar_system = settlement.solar_system
  
  if solar_system.orphaned?
    return {
      success: false,
      source: 'none',
      status: 'BLOCKED: RESOURCE_UNAVAILABLE',
      details: { reason: 'system_orphaned', tier3_skipped: true }
    }
  end
  
  # If connected, attempt Sol/Earth Umbilical import
  # Return { success: true/false, source: 'network_import', details: {...} }
  # Implementation: check Earth/Sol inventory + transport availability
end
```

### Step 3 — Add survival gate for orphaned/distressed systems

In the same service, add a method that checks biological stability in distressed/orphaned systems:

```ruby
# Calculate runway hours remaining for oxygen in a settlement
def calculate_oxygen_runway_hours(settlement)
  o2_available = settlement.inventory.items.where(name: 'oxygen').sum(:amount) || 0
  population = settlement.population || 1
  
  # 0.84 kg O2 per person per day
  daily_consumption = population * 0.84
  return Float::INFINITY if daily_consumption.zero?
  
  (o2_available / daily_consumption) * 24 # hours
end

# Check survival gate — returns CRITICAL_RISK if oxygen runway is insufficient
def check_survival_gate(mission_duration_days, settlement)
  runway_hours = calculate_oxygen_runway_hours(settlement)
  required_hours = mission_duration_days * 1.2 * 24
  
  if runway_hours < required_hours
    {
      status: 'CRITICAL_RISK',
      priority_override: :isu_oxygen,
      runway_hours: runway_hours.round(2),
      required_hours: required_hours.round(2)
    }
  else
    { status: 'OK' }
  end
end
```

### Step 4 — Integrate into existing mission planning flow

Wherever `MissionPlannerService` currently makes resource decisions, call `resolve_resource_request` instead of ad-hoc checks. If the service has a method like `plan_mission` or `evaluate_project`, add the tier sourcing call there.

### Step 5 — Add service spec

In `spec/services/ai_manager/mission_planner_service_spec.rb`, add tests:

```ruby
describe '#resolve_resource_request' do
  let(:settlement) { create(:base_settlement, solar_system: sol_system) }
  let(:sol_system) { create(:solar_system, identifier: 'SOL-01', connectivity_status: 'connected') }
  
  context 'when resource available locally (Tier 1)' do
    it 'returns success with source local_isru' do
      # Set up settlement inventory with resource
      result = service.resolve_resource_request('oxygen', 100, settlement)
      expect(result[:success]).to be true
      expect(result[:source]).to eq('local_isru')
    end
  end
  
  context 'when Tier 1 and Tier 2 fail, Tier 3 succeeds' do
    it 'returns success with source network_import' do
      # No local stock, no system trade available
      result = service.resolve_resource_request('electronics', 5, settlement)
      expect(result[:success]).to be true
      expect(result[:source]).to eq('network_import')
    end
  end
  
  context 'when solar system is orphaned' do
    let(:sol_system) { create(:solar_system, identifier: 'SOL-01', connectivity_status: 'orphaned') }
    
    it 'returns BLOCKED when Tier 3 is the only option' do
      result = service.resolve_resource_request('electronics', 5, settlement)
      expect(result[:success]).to be false
      expect(result[:status]).to eq('BLOCKED: RESOURCE_UNAVAILABLE')
      expect(result[:details][:tier3_skipped]).to be true
    end
  end
end

describe '#calculate_oxygen_runway_hours' do
  it 'calculates correct runway based on inventory and population' do
    # Test with known values
  end
end

describe '#check_survival_gate' do
  it 'returns CRITICAL_RISK when runway is insufficient' do
    # Test with low oxygen, high population
  end
  
  it 'returns OK when runway is sufficient' do
    # Test with adequate oxygen
  end
end
```

### Step 6 — Verify isolation

Run the MissionPlannerService spec:
```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/mission_planner_service_spec.rb -v 2>&1'
```

Expected result: All examples pass, 0 failures.

### Step 7 — Synthesis Report (before committing)

Pause and produce this report — **do not commit until user approves**:

```
SYNTHESIS REPORT
Files modified:
  - app/services/ai_manager/mission_planner_service.rb — added resolve_resource_request + tier methods + survival gate
  - spec/services/ai_manager/mission_planner_service_spec.rb — added tier sourcing tests

Spec result:
  [paste test output summary — should be: X examples, 0 failures]

RISKS
  - MissionPlannerService may have existing resource resolution logic that conflicts
  - Settlement model may need solar_system association (verify)
  - Inventory query patterns may differ from spec assumptions

READY TO APPLY? — waiting for approval
```

---

## Testing Sequence

After Step 7 synthesis report and user approval:

1. **Isolation run**:
```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/mission_planner_service_spec.rb 2>&1'
```

Expected: X examples, 0 failures

2. **Related service specs** (ensure no regressions):
```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/ --exclude spec/services/ai_manager/mission_planner_service_spec.rb 2>&1 | tail -20'
```

Expected: no new failures

3. **Full suite** — only after steps 1 and 2 are green:
```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec > /home/galaxy_game/log/rspec_full_$(date +%s).log 2>&1'
```

Then verify:
```bash
docker exec -it web bash -c 'tail -50 /home/galaxy_game/log/rspec_full_*.log | grep -E "examples?|failures?"'
```

---

## Acceptance Criteria
- [ ] `MissionPlannerService#resolve_resource_request` implements three-tier sourcing hierarchy
- [ ] Tier 1 checks local ISRU (settlement inventory + processing capacity)
- [ ] Tier 2 checks system trade (other nodes in same SolarSystem)
- [ ] Tier 3 gates on `SolarSystem#connectivity_status != 'orphaned'`
- [ ] Orphaned systems return `BLOCKED: RESOURCE_UNAVAILABLE` when only Tier 3 would succeed
- [ ] `calculate_oxygen_runway_hours` returns correct hours based on inventory/population
- [ ] `check_survival_gate` returns `CRITICAL_RISK` with priority override when runway insufficient
- [ ] Service spec passes with 0 failures
- [ ] No regressions in `spec/services/ai_manager/` suite
- [ ] Commit message follows format: `feature: mission_planner — add three-tier sourcing hierarchy with connectivity gate`

---

## Stop Conditions — escalate to user immediately if:
- Settlement model has no solar_system association (need to verify/add)
- Inventory query patterns differ significantly from spec assumptions
- MissionPlannerService has existing resource resolution that conflicts with tier approach
- Any architectural decision required beyond what is specified here

---

## Commit Instructions
Run git commands on **host**, not inside container:
```bash
git add app/services/ai_manager/mission_planner_service.rb
git add spec/services/ai_manager/mission_planner_service_spec.rb
git commit -m "feature: mission_planner — add three-tier sourcing hierarchy with connectivity gate"
git push
```

---

## Dependencies
**Blocked by**: `2026-07-15-HIGH-FEATURE-WORMHOLE-CONNECTIVITY-STATUS.md` (Task 1)
**Blocks**: None directly
**Related tasks**: 
- Wormhole connectivity status (prerequisite task)
- Network Import sourcing validation (downstream)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `app/services/ai_manager/mission_planner_service.rb` — added resolve_resource_request (three-tier) + survival gate methods
- `spec/services/ai_manager/mission_planner_service_spec.rb` — added tier sourcing tests

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: MissionPlannerService tier3 sourcing added with connectivity gate | Orphaned systems blocked from Network Import | Ready for integration testing
