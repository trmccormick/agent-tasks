---
status: backlog
priority: MEDIUM
type: research
system_domain: AI_MANAGER | NPC_ECONOMY
mvp_alignment: NPC_ECONOMY_LIFECYCLE
local_worker_safe: true
blocked_by: []
blocker_reason: "None"
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Research Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-06-MEDIUM-RESEARCH-MARKET-STABILIZATION-SERVICE-HELPER-METHODS.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-06-MEDIUM-RESEARCH-MARKET-STABILIZATION-SERVICE-HELPER-METHODS.md \
         projects/galaxy_game/tasks/active/2026-08-06-MEDIUM-RESEARCH-MARKET-STABILIZATION-SERVICE-HELPER-METHODS.md

Then update YAML status: backlog → active

Do NOT read the task file content, run any commands, or start synthesis until this is done.

READ FIRST (after Step 0): Task file contains all prerequisites, architecture gotchas, and verification steps.
```

## Problem Statement

`AIManager::MarketStabilizationService` has three methods (`handle_unsold_goods`, `handle_production_shortages`, `handle_import_shortages`) that have always been stubs since the file was first committed (commit `78da9b06`). The stubs call helper methods — we need to verify whether those helpers exist and are implemented or also stubs.

## Current State

### Three stubbed public methods:
1. **`handle_unsold_goods`** — Returns `{ action: :buyer_of_last_resort, status: :checked, purchases_made: 0 }` (always zero)
2. **`handle_production_shortages`** — Has inventory-check loop structure, calls helpers
3. **`handle_import_shortages`** — Has source-routing logic, calls helpers

### Helper methods to verify (7 total):

| # | Method | Called from | Status |
|---|--------|-------------|--------|
| 1 | `calculate_minimum_threshold(settlement, item)` | `handle_production_shortages` | **MISSING** — not defined anywhere in the file |
| 2 | `settlement_has_production_capability?(settlement, item)` | `handle_production_shortages` | EXISTS (line 106) — implemented with case statement |
| 3 | `check_active_player_production(settlement, item)` | `handle_production_shortages` | EXISTS (line 237) — queries UnitAssemblyJob |
| 4 | `produce_item_for_market(settlement, item, amount)` | `handle_production_shortages` | EXISTS (line 246) — calls ManufacturingService.manufacture |
| 5 | `identify_import_candidates(settlement)` | `handle_import_shortages` | EXISTS (line 251) — checks non_local_items inventory |
| 6 | `determine_import_source(settlement, item, amount)` | `handle_import_shortages` | EXISTS (line 267) — checks other settlements first |
| 7 | `schedule_cycler_delivery(settlement, item, amount, cycler)` | `handle_import_shortages` | EXISTS (line 302) — returns `{ scheduled: true }` stub |

### Key findings so far:
- **`calculate_minimum_threshold` is MISSING** — called but never defined. This would cause a NoMethodError at runtime if `handle_production_shortages` ever executes.
- **`schedule_cycler_delivery` is a stub** — returns `{ scheduled: true }` without actually scheduling anything
- **`settlement_has_production_capability?`, `check_active_player_production`, `produce_item_for_market`** appear implemented
- **`identify_import_candidates` and `determine_import_source`** have partial logic but may be incomplete

## Research Goals

1. **For each of the 7 helpers**: Confirm whether it exists, is implemented or stubbed
2. **Check for runtime errors**: Does `calculate_minimum_threshold` being missing cause actual failures?
3. **Trace call chains**: Are these methods actually called from any live code path?
4. **Assess completeness**: For implemented helpers, are they complete or partially implemented?

## Verification Steps

1. Search entire codebase for each helper method definition
2. Check if `handle_production_shortages` is called from any live path (would crash on missing `calculate_minimum_threshold`)
3. Verify `schedule_cycler_delivery` behavior — does it integrate with any cycler routing system?
4. Check if `identify_import_candidates` and `determine_import_source` have complete logic or are partial

## Stop Conditions

- Research is complete when all 7 helpers are classified as: EXISTS+IMPLEMENTED, EXISTS+STUB, or MISSING
- Report findings in a clear table format so a complete-or-delete decision can be made

## Completion Report Template

After completion, fill in:
- Table of all 7 helpers with status (implemented/stub/missing)
- Any runtime error risks identified
- Whether the stubbed public methods should be completed or deleted
- Any additional gaps discovered during research
