---
status: backlog
priority: MEDIUM
type: refactor
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/active/2026-06-30-MEDIUM-REFACTOR-STRATEGIC-PLANNER-EXTRACTION.md

READ FIRST: Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Create STATUS SYNTHESIS REPORT in chat BEFORE starting any work (template in task file).
```

---

# TASK: StrategicPlanner Service Extraction — Extract from SystemOrchestrator Stubs

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: refactor
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
The `SystemOrchestrator` class contains an `execute_strategic_planning` method that delegates to four private stub methods: `evaluate_system_opportunities`, `coordinate_expansion_plans`, `balance_economic_development`, and `update_strategic_objectives`. These are untestable as private methods and tightly coupled to the orchestrator. Extracting them into a standalone `StrategicPlanner` service enables independent testing, clearer separation of concerns, and integration with `EconomicForecasterService`.

**Relevant Architecture Docs**:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/projects/galaxy_game/summaries/2026-06-30-RESEARCH-SYSTEM-ORCHESTRATOR-AUDIT.md` — full audit

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1**: StrategicPlanner depends on persistent SystemState (Task 3)
- ❌ Wrong: Create StrategicPlanner before SystemState persistence is implemented
- ✅ Right: Verify Task 3 is committed — planner needs `save_state`/`load_state` to work with objectives across ticks
- Why: Strategic objectives are stored in SystemState. Without persistence, planning results are lost between ticks.

⚠️ **GOTCHA 2**: EconomicForecasterService exists but is disconnected — wire it in during extraction
- ❌ Wrong: Leave EconomicForecasterService as an orphaned service
- ✅ Right: Have StrategicPlanner accept EconomicForecasterService as a dependency and call it for projections
- Why: The audit flagged this as a gap. Fix it while we're already touching the strategic planning code path.

⚠️ **GOTCHA 3**: SystemOrchestrator should DELEGATE to StrategicPlanner, not duplicate logic
- ❌ Wrong: Copy logic into StrategicPlanner while keeping old stubs in SystemOrchestrator
- ✅ Right: Replace `execute_strategic_planning` with a single delegation call to `@strategic_planner.plan(system_state)`
- Why: The goal is extraction, not duplication. Old stubs should be removed after delegation is wired.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

```
## STATUS SYNTHESIS REPORT

**Task**: StrategicPlanner Service Extraction
**Status**: backlog → active
**Date**: 2026-06-30

### What I'm About to Do
Extract strategic planning stubs from SystemOrchestrator into a new StrategicPlanner class.
Wire in EconomicForecasterService as a dependency. Create spec file from scratch.
Verify with docker exec rspec command.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `app/services/ai_manager/system_orchestrator.rb` | Source of stubs to extract | not started |
| `app/services/ai_manager/strategic_planner.rb` | NEW FILE — extraction target | not started |
| `spec/services/ai_manager/strategic_planner_spec.rb` | DOES NOT EXIST — create | not started |
| `app/services/ai_manager/economic_forecaster_service.rb` | Wire in as dependency | pending |

### Prerequisites Completed
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read audit report
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
- New StrategicPlanner class with plan() method
- SystemOrchestrator delegates to StrategicPlanner instead of private stubs
- EconomicForecasterService wired into StrategicPlanner
- New spec file tests planning logic independently
- All specs pass: 0 failures

### Critical Gotchas I Will Avoid
- ❌ Starting before Task 3 (persistence) — instead ✅ Verify SystemState has save/load
- ❌ Leaving EconomicForecaster disconnected — instead ✅ Wire it in as dependency
- ❌ Duplicating logic — instead ✅ Replace stubs with delegation

---

**SYNTHESIS COMPLETE.** Ready to proceed.
```

---

## Problem Statement
`SystemOrchestrator.execute_strategic_planning` contains four private stub methods that are untestable and tightly coupled:
- `evaluate_system_opportunities` — identifies expansion/resource opportunities
- `coordinate_expansion_plans` — plans settlement expansion across bodies
- `balance_economic_development` — balances economic growth
- `update_strategic_objectives` — updates long-term goals

These should be a standalone, testable service.

**Current behavior**: Strategic planning is buried in SystemOrchestrator as private stubs.
**Expected behavior**: Standalone `StrategicPlanner` class with clear API, independently testable.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/ai_manager/system_orchestrator.rb` | Remove stubs, add delegation | `execute_strategic_planning` |
| `app/services/ai_manager/strategic_planner.rb` | NEW FILE — extracted logic | `plan(system_state)` |
| `spec/services/ai_manager/strategic_planner_spec.rb` | DOES NOT EXIST — create | Entire file |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/services/ai_manager/economic_forecaster_service.rb` | Wire in as dependency for projections |
| `app/services/ai_manager/system_state.rb` | Planner reads/writes strategic objectives |

### Migration
- [ ] No migration needed

---

