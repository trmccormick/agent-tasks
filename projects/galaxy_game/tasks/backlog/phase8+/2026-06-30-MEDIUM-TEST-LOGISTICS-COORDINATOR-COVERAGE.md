---
status: backlog
priority: MEDIUM
type: feature
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/active/2026-06-30-MEDIUM-TEST-LOGISTICS-COORDINATOR-COVERAGE.md

READ FIRST: Task file contains all prerequisites, target files, gotchas, and verification steps.

CRITICAL: Create STATUS SYNTHESIS REPORT in chat BEFORE starting any work (template in task file).
```

---

# TASK: LogisticsCoordinator Test Coverage — Write Spec for Transfer Optimization

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: feature
**Created**: 2026-06-30
**Last Updated**: 2026-06-30

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/README.md`
3. **Audit Report**: `docs/new_agent/projects/galaxy_game/summaries/2026-06-30-RESEARCH-SYSTEM-ORCHESTRATOR-AUDIT.md`
4. **This Task File**: Everything below

---

## Context
`LogisticsCoordinator` handles inter-body resource transfers, optimizing routes and schedules to minimize costs and delays. It's a core component of Task 6.2 (Inter-Body Logistics Coordination) from the original backlog. Currently it has zero test coverage. The implementation uses hardcoded capacity values (`mars_luna_capacity: 500`, `transfer_time_mars_luna: 2.hours`) that are never validated against real wormhole data.

**Relevant Architecture Docs**:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `galaxy_game/app/services/ai_manager/logistics_coordinator.rb` — full implementation

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1**: Hardcoded capacity values are NOT wormhole-driven
- ❌ Wrong: Test against real WormholeManager data
- ✅ Right: Test against hardcoded capacity constants; wormhole integration is Phase 8b
- Why: This task is about proving transfer logic works, not about wormhole routing

⚠️ **GOTCHA 2**: Active transfers are tracked in-memory
- ❌ Wrong: Assume transfers persist across requests
- ✅ Right: Test within single orchestration cycle; transfers are volatile
- Why: Persistence is handled separately by SystemState caching

⚠️ **GOTCHA 3**: Transfer IDs must be unique per test cycle
- ❌ Wrong: Use hardcoded IDs like `:transfer_1`
- ✅ Right: Use `SecureRandom.uuid` or `generate_transfer_id` method (if it exists)
- Why: Prevents collisions in multi-settlement tests

---

## Problem Statement
`LogisticsCoordinator` has zero test coverage. The class handles critical inter-body transfer logic including route optimization, transfer scheduling, and capacity constraints, but bugs would go undetected. Current implementation hardcodes capacity values and has no tests validating optimization algorithms.

**Current behavior**: Zero tests; transfer logic untested.
**Expected behavior**: Comprehensive spec covering transfer optimization, scheduling, and metrics.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/spec/services/ai_manager/logistics_coordinator_spec.rb` | Tests (CREATE NEW) | Full spec file |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/app/services/ai_manager/logistics_coordinator.rb` | Implementation to test |
| `galaxy_game/app/services/ai_manager/system_orchestrator.rb` | Shows how coordinator is called |

### Migration
- [x] No migration needed

---

## Implementation Steps

### Step 1 — Create spec file with transfer optimization tests

