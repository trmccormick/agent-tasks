---
status: active
priority: CRITICAL
type: refactor
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: false
---

# TASK: Extract orbital_resupply_cycle from TaskExecutionEngine

**Status**: ACTIVE
**Priority**: CRITICAL
**Type**: refactor
**Created**: 2026-05-27
**Last Updated**: 2026-05-27

---

## Local Worker Triage Report

- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS
- **MVP Alignment**: VALID — hardcoded Luna/L1 logic in TaskExecutionEngine poisons AI Manager pattern learning and blocks correct Luna settlement testing
- **MVP Impact Note**: `orbital_resupply_cycle` hardcodes lunar settlement name lookup and ibeam/panel materials — bypasses blueprint-driven logic entirely
- **Action Line**: READY FOR CLOUD HANDOFF

---

## Agent Assignment

**Assigned To**: GPT-4.1 0x
**Why This Agent**: Surgical extraction, well-defined scope, no architecture decisions required
**Supervision Level**: Watched carefully

---

## Context

`TaskExecutionEngine` is meant to be a pure mission task runner with no knowledge of specific worlds, materials, or construction scenarios. Currently it contains a class method `orbital_resupply_cycle` (line 4) that hardcodes Luna/L1 logic — finding a lunar settlement by name, checking ibeam and modular_structural_panel_base surplus, and scheduling a material ferry. This method bypasses pattern learning and forces hardcoded outcomes during AI Manager testing.

`AIManager::LogisticsCoordinator` already exists at `app/services/ai_manager/logistics_coordinator.rb` and handles route optimization and transfer scheduling. The extracted method should live in a new `OrbitalConstructionLogisticsService` that is blueprint-driven and world-neutral.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules

---

## Problem Statement

**Current behavior**: `TaskExecutionEngine.orbital_resupply_cycle` contains hardcoded references to:
- `Settlement::OrbitalSettlement.where(settlement_type: :station).first` — takes first station blindly
- `BaseSettlement.where("name ILIKE ?", "%lunar%")` — hardcoded name lookup
- `check_material_surplus` for `ibeam` and `modular_structural_panel_base` — hardcoded material names
- `schedule_material_ferry` — hardcoded ferry logic

**Expected behavior**: `TaskExecutionEngine` contains no world-specific logic. Material ferry logic lives in `OrbitalConstructionLogisticsService`, driven by `OrbitalConstructionProject.required_materials` minus `delivered_materials`, world-neutral.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/ai_manager/task_execution_engine.rb` | Pure task runner — remove orbital_resupply_cycle | lines 4-30 |
| `app/services/construction/orbital_construction_logistics_service.rb` | New service — create this file | new |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/services/ai_manager/logistics_coordinator.rb` | Understand existing logistics patterns |
| `app/models/orbital_construction_project.rb` | required_materials, delivered_materials fields |
| `app/models/settlement/orbital_settlement.rb` | OrbitalSettlement structure |
| `app/models/settlement/base_settlement.rb` | BaseSettlement structure |

### Migration
- [ ] No migration needed

---

## Implementation Steps

### Step 1 — Read current orbital_resupply_cycle in full

```bash
grep -n "orbital_resupply_cycle\|check_material_surplus\|schedule_material_ferry" galaxy_game/app/services/ai_manager/task_execution_engine.rb
```

Paste full method bodies. Understand exactly what is being moved before touching anything.

### Step 2 — Synthesis Report

```
SYNTHESIS REPORT
Current orbital_resupply_cycle: [paste full method]
Current check_material_surplus: [paste full method]
Current schedule_material_ferry: [paste full method]
Lines to remove from TaskExecutionEngine: [line numbers]
Proposed OrbitalConstructionLogisticsService structure: [outline]
Any callers of orbital_resupply_cycle outside TaskExecutionEngine: [grep result]
READY TO APPLY? — waiting for approval
```

Check for callers:
```bash
grep -rn "orbital_resupply_cycle" galaxy_game/app/ galaxy_game/spec/
```

### Step 3 — Create OrbitalConstructionLogisticsService (after approval)

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
      # Ferry scheduling logic extracted from orbital_resupply_cycle
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

### Step 4 — Remove orbital_resupply_cycle from TaskExecutionEngine

Remove lines 4-30 (the class method and its private helpers) from `task_execution_engine.rb`. Verify the remaining file is syntactically valid.

### Step 5 — Mark 3 specs pending

In `spec/services/ai_manager/task_execution_engine_spec.rb` find specs testing `orbital_resupply_cycle` and mark them pending:

```ruby
xit 'schedules a material ferry mission' do
  pending 'orbital_resupply_cycle extracted to OrbitalConstructionLogisticsService — see 2026-05-27-CRITICAL-REFACTOR-EXTRACT-ORBITAL-RESUPPLY-CYCLE.md'
  # original test body unchanged
end
```

### Step 6 — Verify

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/task_execution_engine_spec.rb 2>&1 | tail -20'
```

Expected: 0 failures, pending specs show as pending not failing.

---

## Acceptance Criteria
- [ ] `orbital_resupply_cycle` removed from TaskExecutionEngine
- [ ] `check_material_surplus` and `schedule_material_ferry` helpers removed
- [ ] `OrbitalConstructionLogisticsService` created — blueprint-driven, world-neutral
- [ ] No hardcoded world names, settlement names, or material names remain
- [ ] 3 specs marked pending with correct reference
- [ ] TaskExecutionEngine spec passes — 0 failures
- [ ] No regressions in full suite

---

## Stop Conditions — escalate immediately if:
- `orbital_resupply_cycle` is called from outside TaskExecutionEngine — map all callers before removing
- `OrbitalConstructionProject` does not have `required_materials` or `delivered_materials` fields — stop, report
- Removing the method causes immediate load errors — report exact error

---

## Commit Instructions
```bash
git add galaxy_game/app/services/ai_manager/task_execution_engine.rb
git add galaxy_game/app/services/construction/orbital_construction_logistics_service.rb
git add galaxy_game/spec/services/ai_manager/task_execution_engine_spec.rb
git commit -m "refactor: extract orbital_resupply_cycle to OrbitalConstructionLogisticsService — TaskExecutionEngine now world-neutral"
git push
```

---

## Dependencies
**Blocked by**: none
**Blocks**: AI Manager pattern learning test accuracy
**Related**: `2026-05-27-HIGH-REFACTOR-VERIFY-STRUCTURE-CORE-ORBITAL-STRUCTURE.md`

---

## Completion Report
**Completed by**:
**Completion date**:
**Final test result**:

### What was changed
### Issues discovered
### Follow-up tasks needed
### Lessons learned

---

## Handoff Summary
HANDOFF SUMMARY: orbital_resupply_cycle extracted | TaskExecutionEngine world-neutral | OrbitalConstructionLogisticsService created | 3 specs pending | AI Manager testing unblocked
