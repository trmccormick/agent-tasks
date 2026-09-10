# Session Handoff — Economy/Pricing Multi-Agent Thread
**Date**: 2026-09-09
**Written by**: Claude (coordination/review session, ending on context limit)

---

## Where Things Stand

### In flight, check this first
`2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-EVALUATE.md` was dispatched to a Qwen implementation session. It adds `.evaluate_strategy(material:, location:, context:)` to the **existing** `Market::NpcPriceCalculator` (NOT a new `AIManager::NpcPriceCalculator` — an earlier draft proposed that and was corrected before dispatch, since the existing class already has 14+ live callers including `EscalationService` and `ResourceAcquisitionService`). **Status as of this handoff: results not yet seen.** Whoever picks this up next should check on it first.

### Resolved this session
- **SettlementFees mystery, fully resolved**: it's real, complete code (120 lines, 30 tests) — but lives only on the unmerged `market-fee-hold` branch (commit `7db7566c`, Aug 10), never landed on main. Not a fabrication, not a regression. If fee logic is needed for Phase 2, merging/cherry-picking that branch is the stronger option vs. `Market::TransactionFee` (main, Jan 2026, zero callers, dead code — unrelated to SettlementFees, not a replacement).
- **Financial::LedgerEntry/LedgerManager, corrected**: an economy-documentation audit initially claimed these don't exist — wrong. They're real (`LedgerEntry` model fully implemented; `VirtualLedgerService.record_transfer`/`.off_market_volume`/`.corporate_transfers` all working; `Account.can_overdraft?` implemented). Genuinely incomplete: `LedgerManager.reconcile_npc_debts` and `settle_with_usd` are placeholder/skeletal, and the `npc_to_npc` scope is a stub that currently returns `.all` (not actually filtered).
- **Pattern to watch**: two "doesn't exist" audit claims this week both turned out wrong. Worth double-checking search methodology (right directory/namespace, e.g. `app/models/concerns/` and `app/models/financial/` were both initially missed) before trusting a future absence claim.

### Real, unresolved gaps
- **Material production-chain gap**: only 10 of 207 material JSON files have populated `production.input_materials`. Iron is the concrete broken example — empty `input_materials`, and its `sources.natural`/`sources.synthetic` fields reference `iron_ore`/`magnetite`, neither of which exist as material files. This blocks any pricing model that wants cost derived from a real input chain (i.e., blocks the eventual point of wiring up `Manufacturing::CostCalculator`, itself still dead code/never instantiated). No task filed yet.
- **Blueprint completeness**: 3 mk1 blueprints (`asteroid_hollowing_laser_mk1.1`, `planetary_umbilical_hub_mk1`, `planetary_power_management_unit_mk1`) have zero `operational_data_reference`. Still needs the spot-check: does their `unit_type`/`category` map to a real, instantiated `Units::` class via `BlueprintLookupService`? Don't dispatch the "write operational data for 23 blueprints" follow-up task until this is checked — the original 23-blueprint count came from a flawed methodology (zero `app/` references ≠ orphaned, since blueprints load generically).
- **EAP×0.90/0.80 was always meant to be a narrow Luna-only NPC bootstrap mechanism** (Tracy's clarification), not a general local-pricing formula. Doesn't generalize to Mars/Venus/beyond as-is.

---

## Instructions for Each Agent, Next Session

### Perplexity
Session got long this round — start fresh if needed, using its own prepared handoff summary (infant-industry/predatory-pricing/utility-cost-recovery research + the functional pricing form it derived) for context.

**Next task, ready to send**: the material-production-chain research flagged earlier — real-world smelting/refining chains for common materials (iron/steel as the starting example) to inform what `production.input_materials` *should* contain once someone fixes the 197 material files that currently have it empty. This feeds both the economy-pricing work and whoever eventually fixes the material JSON schema — send it independently of anything else, no dependencies.

### Qwen (planning/implementation)
1. Finish `2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-EVALUATE.md` if not already done.
2. Once done: spot-check the 3 blueprints above via `BlueprintLookupService` (does `unit_type`/`category` map to a real `Units::` class) before any operational-data follow-up task gets created.
3. Consider a combined completeness audit across blueprints/materials — `visual_profile` presence (ChatGPT's asset lane), `operational_data_reference` presence, and `production.input_materials` resolvability are all the same shape of "does field X exist and resolve to something real" check, and could be run as one pass instead of three separate ones.
4. Naming decision still open: `backlog/market/` vs. `backlog/economy/` — a separate Qwen prior-art audit was supposed to resolve this; not yet returned as of this handoff.

### Gemini
Hold Phase 2 (`.evaluate_strategy` triple-pricing design: Consumable EAP / Hardware CapEx / Extraction-Floor) until:
1. Qwen's NpcPriceCalculator-evaluate task confirms the real interface exists to build against.
2. Review the bootstrap-pricing functional form Perplexity produced (below) — it's code-agnostic and meant to inform the Extraction-Floor/CapEx leg specifically.
3. Do NOT assume `SettlementFees` is live on main (it's on an unmerged branch) or that `production.input_materials` is populated (it's empty for ~95% of materials) — both are real gaps, not available building blocks yet.

### Grok
Phase 3 (acquisition + ROI) stays parked until Gemini's Phase 2 interface is stable — no change to that sequencing. The Escalation-inventory task (separate from Phase 3, no dependencies) should be safe to run any time: summarize what `EscalationService` actually does vs. its gaps, out of scope = new ProcurementService/material JSON rewrite/Foothold changes.

---

## Bootstrap-Pricing Functional Form (for Gemini, from Perplexity)

Variables: `K` (CapEx), `c_op` (operating cost/unit), `P_imp` (Earth-import landed price), `Q_cum` (cumulative output), `Q_target` (design output at which CapEx is recovered), `r` (target return rate, 0 = break-even).

- `α(Q_cum) = min(Q_cum/Q_target, 1)` — amortization factor, 0→1 over the plant's life.
- `P_full(Q_cum) = c_op + [K·(1+r)/Q_target] · [1/max(α, α_min)]` — full-cost-recovery price (α_min ≈ 0.01 floor).
- `d(Q_cum) = exp(−λ · Q_cum/Q_target)` — bootstrap discount factor, tapers from 1→0 (λ controls speed; λ=3 → ~95% gone by Q_target).
- `P_pen = min(P_imp, c_op + m)` — initial penetration price (m = small/zero/negative markup).
- `P_boot(Q_cum) = (1−d)·P_full + d·P_pen` — the actual charged price, smoothly transitions from penetration pricing to full-cost recovery.
- **Optional two-part tariff**: `F_cap = [K·(1+r)·θ] / [T_payback · N_customers]` (θ = fraction of CapEx recovered via fixed fees). Effective price for a customer buying `q` units: `P_eff(q) = P_boot + F_cap/q`.
- **Demand-undershoot triggers**: utilization ratio `u(t) = Q_cum(t)/Q_plan(t)`, sustained below ~0.5 for 6-12 months signals trouble. Payback-horizon check: `T_remaining = (Q_target − Q_cum)/Q̇(t)` — if this is 2-3× the target payback period, economics are off. A stress factor `s ∈ [0,1]` can scale down the CapEx-recovery ambition when triggers fire.

**Open tuning decisions** (not derivable from research, need a design call): exact values for λ, Q_target, θ, and trigger thresholds; who sets bootstrap pricing in-game (colony / LDC / a regulated utility-style entity); how capacity fees are allocated among customers.

---

*Full research detail, complete audit trails, and additional context available on request — this handoff covers the actionable next steps.*
