## STATUS SYNTHESIS REPORT

**Task**: 2026-09-11-HIGH-ARCHITECTURE-ACQUISITION-WIRE-EVALUATE-STRATEGY
**Status**: backlog → active
**Date**: 2026-09-11

### What I'm About to Do
Remove all three dead `calculate_eap_ceiling` calls (ResourceAcquisitionService:140, decision_tree.rb:284, special_mission_service.rb:8). Replace each with `Market::NpcPriceCalculator.evaluate_strategy(material:, location:, context:)` consumption. Create focused specs from scratch where none exist. Do not implement the full player-first decision tree. Do not modify the calculator API.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `galaxy_game/app/services/ai_manager/resource_acquisition_service.rb` | Dead call at line 140 | pending |
| `galaxy_game/app/services/ai_manager/decision_tree.rb` | Dead call at line 284 | pending |
| `galaxy_game/app/services/special_mission_service.rb` | Dead call at line 8 | pending |
| `galaxy_game/app/services/market/npc_price_calculator.rb` | evaluate_strategy (read-only) | pending |
| `galaxy_game/app/services/ai_manager/escalation_service.rb` | Spine; legacy bid/ask — leave unchanged | pending |
| `galaxy_game/spec/services/market/npc_price_calculator_spec.rb` | Must remain green | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ (mv + git add, find output confirmed single copy)
- ✅ Step 0: YAML status updated backlog → active
- ✅ Read this task file and gotchas
- ✅ Confirmed evaluate_strategy is live at `galaxy_game/app/services/market/npc_price_calculator.rb` line 67
- ✅ Verified all three dead call sites with exact line numbers

### Expected Outcomes
- Zero remaining `calculate_eap_ceiling` references in the three primary files
- Strategy cost checks use `evaluate_strategy`
- No new service class; calculator API unchanged
- Focused specs green; calculator suite still green
- Full decision tree still deferred

### Critical Gotchas I Will Avoid
- ❌ Resurrect calculate_eap_ceiling on calculator — instead ✅ consume evaluate_strategy
- ❌ Fix only one of three call sites — instead ✅ fix all three
- ❌ Full buy-order / mission / cycler implementation — instead ✅ wiring + crash fix only

---
**SYNTHESIS COMPLETE.** Ready to proceed.
