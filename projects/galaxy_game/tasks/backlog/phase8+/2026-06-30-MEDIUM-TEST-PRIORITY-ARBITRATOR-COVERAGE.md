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
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/active/2026-06-30-MEDIUM-TEST-PRIORITY-ARBITRATOR-COVERAGE.md

READ FIRST: Task file contains all prerequisites, target files, gotchas, and verification steps.

CRITICAL: Create STATUS SYNTHESIS REPORT in chat BEFORE starting any work (template in task file).
```

---

# TASK: PriorityArbitrator Test Coverage — Write Spec for Conflict Resolution Logic

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
`PriorityArbitrator` is a critical component of `SystemOrchestrator` that handles conflict resolution when multiple settlements request the same resource at different priority levels. It implements priority-based arbitration, crisis escalation, and conflict tracking. Currently it has zero test coverage, meaning allocation bugs would go undetected.

**Relevant Architecture Docs**:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `galaxy_game/app/services/ai_manager/priority_arbitrator.rb` — full implementation

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1**: Priority values are ordinal, not absolute
- ❌ Wrong: Assume `critical: 4` means allocation is 4x `low: 1`
- ✅ Right: Priority levels are sorting keys; allocation should use fair-sharing with priority multiplier
- Why: Check `priority_multiplier` method in arbitrator to understand the math

⚠️ **GOTCHA 2**: Conflicts must be tracked for crisis handling
- ❌ Wrong: Test only arbitration logic, ignore conflict tracking
- ✅ Right: Test that conflicts are recorded in `@active_conflicts` when resources can't satisfy all demands
- Why: Crisis escalation depends on conflict history

⚠️ **GOTCHA 3**: `settlement_priority_level` is hardcoded — don't test external data
- ❌ Wrong: Mock SettlementManager to return health scores
- ✅ Right: Test with mock settlement IDs; arbitrator only cares about priority level
- Why: Settlement data belongs in SettlementManager spec, not here

---

## Problem Statement
`PriorityArbitrator` has no test coverage. The class handles critical conflict resolution logic but is never tested, so bugs in priority ordering or fair-sharing algorithms would silently cause incorrect resource allocations across settlements.

**Current behavior**: Zero tests; logic untested in isolation.
**Expected behavior**: Comprehensive spec covering arbitration, crisis escalation, and conflict resolution.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/spec/services/ai_manager/priority_arbitrator_spec.rb` | Tests (CREATE NEW) | Full spec file |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/app/services/ai_manager/priority_arbitrator.rb` | Implementation to test |
| `galaxy_game/app/services/ai_manager/system_orchestrator.rb` | Shows how arbitrator is called |

### Migration
- [x] No migration needed

---

## Implementation Steps

### Step 1 — Create spec file with comprehensive test cases

```ruby
require 'rails_helper'

