---
id: 2026-06-04-HIGH-FEATURE-LUNA-PHASE1-AI-MANAGER-COST-ANALYZER-INTEGRATION
status: active
priority: HIGH
type: FEATURE
created: 2026-06-04
author: Claude (Session Strategist)
agent: Qwen3.5-27B
depends_on:
  - 2026-06-03-HIGH-BUG-FIX-TASK-EXECUTION-ENGINE-V2-LOAD-SETTLEMENT (must complete first)
blocks:
  - Luna Phase 2 (ManifestGenerator)
  - Luna Phase 3 (ShortageDetector + ImportRequestGenerator → AI Manager)
---

# Luna Phase 1 — AI Manager Cost Analyzer Integration

## Objective

Wire `Settlements::CostAnalyzer` into the AI Manager decision loop so that `StrategySelector` can evaluate settlement cost health and act on it. This is the first phase of the Luna autonomous settlement management pipeline.

Before writing any code, read the README at:
/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/README.md

---

## Background

The AI Manager entry point is:
`AIManager::Manager#advance_time` → `@strategy_selector.evaluate_next_action(settlement)`

`StrategySelector#evaluate_next_action` calls (in order):
1. `@state_analyzer.analyze_state(settlement)` — produces `state_analysis` hash
2. `generate_mission_options(settlement, state_analysis)` — produces candidate actions
3. `score_mission_options` → `perform_strategic_tradeoff_analysis` → `apply_strategic_adjustments`
4. `select_optimal_action_with_strategy` — returns the best action hash

The hook point for Phase 1 is **`generate_mission_options`** and **`execute_action`**.

`EscalationService` is NOT involved in Phase 1. It is a downstream handler for expired buy orders only (`handle_expired_buy_orders`). Do not touch it.

---

## Locked Architectural Constraints

These are non-negotiable. Flag to strategist before overriding any of them.

