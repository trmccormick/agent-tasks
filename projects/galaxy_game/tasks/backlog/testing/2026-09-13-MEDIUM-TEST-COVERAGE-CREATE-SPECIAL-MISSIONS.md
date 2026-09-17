---
task_id: "2026-09-13-MEDIUM-TEST-COVERAGE-CREATE-SPECIAL-MISSIONS"
title: "Add Direct Test Coverage for create_special_missions_for_critical_needs"
status: backlog
priority: medium
project: galaxy_game
area: testing
created_at: "2026-09-13"
tags: [test-coverage, ai-manager, decision-tree, evaluate_strategy]

---

## Context

`AIManager::DecisionTree#create_special_missions_for_critical_needs` (line ~278 in `decision_tree.rb`) calls `Market::NpcPriceCalculator.evaluate_strategy` and uses the result to compute mission rewards. This path has **no direct test coverage** — `decision_tree_spec.rb` only tests `#make_decisions` routing, not this method.

The fix commit `1c684a37` wired three call sites to `evaluate_strategy`, but the decision_tree spec was never updated. The 28 calculator spec tests cover `evaluate_strategy` in isolation; the 3 resource_acquisition_service tests cover that service's wiring. The decision_tree path is a gap.

## Target File

- `galaxy_game/spec/services/ai_manager/decision_tree_spec.rb`

## What to Test

Add a new `describe '#create_special_missions_for_critical_needs'` block with:

### 1. Happy path — reference_cost present
- Stub `identify_critical_shortages` to return `{ 'O2' => 100 }`
- Stub `Market::NpcPriceCalculator.evaluate_strategy` to return an OpenStruct with `reference_cost: 50.0`
- Verify `SpecialMission.create!` is called with:
  - `reward_gcc: 50.0 * 100 * 1.5 = 7500.0` (EAP price × quantity × 1.5 urgency bonus)
  - `bonus_multiplier: 2.0`
  - `status: :open`
  - `expires_at: 24.hours.from_now`

### 2. Guard path — reference_cost nil
- Stub `evaluate_strategy` to return `OpenStruct.new(reference_cost: nil)`
- Verify `SpecialMission.create!` is **not** called (the `next unless result&.reference_cost` guard)

### 3. Multiple resources
- Stub `identify_critical_shortages` to return `{ 'O2' => 100, 'H2O' => 50 }`
- Verify two `SpecialMission.create!` calls with correct per-resource rewards

## Stop Conditions

- New tests pass (no existing tests broken)
- No changes to production code
- No task file moves

## Return Format

Report:
- Number of new examples added
- Any failures or unexpected behavior discovered
- Commit hash
