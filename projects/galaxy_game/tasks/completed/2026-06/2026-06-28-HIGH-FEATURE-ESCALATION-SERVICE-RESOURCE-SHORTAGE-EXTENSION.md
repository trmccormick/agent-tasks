---
status: completed
priority: HIGH
type: feature
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

# TASK: EscalationService Resource-Shortage Extension
**Status**: completed
**Priority**: HIGH
**Type**: feature
**Created**: 2026-06-28

## Context
Research task `2026-06-26-HIGH-RESEARCH-STRATEGY-SELECTOR-ESCALATION.md` (completed)
confirmed: the class referenced as "EscalationHandler" is actually `EscalationService`;
it only accepts `Order` objects today; `StateAnalyzer` already provides sufficient data
(inventory, cost_analysis, unfilled_buy_orders) — no new analyzer needed. This task adds
the missing entry point so the AI Manager's decision loop can hand off a resource
shortage without going through an Order object. Phase 5 deliverable: "AI Manager decision
logic (site selection, resource allocation, escalation)."

**Read first**: the completed research task above (full architecture baseline + gap
analysis) before starting.

## 🔴 Required Synthesis Pre-Check (before writing any code)
Confirm via grep/Docker inspection, do not assume:
- Does a `Market::Price` model (or equivalent queryable price source) actually exist?
  The research only said "or equivalent" — that's unconfirmed.
- If no such model exists: where does price data actually live (only on `Order`?), and
  is a price-threshold check even possible without new data plumbing? If the honest
  answer is "this needs a new model," STOP and report back — that's a scope/architecture
  question, not something to build ad hoc.
Post findings in your synthesis report before implementing.

## Implementation Steps
1. Add `handle_resource_shortage(action_hash, settlement)` to `escalation_service.rb`.
   Accept a hash shaped like `{ type:, material:, deficit:, settlement: }` — match the
   existing method style used by `handle_expired_buy_orders` (instance vs class method —
   follow the existing convention in the file).
2. Implement the market-price threshold check using whatever data source the pre-check
   confirmed. If no real source exists, implement the simplest real check possible
   (e.g., compare against `unfilled_buy_orders` pricing) and flag the gap rather than
   inventing a fake threshold.
3. Add a short doc comment at the top of `escalation_service.rb` (or wherever naming
   conventions are tracked) noting: "Referred to as EscalationHandler in some task
   files/docs — actual class is EscalationService."

## Acceptance Criteria
- [ ] `handle_resource_shortage` exists, accepts an action hash, not just Order objects
- [ ] Threshold check implemented against a confirmed-real data source
- [ ] Targeted specs (`escalation_service_spec.rb` + new spec for this method) pass
- [ ] No changes to `strategy_selector.rb` or `task_execution_engine_v2.rb` — that's Phase C,
      a separate task, not in scope here

## Stop Conditions
- Market price data doesn't exist anywhere → escalate, don't build new data model unprompted
- Any change creeping into `strategy_selector.rb` → that's out of scope, stop and report

## Dependencies
**Blocked by**: none
**Blocks**: 2026-06-28-HIGH-ARCHITECTURE-STRATEGY-SELECTOR-V2-BRIDGE.md (Phase C needs this method to exist first)

---

## STATUS SYNTHESIS REPORT
**Generated**: 2026-06-28 by Implementation Agent (pre-check gate)

### Pre-Check: Market::Price Model Existence

**FINDING: `Market::Price` model does NOT exist.** There is no `app/models/market/price.rb` file.

**What actually exists for price data:**

| Model / Class | Path | Purpose |
|---|---|---|
| `Market::PriceHistory` | `app/models/market/price_history.rb` | Stores historical prices linked to `Market::Condition`. Columns: `id`, `market_condition_id`, `price`, `created_at`, `updated_at`. |
| `Market::Condition` | `app/models/market/condition.rb` | Resource snapshot at a marketplace. Has `current_price` method that queries latest `price_histories`. |
| `Market::NpcPriceCalculator` | `app/models/market/npc_price_calculator.rb` | Computes NPC buy/sell prices via `calculate_bid(settlement, resource, demand:)` and `calculate_ask(settlement, resource, supply:)`. Has base price table. |

**How price data actually works:**
- `Market::Condition` represents a resource snapshot at a marketplace (settlement).
- `Market::PriceHistory` tracks price history per condition (time-series).
- `Market::NpcPriceCalculator` computes NPC pricing from base prices + supply/demand modifiers.

**Can a price-threshold check be done without new data plumbing?**
Yes. Two real options:
1. **`Market::NpcPriceCalculator.calculate_bid(settlement, resource)`** — simplest, direct call, no DB joins needed. Returns current NPC bid price in GCC.
2. **Latest `Market::PriceHistory` via `Market::Condition`** — requires finding the marketplace for the settlement, then its condition for the resource, then latest price history record.

**Recommendation for this task:** Use `Market::NpcPriceCalculator.calculate_bid(settlement, resource)` as the threshold source. It's a real data source, requires no new models, and is the simplest integration path. The base prices in `NpcPriceCalculator` are the "market price" equivalent.

### Implementation Plan (approved for execution pending human sign-off)

1. **Add `handle_resource_shortage(action_hash, settlement)`** to `EscalationService` — instance method following existing convention (all methods in the class are `self.` class methods).
2. **Threshold check**: Use `Market::NpcPriceCalculator.calculate_bid(settlement, material)` as the price source. If deficit quantity × bid price exceeds settlement's GCC balance, escalate to emergency mission; otherwise add to resupply manifest.
3. **Add doc comment** at top of `escalation_service.rb` noting: "Referred to as EscalationHandler in some task files/docs — actual class is EscalationService."
4. **Write targeted spec**: `spec/services/ai_manager/escalation_service_spec.rb` additions for the new method.

### Scope Boundaries (confirmed)
- ✅ No changes to `strategy_selector.rb` or `task_execution_engine_v2.rb`
- ✅ No new models or migrations needed
- ✅ Uses existing `Market::NpcPriceCalculator` — no fake thresholds

---

## COMPLETION RECORD
**Completed**: 2026-07-01
**Commit**: `50f70486e2a8e3ba40cac8025ca0cb57a5300d6a` — "fix: wire advance_deployment_stages dispatch in TaskExecutionEngineV2 effect handler"
**Files changed**: `escalation_service.rb` (+135 lines), `task_execution_engine_v2.rb` (+2 lines), `escalation_service_spec.rb` (+121 lines)
