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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase8+/2026-06-30-MEDIUM-TEST-PRIORITY-ARBITRATOR-COVERAGE.md

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

**That's it.** Everything else should be IN this task file, not duplicated in handoff. Never copy task files between folders
READ FIRST: Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: PriorityArbitrator Test Coverage — Write Spec for Conflict Resolution Logic

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: feature
**Created**: 2026-06-30

---

## Context
`PriorityArbitrator` handles conflict resolution when multiple settlements request the same resource at different priority levels. Currently it has zero test coverage.

---

## Problem Statement
`PriorityArbitrator` has no test coverage. The class handles critical conflict resolution logic but is never tested, so bugs would go undetected.

---

## Files Involved

| File | Purpose |
|---|---|
| `galaxy_game/spec/services/ai_manager/priority_arbitrator_spec.rb` | Tests (CREATE NEW) |

---

## Implementation Steps

### Step 1 — Create spec file

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
    end

    it 'sets escalation deadline' do
      arbitrator.escalate_crisis(1, [:energy])
      
      conflict = arbitrator.active_conflicts.first
      expect(conflict[:escalated_at]).to be_a(Time)
      expect(conflict[:resolution_deadline]).to be_a(Time)
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
end
```

### Step 2 — Verify spec passes
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/priority_arbitrator_spec.rb 2>&1 | tail -20'
```

---

## Acceptance Criteria
- [ ] New spec file created: `priority_arbitrator_spec.rb`
- [ ] All tests pass with 0 failures
- [ ] At least 8 test cases

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
**Blocks**: Multi-settlement integration test
