# Multi-Agent Coordination Summary — Galaxy Game
**Generated:** 2026-09-08 by Planning Agent (Qwen)  
**Scope:** Cross-lane coordination for Claude, Grok, Gemini, Haiku, ChatGPT  
**Purpose:** Ground truth reference to prevent wasted work from false premises

---

## 1. Acquisition Services — Four Confirmed Paths

All four services live under `app/services/ai_manager/`. Line counts verified:

| Service | Lines | Role |
|---------|-------|------|
| `EscalationService` | 627 | Trigger layer: expired orders → emergency vs resupply manifest; `can_harvest_locally?` gate |
| `ProcurementService` | 112 | ISRU/local-production check first, then market purchase; placeholder pricing (non-functional) |
| `ResourceAcquisitionService` | 148 | Local-GCC vs external-USD fork; `process_external_import` for direct imports |
| `ResourceFulfillmentService` | 33 | Order fulfillment/execution |

### Two Parallel Runtime Paths (Verified via grep)

**Path A:** `OperationalManager#procure_resource` → `ProcurementService.procure_resource`  
(ISRU/local-production check first, then market purchase)

**Path B:** `ResourcePlanner` → `ResourceAcquisitionService.acquisition_method_for`  
AND `market_stabilization_service.rb:171` → `ResourceAcquisitionService.process_external_import` **directly** (bypasses ResourcePlanner entirely)

### ⚠️ Consolidation Question for Grok
`market_stabilization_service.rb`'s direct call to `process_external_import` needs an explicit decision: should it stay a narrower/faster path, or get routed through `ResourcePlanner` for consistency? **This blocks any acquisition consolidation work.**

### Pricing Chain (Verified)
- `Tier1PriceModeler` → live EAP computation, called from:
  - `economics/market_price_service.rb:136`
  - `market/npc_price_calculator.rb:113`
- `NpcPriceCalculator.cost_based_bid` (line 78) → currently enforces **Earth import cost as price floor for ALL resources** — primary target for Gemini's Phase 2 refactor
- `Manufacturing::CostCalculator` → fully implemented but **never instantiated anywhere** in `app/`. Dead code. Not a live risk.

---

## 2. Curriculum Framing — Narrative, Not Architecture

### What Exists (Real Code/Data)
| Component | Location | Status |
|-----------|----------|--------|
| `missions_v2` profiles | `data/json-data/missions_v2/profiles/` | 32 JSON files |
| `tasks_v2` library | `data/json-data/missions/tasks_v2/` | 163 JSON files |
| `TaskExecutionEngineV2` | `app/services/ai_manager/task_execution_engine_v2.rb` | Real code — loads tasks_v2, accepts manifest/profile, plans+executes |

### What's Narrative (Not New Code)
- "AI training curriculum" is a **coordination story** overlaying existing data structures
- missions_v2 and tasks_v2 existed before the Haiku session that reframed them
- TaskExecutionEngineV2 existed before the curriculum framing
- **Risk:** Agents might build "curriculum" features that don't map to actual data
- **Recommendation:** Keep curriculum as coordination narrative; ground all implementation in missions_v2/tasks_v2/TaskExecutionEngineV2

---

## 3. False Premises — Do NOT Dispatch These Tasks

### ❌ SettlementFees Parity Bug (Gemini Planning Doc Phase 1) — CORRECTED
**What was claimed:** "OrbitalSettlement lacks SettlementFees concern; BaseSettlement has it"  
**What actually happened:** `SettlementFees` **was created on Aug 10, 2026** (commit `7db7566c`) but only on the `market-fee-hold` branch — never merged to main. The Sept 5 RSpec logs showing `SettlementFees#apply_default_fees!` were running against that branch or uncommitted changes, not mainline code.

**What exists on market-fee-hold:**
- `settlement_fees.rb` (120 lines) — per-settlement fees in `operational_data['fees']` JSONB
- `LogisticsCoordinator#set_location_fees/get_location_fees/apply_default_fees` — not on main
- `UniversalDockingService#calculate_docking_fees/process_docking_fees` — not on main
- 30 passing tests in `per_location_fees_spec.rb`

**What exists on main:**
- `Market::TransactionFee` model + `market_transaction_fees` table — **zero callers**, draft language (`"# If you want a separate model"`)
- Neither fee mechanism is wired into any pricing service

**Bottom line:** This is not a "parity bug" or "false premise." It's a **real design that exists on a branch but never reached production**. The hold-branch design (per-settlement, per-location, broker + transaction types) is the stronger prior art and should be preferred over the dead TransactionFee model.

### ❌ "19 Blueprints Need Operational Data" (Claude Handoff)
**Claim:** 19→23 mk1 blueprints are "active deployable units needing operational data"  
**Methodology gap:** Classification was based on zero `app/` references — but BlueprintLookupService loads JSON generically, so **every blueprint looks the same from grep's perspective**. The CNT-fabricator blueprint (an established active unit) also has zero literal `app/` references.

**What to check instead:** Spot-check 2-3 of the 23 against whether their `unit_type`/`category` maps to a real `Units::` class through BlueprintLookupService — not grep.

