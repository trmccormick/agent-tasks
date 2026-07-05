---
status: backlog
priority: HIGH
type: refactor
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase8+/2026-06-30-HIGH-REFACTOR-RESOURCE-ALLOCATOR-STABILITY.md

READ FIRST: Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Create STATUS SYNTHESIS REPORT in chat BEFORE starting any work (template in task file).
```

---

# TASK: ResourceAllocator Stability Refactor — Fix Dual Constructor Bug

**Status**: BACKLOG
**Priority**: HIGH
**Type**: refactor
**Created**: 2026-06-30
**Last Updated**: 2026-07-04 (spec bodies filled, handoff path corrected)

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/README.md`
3. **Audit Report**: `docs/new_agent/projects/galaxy_game/summaries/2026-06-30-RESEARCH-SYSTEM-ORCHESTRATOR-AUDIT.md`
4. **This Task File**: Everything below

---

## Context
The `ResourceAllocator` service is the core component for cross-settlement resource allocation in the SystemOrchestrator pipeline. It receives arbitrated resource requests and distributes them using a fair-sharing algorithm. Currently the file contains TWO class definitions — a legacy single-settlement version (with `SUPPLY_REQUIREMENTS` constants and `settlement_size` constructor) shadowing the new system-wide version (with `shared_context` constructor). This is a refactoring artifact from when the orchestrator was first scaffolded.

**Relevant Architecture Docs**:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/projects/galaxy_game/summaries/2026-06-30-RESEARCH-SYSTEM-ORCHESTRATOR-AUDIT.md` — full audit of orchestrator state

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1**: The existing spec tests the OLD constructor
- ❌ Wrong: Leave `resource_allocator_spec.rb` as-is (it tests `new(settlement_size: 'small')`)
- ✅ Right: Rewrite the spec to test `new(shared_context)` and system-wide allocation
- Why: After merging constructors, the old API no longer exists — the spec will fail if not updated

⚠️ **GOTCHA 2**: The new constructor takes a `shared_context` object, not a settlement
- ❌ Wrong: Pass a settlement instance to the new constructor
- ✅ Right: Pass an `AIManager::SharedContext` instance (use existing factory or build in spec)
- Why: System-wide allocator operates on SharedContext event bus, not single settlements

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

```
## STATUS SYNTHESIS REPORT

**Task**: ResourceAllocator Stability Refactor
**Status**: backlog → active
**Date**: 2026-06-30

### What I'm About to Do
Merge the dual class definitions in ResourceAllocator into a single system-wide implementation.
Rewrite the existing spec to test the new constructor and allocation logic.
Verify with docker exec rspec command.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `app/services/ai_manager/resource_allocator.rb` | Dual class def — merge target | not started |
| `spec/services/ai_manager/resource_allocator_spec.rb` | Tests OLD constructor — rewrite needed | not started |
| `app/services/ai_manager/shared_context.rb` | Constructor dependency — read only | pending |

### Prerequisites Completed
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read audit report
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
- Single class definition with `initialize(shared_context)` constructor
- Legacy SUPPLY_REQUIREMENTS / ISRU_PRIORITIES constants removed (or moved to appropriate service)
- Updated spec testing system-wide allocation via shared_context
- All specs pass: 0 failures

### Critical Gotchas I Will Avoid
- ❌ Leaving old spec as-is — instead ✅ Rewrite spec for new constructor
- ❌ Keeping both class definitions — instead ✅ Merge into one, prune legacy

---

