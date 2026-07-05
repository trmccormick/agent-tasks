---
status: backlog
priority: HIGH
type: data
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase8+/2026-06-30-HIGH-DATA-SETTLEMENT-MANAGER-INTEGRATION.md

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv to new folder
  - New/untracked file: move with filesystem (mv), then git add the final path
  - Never copy task files between folders
READ FIRST: Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: SettlementManager Real Data Integration — Connect to Live BaseSettlement Inventory

**Status**: BACKLOG
**Priority**: HIGH
**Type**: data
**Created**: 2026-06-30
**Last Updated**: 2026-07-04

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/README.md`
3. **Audit Report**: `docs/new_agent/projects/galaxy_game/summaries/2026-06-30-RESEARCH-SYSTEM-ORCHESTRATOR-AUDIT.md`
4. **This Task File**: Everything below

---

## Context
The `SettlementManager` class is the per-settlement wrapper that SystemOrchestrator uses to query settlement state. Currently `settlement_resources` returns a hardcoded hash (`{ minerals: 100, energy: 80, food: 60, ... }`). This means all system-wide coordination logic operates on fake data. The method needs to query the actual `BaseSettlement` model — its inventory, account balance, and operational_data — to provide real resource state to the orchestrator.

**Relevant Architecture Docs**:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions (Unit Model Pattern, Data Paths)
- `docs/new_agent/projects/galaxy_game/summaries/2026-06-30-RESEARCH-SYSTEM-ORCHESTRATOR-AUDIT.md` — full audit

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1**: BaseSettlement uses JSONB operational_data — read via Rails jsonb accessor
- ❌ Wrong: Use `settlement.operational_data['key']` without nil guard (can be `{}` or nil)
- ✅ Right: Use `(settlement.operational_data || {}).dig('path', 'to', 'data')` pattern
- Why: DECISIONS.md locks the Unit Model Pattern — read everything from operational_data with nil safety

⚠️ **GOTCHA 2**: Settlement inventory is an associated model, not a hash on the settlement
- ❌ Wrong: `settlement.resources[:minerals]` (doesn't exist)
- ✅ Right: `settlement.inventory.current_storage_of('minerals')` or query inventory directly
- Why: Inventory is a separate model with its own storage tracking methods

⚠️ **GOTCHA 3**: This task depends on Task 1 (ResourceAllocator refactor) completing first
- ❌ Wrong: Start this task before ResourceAllocator is fixed
- ✅ Right: Verify Task 1 is committed before starting
- Why: SettlementManager calls into ResourceAllocator — broken constructor will cascade

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

```
## STATUS SYNTHESIS REPORT

**Task**: SettlementManager Real Data Integration
**Status**: backlog → active
**Date**: YYYY-MM-DD

### What I'm About to Do
Replace mock data in SettlementManager with real queries against BaseSettlement inventory,
account, and operational_data. Create spec file from scratch since none exists.
Verify with docker exec rspec command using real settlement factories.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `app/services/ai_manager/settlement_manager.rb` | Mock data — replace with real queries | not started |
| `spec/services/ai_manager/settlement_manager_spec.rb` | DOES NOT EXIST — create from scratch | not started |
| `app/models/settlement/base_settlement.rb` | Real data source — read only | pending |
| `spec/factories/base_settlement.rb` | Factory for test settlements | pending |

### Prerequisites Completed
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read audit report
- ✅ Read this task file
- ✅ Understand architecture gotchas above
- ✅ Task 1 (ResourceAllocator) committed

### Expected Outcomes
- settlement_resources() returns real inventory data from BaseSettlement
- settlement_health() calculates from real population/infrastructure data
- collect_resource_requests() uses real settlement state, not mock strategy selector output
- New spec file with factory-based tests passes: 0 failures

### Critical Gotchas I Will Avoid
- ❌ Accessing operational_data without nil guard — instead ✅ Use (|| {}).dig() pattern
- ❌ Assuming settlement has .resources hash — instead ✅ Query inventory model
- ❌ Starting before Task 1 completes — instead ✅ Verify ResourceAllocator is fixed first

---

