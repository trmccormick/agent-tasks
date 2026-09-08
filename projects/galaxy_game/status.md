# Galaxy Game — Project Status & Task Tracking
**Last Updated:** 2026-09-07 — AI Manager Acquisition Surface Inventory COMPLETED + Canonical-Path decision DRAFT (Path B recommended, awaiting Tracy)

> **NOTE**: Session narrative belongs in handoff docs, not here. This file is a fast
> snapshot only. Do not add verbose session summaries above Active Tasks.

---

## 🟢 Current Work (2026-09-07)

### ⚠️ CRITICAL INTEGRATION: Economy System ↔ AI Manager Acquisition
**The economy subsystem is NOT optional to AI Manager work — it is foundational.**

Path through the code:
1. **Economy layer** (Gemini): Computes reference price via NpcPriceCalculator.cost_based_bid (formula = EAP → TCO → extraction-floor)
2. **Cost comparison** (used by AI Manager): CostAnalyzer.compare_costs (local vs import), ImportRequestGenerator
3. **Acquisition decisions** (Grok): Buy order pricing, escalation thresholds, import fallback decisions, excess-listing decisions
4. **Result**: If pricing formula is wrong, acquisition strategy fails (wrong escalation point, wrong import decision, budget blown)

**Gemini's work directly enables Grok's work.** No proper pricing floor → AI Manager cannot distinguish between "sane market price" and "price gouge" → escalation fails → resource crisis when it shouldn't happen.

### AI Manager Acquisition Surface Inventory — COMPLETED ✅
- **Task**: `2026-09-01-LOW-RESEARCH-AI-MANAGER-SERVICE-INVENTORY-AND-GAPS.md`
- **Synthesis Report**: `summaries/2026-09-07-RESEARCH-AI-MANAGER-SERVICE-INVENTORY-AND-GAPS.md`
- **Findings**: Four acquisition services confirmed (EscalationService 627L, ProcurementService 112L, ResourceAcquisitionService 148L, ResourceFulfillmentService 33L). Two parallel paths in manager loop. Placeholder pricing reachable but non-functional.
- **Gaps**: EAP enforcement, cycler preference, excess-listing-after-self-harvest, unified "can afford" logic.
- **Recommendation**: Clarify canonical path before implementing gaps.
- **Commits**: `6b3dbf1`, `f8d8a49`, and closing commits

### Acquisition Canonical Path — DECISION DRAFT (awaiting Tracy) ⏸️
- **Decision doc**: `summaries/2026-09-07-ARCHITECTURE-DECISION-ACQUISITION-CANONICAL-PATH.md` (DRAFT, not dispatched)
- **Recommendation**: Make **Path B** (`ResourcePlanner` → `ResourceAcquisitionService` → `ResourceFulfillmentService` → `MaterialRequestService`) canonical — already uses real NPC pricing + contract creation.
- **Path A deprecation**: Keep ISRU/`can_produce_locally?` check; deprecate no-op market path.
- **Service boundaries**: EscalationService = shortage/emergency/strategy; Path B = execution; ProcurementService = local-capability check only.
- **Gaps to close**: EAP enforcement, cycler/resupply preference, excess-listing-after-self-harvest, unified "can afford", remove placeholder `base_prices`.
- **CRITICAL CONSTRAINT** (Grok, 2026-09-07): DO NOT hard-code pricing multipliers or formulas in acquisition logic. Acquisition should call NpcPriceCalculator/CostAnalyzer for reference pricing and remain agnostic to exact formula (EAP/TCO/extraction-floor). Pricing formula is economy subsystem concern; acquisition only consumes the interface.
- **Blocks**: Material Sourcing & Acquisition Architecture task (`backlog/ai-manager/2026-09-03-...`) — remains DRAFT until Tracy advances this.