RSpec.describe AIManager::PriorityArbitrator, type: :service do
  let(:arbitrator) { described_class.new }

  describe '#arbitrate_requests' do
    it 'returns requests unchanged if only one request per resource' do
      requests = [
        { settlement_id: 1, resource: :energy, quantity: 100, priority: :high }
      ]
      
      result = arbitrator.arbitrate_requests(requests)
      
      expect(result.size).to eq(1)
      expect(result.first[:settlement_id]).to eq(1)
    end

    it 'prioritizes critical requests over low requests for same resource' do
      requests = [
        { settlement_id: 1, resource: :energy, quantity: 100, priority: :critical },
        { settlement_id: 2, resource: :energy, quantity: 100, priority: :low }
      ]
      
      result = arbitrator.arbitrate_requests(requests)
      
      # Critical should come first in sorted results
      critical_req = result.find { |r| r[:priority] == :critical }
      low_req = result.find { |r| r[:priority] == :low }
      
      expect(critical_req).not_to be_nil
      expect(low_req).not_to be_nil
    end

    it 'groups requests by resource before arbitrating' do
      requests = [
        { settlement_id: 1, resource: :energy, quantity: 50, priority: :high },
        { settlement_id: 2, resource: :energy, quantity: 50, priority: :medium },
        { settlement_id: 3, resource: :water, quantity: 50, priority: :high }
      ]
      
      result = arbitrator.arbitrate_requests(requests)
      
      # Should have all 3 requests, grouped by resource internally
      expect(result.size).to eq(3)
    end
  end

  describe '#escalate_crisis' do
    it 'marks resources as critical priority in active conflicts' do
      arbitrator.escalate_crisis(1, [:energy, :water])
      
      expect(arbitrator.active_conflicts.size).to eq(1)
      conflict = arbitrator.active_conflicts.first
      expect(conflict[:type]).to eq(:crisis)
      expect(conflict[:settlement_id]).to eq(1)
      expect(conflict[:resources]).to eq([:energy, :water])
    end

    it 'sets escalation deadline' do
      arbitrator.escalate_crisis(1, [:energy])
      
      conflict = arbitrator.active_conflicts.first
      expect(conflict[:escalated_at]).to be_a(Time)
      expect(conflict[:resolution_deadline]).to be_a(Time)
      expect(conflict[:resolution_deadline] > conflict[:escalated_at]).to be true
    end
  end

  describe '#resolve_conflict' do
    it 'removes resolved conflict from active list' do
      arbitrator.escalate_crisis(1, [:energy])
      conflict_id = arbitrator.active_conflicts.first[:id]
      
      result = arbitrator.resolve_conflict(conflict_id, :allocated)
      
      expect(result).to be true
      expect(arbitrator.active_conflicts.find { |c| c[:id] == conflict_id }).to be_nil
    end

    it 'returns false for non-existent conflict' do
      result = arbitrator.resolve_conflict(999, :allocated)
      expect(result).to be false
    end
  end

  describe '#settlement_priority_level' do
    it 'returns critical for settlements with active crisis conflicts' do
      arbitrator.escalate_crisis(1, [:energy])
      
      level = arbitrator.settlement_priority_level(1)
      expect(level).to eq(:critical)
    end

    it 'returns medium as default for settlements without conflicts' do
      level = arbitrator.settlement_priority_level(999)
      expect(level).to eq(:medium)
    end
  end

  describe '#can_grant_request?' do
    it 'checks resource availability adjusted by priority' do
      request = { resource: :energy, quantity: 100, priority: :critical }
      system_state = instance_double(AIManager::SystemState, 
                                     total_resources: { energy: 150 })
      
      result = arbitrator.can_grant_request?(request, system_state)
      expect(result).to be true
    end

    it 'returns false when resource insufficient even at high priority' do
      request = { resource: :energy, quantity: 200, priority: :low }
      system_state = instance_double(AIManager::SystemState,
                                     total_resources: { energy: 100 })
      
      result = arbitrator.can_grant_request?(request, system_state)
      expect(result).to be false
    end
  end
end
```

### Step 2 — Verify spec passes
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/priority_arbitrator_spec.rb 2>&1 | tail -30'
```

Expected result: 8+ examples, 0 failures

### Step 3 — Verify no regressions in orchestrator spec
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/manager_system_orchestrator_integration_spec.rb 2>&1 | tail -20'
```

---

## Acceptance Criteria
- [ ] New spec file created: `priority_arbitrator_spec.rb`
- [ ] Tests cover: arbitration logic, crisis escalation, conflict resolution, priority levels, request validation
- [ ] All tests pass with 0 failures
- [ ] At least 8 test cases
- [ ] No regressions in orchestrator specs

---

## Stop Conditions — escalate to user immediately if:
- Priority multiplier logic is unclear (read the implementation first)
- Instance variable `@active_conflicts` structure differs from spec expectations
- Arbitration logic is more complex than documented

---

## Commit Instructions
```bash
git add galaxy_game/spec/services/ai_manager/priority_arbitrator_spec.rb
git commit -m "test: add comprehensive PriorityArbitrator spec; covers conflict resolution logic"
git push
```

---

## Dependencies
**Blocked by**: None (can run independently)
**Blocks**: `2026-06-30-HIGH-INTEGRATION-MULTI-SETTLEMENT-ORCHESTRATOR-SPEC.md` (integration test validates arbitrator in context)
**Related tasks**: All Phase 8 SystemOrchestrator tasks

---

## Completion Report
*Filled in by the implementing agent after completion*