- All Luna-specific values must come from `operational_data` or JSON config — never hardcoded in Ruby (DECISIONS.md: No Hardcoded Luna Logic)
- `operational_data` on settlements is JSONB — always use string keys or `with_indifferent_access`, never bare symbol keys
- All persistence uses `save!` (bang method)
- Always use `GalaxyGame::Paths` constants — never `Rails.root.join` directly
- `EscalationService` entry point is `handle_expired_buy_orders` only — Phase 1 does not route through it
- HLT is the only transport until L1 shipyard. Luna HLT materials are: `['oxygen', 'nitrogen', 'methane', 'carbon_dioxide', 'ethane', 'hydrocarbons', 'co2', 'n2', 'ch4']` (confirmed in EscalationService#hlt_mission_manifest)
- `Logistics::Contract` transport method for Luna HLT: **`surface_conveyance`** — confirmed from `logistics/contract.rb` (string enum value `'surface_conveyance'`). Use this constant when creating any Contract for Luna HLT delivery. Do not read the file again for this question.

---

## Pre-Implementation Read List

Before writing any code, read these files on the host:

1. `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/services/settlements/cost_analyzer.rb`
   — Understand the public API: what method(s) does it expose, what does it return, what inputs does it require?

2. `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/services/ai_manager/state_analyzer.rb`
   — Understand what keys `analyze_state` already returns. Phase 1 must add cost-related keys without breaking existing keys.

3. ~~`/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/models/logistics/contract.rb`~~ — **RESOLVED.** Transport method confirmed as `surface_conveyance`. Do not re-read.

4. `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/services/ai_manager/strategy_selector.rb`
   — Already reviewed by strategist. Key integration points documented below.

5. Existing spec for CostAnalyzer if one exists:
   `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/spec/services/settlements/cost_analyzer_spec.rb`
   — Do not break any passing examples.

---

## Implementation Scope

### 1. StateAnalyzer — add cost analysis keys

`StateAnalyzer#analyze_state` must return cost health data that `StrategySelector` can act on. Add to the returned hash:

```ruby
cost_analysis: {
  viable: <boolean>,          # is the settlement economically viable?
  cost_pressure: <float 0..1>, # normalized pressure score (0 = fine, 1 = critical)
  recommendations: <array>    # strings or symbols — what CostAnalyzer recommends
}
```

Call `Settlements::CostAnalyzer` here. Do not call it directly from `StrategySelector` — state analysis is `StateAnalyzer`'s responsibility.

If `CostAnalyzer` raises or returns nil, rescue and return safe defaults:
```ruby
cost_analysis: { viable: true, cost_pressure: 0.0, recommendations: [] }
```

### 2. StrategySelector#generate_mission_options — add cost action

Add a branch for cost pressure after the existing infrastructure block:

```ruby
if state_analysis.dig(:cost_analysis, :cost_pressure).to_f >= 0.6
  options << {
    type: :cost_reduction,
    priority: state_analysis.dig(:cost_analysis, :cost_pressure).to_f >= 0.85 ? :critical : :high,
    recommendations: state_analysis.dig(:cost_analysis, :recommendations) || [],
    rationale: "Settlement cost pressure requires intervention"
  }
end
```

Threshold values (0.6 high, 0.85 critical) are a starting point — they must come from `operational_data` or a named constant, not magic numbers inline. Use a constant in `StrategySelector` or read from settlement `operational_data` if present.

### 3. StrategySelector#viable_action? — add cost_reduction case

```ruby
when :cost_reduction
  state_analysis.dig(:cost_analysis, :cost_pressure).to_f >= 0.6
```

Without this the action will always be considered viable regardless of state (falls through to `else true`). This must be added.

### 4. StrategySelector#execute_action — add cost_reduction case

```ruby
when :cost_reduction
  execute_cost_reduction(action, settlement)
```

And add the private method:

```ruby
def execute_cost_reduction(action, settlement)
  Rails.logger.info "[StrategySelector] Initiating cost reduction for #{settlement.name} — recommendations: #{action[:recommendations].join(', ')}"
  # Phase 1: log and surface recommendations only.
  # Phase 2 will wire ManifestGenerator for import cost reduction.
  # Phase 3 will wire ShortageDetector for shortage-driven cost actions.
  true
end
```

Phase 1 execution is intentionally shallow — log and return true. The full execution pipeline comes in Phase 2 and 3.

### 5. StrategySelector#apply_strategic_adjustments — add cost_reduction multiplier

In the `case focus` block, extend `:building_focus` or add a new `:cost_focus` branch:

```ruby
when :cost_focus
  strategic_multiplier = option[:type] == :cost_reduction ? 1.3 : 0.8
```

And add `:cost_focus` as a valid key in `determine_overall_strategic_focus`'s `focus_counts` hash.

---

## Spec Requirements

Write or extend specs for:

1. `spec/services/ai_manager/state_analyzer_spec.rb`
   — Test that `analyze_state` returns a `cost_analysis` key with correct shape when `CostAnalyzer` returns pressure data.
   — Test the rescue path: when `CostAnalyzer` raises, `cost_analysis` defaults to `{ viable: true, cost_pressure: 0.0, recommendations: [] }`.

2. `spec/services/ai_manager/strategy_selector_spec.rb`
   — Test that `generate_mission_options` includes a `:cost_reduction` option when `cost_pressure >= 0.6`.
   — Test that it does NOT include `:cost_reduction` when `cost_pressure < 0.6`.
   — Test that `viable_action?` gates `:cost_reduction` correctly.
   — Test that `execute_action` handles `:cost_reduction` without raising.

Do not stub `CostAnalyzer` in integration specs. Stubs only for external dependencies (DECISIONS.md testing principle).

---

## What NOT to Do

- Do not touch `EscalationService` — it is not in scope for Phase 1
- Do not touch `Logistics::ManifestGenerator` — that is Phase 2
- Do not touch `Logistics::ShortageDetector` or `Logistics::ImportRequestGenerator` — that is Phase 3
- Do not hardcode Luna resource names or transport methods in Ruby — read from config or confirm from model
- Do not use symbol keys on `operational_data` JSONB fields — string keys only
- Do not use `Rails.root.join` — use `GalaxyGame::Paths` constants
- Do not create a new action type in `generate_mission_options` without also adding it to `viable_action?` AND `execute_action`

---

## Task Lifecycle

Move this task to active before writing any code:

```
git mv tasks/backlog/2026-06/2026-06-04-HIGH-FEATURE-LUNA-PHASE1-AI-MANAGER-COST-ANALYZER-INTEGRATION.md \
        tasks/active/2026-06/2026-06-04-HIGH-FEATURE-LUNA-PHASE1-AI-MANAGER-COST-ANALYZER-INTEGRATION.md
```

Update YAML: `status: backlog` → `status: active`

Commit: `git commit -m "Start task: Luna Phase 1 AI Manager cost analyzer integration"`

On completion, produce a synthesis report and stop. Do not move to completed without human or strategist review.

---

## Synthesis Report Format

When complete, append to this file:

```
## Completion Report
- Date:
- Agent:
- Files modified:
- New specs added:
- Specs run (command + result):
- CostAnalyzer public API confirmed as:
- Logistics::Contract transport method for Luna HLT confirmed as:
- Open questions for strategist:
```

---

## Open Questions (Strategist Flagged)

1. ~~**`Logistics::Contract` transport method for Luna HLT**~~ — **RESOLVED 2026-06-04.** Confirmed `surface_conveyance` (string enum). No further action needed.

2. **`Settlements::CostAnalyzer` public API** — not reviewed by strategist yet. Executor reads the file first and documents the public method signature in the synthesis report. If the API shape differs from what Phase 1 assumes, flag before writing integration code.

3. **Cost pressure thresholds** — 0.6 (high) and 0.85 (critical) are working values. If `CostAnalyzer` already returns a categorized severity, use that instead of applying a second threshold layer.