**Confirmed:** 3 mk1 blueprints have zero `operational_data_reference` at all:
- `asteroid_hollowing_laser_mk1.1_bp.json`
- `planetary_umbilical_hub_mk1_bp.json`  
- `planetary_power_management_unit_mk1_bp.json`

The other ~46 mk1 blueprints all have references (even if empty `{}`). Question: do these 3 need data filled in, or are they intentionally minimal?

---

## 4. Economy/ vs Market/ Naming — Pending Qwen's Audit

| Source | Recommendation |
|--------|---------------|
| Claude handoff | "Qwen's prior-art audit should inform whether `economy/` fits better than `market/`" |
| Gemini planning doc | Use `economy/` to distinguish macroeconomic production from exchange-order books |
| Current state | `app/services/economy/scheduled_trade_service.rb` exists (1 file); `app/services/market/` has 5 files; `app/services/economics/` has 2 files |

**Decision pending:** Qwen's prior-art audit results should resolve this before any new code lands under either name.

---

## 5. Product Rules — Preserve These in All Work

From Grok's AI Manager handoff (verified as binding):

1. **EAP = Earth cost + transport** (expensive fallback); new local list seeds at **EAP × 0.9** without history
2. **Player-first market buy** when price sane **and** real **GCC** on hand
3. **Too expensive + harvestable → self-harvest**, keep need, list excess
4. **Normal shortage → wait for cycler/resupply** over feeding gouges (unless emergency)
5. **Preferred path:** real GCC + logistics corp as go-between
6. **Virtual ledger:** DC–DC / NPC–NPC; not default player market
7. **DC priority:** keep settlement running even at trade imbalance

---

## 6. Recommended Priority Order

| Priority | Task | Agent | Dependencies |
|----------|------|-------|-------------|
| **1** | Escalation/acquisition read-only inventory | Grok | None — can start immediately |
| **2** | Qwen's prior-art audit → resolve economy/ vs market/ naming | Qwen | None |
| **3** | Blueprint methodology check (spot-check 2-3 blueprints) | Planning agent | None |
| **4** | NpcPriceCalculator refactor (EAP floor → local extraction break-even) | Gemini | Parallel, no blockage |
| **5** | Acquisition logic (ROI evaluation) | Grok | Waits for #4 + #1 |

---

## 7. Cross-Link References

| Document | Location |
|----------|----------|
| Claude project handoff | `docs/new_agent/projects/galaxy_game/handoffs/claude(free web)/2026-09-07-SESSION-HANDOFF.md` |
| Grok AI Manager handoff | `docs/new_agent/projects/galaxy_game/handoffs/grok(free web)/2026-09-06-SESSION-HANDOFF-AI-MANAGER.md` |
| Haiku curriculum handoff | `docs/new_agent/projects/galaxy_game/handoffs/claude(hakiu copilot)/2026-09-07-session-closout.md` |
| Gemini economy planning | `docs/new_agent/projects/galaxy_game/tasks/backlog/economy/projects_galaxy_game_backlog_economy_2026-09-07-PLANNING-OVERVIEW-ECONOMIC-SUBSYSTEM.md` |
| Status.md | `docs/new_agent/projects/galaxy_game/status.md` |
| Material sourcing convention | `/memories/repo/material_sourcing_convention.md` |
| **Fee mechanism research** | `summaries/2026-09-08-RESEARCH-FEE-MECHANISM-HISTORY.md` |
| **Grok→Gemini dependency note** | Below (§8) |

---

## 8. Grok→Gemini Dependency Note (Acquisition Sequencing)

**Source:** Grok's direct note for Gemini, verified by planning agent research

### Blocker for Grok Phase 3 (Acquisition Logic)
Grok's acquisition work is explicitly sequenced behind Gemini Phase 2.

**What Gemini must deliver before acquisition can resume:**
A stable **location-aware pricing / strategy-selection interface** with minimum requirements:

1. Given material + settlement/location context → return reference costs or strategy options (caller does NOT hard-code EAP or multipliers)
2. Support strategy distinctions: consumable EAP vs hardware CapEx amortization vs local extraction floor
3. Interface signature stable enough that acquisition can depend on it

**What acquisition does NOT need from Gemini:**
- Fee wiring (SettlementFees / TransactionFee) — economy-subsystem work; acquisition treats fees as part of the price it receives
- Any assumption that SettlementFees is already live on main (it is not; lives only on market-fee-hold)

**Design intent acquisition will wire to once interface exists:**
- Player-first proactive buy orders as default
- Player-offered missions as first escalation
- Local production/harvest only when market + player-mission paths fail or are inferior
- Cycler/resupply preference on normal shortages
- Hard emergency only when time-to-critical demands it
- System as backstop when players are absent

**Unblock scope:** Luna + Earth pricing (Phase 5-ready) is enough. No pressure on fee work or temporal/infrastructure pricing — those can come later.

---

**Status:** Ready for dispatch. Priority #1 (Grok's Escalation inventory) is unblocked and recommended as the next single mission.
