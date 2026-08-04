---
status: backlog
priority: MEDIUM
type: feature
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
created: 2026-08-03
updated: 2026-08-03
estimated_effort: 3-4 hours
blocker_for: []
---

# Task: Per-Location Market Fee Management — AI Manager Sets Settlement Fees

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/current/2026-08-03-MEDIUM-FEATURE-PER-LOCATION-MARKET-FEE-MANAGEMENT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv docs/new_agent/projects/galaxy_game/tasks/backlog/current/2026-08-03-MEDIUM-FEATURE-PER-LOCATION-MARKET-FEE-MANAGEMENT.md \
         docs/new_agent/projects/galaxy_game/tasks/active/2026-08-03-MEDIUM-FEATURE-PER-LOCATION-MARKET-FEE-MANAGEMENT.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find docs/new_agent/projects/galaxy_game/tasks -name "2026-08-03-MEDIUM-FEATURE-PER-LOCATION-MARKET-FEE-MANAGEMENT.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.
```

## Prerequisites

- Read `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- Read `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- Understand that fees are per-location (set by settlement/orbital station owner), not universal defaults

## Context

**Problem**: The AI Manager needs the ability to set market fees (broker fee, transaction fee, order duration) on a per-location basis. Each settlement or orbital station should be able to configure its own fees based on financial strategy and local conditions — not use universal defaults from `economic_parameters.yml`.

**Why this matters**: 
- A lunar settlement might want low fees to attract trade
- An orbital depot might charge premium fees for convenience
- The AI Manager (managing settlements) needs to adjust fees dynamically based on financial need
- This enables realistic economic simulation where different locations have different fee structures

**Supersedes**: `2026-04-16-MEDIUM-DATA-ECONOMIC-PARAMETERS-MARKET-FEES.md` (in `superseded/`) — that task incorrectly assumed universal defaults in global config. This task correctly frames fees as per-location, managed by the AI Manager.

## Architecture Gotchas

1. **No universal defaults**: Fees are NOT in `economic_parameters.yml`. Each settlement/orbital station stores its own fee configuration.

2. **AI Manager role**: The AI Manager can read/set fees on settlements it manages (Luna settlements, orbital stations). This is a management capability, not a hardcoded system behavior.

3. **Existing model to extend**: `Settlement::BaseSettlement` and `Settlement::OrbitalSettlement` models need fee fields added. Check if any existing model already has fee-related fields before adding new ones.

4. **Market::TransactionFee model exists**: A generic fee calculator exists at `app/models/market/transaction_fee.rb` with `percentage`/`fixed` types and a `calculate(amount)` method. This can be reused as the calculation engine — the per-location config just needs to reference which fee type and value to use.

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Section |
|---|---|---|
| `app/models/settlement/base_settlement.rb` | Add fee configuration fields | New fields for broker_fee, transaction_fee, order_duration |
| `app/models/settlement/orbital_settlement.rb` | Add fee configuration fields (if not inherited) | Same as above |
| `app/services/ai_manager/logistics_coordinator.rb` | Add method to set fees on managed settlements | New `set_location_fees` method |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/models/market/transaction_fee.rb` | Existing fee calculator — reuse for calculation logic |
| `galaxy_game/config/economic_parameters.yml` | Check existing economic config structure (do NOT add fees here) |
| `app/services/ai_manager/universal_docking_service.rb` | Understand how docking transactions work currently |

## Implementation Steps

### Step 0: Move Task to Active & Verify Synthesis
**PREREQUISITE — Do NOT skip:**
1. Move task from `backlog/current/` → `active/`
2. Update YAML header: `status: backlog` → `status: active`
3. Commit move before writing any code
4. Complete Synthesis Report below (document current state)

### Step 1: Synthesis Report — Document Current State

```bash
# Check existing settlement models for fee-related fields
grep -n "fee\|commission\|broker" galaxy_game/app/models/settlement/base_settlement.rb galaxy_game/app/models/settlement/orbital_settlement.rb

# Check if TransactionFee model is currently used anywhere
grep -rn "TransactionFee" galaxy_game/app/ galaxy_game/spec/

# Check existing economic_parameters.yml structure
cat galaxy_game/config/economic_parameters.yml | head -60
```

Document in the task file:
```
## Synthesis Findings

### Existing fee fields on settlement models
[What fields already exist, if any]

### TransactionFee model usage
[Where it's used, how to integrate]

### Proposed fee fields for BaseSettlement/OrbitalSettlement
[broker_fee_type (percentage/fixed), broker_fee_value, transaction_fee_type, transaction_fee_value, order_duration_min, order_duration_max]
```