**SYNTHESIS COMPLETE.** Ready to proceed.
```

---

## Problem Statement
`SettlementManager.settlement_resources` returns a hardcoded hash:
```ruby
{ minerals: 100, energy: 80, food: 60, water: 70, steel: 40, electronics: 30 }
```
This mock data means SystemOrchestrator's resource allocation, logistics coordination, and strategic planning all operate on fabricated numbers.

**Current behavior**: All orchestrator methods receive fake resource quantities.
**Expected behavior**: SettlementManager queries `BaseSettlement.inventory`, `account`, and `operational_data` to provide accurate state.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/ai_manager/settlement_manager.rb` | Mock data → real queries | `settlement_resources`, `settlement_health`, `collect_resource_requests` |
| `spec/services/ai_manager/settlement_manager_spec.rb` | DOES NOT EXIST — create | Entire file |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/models/settlement/base_settlement.rb` | Understand inventory/account associations |
| `spec/factories/base_settlement.rb` | Factory structure for creating test settlements |
| `app/services/ai_manager/shared_context.rb` | Constructor dependency |

### Migration
- [ ] No migration needed

---

## Implementation Steps

### Step 1 — Create spec file from scratch

```ruby
require 'rails_helper'

RSpec.describe AIManager::SettlementManager, type: :service do
  let(:settlement) { create(:base_settlement, name: 'Test Luna Base') }
  let(:shared_context) { AIManager::SharedContext.new(settlement: settlement) }
  let(:manager) { described_class.new(settlement, shared_context) }

  describe '#settlement' do
    it 'returns the associated settlement' do
      expect(manager.settlement).to eq(settlement)
    end
  end

  describe '#settlement_resources' do
    it 'returns a hash of resource quantities' do
      resources = manager.settlement_resources
      expect(resources).to be_a(Hash)
    end

    it 'does not return hardcoded mock values' do
      resources = manager.settlement_resources
      expect(resources).not_to eq({ minerals: 100, energy: 80, food: 60, water: 70, steel: 40, electronics: 30 })
    end
  end

  describe '#settlement_health' do
    it 'returns a score between 0.0 and 1.0' do
      health = manager.settlement_health
      expect(health).to be_between(0.0, 1.0)
    end
  end

  describe '#collect_resource_requests' do
    it 'returns an array of resource requests' do
      requests = manager.collect_resource_requests
      expect(requests).to be_an(Array)
    end

    it 'scopes requests to the correct settlement' do
      requests = manager.collect_resource_requests
      requests.each { |r| expect(r[:settlement_id]).to eq(settlement.id) } if requests.any?
    end
  end
end
```

### Step 2 — Refactor `settlement_resources` to query real inventory

```ruby
def settlement_resources
  return {} unless settlement.inventory

  resources = {}
  if settlement.inventory.respond_to?(:items)
    settlement.inventory.items.each do |item|
      resources[item.resource_name.to_sym] = item.quantity.to_f
    end
  end

  energy_output = (settlement.operational_data || {}).dig('generated', 'energy_kwh', 'current_output')
  resources[:energy] = energy_output.to_f if energy_output
  resources
end
```

### Step 3 — Refactor `settlement_health` to use real data

Replace mock `0.75` with a weighted calculation from population, resource coverage, and infrastructure completeness.

### Step 4 — Refactor `collect_resource_requests` to use real state

Replace mock strategy selector output with actual settlement needs assessment.

### Step 5 — Verify with RSpec

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/settlement_manager_spec.rb --format documentation 2>&1' | tail -30
```

### Step 6 — Run integration spec

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/manager_system_orchestrator_integration_spec.rb --format documentation 2>&1' | tail -30
```

---

## Acceptance Criteria
- [ ] `settlement_resources` returns real inventory data from BaseSettlement (not hardcoded hash)
- [ ] `settlement_health` calculates score from real population/resource/infrastructure data
- [ ] `collect_resource_requests` uses real settlement state
- [ ] New spec file created with factory-based tests — no hollow test bodies
- [ ] Isolation run: 0 failures for `settlement_manager_spec.rb`
- [ ] No regressions in `manager_system_orchestrator_integration_spec.rb`

---

## Stop Conditions — escalate to user immediately if:
- Settlement inventory model has unexpected structure requiring architectural decision
- Fix causes new failures in specs you did not touch
- Any architectural decision is required

---

## Commit Instructions
```bash
git add galaxy_game/app/services/ai_manager/settlement_manager.rb
git add galaxy_game/spec/services/ai_manager/settlement_manager_spec.rb
git commit -m "data: wire SettlementManager to real BaseSettlement inventory data; create spec"
git push
```

---

## Dependencies
**Blocked by**: `2026-06-30-HIGH-REFACTOR-RESOURCE-ALLOCATOR-STABILITY.md` (Task 1)
**Blocks**: `2026-06-30-MEDIUM-ARCHITECTURE-SYSTEM-STATE-PERSISTENCE.md` (Task 3)
**Related tasks**: `2026-06-30-HIGH-INTEGRATION-MULTI-SETTLEMENT-ORCHESTRATOR-SPEC.md`

---

## Completion Report
**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `[file]` — [description]

---

## Handoff Summary
HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
