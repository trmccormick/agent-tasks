[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase5+/2026-06-28-HIGH-ARCHITECTURE-STRATEGY-SELECTOR-V2-BRIDGE.md]
[RATIONALE: Already in phase5+ — Luna simulation calibration, no move needed]
---
status: completed
priority: HIGH
type: architecture
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: false
commit_hash: 4441a74d
---

# TASK: StrategySelector → TaskExecutionEngineV2 Bridge (Full Cutover)
**Status**: BACKLOG — unblocked, dispatch after Phase A+B lands
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-06-28

## Architectural Decision (confirmed)
**Full cutover.** `:resource_acquisition` always routes through `TaskExecutionEngineV2`.
The legacy `ServiceCoordinator.acquire_resource()` path is decommissioned, not kept as a
fallback. This is a deliberate choice over a feature-flagged opt-in — accept the larger
single change in exchange for not running two parallel resource-acquisition systems
indefinitely.

## 🔴 Required Synthesis Pre-Check (before writing any code)
1. **Confirm `execute_resource_task` does not already exist** (expected: confirmed absent
   per prior research) — this task includes *building* that entrypoint, not stubbing it.
2. **Grep for all callers of `ServiceCoordinator.acquire_resource()`** across the codebase
   before removing it. If anything other than `StrategySelector` calls it, STOP and report
   back before deleting — decommissioning may need to be scoped down to "stop calling it
   from StrategySelector" rather than "delete the method."
3. **Verify `Market::NpcPriceCalculator` before depending on it.** Confirm via grep
   whether this class exists, and if so, whether it has a `calculate_bid(settlement,
   resource)` method (or equivalent) that returns a usable price.
   - If it exists and matches: use it for the request_import price check.
   - If it does NOT exist, or doesn't do what's described: this contradicts the original
     research's finding that no market-price-threshold check exists in current code.
     STOP and report back rather than building against an assumed method signature —
     do not invent the class.
4. **Define the price threshold concretely.** No acronym ("EAP" or otherwise) should be
   taken as self-explanatory. If a threshold concept already exists somewhere real
   (config value, constant, settlement field), use it and name it explicitly in your
   synthesis report. If it doesn't exist, propose a concrete default and flag it for
   Tracy's approval before treating it as final — don't silently invent a number.
5. **request_import is net-new** — build it inside `TaskExecutionEngineV2#execute_effect`
   as Gemini's clarification confirms, bridging to whatever logistics/purchase-order path
   pre-check #3 confirms is real. No pre-existing central market controller to wire into.
Post findings in your synthesis report before implementing.

## Context
Depends on `2026-06-28-HIGH-FEATURE-ESCALATION-SERVICE-RESOURCE-SHORTAGE-EXTENSION.md`
landing first — `handle_resource_shortage` must exist before StrategySelector can call it.

## Implementation Steps
1. Add `execute_via_task_engine(action, settlement)` to `strategy_selector.rb`.
2. Route `:resource_acquisition` through it exclusively — no flag, no fallback branch.
3. Build the real `execute_resource_task(...)` entrypoint on `TaskExecutionEngineV2`
   (confirmed not to exist) mapping StrategySelector intents to V2 effect actions.
4. Add `"request_import"` to `TaskExecutionEngineV2#execute_effect`'s case statement —
   built fresh per pre-check #3 above.
5. Remove (or scope down per pre-check #2) `ServiceCoordinator.acquire_resource()`.

## Stop Conditions
- Pre-check #2 finds other callers → stop, report, don't delete
- Pre-check #3 can't confirm a real data source for `request_import` → stop, report,
  don't invent one
- Any change requires touching more files than listed above → stop, report

## Dependencies
**Blocked by**: 2026-06-28-HIGH-FEATURE-ESCALATION-SERVICE-RESOURCE-SHORTAGE-EXTENSION.md
**Blocks**: none yet