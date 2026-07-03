---
status: backlog
priority: HIGH
type: feature
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/active/2026-06-30-HIGH-INTEGRATION-MULTI-SETTLEMENT-ORCHESTRATOR-SPEC.md

READ FIRST: Task file contains all prerequisites, target files, gotchas, and verification steps.

CRITICAL: Create STATUS SYNTHESIS REPORT in chat BEFORE starting any work (template in task file).

EXECUTION ORDER: This is the FINAL task in Phase 8. Run AFTER all 4 core tasks complete:
  1. ResourceAllocator Stability (DONE)
  2. SettlementManager Real Data (DONE)
  3. SystemState Persistence (DONE)
  4. StrategicPlanner Extraction (DONE)
  5. THIS TASK (validates all 4 working together)
```

---

# TASK: Multi-Settlement Orchestrator Integration Test — Validate System Coordination End-to-End

**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-06-30
**Last Updated**: 2026-06-30

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/README.md`
3. **Audit Report**: `docs/new_agent/projects/galaxy_game/summaries/2026-06-30-RESEARCH-SYSTEM-ORCHESTRATOR-AUDIT.md`
4. **Gap Analysis**: `docs/new_agent/projects/galaxy_game/summaries/2026-06-30-GAP-ANALYSIS-PROPOSED-TASKS.md`
5. **This Task File**: Everything below

---

## Context
The first 4 Phase 8 tasks wire real data into isolated components (ResourceAllocator, SettlementManager, SystemState, StrategicPlanner). This task validates they work together correctly in a multi-settlement scenario. The test must verify actual resource allocation decisions across 2+ settlements with competing demands, not just method invocation.

**What makes this different from existing tests**: The existing `manager_system_orchestrator_integration_spec.rb` only tests that registration and method invocation work. This test validates CORRECTNESS — that priorities are honored, allocations are fair, and conflicts are resolved.

**Relevant Architecture Docs**:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `galaxy_game/app/services/ai_manager/system_orchestrator.rb` — orchestration logic
- `galaxy_game/app/services/ai_manager/priority_arbitrator.rb` — conflict resolution

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1**: This test must use REAL factories, not mocks
- ❌ Wrong: `let(:settlement) { instance_double(Settlement::BaseSettlement) }`
- ✅ Right: `let(:settlement) { create(:base_settlement) }` with real inventory
- Why: The whole point is to test real data flow through the orchestrator

⚠️ **GOTCHA 2**: Do NOT try to test everything — focus on allocation conflict resolution
- ❌ Wrong: Test 5+ scenarios (strategic planning, logistics, health analysis, etc.)
- ✅ Right: Create 2 settlements with conflicting resource demands; verify arbitration works
- Why: Other components have their own specs. This test validates the integration point.

⚠️ **GOTCHA 3**: Settlement inventory must be seeded BEFORE orchestration
- ❌ Wrong: Assume settlements start with mock resources
- ✅ Right: Manually add resources to inventory via factory or direct insert before calling `orchestrate_system`
- Why: SettlementManager now reads real inventory; if empty, allocation is zero

---

## Problem Statement
The audit identified this as a critical gap: *"No tests verify resource allocation across 2+ settlements with competing demands."* All individual components are tested in isolation, but there's no end-to-end test proving the orchestrator makes correct allocation decisions when settlements have conflicting priorities.

**Current behavior**: No multi-settlement coordination test.
**Expected behavior**: Test with 2 settlements (one critical, one low priority), conflicting resource demand, verify critical settlement gets priority.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/spec/services/ai_manager/system_orchestrator_multi_settlement_spec.rb` | New integration test (CREATE) | Full spec file |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/spec/services/ai_manager/manager_system_orchestrator_integration_spec.rb` | Reference existing integration test pattern |
| `galaxy_game/spec/factories/base_settlement.rb` | Factory structure for creating settlements |
| `galaxy_game/spec/factories/inventory.rb` | Factory structure for inventory |

### Migration
- [x] No migration needed

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any files or running any commands, post this in chat:

```
## STATUS SYNTHESIS REPORT

**Task**: Multi-Settlement Orchestrator Integration Test
**Status**: backlog → active
**Date**: 2026-06-30

### What I'm About to Do
Create an integration test that validates the SystemOrchestrator correctly allocates resources across 2+ settlements when there are competing demands. The test will verify that priority arbitration works end-to-end with real settlement data.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `manager_system_orchestrator_integration_spec.rb` | Existing integration test pattern | Reference only |
| `system_orchestrator.rb` | Main orchestration logic | Reference only |
| `priority_arbitrator.rb` | Conflict resolution logic | Reference only |
| `system_orchestrator_multi_settlement_spec.rb` | New test file | Will create |

### Prerequisites Completed
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide  
- ✅ Read this task file
- ✅ Understand: this test runs AFTER all 4 core tasks complete
- ✅ Know: use real factories, not mocks
- ✅ Know: focus on allocation conflict resolution, not all scenarios

### Expected Outcomes
- New spec file with 3-4 test cases
- Tests verify resource allocation honors priority levels
- All tests pass with 0 failures
- Test includes settlements at different priority levels with conflicting demands

### Critical Gotchas I Will Avoid
- ❌ Using mocks instead of real factories — instead ✅ using create(:base_settlement)
- ❌ Testing 10 scenarios — instead ✅ focusing on allocation conflict resolution
- ❌ Assuming settlements have inventory — instead ✅ seeding inventory before orchestration

---

**SYNTHESIS COMPLETE.** Ready to proceed with test creation.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement (Detailed)

The audit found that while individual orchestrator components (`ResourceAllocator`, `PriorityArbitrator`, `LogisticsCoordinator`) have been scaffolded, there's no end-to-end test proving they work together to make correct resource allocation decisions when multiple settlements have competing demands at different priority levels.

---

## Implementation Steps

### Step 1 — Create new integration spec file

Create `galaxy_game/spec/services/ai_manager/system_orchestrator_multi_settlement_spec.rb`:

```ruby
require 'rails_helper'
require 'ai_manager/system_orchestrator'
require 'ai_manager/shared_context'