**SYNTHESIS COMPLETE.** Ready to proceed.
```

---

## Problem Statement
`app/services/ai_manager/resource_allocator.rb` contains TWO `module AIManager; class ResourceAllocator` definitions. The first (lines ~1-25) has `initialize(settlement_size: 'small')` with hardcoded `SUPPLY_REQUIREMENTS` and `ISRU_PRIORITIES`. The second (later in file) has `initialize(shared_context)` with system-wide allocation logic. Ruby loads the second definition, but the existing spec tests the first — creating a false sense of test coverage.

**Current behavior**: Spec passes against old constructor that's shadowed by new code.
**Expected behavior**: Single class definition; spec tests actual running code.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/ai_manager/resource_allocator.rb` | Dual class def — merge into one | Both `initialize` methods |
| `spec/services/ai_manager/resource_allocator_spec.rb` | Tests OLD constructor — rewrite | Entire file |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/services/ai_manager/shared_context.rb` | Constructor parameter type |
| `app/services/ai_manager/priority_arbitrator.rb` | Calls into ResourceAllocator — verify API compatibility |
| `app/services/ai_manager/system_orchestrator.rb` | Uses ResourceAllocator in `allocate_system_resources` |

### Migration
- [ ] No migration needed

---

## Implementation Steps

### Step 1 — Read and understand both class definitions
Read `resource_allocator.rb` in full. Identify:
- Which methods belong to the legacy single-settlement version
- Which methods belong to the new system-wide version
- Any overlapping method names that need merging

### Step 2 — Merge into single class definition
Keep the system-wide version as the base. Remove the legacy `SUPPLY_REQUIREMENTS` and `ISRU_PRIORITIES` constants (these belong in `SettlementManager` or a settlement bootstrap service, not the system allocator).

```ruby
# AFTER — single class definition
module AIManager
  class ResourceAllocator
    def initialize(shared_context)
      @shared_context = shared_context
    end

    # allocate_resources, identify_transfers, etc. (system-wide methods)
  end
end
```

### Step 3 — Rewrite spec for new constructor

Replace `resource_allocator_spec.rb` entirely:

```ruby
require 'rails_helper'

RSpec.describe AIManager::ResourceAllocator, type: :service do
  let(:shared_context) { AIManager::SharedContext.new }
  let(:allocator) { described_class.new(shared_context) }

  describe '#allocate_resources' do
    it 'returns an array of allocations for valid requests' do
      system_state = instance_double(AIManager::SystemState)
      requests = [
        { settlement_id: 1, resource: :energy, quantity: 100, priority: :high },
        { settlement_id: 2, resource: :water,  quantity: 50,  priority: :medium }
      ]
      result = allocator.allocate_resources(requests, system_state)
      expect(result).to be_an(Array)
    end

    it 'returns empty array when no requests provided' do
      system_state = instance_double(AIManager::SystemState)
      result = allocator.allocate_resources([], system_state)
      expect(result).to eq([])
    end
  end

  describe '#identify_transfers' do
    it 'returns an array of transfer objects between settlements' do
      source = instance_double(AIManager::SettlementManager)
      target = instance_double(AIManager::SettlementManager)
      result = allocator.identify_transfers(source, target)
      expect(result).to be_an(Array)
    end
  end
end
```

### Step 4 — Verify API compatibility
Confirm that `SystemOrchestrator` and `PriorityArbitrator` still call into ResourceAllocator correctly:
- `allocate_resources(arbitrated_requests, system_state)` — should return array of allocations
- `identify_transfers(source_manager, target_manager)` — should return array of transfers

### Step 5 — Verify with RSpec

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/resource_allocator_spec.rb --format documentation 2>&1' | tail -20
```

Expected result: All examples pass, 0 failures.

### Step 6 — Run integration spec to confirm no regressions

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/manager_system_orchestrator_integration_spec.rb --format documentation 2>&1' | tail -20
```

---

## Acceptance Criteria
- [ ] Single `ResourceAllocator` class definition with `initialize(shared_context)` constructor
- [ ] Legacy `SUPPLY_REQUIREMENTS` and `ISRU_PRIORITIES` constants removed
- [ ] Updated spec tests system-wide allocation via shared_context
- [ ] Isolation run: 0 failures for `resource_allocator_spec.rb`
- [ ] No regressions in `manager_system_orchestrator_integration_spec.rb`

---

## Stop Conditions — escalate to user immediately if:
- Fix causes new failures in specs you did not touch
- Same failure persists after two attempts
- Root cause is in a shared concern, base class, or factory used across many specs
- `SystemOrchestrator` or `PriorityArbitrator` break due to API change
- Any architectural decision is required

---

## Commit Instructions
```bash
git add galaxy_game/app/services/ai_manager/resource_allocator.rb
git add galaxy_game/spec/services/ai_manager/resource_allocator_spec.rb
git commit -m "refactor: merge dual ResourceAllocator class definitions; rewrite spec for system-wide constructor"
git push
```

---

## Dependencies
**Blocked by**: none (this is Task 1 in the pipeline)
**Blocks**: `2026-06-30-HIGH-DATA-SETTLEMENT-MANAGER-INTEGRATION.md` (SettlementManager calls into ResourceAllocator)
**Related tasks**: `2026-06-30-HIGH-INTEGRATION-MULTI-SETTLEMENT-ORCHESTRATOR-SPEC.md`

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