## Implementation Steps

### Step 1 — Create spec file from scratch
Create `spec/services/ai_manager/strategic_planner_spec.rb`:

```ruby
require 'rails_helper'

RSpec.describe AIManager::StrategicPlanner, type: :service do
  let(:shared_context) { AIManager::SharedContext.new }
  let(:system_state) { AIManager::SystemState.new }
  let(:forecaster) { instance_double(AIManager::EconomicForecasterService) }
  let(:planner) { described_class.new(shared_context, forecaster) }

  describe '#plan' do
    it 'returns strategic plan based on system state' do
      plan = planner.plan(system_state)
      expect(plan).to be_a(Hash)
      expect(plan.keys).to include(:objectives, :expansion_plans, :economic_balance)
    end
  end

  describe '#evaluate_opportunities' do
    it 'identifies expansion and resource opportunities' do
      # Test opportunity evaluation
    end
  end
end
```

### Step 2 — Create StrategicPlanner class
Create `app/services/ai_manager/strategic_planner.rb`:

```ruby
module AIManager
  class StrategicPlanner
    attr_reader :shared_context, :forecaster

    def initialize(shared_context, forecaster = nil)
      @shared_context = shared_context
      @forecaster = forecaster || AIManager::EconomicForecasterService.new(shared_context)
    end

    # Main planning entry point
    def plan(system_state)
      opportunities = evaluate_opportunities(system_state)
      expansion_plans = coordinate_expansion(opportunities, system_state)
      economic_balance = balance_economy(system_state)
      objectives = generate_objectives(opportunities, expansion_plans, system_state)

      {
        objectives: objectives,
        expansion_plans: expansion_plans,
        economic_balance: economic_balance,
        opportunities: opportunities,
        generated_at: Time.current
      }
    end

    private

    def evaluate_opportunities(system_state)
      # Extracted from SystemOrchestrator stub
    end

    def coordinate_expansion(opportunities, system_state)
      # Extracted from SystemOrchestrator stub
    end

    def balance_economy(system_state)
      # Use forecaster for projections if available
      if @forecaster.respond_to?(:forecast)
        forecast = @forecaster.forecast(system_state)
        # Balance based on forecast
      end
    end

    def generate_objectives(opportunities, expansion_plans, system_state)
      # Generate strategic objectives from analysis
    end
  end
end
```

### Step 3 — Wire into SystemOrchestrator
Update `system_orchestrator.rb`:

```ruby
# In initialize, add:
@strategic_planner = AIManager::StrategicPlanner.new(@shared_context)

# Replace execute_strategic_planning:
def execute_strategic_planning
  plan = @strategic_planner.plan(@system_state)

  # Apply results to system state
  @system_state.update_strategic_objectives(plan[:objectives])
end
```

### Step 4 — Remove old private stubs from SystemOrchestrator
Delete `evaluate_system_opportunities`, `coordinate_expansion_plans`, `balance_economic_development`, `update_strategic_objectives` from SystemOrchestrator.

### Step 5 — Verify with RSpec

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/strategic_planner_spec.rb --format documentation 2>&1 | tail -30'
```

Expected result: All examples pass, 0 failures.

### Step 6 — Run integration spec to confirm no regressions

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/manager_system_orchestrator_integration_spec.rb --format documentation 2>&1 | tail -30'
```

---

## Acceptance Criteria
- [ ] New `StrategicPlanner` class with `plan(system_state)` method
- [ ] SystemOrchestrator delegates to StrategicPlanner (no private stubs remaining)
- [ ] EconomicForecasterService wired as dependency
- [ ] New spec file tests planning logic independently
- [ ] Isolation run: 0 failures for `strategic_planner_spec.rb`
- [ ] No regressions in integration spec

---

## Stop Conditions — escalate to user immediately if:
- Fix causes new failures in specs you did not touch
- Same failure persists after two attempts
- EconomicForecasterService has incompatible API requiring architectural decision
- Any architectural decision is required

---

## Commit Instructions
```bash
git add galaxy_game/app/services/ai_manager/strategic_planner.rb
git add galaxy_game/app/services/ai_manager/system_orchestrator.rb
git add galaxy_game/spec/services/ai_manager/strategic_planner_spec.rb
git commit -m "refactor: extract StrategicPlanner from SystemOrchestrator; wire in EconomicForecasterService"
git push
```

---

## Dependencies
**Blocked by**: `2026-06-30-MEDIUM-ARCHITECTURE-SYSTEM-STATE-PERSISTENCE.md` (Task 3 — needs persistent state)
**Blocks**: `2026-06-30-HIGH-INTEGRATION-MULTI-SETTLEMENT-ORCHESTRATOR-SPEC.md` (integration test needs complete orchestrator)
**Related tasks**: none

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation]

### Follow-up tasks needed
[Any new backlog items identified]

---

## Handoff Summary
HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]