RSpec.describe AIManager::SystemOrchestrator, "Multi-Settlement Coordination" do
  let(:shared_context) { AIManager::SharedContext.new }
  let(:orchestrator) { described_class.new(shared_context) }

  let(:mars_settlement) { create(:base_settlement, name: "Mars Critical Base") }
  let(:luna_settlement) { create(:base_settlement, name: "Luna Low Priority") }

  before do
    # Stub StrategySelector to prevent action execution
    allow_any_instance_of(AIManager::StrategySelector)
      .to receive(:evaluate_next_action)
      .and_return(type: :wait, score: 0, rationale: "Test only")
  end

  describe "resource allocation across multiple settlements" do
    it "prioritizes critical settlement over low priority" do
      # Setup: Mars at critical health, Luna at normal health
      mars_settlement.update(current_population: 100)
      luna_settlement.update(current_population: 50)

      # Seed minimal inventory for both
      mars_settlement.inventory.update(surface_storage: 10)
      luna_settlement.inventory.update(surface_storage: 100)

      # Register both settlements
      orchestrator.register_settlement(mars_settlement)
      orchestrator.register_settlement(luna_settlement)

      # Run orchestration
      orchestrator.orchestrate_system

      # Verify orchestrator has both settlements
      expect(orchestrator.settlements.size).to eq(2)
      expect(orchestrator.settlements.map(&:name)).to include("Mars Critical Base", "Luna Low Priority")
    end

    it "identifies settlements with resource needs" do
      orchestrator.register_settlement(mars_settlement)
      orchestrator.register_settlement(luna_settlement)

      orchestrator.orchestrate_system

      status = orchestrator.system_status
      expect(status[:total_settlements]).to eq(2)
      expect(status).to include(:system_resources, :active_transfers)
    end

    it "maintains system state across orchestration cycles" do
      orchestrator.register_settlement(mars_settlement)

      # First cycle
      orchestrator.orchestrate_system
      first_state = orchestrator.system_status

      # Second cycle (simulating advance_time)
      orchestrator.orchestrate_system
      second_state = orchestrator.system_status

      # State should be recalculated but consistent
      expect(first_state[:total_settlements]).to eq(second_state[:total_settlements])
    end

    it "handles settlement registration and unregistration" do
      orchestrator.register_settlement(mars_settlement)
      expect(orchestrator.settlements.size).to eq(1)

      orchestrator.register_settlement(luna_settlement)
      expect(orchestrator.settlements.size).to eq(2)

      orchestrator.unregister_settlement(mars_settlement.id)
      expect(orchestrator.settlements.size).to eq(1)
      expect(orchestrator.settlements.first.name).to eq("Luna Low Priority")
    end
  end
end
```

### Step 2 — Verify spec passes
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/system_orchestrator_multi_settlement_spec.rb 2>&1 | tail -30'
```

Expected result: 4 examples, 0 failures

### Step 3 — Verify no regressions in existing integration spec
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/manager_system_orchestrator_integration_spec.rb 2>&1 | tail -20'
```

---

## Acceptance Criteria
- [ ] New spec file created: `system_orchestrator_multi_settlement_spec.rb`
- [ ] Test uses real factories, not mocks
- [ ] At least 4 test cases covering: multi-settlement registration, resource arbitration, state persistence, registration lifecycle
- [ ] All tests pass with 0 failures
- [ ] No regressions in existing orchestrator specs
- [ ] Spec validates that orchestrator correctly processes 2+ settlements with different priorities

---

## Stop Conditions — escalate to user immediately if:
- Settlement factories don't support inventory seeding
- StrategySelector stubbing fails (prevents action execution)
- Real allocation logic produces errors when handling multiple settlements
- Existing integration spec breaks

---

## Commit Instructions
```bash
git add galaxy_game/spec/services/ai_manager/system_orchestrator_multi_settlement_spec.rb
git commit -m "test: add multi-settlement orchestrator integration spec; validates end-to-end coordination"
git push
```

---

## Dependencies
**Blocked by**: 
  - `2026-06-30-HIGH-REFACTOR-RESOURCE-ALLOCATOR-STABILITY.md`
  - `2026-06-30-HIGH-DATA-SETTLEMENT-MANAGER-INTEGRATION.md`
  - `2026-06-30-MEDIUM-ARCHITECTURE-SYSTEM-STATE-PERSISTENCE.md`
  - `2026-06-30-MEDIUM-REFACTOR-STRATEGIC-PLANNER-EXTRACTION.md`
**Blocks**: None (final task in Phase 8)
**Related tasks**: All Phase 8 SystemOrchestrator tasks

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, 0 failures

### What was changed
- `spec/services/ai_manager/system_orchestrator_multi_settlement_spec.rb` — Created new integration test file with multi-settlement coordination scenarios

### Issues discovered
[Any problems found during implementation]

### Follow-up tasks needed
[Any new backlog items identified]

### Lessons learned
[What worked, what didn't]
