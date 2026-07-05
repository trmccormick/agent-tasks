---
status: backlog
priority: MEDIUM
type: architecture
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase8+/2026-06-30-MEDIUM-ARCHITECTURE-SYSTEM-STATE-PERSISTENCE.md

READ FIRST: Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Create STATUS SYNTHESIS REPORT in chat BEFORE starting any work (template in task file).
```

---

# TASK: SystemState Persistence — Add Redis Caching for System-Wide State

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: architecture
**Created**: 2026-06-30
**Last Updated**: 2026-07-04 (cache key nil-risk fixed, spec body filled, handoff path corrected)

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/README.md`
3. **Audit Report**: `docs/new_agent/projects/galaxy_game/summaries/2026-06-30-RESEARCH-SYSTEM-ORCHESTRATOR-AUDIT.md`
4. **This Task File**: Everything below

---

## Context
The `SystemState` class tracks system-wide state: total resources across all settlements, health metrics, strategic objectives, inter-settlement dependencies, and economic balance. Currently all state lives in Ruby instance variables (`@total_resources`, `@settlement_states`, etc.), meaning it's lost between requests. The SystemOrchestrator needs persistent state to make informed decisions across `advance_time` ticks.

**Relevant Architecture Docs**:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/projects/galaxy_game/summaries/2026-06-30-RESEARCH-SYSTEM-ORCHESTRATOR-AUDIT.md` — full audit

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1**: Use Redis caching, NOT a database model (for now)
- ❌ Wrong: Create a new ActiveRecord model with migrations for SystemState
- ✅ Right: Use Rails.cache (Redis-backed in Docker) with structured keys
- Why: System state is ephemeral/computed data that changes every tick. A DB model adds unnecessary migration complexity. Redis is already available in the Docker stack. Phase 8b can add DB persistence if needed.

⚠️ **GOTCHA 2**: SystemState is instantiated fresh each time SystemOrchestrator initializes
- ❌ Wrong: Assume a singleton pattern — SystemState is created per orchestrator instance
- ✅ Right: Add `load_state` / `save_state` methods that read/write to cache, called by SystemOrchestrator
- Why: The orchestrator lifecycle creates new instances. Persistence must be explicit, not implicit.

⚠️ **GOTCHA 3**: Cached state should have a TTL (time-to-live)
- ❌ Wrong: Cache forever with no expiration
- ✅ Right: Set reasonable TTL (e.g., 1 hour) — state is recomputed on each orchestration cycle anyway
- Why: Stale cached state is worse than fresh computation.

⚠️ **GOTCHA 4**: Do NOT use `shared_context.id` as the cache key — it may be nil
- ❌ Wrong: `"ai_manager/system_state/#{shared_context.id}"` — SharedContext may not have an `id`,
  producing key `"ai_manager/system_state/"` and causing all settlements to share one cache slot
- ✅ Right: Use a stable literal key: `"ai_manager/system_state/global"` or derive from
  a field that is guaranteed to exist (e.g., settlement id if orchestrator is settlement-scoped)
- Why: A nil interpolation silently produces a valid-looking but wrong cache key

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

```
## STATUS SYNTHESIS REPORT

**Task**: SystemState Persistence via Redis Caching
**Status**: backlog → active
**Date**: 2026-06-30

### What I'm About to Do
Add save_state/load_state methods to SystemState using Rails.cache (Redis).
Create spec file from scratch. Ensure state survives across advance_time ticks.
Verify with docker exec rspec command.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `app/services/ai_manager/system_state.rb` | Add persistence methods | not started |
| `spec/services/ai_manager/system_state_spec.rb` | DOES NOT EXIST — create | not started |
| `app/services/ai_manager/system_orchestrator.rb` | Call load/save at right lifecycle points | pending |

### Prerequisites Completed
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read audit report
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
- SystemState has save_state() and load_state(cache_key) methods
- State survives across separate SystemState instances via Redis cache
- TTL set to prevent stale data accumulation
- Cache key is a stable literal — never derived from a potentially nil value
- New spec file tests persistence round-trip
- All specs pass: 0 failures

### Critical Gotchas I Will Avoid
- ❌ Creating DB model/migration — instead ✅ Use Rails.cache (Redis)
- ❌ Assuming singleton — instead ✅ Explicit save/load methods
- ❌ No TTL — instead ✅ Set 1-hour expiration
- ❌ shared_context.id as cache key — instead ✅ Use stable literal key

---

**SYNTHESIS COMPLETE.** Ready to proceed.
```

---

## Problem Statement
`SystemState` stores all state in instance variables:
```ruby
@total_resources = {}
@settlement_states = {}
@system_health = {}
@strategic_objectives = []
@dependencies = {}
@economic_balance = {}
```
When a new `SystemOrchestrator` instance is created (e.g., between requests), all this state is lost. The orchestrator cannot track trends, maintain strategic objectives across ticks, or remember resolved conflicts.

**Current behavior**: State resets to empty on every orchestrator initialization.
**Expected behavior**: State persists in Redis cache, loaded on initialization, saved after updates.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/ai_manager/system_state.rb` | Add persistence layer | New `save_state`, `load_state` methods |
| `spec/services/ai_manager/system_state_spec.rb` | DOES NOT EXIST — create | Entire file |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/services/ai_manager/system_orchestrator.rb` | Understand lifecycle — where to call load/save |
| `config/environments/test.rb` | Verify Rails.cache configuration in test env |

### Migration
- [ ] No migration needed (Redis caching, no schema change)

---

## Implementation Steps

### Step 1 — Create spec file from scratch

Create `spec/services/ai_manager/system_state_spec.rb`:

```ruby
require 'rails_helper'

