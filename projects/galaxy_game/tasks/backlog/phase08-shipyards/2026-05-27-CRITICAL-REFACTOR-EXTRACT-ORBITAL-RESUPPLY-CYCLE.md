---
status: backlog
priority: MEDIUM
type: refactor
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
created: 2026-05-27
updated: 2026-08-03
estimated_effort: 2-3 hours
blocker_for: []
---

# Task: Extract orbital_resupply_cycle from TaskExecutionEngine

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/phase8+/2026-05-27-CRITICAL-REFACTOR-EXTRACT-ORBITAL-RESUPPLY-CYCLE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv docs/new_agent/projects/galaxy_game/tasks/backlog/phase8+/2026-05-27-CRITICAL-REFACTOR-EXTRACT-ORBITAL-RESUPPLY-CYCLE.md \
         docs/new_agent/projects/galaxy_game/tasks/active/2026-05-27-CRITICAL-REFACTOR-EXTRACT-ORBITAL-RESUPPLY-CYCLE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find docs/new_agent/projects/galaxy_game/tasks -name "2026-05-27-CRITICAL-REFACTOR-EXTRACT-ORBITAL-RESUPPLY-CYCLE.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.
```

## Prerequisites

- Read `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- Read `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- Verify `OrbitalConstructionProject` has `required_materials` and `delivered_materials` fields (grep model file)

## Context

**Problem**: `TaskExecutionEngine` is meant to be a pure mission task runner with no knowledge of specific worlds, materials, or construction scenarios. Currently it contains a class method `orbital_resupply_cycle` (line 4) that hardcodes Luna/L1 logic — finding a lunar settlement by name, checking ibeam and modular_structural_panel_base surplus, and scheduling a material ferry. This method bypasses pattern learning and forces hardcoded outcomes during AI Manager testing.

**Note**: This task is a refile of the same ask from `2026-04-18-CRITICAL-ARCHITECTURE-TASK-EXECUTION-ENGINE-BLUEPRINT-DRIVEN.md` (in `review/backlog_april_2026/deprecated/`). The April task was broader; this one is surgically scoped to just the orbital_resupply_cycle extraction. Both share the same goal — no duplicate work needed.

**Expected behavior**: `TaskExecutionEngine` contains no world-specific logic. Material ferry logic lives in a new `OrbitalConstructionLogisticsService`, driven by `OrbitalConstructionProject.required_materials` minus `delivered_materials`, world-neutral.

## Architecture Gotchas

1. **Three methods to move together**: `orbital_resupply_cycle`, `check_material_surplus`, and `schedule_material_ferry` are all class methods on the same class. Remove them as a unit — don't leave orphaned references.

2. **Specs already marked pending**: 3 specs in `task_execution_engine_spec.rb` (lines 648/655/667) are already `xit` with references to this task and the April ancestor. Do NOT create new pending specs — verify these still exist and update their reference text if needed.

3. **LogisticsCoordinator is not a substitute**: `AIManager::LogisticsCoordinator` exists but handles route optimization and transfer scheduling, NOT orbital construction logistics. The extracted service is a new concern.

4. **Greenfield service**: This is not a simple extraction — it requires designing a new service from scratch. That's why priority was downgraded from CRITICAL to MEDIUM (stalled 2.5 months because of this).

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/ai_manager/task_execution_engine.rb` | Remove orbital_resupply_cycle + helpers | lines 4-30 |
| `app/services/construction/orbital_construction_logistics_service.rb` | New service — create this file | new |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/services/ai_manager/logistics_coordinator.rb` | Understand existing logistics patterns |
| `app/models/orbital_construction_project.rb` | required_materials, delivered_materials fields |
| `app/models/settlement/orbital_settlement.rb` | OrbitalSettlement structure |
| `app/models/settlement/base_settlement.rb` | BaseSettlement structure |

### Existing specs to update (not create)
| File | What to do |
|---|---|
| `spec/services/ai_manager/task_execution_engine_spec.rb` lines 630-680 | Verify 3 xit specs still reference this task; update if needed |

## Implementation Steps

### Step 0: Move Task to Active & Verify Synthesis
**PREREQUISITE — Do NOT skip:**
1. Move task from `backlog/phase8+/` → `active/`
2. Update YAML header: `status: backlog` → `status: active`
3. Commit move before writing any code
4. Complete Synthesis Report below (document current state)

### Step 1: Synthesis Report — Document Current State

```bash
grep -n "orbital_resupply_cycle\|check_material_surplus\|schedule_material_ferry" galaxy_game/app/services/ai_manager/task_execution_engine.rb
grep -rn "orbital_resupply_cycle" galaxy_game/app/ galaxy_game/spec/
```

Document in the task file:
```
## Synthesis Findings