### 🆕 Economy Subsystem Refactor — PLANNING STAGE (Gemini lead) — **BLOCKER FOR GROK**
**⚠️ CRITICAL CONTEXT**: EAP (Earth Anchor Price) is **location-specific pricing** = Earth Cost + Transport Cost to that location. Transport costs vary by destination (Luna $100M, Mars $150M, Ceres $180M, Titan $300M+, etc.) and orbital mechanics (synodic windows cause price spikes/scarcity). Extraction-based pricing (break-even floors per location) competes with EAP, capped at Transport Anchor × 0.9 moat price. 

**BREAKTHROUGH (Gemini's work today)**: Never ship **consumables** (LOX, water, CO2) across deep space — shipping costs are astronomical and uneconomical. Instead, ship **factories/hardware** (electrolysis units, atmospheric harvesters, power systems) that can produce thousands of tons locally. CapEx amortization model: hardware import cost ÷ operational lifespan = effective per-ton production cost near-zero once online. This means:
- Luna: Ship consumables (EAP model still works)
- Mars/Venus/deep-space: Ship the factories, then local extraction replaces imports instantly
- AI Manager must model THREE pricing strategies simultaneously: consumable imports (EAP), hardware imports (CapEx amortization), and local extraction (near-zero after hardware online)

The system must support location-aware triple-model pricing: consumable EAP (per location) + hardware CapEx amortization (per equipment type) + extraction-floor (per location after hardware deployed).

Building acquisition logic that assumes flat EAP, ignores CapEx hardware imports, or assumes location-independent pricing = breaking when deep-space expansion requires bootstrap hardware shipping or multi-planet trade.

**WHY THIS MATTERS**: Without location-aware multi-model pricing, acquisition logic cannot make correct decisions. The dependency chain:
1. Economy layer computes location-specific AND strategy-specific prices (consumable EAP vs hardware CapEx vs local extraction)
2. AI Manager uses location+strategy prices in cost comparisons (CostAnalyzer.compare_costs: should we ship the factory or the consumable?)
3. Acquisition decides: import consumables to Luna? import hardware to Mars? enable local extraction? → All depend on correct pricing MODEL
4. **If pricing doesn't distinguish consumable EAP from hardware CapEx from extraction-floor, import decisions fail**
5. **If pricing is flat/global and single-model, system breaks when moving from Luna bootstrap to deep-space multi-planet scenarios**

**Scope**: Implement location-aware triple-model pricing (consumable EAP per location + hardware CapEx amortization + extraction-floor per location)
- **EAP per location**: Earth Cost + Transport Cost to that specific location (Luna $100M, Mars $150M, Venus $200M, Ceres $180M, Titan $300M+, etc.) — used for consumables
- **Hardware CapEx amortization**: Equipment import cost ÷ operational lifespan (e.g., $50M electrolysis plant ÷ 5 years = cost basis for effective local production) — used for factory/capabilities
- **Extraction-floor per location**: Transport Anchor × 0.9 moat price (each location calculates its own moat ceiling once hardware is online) — competes with consumable EAP after bootstrap
- **Bootstrap behavior**: New locations evaluate: "cheaper to import consumables (EAP) or import the factory (hardware CapEx)?" → AI Manager makes strategic choice based on amortization math
- Fix per-location fee parity bug: BaseSettlement has SettlementFees concern; OrbitalSettlement missing
- Refactor NpcPriceCalculator.cost_based_bid to support three strategies and location awareness (not flat global prices)
- Document settlement fee structure, location-specific cost multipliers, transport anchors, hardware CapEx examples, and bootstrap decision logic

**Integration points** (these MUST work before Grok can implement acquisition gaps):
- `app/services/market/npc_price_calculator.rb` — acquisition calls this; being refactored for location-aware triple-model (consumable EAP per location + hardware CapEx + extraction-floor per location)
- `app/services/market/tier1_price_modeler.rb` — EAP logic must calculate per location; CapEx amortization logic for hardware added
- `app/services/settlements/cost_analyzer.rb` — shortage detection must be location-aware AND strategy-aware (compare local extraction vs consumable import vs hardware import TO THIS LOCATION)
- `app/services/ai_manager/escalation_service.rb` — escalation thresholds must work per location and per strategy (synodic windows, transport costs, hardware payback period, availability changes)

**Critical constraint** (Grok's requirement): Acquisition must NOT assume flat pricing, location-independent costs, or single-model (consumable-only) logic. It calls NpcPriceCalculator/CostAnalyzer with location AND strategy context and remains agnostic to calculation method. When Gemini implements location-aware triple-model, acquisition auto-adapts to any location, any transport scenario, and any bootstrap strategy (consumable-heavy vs hardware-heavy).

**Status**: Audit + planning docs completed 2026-09-07 at `summaries/2026-09-07-RESEARCH-MARKET-*.md`; location-aware matrix, CapEx amortization math, and bootstrap decision logic documented by Gemini/Tracy sample
**Pricing priority order** (locked, not changing): atmospheric gases → regolith → mining+ISRU → imports

---

## 📋 Active Tasks: 0

> No tasks currently in `active/`. All current work is documented above; no agents currently assigned to active tasks.

---

## � PRIORITY SEQUENCING (Critical Blocker Ordering)

**DO NOT dispatch additional AI Manager implementation work until Gemini's economy subsystem work advances.**

**Sequence**:
1. ✅ **PHASE 1** (Completed): AI Manager Acquisition Surface Inventory + Canonical Path Decision
2. 🟡 **PHASE 2 (BLOCKING)**: Economy Subsystem Refactor (Gemini)
   - Task location: `backlog/economy/2026-09-07-PLANNING-OVERVIEW-ECONOMIC-SUBSYSTEM.md`
   - Must deliver: Extraction break-even pricing formula implemented in NpcPriceCalculator.cost_based_bid
   - Must deliver: SettlementFees parity fix (OrbitalSettlement)
   - Blocks: All of Phase 3
3. 🔒 **PHASE 3 (Blocked)**: AI Manager Acquisition Implementation (Grok)
   - Task location: Material Sourcing & Acquisition Architecture (in `backlog/ai-manager/`)
   - Cannot proceed until: Gemini's pricing interface is stable and tested
   - Depends on: Valid extraction break-even floors in NpcPriceCalculator

**Why this matters**: Grok's acquisition logic will be broken if it's implemented against EAP-only pricing. The AI Manager must wait for Gemini's extraction floors before finalizing buy-order pricing, escalation thresholds, and import-fallback logic.

**Next action**: Assign Gemini to Economy work; monitor for NpcPriceCalculator refactor commits; when complete, validate CostAnalyzer + ImportRequestGenerator against new pricing before proceeding with Grok's implementation.

---

## �📋 Current Backlog — Ready for Dispatch

### 🆕 Asset/UI Workstream (2026-09-01) — HELD / READY FOR REVIEW
| Task | Location | Notes |
|------|----------|-------|
| **Asset/UI Tasks A1–A6, B1–B3, C1–C5, D1–D3** (17 files) | `backlog/current/2026-08-31-*-ASSET-UI-*.md` | Created, content-verified, prerequisite gaps fixed. A1 is the natural starting point. |

### HIGH Priority
| Task | Location | Notes |
|------|----------|-------|
| ~~**Epoxy Resin Blueprint**~~ | `completed/2026-08/2026-08-20-HIGH-DATA-CREATE-EPOXY-RESIN-BLUEPRINT.md` | ✅ COMPLETED — sourcing structure insufficient; see rework task below |
| **Epoxy Resin Sourcing Rework** | `backlog/current/2026-09-02-HIGH-DATA-REWORK-EPOXY-RESIN-SOURCING-STRUCTURE.md` | HELD for review — rework to facility-based + market-driven pattern |
| **Fabrication Plant Blueprint** | `backlog/current/2026-08-20-HIGH-DATA-CREATE-FABRICATION-PLANT-BLUEPRINT.md` | DEFERRED (Phase 11+) — do not dispatch until Phase 11+ work begins |
| **Orbital Mechanics Data Layer** | `backlog/current/2026-08-19-HIGH-FEATURE-ORBITAL-MECHANICS-DATA-LAYER.md` | Phase 1-4 complete, Phase 5 pending |
| **Launch Window + Transit Timing Engine** | `backlog/current/2026-08-18-HIGH-FEATURE-LAUNCH-WINDOW-TRANSIT-TIMING-ENGINE.md` | Architecture feature |

### 🔴 **BLOCKED** — Waiting for Economy Subsystem Work
| Task | Location | Notes |
|------|----------|-------|
| **Material Sourcing & Acquisition Architecture** | `backlog/ai-manager/2026-09-03-ADJUSTMENT-MATERIAL-SOURCING-AND-ACQUISITION-LOGIC.md` | 🔒 **BLOCKED until Gemini completes NpcPriceCalculator refactor** — Grok cannot implement canonical path acquisition logic until pricing interface is stable |

### MEDIUM Priority
| Task | Location | Notes |
|------|----------|-------|
| **Classify 19 Blueprints** | `completed/2026-08/2026-08-16-MEDIUM-RESEARCH-CLASSIFY-19-BLUEPRINTS-OPERATIONAL-DATA.md` | ✅ COMPLETED — classified 23 mk1 blueprints (scope expanded from 19), ALL are active deployable units, none referenced in app/spec. Follow-up task needed for operational data writing. |
| **19-Blueprint Operational Data Writing** | backlog (to be created) | Follow-up: for blueprints classified as "active" in task above — 23 files to write |

### LOW Priority
| Task | Location | Notes |
|------|----------|-------|
| **Backlog Folder Cleanup Sweeps** (14 tasks) | `backlog/*/2026-09-03-LOW-DOCUMENTATION-CLEANUP-*.md` | One per folder — work through slowly; not urgent |
| **Financial Transaction Enum** | `review/2026-05-28-LOW-FEATURE-FINANCIAL-TRANSACTION-ENUM-AND-SPEC.md` | SUPERSEDED |

---

## 📊 Baseline & Test Status
- **RSpec Baseline:** 4714 examples, 174 failures, 55 pending (from 08-13/14 pre-push audit)
- **Rake Baseline:** 17/17 ✅ all phases PASSED — verified in container

---

## 📋 NEEDS_REVIEW — OPEN Entries (Summary)
| # | Date | Issue | Status |
|---|------|-------|--------|
| 1 | 07-31 | Sprite/biome/unit assets replaced with placeholders + mount architecture bug | **OPEN** — mount verified working; real sprites restored from Time Machine |
| 2 | 07-31 | Gemini Lava Tube Outpost specs review gaps | **OPEN** |
| 3 | 08-01 | Unit naming conventions (mk{num} vs codenames) — blocked on wiki reorg | **OPEN** |
| 4 | 08-02 | 19 renamed blueprints have no operational data | **OPEN** — task filed, now in active/ for dispatch |
| 5 | 08-02 | Possible CNT fabricator naming collision | **RESOLVED** ✅ — industrial variant renamed to `cnt_industrial_weaver_mk1` |
| 6 | 08-05 | Magnetosphere: 41 bodies defaulting to 0.5 | **OPEN** — low urgency, surface when Task 2 runs |
| 7 | 08-15 | **FABRICATED COMPLETION**: Data-driven task claims done but stubs remain, test counts don't match | **OPEN** — critical trust issue; see re-opened task file for details |
| 8 | 08-22 | **Oxygen fixture chain-tracing**: Storage-bucket fix works but O2 may short-circuit ISRU chain | **OPEN** — Claude handoff #1A pending verification |

> See `projects/galaxy_game/NEEDS_REVIEW.md` in agent-tasks repo for full verbatim entries.

---

## 🎯 Priority Queue for Next Session

### Must Do First:
1. **Create operational data writing task** — 23 active blueprints need operational data files (see summaries/)

### Ready to Dispatch (No Sign-off Needed):
2. **Orbital Mechanics Data Layer Phase 5** — TransitEngine integration pending (needs verification pass first)
3. **Launch Window + Transit Timing Engine** — Architecture feature (must complete before Orbital Phase 5)

### Waiting for Review/Decision:
- **Acquisition Canonical Path decision** — awaiting Tracy's review of Path B recommendation
- **Asset/UI Workstream** — held pending review, A1 is natural start point
- **Epoxy Resin Sourcing Rework** — held pending review

### Do NOT Touch This Session:
- `market-fee-hold` branch — Synthesis Report drafted, awaiting sign-off before push
- Anything touching shared/global code without Synthesis Report + approval

---

## 📝 Notes from Previous Sessions
- Agent commits use Tracy's git identity by default — commit authorship is not evidence of independent human verification.
- Green tests are not sufficient sign-off for shared/global code changes — Synthesis Report + approval required before committing, not after.
- Backlog folder cleanup sweeps were created after the Lookup Service Caching duplicate incident — work through slowly alongside Phase 05 work.

---

## 🔴 Pending Handoff to Grok (AI Manager Development Lead)

### ⚠️ CROSS-AGENT COORDINATION: Gemini Economy Work
- **Important**: Gemini is refactoring pricing floors (extraction break-even model) in parallel with Grok's AI Manager work
- **Impact on Path B**: Pricing assumptions in Acquisition Canonical Path decision may change when Gemini's extraction floors are implemented
- **Action**: When Grok reviews Path B, note that `NpcPriceCalculator.cost_based_bid` floor for harvested resources will be `(fuel+depreciation+energy+risk)/kg`, not Earth import cost
- **Coordination point**: Current `ImportRequestGenerator` cost comparisons (used in shortage detection) will need validation against new extraction pricing
- **Timeline**: Gemini's work is in planning stage; audit + overview docs ready for review at `/summaries/2026-09-07-RESEARCH-MARKET-*.md`

### ⚠️ CRITICAL: Pricing Interface (Not Formula) — Grok's Guidance
- **Grok's constraint** (2026-09-07): Acquisition logic must NOT hard-code pricing multipliers or assume EAP is permanent reference
- **What acquisition needs**: A stable interface to "current reference price" — whatever economy subsystem provides (EAP, TCO, extraction-floor, etc.)
- **What acquisition calls**: `NpcPriceCalculator.cost_based_bid`, `CostAnalyzer.compare_costs`, etc. — remains agnostic to the formula inside
- **Stable behavior** (independent of pricing formula):
  1. Maintain intentional stockpiles (proactive, not reactive)
  2. Buy orders default (player-first) well before emergency
  3. Local production only when market path fails or clearly inferior
  4. List excess after self-production
  5. Prefer cycler/resupply wait on normal shortages
  6. Emergency escalates only when time demands it
  7. Survival guaranteed when players absent
  8. Material data stays facility-based; no location-keyed sourcing
- **Implication**: When Gemini's pricing-floor refactor lands, acquisition logic automatically uses new formula (no rewiring needed) because it calls the interface, not the formula

### Material Sourcing Convention (Pass to Grok)
- **Issue**: Material JSON had hardcoded location keys (`lunar/martian/earth`) — doesn't scale to procedural worlds
- **Convention**: Materials carry recipes/pricing, NOT sourcing options; routing is runtime AI Manager decision
- **Documented**: `/memories/repo/material_sourcing_convention.md`
- **Handoff file**: `backlog/ai-manager/2026-09-03-ADJUSTMENT-MATERIAL-SOURCING-AND-ACQUISITION-LOGIC.md`

### AI Manager Acquisition Logic (Pass to Grok)
- **Decision tree**: market scan → depot check → cost comparison → present options
- **Key constraint**: Travel time + transport cost drive ISRU-first strategy
- **Scope**: Requires runtime implementation; architecture defined, not yet coded
- **Integration point**: ProcurementService when AI Manager needs material
- **Note**: Calls NpcPriceCalculator/CostAnalyzer interface (not embedded pricing logic) — remains stable as Gemini's formula evolves

---

## 📌 Archive Reference
Historical entries from 2026-09-03 through 2026-08-20 archived to:  
`status_archive/2026-09-07-archived-2026-09-03-to-2026-08-20.md`