RSpec.describe AIManager::SystemState, type: :service do
  let(:state) { described_class.new }
  let(:cache_key) { 'test_system_state' }

  before { Rails.cache.delete(cache_key) }
  after  { Rails.cache.delete(cache_key) }

  describe '#save_state / #load_state' do
    it 'persists and restores state across instances' do
      state.total_resources = { minerals: 500, energy: 300 }
      state.strategic_objectives = ['expand_to_mars']
      state.save_state(cache_key)

      new_state = described_class.new
      new_state.load_state(cache_key)

      expect(new_state.total_resources).to eq({ minerals: 500, energy: 300 })
      expect(new_state.strategic_objectives).to eq(['expand_to_mars'])
    end

    it 'returns false when no cached state exists' do
      result = state.load_state('nonexistent_key')
      expect(result).to be false
    end

    it 'overwrites previous cache entry on re-save' do
      state.total_resources = { minerals: 100 }
      state.save_state(cache_key)

      state.total_resources = { minerals: 999 }
      state.save_state(cache_key)

      new_state = described_class.new
      new_state.load_state(cache_key)
      expect(new_state.total_resources[:minerals]).to eq(999)
    end
  end

  describe '#analyze_system_health' do
    it 'returns a hash with health metric keys' do
      result = state.analyze_system_health
      expect(result).to be_a(Hash)
    end

    it 'returns numeric values for all health metrics' do
      result = state.analyze_system_health
      result.each_value do |v|
        expect(v).to be_a(Numeric)
      end
    end
  end
end
```

### Step 2 — Add persistence methods to SystemState

Add `save_state` and `load_state` using Rails.cache:

```ruby
CACHE_TTL = 1.hour.freeze

def save_state(cache_key)
  state_data = {
    total_resources:     @total_resources,
    settlement_states:   @settlement_states,
    system_health:       @system_health,
    strategic_objectives: @strategic_objectives,
    dependencies:        @dependencies,
    economic_balance:    @economic_balance,
    saved_at:            Time.current.iso8601
  }
  Rails.cache.write(cache_key, state_data, expires_in: CACHE_TTL)
  Rails.logger.info "[SystemState] Saved state to cache key #{cache_key}"
end

def load_state(cache_key)
  state_data = Rails.cache.read(cache_key)
  return false unless state_data

  @total_resources      = state_data[:total_resources]      || {}
  @settlement_states    = state_data[:settlement_states]    || {}
  @system_health        = state_data[:system_health]        || {}
  @strategic_objectives = state_data[:strategic_objectives] || []
  @dependencies         = state_data[:dependencies]         || {}
  @economic_balance     = state_data[:economic_balance]     || {}

  Rails.logger.info "[SystemState] Loaded state from cache key #{cache_key}"
  true
end
```

### Step 3 — Wire into SystemOrchestrator lifecycle

Use a stable literal cache key — NOT `shared_context.id` which may be nil:

```ruby
# In initialize, after creating @system_state:
@system_state.load_state("ai_manager/system_state/global")

# In orchestrate_system, at the end:
@system_state.save_state("ai_manager/system_state/global")
```

If the orchestrator ever becomes settlement-scoped, use the settlement's database id:
```ruby
# Settlement-scoped variant (only if orchestrator has a real settlement record):
@system_state.load_state("ai_manager/system_state/settlement_#{@settlement.id}")
```

### Step 4 — Verify with RSpec

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/system_state_spec.rb --format documentation 2>&1' | tail -20
```

Expected result: All examples pass, 0 failures.

### Step 5 — Run integration spec to confirm no regressions

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/manager_system_orchestrator_integration_spec.rb --format documentation 2>&1' | tail -20
```

---

## Acceptance Criteria
- [ ] `SystemState` has `save_state(cache_key)` and `load_state(cache_key)` methods
- [ ] Uses Rails.cache with TTL of 1 hour
- [ ] SystemOrchestrator uses stable literal cache key (never `shared_context.id`)
- [ ] SystemOrchestrator calls load on init, save after orchestration
- [ ] New spec file tests persistence round-trip with real assertions (not hollow comments)
- [ ] Isolation run: 0 failures for `system_state_spec.rb`
- [ ] No regressions in integration spec

---

## Stop Conditions — escalate to user immediately if:
- Rails.cache is not available in test environment (may need configuration)
- Fix causes new failures in specs you did not touch
- Same failure persists after two attempts
- Any architectural decision is required

---

## Commit Instructions
```bash
git add galaxy_game/app/services/ai_manager/system_state.rb
git add galaxy_game/app/services/ai_manager/system_orchestrator.rb
git add galaxy_game/spec/services/ai_manager/system_state_spec.rb
git commit -m "architecture: add Redis persistence to SystemState via Rails.cache with TTL"
git push
```

---

## Dependencies
**Blocked by**: `2026-06-30-HIGH-DATA-SETTLEMENT-MANAGER-INTEGRATION.md` (Task 2 — real data should flow before persisting)
**Blocks**: `2026-06-30-MEDIUM-REFACTOR-STRATEGIC-PLANNER-EXTRACTION.md` (StrategicPlanner needs persistent state)
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