### Current orbital_resupply_cycle (lines X-Y)
[paste full method]

### Current check_material_surplus (lines X-Y)
[paste full method]

### Current schedule_material_ferry (lines X-Y)
[paste full method]

### All callers of orbital_resupply_cycle
[grep result — should only be specs]

### Existing pending specs (verify still present)
grep -n "xit.*orbital_resupply" galaxy_game/spec/services/ai_manager/task_execution_engine_spec.rb
```

### Step 2: Create OrbitalConstructionLogisticsService

Create `app/services/construction/orbital_construction_logistics_service.rb`:

```ruby
module Construction
  class OrbitalConstructionLogisticsService
    def initialize(project)
      @project = project
      @station = project.station
    end

    def run
      return unless @station
      needed = calculate_needed_materials
      return if needed.empty?

      source_settlement = find_source_settlement(needed)
      return unless source_settlement

      schedule_ferry(source_settlement, needed)
    end

    private

    def calculate_needed_materials
      required = @project.required_materials || {}
      delivered = @project.delivered_materials || {}
      required.each_with_object({}) do |(material, qty), needed|
        shortfall = qty.to_i - delivered[material].to_i
        needed[material] = shortfall if shortfall > 0
      end
    end

    def find_source_settlement(needed)
      # World-neutral — find any settlement with surplus of needed materials
      Settlement::BaseSettlement.where.not(settlement_type: :station).find do |s|
        needed.any? { |material, _| s.inventory.current_storage_of(material).to_i > 0 }
      end
    end

    def schedule_ferry(source, needed)
      Rails.logger.info("OrbitalConstructionLogisticsService: scheduling ferry from #{source.name} to #{@station.name}")
      needed.each do |material, qty|
        surplus = source.inventory.current_storage_of(material).to_i
        amount = [surplus, qty].min
        next if amount <= 0
        Rails.logger.info("OrbitalConstructionLogisticsService: ferrying #{amount} #{material}")
      end
    end
  end
end
```

### Step 3: Remove orbital_resupply_cycle from TaskExecutionEngine

Remove lines 4-30 (the class method and its private helpers) from `task_execution_engine.rb`. Verify the remaining file is syntactically valid.

### Step 4: Update Existing Pending Specs

In `spec/services/ai_manager/task_execution_engine_spec.rb` verify the 3 existing `xit` specs (lines ~648/655/667) still reference this task correctly. Update the reference text if needed — do NOT create new pending specs.

### Step 5: Verify

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/task_execution_engine_spec.rb 2>&1 | tail -20'
```

Expected: 0 failures, pending specs show as pending not failing.

## Acceptance Criteria
- [ ] `orbital_resupply_cycle` removed from TaskExecutionEngine
- [ ] `check_material_surplus` and `schedule_material_ferry` helpers removed
- [ ] `OrbitalConstructionLogisticsService` created — blueprint-driven, world-neutral
- [ ] No hardcoded world names, settlement names, or material names remain
- [ ] 3 existing specs still marked pending with correct reference to this task
- [ ] TaskExecutionEngine spec passes — 0 failures
- [ ] No regressions in full suite

## Stop Conditions — escalate immediately if:
- `orbital_resupply_cycle` is called from outside TaskExecutionEngine — map all callers before removing
- `OrbitalConstructionProject` does not have `required_materials` or `delivered_materials` fields — stop, report
- Removing the method causes immediate load errors — report exact error

## Dependencies
**Blocked by**: none
**Blocks**: AI Manager pattern learning test accuracy
**Related**: 
- `2026-04-18-CRITICAL-ARCHITECTURE-TASK-EXECUTION-ENGINE-BLUEPRINT-DRIVEN.md` (deprecated ancestor in `review/backlog_april_2026/deprecated/`)
- `2026-05-27-HIGH-REFACTOR-VERIFY-STRUCTURE-CORE-ORBITAL-STRUCTURE.md`

## Completion Report
**Completed by**:
**Completion date**:
**Final test result**:

### What was changed
### Issues discovered
### Follow-up tasks needed
### Lessons learned

## Handoff Summary
HANDOFF SUMMARY: orbital_resupply_cycle extracted | TaskExecutionEngine world-neutral | OrbitalConstructionLogisticsService created | 3 specs pending | AI Manager testing unblocked
