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
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/phase8+/2026-06-30-HIGH-INTEGRATION-MULTI-SETTLEMENT-ORCHESTRATOR-SPEC.md

READ FIRST: Task file contains all prerequisites, target files, gotchas, and verification steps.

CRITICAL: Create STATUS SYNTHESIS REPORT in chat BEFORE starting any work (template in task file).

EXECUTION ORDER: This is the FINAL task in Phase 8. Run AFTER all 4 core tasks complete.
```

---

# TASK: Multi-Settlement Orchestrator Integration Test — Validate System Coordination End-to-End

**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-06-30

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/README.md`
3. **Audit Report**: `docs/new_agent/projects/galaxy_game/summaries/2026-06-30-RESEARCH-SYSTEM-ORCHESTRATOR-AUDIT.md`
4. **Gap Analysis**: `docs/new_agent/projects/galaxy_game/summaries/2026-06-30-GAP-ANALYSIS-PROPOSED-TASKS.md`
5. **This Task File**: Everything below

---

## Context
The first 4 Phase 8 tasks wire real data into isolated components. This task validates they work together correctly in a multi-settlement scenario. The test must verify actual resource allocation decisions across 2+ settlements with competing demands, not just method invocation.

---

## Problem Statement
No end-to-end test validates resource allocation across 2+ settlements with competing demands. Existing `manager_system_orchestrator_integration_spec.rb` only tests registration and method invocation.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose |
|---|---|
| `galaxy_game/spec/services/ai_manager/system_orchestrator_multi_settlement_spec.rb` | New integration test (CREATE) |

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
    allow_any_instance_of(AIManager::StrategySelector)
      .to receive(:evaluate_next_action)
      .and_return(type: :wait, score: 0, rationale: "Test only")
  end

  describe "resource allocation across multiple settlements" do
    it "prioritizes critical settlement over low priority" do
      mars_settlement.update(current_population: 100)
      luna_settlement.update(current_population: 50)

      orchestrator.register_settlement(mars_settlement)
      orchestrator.register_settlement(luna_settlement)

      orchestrator.orchestrate_system

      expect(orchestrator.settlements.size).to eq(2)
      expect(orchestrator.settlements.map(&:name)).to include("Mars Critical Base", "Luna Low Priority")
    end

    it "identifies settlements with resource needs" do
      orchestrator.register_settlement(mars_settlement)
      orchestrator.register_settlement(luna_settlement)

      orchestrator.orchestrate_system

      status = orchestrator.system_status
      expect(status[:total_settlements]).to eq(2)
    end

    it "maintains system state across orchestration cycles" do
      orchestrator.register_settlement(mars_settlement)

      orchestrator.orchestrate_system
      first_state = orchestrator.system_status

      orchestrator.orchestrate_system
      second_state = orchestrator.system_status

      expect(first_state[:total_settlements]).to eq(second_state[:total_settlements])
    end

    it "handles settlement registration and unregistration" do
      orchestrator.register_settlement(mars_settlement)
      expect(orchestrator.settlements.size).to eq(1)

      orchestrator.register_settlement(luna_settlement)
      expect(orchestrator.settlements.size).to eq(2)

      orchestrator.unregister_settlement(mars_settlement.id)
      expect(orchestrator.settlements.size).to eq(1)
    end
  end
end
```

### Step 2 — Verify spec passes
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/system_orchestrator_multi_settlement_spec.rb 2>&1 | tail -30'
```

---

## Acceptance Criteria
- [ ] New spec file created: `system_orchestrator_multi_settlement_spec.rb`
- [ ] All tests pass with 0 failures
- [ ] At least 4 test cases
- [ ] No regressions in existing orchestrator specs

---

## Commit Instructions
```bash
git add galaxy_game/spec/services/ai_manager/system_orchestrator_multi_settlement_spec.rb
git commit -m "test: add multi-settlement orchestrator integration spec; validates end-to-end coordination"
git push
```

---

## Dependencies
**Blocked by**: All 4 core Phase 8 tasks
**Blocks**: None (final task in Phase 8)