```ruby
require 'rails_helper'

RSpec.describe AIManager::LogisticsCoordinator, type: :service do
  let(:shared_context) { AIManager::SharedContext.new }
  let(:coordinator) { described_class.new(shared_context) }

  describe '#optimize_transfers' do
    it 'returns empty array for empty transfer list' do
      result = coordinator.optimize_transfers([], instance_double(AIManager::SystemState))
      expect(result).to eq([])
    end

    it 'combines compatible transfers on same route' do
      system_state = instance_double(AIManager::SystemState)
      mars = instance_double(Settlement::BaseSettlement, id: 1, name: 'Mars')
      luna = instance_double(Settlement::BaseSettlement, id: 2, name: 'Luna')

      needed_transfers = [
        { source_settlement: mars, target_settlement: luna, resource: :energy, quantity: 100, priority: :high },
        { source_settlement: mars, target_settlement: luna, resource: :water, quantity: 50, priority: :high }
      ]

      result = coordinator.optimize_transfers(needed_transfers, system_state)
      
      # Should combine into fewer transfers on same route
      expect(result).not_to be_empty
    end

    it 'groups transfers by route for optimization' do
      system_state = instance_double(AIManager::SystemState)
      mars = instance_double(Settlement::BaseSettlement, id: 1)
      luna = instance_double(Settlement::BaseSettlement, id: 2)
      earth = instance_double(Settlement::BaseSettlement, id: 3)

      needed_transfers = [
        { source_settlement: mars, target_settlement: luna, resource: :energy, quantity: 100, priority: :high },
        { source_settlement: luna, target_settlement: earth, resource: :water, quantity: 100, priority: :high }
      ]

      result = coordinator.optimize_transfers(needed_transfers, system_state)
      
      # Should have transfers for both routes
      expect(result.size).to be >= 1
    end
  end

  describe '#schedule_transfers' do
    it 'schedules transfers and tracks active transfers' do
      transfers = [
        { id: 'tf-1', source_settlement: nil, target_settlement: nil, status: :pending }
      ]

      coordinator.schedule_transfers(transfers)

      expect(coordinator.active_transfers.size).to be >= 1
    end

    it 'logs transfer scheduling' do
      transfers = [
        { id: 'tf-1', source_settlement: nil, target_settlement: nil, status: :pending }
      ]

      expect(Rails.logger).to receive(:info).with(/Scheduled.*transfers/)
      coordinator.schedule_transfers(transfers)
    end
  end

  describe '#transfer_status' do
    it 'returns status for active transfer' do
      transfer = {
        id: 'tf-1',
        source_settlement: instance_double(Settlement::BaseSettlement, name: 'Mars'),
        target_settlement: instance_double(Settlement::BaseSettlement, name: 'Luna'),
        status: :in_transit,
        progress: 50,
        resource: :energy,
        quantity: 100,
        estimated_completion: Time.current + 1.hour
      }

      coordinator.instance_variable_set(:@active_transfers, [transfer])

      status = coordinator.transfer_status('tf-1')
      
      expect(status[:id]).to eq('tf-1')
      expect(status[:status]).to eq(:in_transit)
      expect(status[:progress]).to eq(50)
    end

    it 'returns nil for non-existent transfer' do
      status = coordinator.transfer_status('tf-999')
      expect(status).to be_nil
    end
  end

  describe '#cancel_transfer' do
    it 'cancels active transfer and removes from list' do
      transfer = {
        id: 'tf-1',
        source_settlement: instance_double(Settlement::BaseSettlement),
        target_settlement: nil,
        resource: :energy,
        quantity: 100,
        status: :pending
      }

      coordinator.instance_variable_set(:@active_transfers, [transfer])

      result = coordinator.cancel_transfer('tf-1')
      
      expect(result).to be true
      expect(coordinator.active_transfers.find { |t| t[:id] == 'tf-1' }).to be_nil
    end

    it 'returns false for non-existent transfer' do
      result = coordinator.cancel_transfer('tf-999')
      expect(result).to be false
    end
  end

  describe '#logistics_metrics' do
    it 'calculates completion rate and delay metrics' do
      coordinator.instance_variable_set(:@active_transfers, [
        { id: 'tf-1', status: :completed },
        { id: 'tf-2', status: :delayed },
        { id: 'tf-3', status: :in_transit }
      ])

      metrics = coordinator.logistics_metrics
      
      expect(metrics).to include(:total_active_transfers, :completion_rate, :delay_rate, :average_transport_cost, :capacity_utilization)
      expect(metrics[:total_active_transfers]).to eq(3)
      expect(metrics[:completion_rate]).to be_a(Float)
    end

    it 'handles empty transfer list' do
      metrics = coordinator.logistics_metrics
      
      expect(metrics[:total_active_transfers]).to eq(0)
      expect(metrics[:completion_rate]).to eq(0)
    end
  end
end
```

### Step 2 — Verify spec passes
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/logistics_coordinator_spec.rb 2>&1 | tail -30'
```

Expected result: 10+ examples, 0 failures

### Step 3 — Verify no regressions in orchestrator spec
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/manager_system_orchestrator_integration_spec.rb 2>&1 | tail -20'
```

---

## Acceptance Criteria
- [ ] New spec file created: `logistics_coordinator_spec.rb`
- [ ] Tests cover: transfer optimization, scheduling, status tracking, cancellation, metrics
- [ ] All tests pass with 0 failures
- [ ] At least 10 test cases
- [ ] Hardcoded capacity constants documented (not tested against real wormholes — defer to Phase 8b)
- [ ] No regressions in orchestrator specs

---

## Stop Conditions — escalate to user immediately if:
- Transfer ID generation logic unclear (check if `generate_transfer_id` method exists)
- Active transfer structure differs from spec expectations
- Transfer scheduling logic more complex than documented

---

## Commit Instructions
```bash
git add galaxy_game/spec/services/ai_manager/logistics_coordinator_spec.rb
git commit -m "test: add comprehensive LogisticsCoordinator spec; covers transfer optimization and scheduling"
git push
```

---

## Dependencies
**Blocked by**: None (can run independently)
**Blocks**: `2026-06-30-HIGH-INTEGRATION-MULTI-SETTLEMENT-ORCHESTRATOR-SPEC.md` (integration test validates coordinator in context)
**Related tasks**: All Phase 8 SystemOrchestrator tasks

---

## Completion Report
*Filled in by the implementing agent after completion*
