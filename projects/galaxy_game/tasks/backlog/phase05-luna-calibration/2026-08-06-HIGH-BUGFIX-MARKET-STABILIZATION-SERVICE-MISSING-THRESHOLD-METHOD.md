---
status: backlog
priority: HIGH
type: bugfix
system_domain: AI_MANAGER | NPC_ECONOMY
mvp_alignment: NPC_ECONOMY_LIFECYCLE
local_worker_safe: true
blocked_by: []
blocker_reason: "None"
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase05-luna-calibration/2026-08-06-HIGH-BUGFIX-MARKET-STABILIZATION-SERVICE-MISSING-THRESHOLD-METHOD.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/galaxy_game/tasks/backlog/phase05-luna-calibration/2026-08-06-HIGH-BUGFIX-MARKET-STABILIZATION-SERVICE-MISSING-THRESHOLD-METHOD.md \
         projects/galaxy_game/tasks/active/2026-08-06-HIGH-BUGFIX-MARKET-STABILIZATION-SERVICE-MISSING-THRESHOLD-METHOD.md

Then update YAML status: backlog → active

Do NOT read the task file content, run any commands, or start synthesis until this is done.

READ FIRST (after Step 0): Task file contains all prerequisites, architecture gotchas, and verification steps.
```

## Problem Statement — CRITICAL BUG

`calculate_minimum_threshold(settlement, item)` is called by `handle_production_shortages` in `AIManager::MarketStabilizationService` but **never defined**. This would cause a `NoMethodError` at runtime if `handle_production_shortages` is ever invoked.

Additionally, `schedule_cycler_delivery` returns `{ scheduled: true }` without any actual scheduling — it should either be implemented or explicitly stubbed with a comment indicating it's intentionally unimplemented.

## Context: Helper Method Audit (7 total)

All helpers verified against current codebase state:

| # | Method | Called from | Status |
|---|--------|-------------|--------|
| 1 | `calculate_minimum_threshold(settlement, item)` | `handle_production_shortages` | **MISSING** — called but never defined. **CRITICAL: causes NoMethodError at runtime.** |
| 2 | `settlement_has_production_capability?(settlement, item)` | `handle_production_shortages` | EXISTS (line 106) — implemented with case statement mapping items to capability checks |
| 3 | `check_active_player_production(settlement, item)` | `handle_production_shortages` | EXISTS (line 237) — queries UnitAssemblyJob for pending/in_progress jobs |
| 4 | `produce_item_for_market(settlement, item, amount)` | `handle_production_shortages` | EXISTS (line 246) — calls ManufacturingService.manufacture |
| 5 | `identify_import_candidates(settlement)` | `handle_import_shortages` | EXISTS (line 251) — checks non_local_items inventory against threshold |
| 6 | `determine_import_source(settlement, item, amount)` | `handle_import_shortages` | EXISTS (line 267) — checks other settlements first, then cycler/earth fallback |
| 7 | `schedule_cycler_delivery(settlement, item, amount, cycler)` | `handle_import_shortages` | EXISTS (line 302) — **STUB**: returns `{ scheduled: true }` without actual scheduling |

## Scope

### Fix 1 — Implement or stub `calculate_minimum_threshold`
- This is the critical bug: called but never defined
- Options: implement a real threshold calculation, or add an explicit stub with `# TODO: implement` comment if business logic is unclear
- Must not silently return nil or raise NoMethodError

### Fix 2 — Address `schedule_cycler_delivery`
- Currently returns `{ scheduled: true }` without any actual scheduling
- Decide: implement real cycler integration, or explicitly stub with a comment indicating it's intentionally unimplemented
- If implementing: integrate with existing cycler routing system if one exists

## Architecture Gotchas

- The three public methods (`handle_unsold_goods`, `handle_production_shortages`, `handle_import_shortages`) have always been stubs since initial commit `78da9b06`
- `handle_production_shortages` has inventory-check loop structure but crashes on the missing helper
- `handle_import_shortages` has source-routing logic that calls helpers 5 and 6 (which exist) and helper 7 (stub)
- The service's public `stabilize_market` method calls all three — if any settlement triggers this path, it will crash on `calculate_minimum_threshold`
- Check if any specs reference these methods before modifying

## Verification Steps

1. Run RSpec for affected services: `ai_manager/market_stabilization_service_spec.rb`
2. Verify no regressions in existing market/economy-related specs
3. Confirm `calculate_minimum_threshold` is defined (either implemented or explicitly stubbed)
4. Confirm `schedule_cycler_delivery` behavior is intentional (implemented or documented stub)

## Stop Conditions

- Task is complete when both missing/stub methods are addressed AND all related specs pass
- If business logic for threshold calculation requires human input, stop and report findings

## Completion Report Template

After completion, fill in:
- How `calculate_minimum_threshold` was resolved (implemented vs stubbed)
- How `schedule_cycler_delivery` was resolved (implemented vs documented stub)
- Files created/modified
- Test results (examples/run failures)
- Any design decisions made during implementation