### Step 2: Add Fee Fields to Settlement Models

Add fee configuration fields to `BaseSettlement` (and `OrbitalSettlement` if not inherited):

```ruby
# In app/models/settlement/base_settlement.rb or as a concern

# Fee configuration — per-location, set by AI Manager or owner
attr_accessor :broker_fee_type        # 'percentage' or 'fixed'
attr_accessor :broker_fee_value       # numeric value (e.g., 5.0 for 5%, or 100.0 for $100)
attr_accessor :transaction_fee_type   # 'percentage' or 'fixed'
attr_accessor :transaction_fee_value  # numeric value
attr_accessor :order_duration_min     # minimum order duration in hours
attr_accessor :order_duration_max     # maximum order duration in hours

# Method to calculate broker fee for a given amount
def calculate_broker_fee(amount)
  return 0.0 unless broker_fee_type && broker_fee_value
  
  case broker_fee_type
  when 'percentage'
    (amount * broker_fee_value / 100.0).round(2)
  when 'fixed'
    broker_fee_value.to_f
  else
    0.0
  end
end

# Method to calculate transaction fee for a given amount
def calculate_transaction_fee(amount)
  return 0.0 unless transaction_fee_type && transaction_fee_value
  
  case transaction_fee_type
  when 'percentage'
    (amount * transaction_fee_value / 100.0).round(2)
  when 'fixed'
    transaction_fee_value.to_f
  else
    0.0
  end
end

# Default fee configuration (can be overridden by AI Manager)
def default_fee_configuration
  {
    broker_fee_type: 'percentage',
    broker_fee_value: 5.0,
    transaction_fee_type: 'percentage',
    transaction_fee_value: 2.0,
    order_duration_min: 1,
    order_duration_max: 72
  }
end
```

### Step 3: Add AI Manager Fee Management Method

Add to `AIManager::LogisticsCoordinator`:

```ruby
def set_location_fees(settlement, fee_config)
  # Validate fee configuration
  valid_types = ['percentage', 'fixed']
  return false unless valid_types.include?(fee_config[:broker_fee_type])
  return false unless valid_types.include?(fee_config[:transaction_fee_type])
  
  # Apply fees to settlement
  settlement.update!(
    broker_fee_type: fee_config[:broker_fee_type],
    broker_fee_value: fee_config[:broker_fee_value],
    transaction_fee_type: fee_config[:transaction_fee_type],
    transaction_fee_value: fee_config[:transaction_fee_value],
    order_duration_min: fee_config[:order_duration_min],
    order_duration_max: fee_config[:order_duration_max]
  )
  
  Rails.logger.info("AIManager::LogisticsCoordinator: fees set for #{settlement.name}")
  true
end

def get_location_fees(settlement)
  settlement.attributes.slice(
    'broker_fee_type', 'broker_fee_value',
    'transaction_fee_type', 'transaction_fee_value',
    'order_duration_min', 'order_duration_max'
  )
end
```

### Step 4: Integrate with Docking Transactions

Update `UniversalDockingService` to use per-location fees when processing docking transactions. Check how it currently handles fees and replace any hardcoded values with calls to `settlement.calculate_broker_fee(amount)` and `settlement.calculate_transaction_fee(amount)`.

### Step 5: Write Tests

Create `spec/services/ai_manager/per_location_fees_spec.rb`:

```ruby
describe 'Per-Location Market Fee Management' do
  let(:luna_settlement) { create(:base_settlement, name: 'Lunar Base') }
  let(:orbital_station) { create(:orbital_settlement, name: 'L1 Depot') }
  
  describe '#calculate_broker_fee' do
    it 'calculates percentage-based broker fee' do
      luna_settlement.broker_fee_type = 'percentage'
      luna_settlement.broker_fee_value = 5.0
      
      expect(luna_settlement.calculate_broker_fee(1000)).to eq(50.0)
    end
    
    it 'calculates fixed broker fee' do
      luna_settlement.broker_fee_type = 'fixed'
      luna_settlement.broker_fee_value = 100.0
      
      expect(luna_settlement.calculate_broker_fee(1000)).to eq(100.0)
    end
    
    it 'returns 0 for nil fee configuration' do
      luna_settlement.broker_fee_type = nil
      luna_settlement.broker_fee_value = nil
      
      expect(luna_settlement.calculate_broker_fee(1000)).to eq(0.0)
    end
  end
  
  describe '#calculate_transaction_fee' do
    it 'calculates percentage-based transaction fee' do
      luna_settlement.transaction_fee_type = 'percentage'
      luna_settlement.transaction_fee_value = 2.0
      
      expect(luna_settlement.calculate_transaction_fee(1000)).to eq(20.0)
    end
    
    it 'calculates fixed transaction fee' do
      luna_settlement.transaction_fee_type = 'fixed'
      luna_settlement.transaction_fee_value = 50.0
      
      expect(luna_settlement.calculate_transaction_fee(1000)).to eq(50.0)
    end
  end
  
  describe '#default_fee_configuration' do
    it 'returns sensible defaults' do
      config = luna_settlement.default_fee_configuration
      
      expect(config[:broker_fee_type]).to eq('percentage')
      expect(config[:broker_fee_value]).to eq(5.0)
      expect(config[:transaction_fee_type]).to eq('percentage')
      expect(config[:transaction_fee_value]).to eq(2.0)
      expect(config[:order_duration_min]).to eq(1)
      expect(config[:order_duration_max]).to eq(72)
    end
  end
  
  describe 'AIManager::LogisticsCoordinator#set_location_fees' do
    let(:coordinator) { AIManager::LogisticsCoordinator.new }
    
    it 'sets fees on a settlement' do
      fee_config = {
        broker_fee_type: 'percentage',
        broker_fee_value: 3.0,
        transaction_fee_type: 'fixed',
        transaction_fee_value: 75.0,
        order_duration_min: 2,
        order_duration_max: 48
      }
      
      expect(coordinator.set_location_fees(luna_settlement, fee_config)).to be true
      luna_settlement.reload
      
      expect(luna_settlement.broker_fee_value).to eq(3.0)
      expect(luna_settlement.transaction_fee_value).to eq(75.0)
    end
    
    it 'rejects invalid fee types' do
      bad_config = { broker_fee_type: 'invalid', broker_fee_value: 5.0 }
      
      expect(coordinator.set_location_fees(luna_settlement, bad_config)).to be false
    end
  end
  
  describe 'Per-location fee variation' do
    it 'allows different fees for different settlements' do
      luna_settlement.broker_fee_type = 'percentage'
      luna_settlement.broker_fee_value = 3.0
      
      orbital_station.broker_fee_type = 'fixed'
      orbital_station.broker_fee_value = 200.0
      
      expect(luna_settlement.calculate_broker_fee(1000)).to eq(30.0)
      expect(orbital_station.calculate_broker_fee(1000)).to eq(200.0)
    end
  end
end
```

### Step 6: Run Tests

```bash
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rspec \
  spec/services/ai_manager/per_location_fees_spec.rb \
  --format documentation
```

Expected: All tests pass (0 failures)

## Acceptance Criteria
- [ ] `BaseSettlement` has fee configuration fields (broker_fee_type, broker_fee_value, transaction_fee_type, transaction_fee_value, order_duration_min, order_duration_max)
- [ ] `OrbitalSettlement` inherits or has its own fee fields
- [ ] `calculate_broker_fee` and `calculate_transaction_fee` methods work correctly for both percentage and fixed types
- [ ] `default_fee_configuration` returns sensible defaults
- [ ] `AIManager::LogisticsCoordinator#set_location_fees` can set fees on managed settlements
- [ ] `AIManager::LogisticsCoordinator#get_location_fees` can read current fees
- [ ] `UniversalDockingService` uses per-location fees (not hardcoded/universal values)
- [ ] Tests pass (RSpec) — 0 failures
- [ ] No changes to `economic_parameters.yml` for fee defaults

## Stop Conditions — escalate immediately if:
- Settlement models already have fee fields with different naming — report exact field names
- `UniversalDockingService` has no fee logic at all — report what it currently does
- Fee calculation conflicts with existing `Market::TransactionFee` model — report how to integrate

## Dependencies
**Blocked by**: none
**Blocks**: Realistic economic simulation across multiple locations
**Supersedes**: `2026-04-16-MEDIUM-DATA-ECONOMIC-PARAMETERS-MARKET-FEES.md` (in `superseded/`) — that task incorrectly assumed universal defaults

## Completion Report
**Completed by**:
**Completion date**:
**Final test result**:

### What was changed
### Issues discovered
### Follow-up tasks needed
### Lessons learned

## Handoff Summary
HANDOFF SUMMARY: Per-location market fee management implemented | AI Manager can set fees per settlement | UniversalDockingService uses location-specific fees | 0 failures
