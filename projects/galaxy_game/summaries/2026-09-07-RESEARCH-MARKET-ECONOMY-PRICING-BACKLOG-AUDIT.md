# Synthesis Report: Market/Economy/Pricing Backlog Audit

**Date**: 2026-09-07
**Type**: Read-only audit — no edits, no design
**Scope**: All files in agent-tasks/projects/galaxy_game/ matching economy/market/cost keywords + all deferred-cleanup/superseded/current backlog dirs

---

## Methodology

### Step 1 — Keyword grep (206 matches total)
Searched: `pricing|market|EAP|cost.*calculat|amortiz|TCO|COGS|logistics.*cost|construction.*cost` across all task/summary/status/NEEDS_REVIEW directories.

### Step 2 — Explicit directory listing (deferred-cleanup, superseded, current)
17 files in deferred-cleanup, 8 files in superseded, 23 files in current — read all filenames for economy-adjacency regardless of keyword match.

### Step 3 — Full read of every economy/market/cost-relevant file
Read in full: all files from Steps 1-2 that are plausibly economy/market/cost-related (titles or content contain pricing, market, EAP, cost, fee, GCC, financial, consortium, profit, logistics-cost, depot-staging, etc.).

---

## Summary Table

| Filename | Category | One-Line Reason | Prior Art Value |
|----------|----------|-----------------|-----------------|
| **TASKS — BACKLOG** | | | |
| `2026-05-23-HIGH-DOCUMENTATION-NPC-PRICE-CALCULATOR-WIKI-VERIFICATION.md` (deferred-cleanup) | PRIOR ART | NpcPriceCalculator source verification task — pricing formula, EAP ceiling, local facility detection, scarcity scaling, warehouse cap. Still backlog but domain-relevant. | Confirms NpcPriceCalculator is the active pricing oracle; has cost_based_bid/cost_based_ask with Earth import floor, market-based pricing tiers, and EAP comparison via Tier1PriceModeler. Any new design must account for these existing mechanisms. |
| `2026-04-16-HIGH-FEATURE-MARKETPLACE-ON-STRUCTURE.md` (superseded) | SUPERSEDED/DEAD | Proposed polymorphic Marketplace ownership on structures — explicitly deprecated 2026-07-29 as scope creep. Superseded by orbital cargo logistics research. | Why it was killed: polymorphic Marketplace ownership caused massive scope creep. The real gap is buy-vs-physical-transfer, not marketplace ownership. New designs should avoid this trap. |
| `2026-04-16-MEDIUM-DATA-ECONOMIC-PARAMETERS-MARKET-FEES.md` (superseded) | SUPERSEDED/DEAD | Universal market fee defaults in economic_parameters.yml — superseded by per-location fee management task. | Why it was killed: fees are per-location, not universal. The per-location approach (see 2026-08-03 task below) is the correct pattern. |
| `2026-07-17-HIGH-REFACTOR-PROCUREMENT-SERVICE-CAN-PRODUCE-LOCALLY.md` (superseded) | SUPERSEDED/DEAD | Wire ProcurementService.can_produce_locally? to PrecursorCapabilityService — superseded by completed task 2026-07-19. | Already resolved: commits f93a5d47 + 1a60fce8. No further action needed. |
| `2026-04-16-MEDIUM-ARCHITECTURE-MATERIAL-STORAGE-CLASSIFICATION.md` (superseded) | SUPERSEDED/DEAD | Material storage classification architecture — superseded by existing surface_storage system. | Already resolved: surface_storage system handles outdoor storage. No additional classification needed. |
| `2026-09-03-MEDIUM-RESEARCH-GCC-SAT-POWER-BATTERY-DISCREPANCY.md` (current) | PRIOR ART | GCC mining satellite power/battery discrepancy — tick 1 success / ticks 2-3 failure pattern. Real root cause investigation needed. | Confirms GCC mining has real economic value (100 GCC/tick initially). Power/battery state management is a prerequisite for any GCC production economics design. |
| `2026-04-16-HIGH-ARCHITECTURE-RAW-RESOURCE-EXTRACTION-PRICING.md` (backlog/phase14, superseded) | SUPERSEDED/DEAD | Raw resource extraction break-even cost model — superseded by 2026-06-21 version. | Why superseded: refined scope from broad architecture to specific NpcPriceCalculator integration. |
| `2026-06-21-HIGH-ARCHITECTURE-RAW-RESOURCE-EXTRACTION-PRICING.md` (backlog/phase14) | PRIOR ART | Raw resource extraction break-even cost model — still backlog. Proposes `(fuel + depreciation + energy + risk) / total_kg` formula for NPC bid floor on harvested raw resources. | **HIGH VALUE**: This is the most directly relevant prior art. NpcPriceCalculator.cost_based_bid currently uses Earth import cost as floor for ALL resources. The gap: harvested raw resources need extraction break-even as floor, not import cost. Formula and integration design are fully specified. |
| `2026-06-17-MEDIUM-FEATURE-L1-LEO-Depot-Staging-Cost-Reduction.md` (backlog/phase07) | PRIOR ART | L1/LEO depot staging for Earth-import cost reduction — draft status. Focuses on routing/buffering high-value Earth components to reduce repeated import costs. | Confirms the economic concept of depot buffering as a cost-reduction mechanism. Not directly about pricing formulas but relevant to logistics cost modeling. |
| `2026-08-16-LOW-SPEC-DISTRIBUTE-CONSORTIUM-PROFITS.md` (backlog/phase11) | PRIOR ART | Consortium profit distribution spec — test coverage gap for BaseOrganization#distribute_consortium_profits. | Confirms consortium financial model exists with ownership_percentage-based profit sharing. Relevant to NPC economic behavior but not directly to pricing formulas. |
| `2026-07-29-HIGH-RESEARCH-ORBITAL-CARGO-LOGISTICS-AND-MARKET-LOCATION.md` (backlog/phase07) | PRIOR ART | Orbital cargo logistics & market location research — buy vs physical transfer gap. Replaces the superseded marketplace-on-structure task. | **HIGH VALUE**: Defines the real gap: buy = ownership change (no location constraint), loading = physical transfer (gated by dock-location for orbital). Shuttle system as bridge mechanism. Any new market design must handle this buy/transfer split. |
| `2026-08-03-MEDIUM-FEATURE-PER-LOCATION-MARKET-FEE-MANAGEMENT.md` (backlog/phase05, status: active) | PRIOR ART | Per-location market fee management — AI Manager sets settlement broker/transaction fees. Status: active. | **HIGH VALUE**: Already implemented in commit 7db7566c (unpushed). SettlementFees concern with broker_fee_type/value, transaction_fee_type/value, order_duration_min/max. LogisticsCoordinator#set_location_fees. UniversalDockingService fee integration. NOTE: OrbitalSettlement SettlementFees was reverted — potential bug. |
| **TASKS — COMPLETED** | | | |
| `2026-05-29-HIGH-FEATURE-LUNA-COST-ANALYSIS-SERVICE.md` (completed) | ALREADY RESOLVED | Luna Phase 1 CostAnalyzer service — completed f0c3b8ca. Settlements::CostAnalyzer.compare_costs(local vs import). | Confirmed: `Settlements::CostAnalyzer` exists with compare_costs, local_production_cost, current_import_price methods. All specs pass (5 examples, 0 failures). This is the existing cost comparison service — any new pricing design should integrate with or supersede it. |
| `2026-06-03-HIGH-BUG-FIX-IMPORT-REQUEST-GENERATOR-CALCULATE-COST.md` (completed) | ALREADY RESOLVED | ImportRequestGenerator calculate_cost NoMethodError — fixed by replacing with compare_costs call. | Confirms the integration path: ImportRequestGenerator → CostAnalyzer.compare_costs → market price lookup. This is how import cost data flows into shortage detection. |
| `2026-08-06-HIGH-BUGFIX-MARKET-STABILIZATION-SERVICE-MISSING-THRESHOLD-METHOD.md` (active) | PRIOR ART | MarketStabilizationService missing calculate_minimum_threshold method — critical runtime bug. Also schedule_cycler_delivery stub. | Confirms AI Manager has a market stabilization service that handles unsold goods, production shortages, and import shortages. The missing threshold method is a blocker for this service working at all. Relevant to understanding the existing market intervention layer. |
| `2026-06-29-COMPLETION-GCC-ACCOUNT-PHASE1.md` (summary) | ALREADY RESOLVED | GCC Account refactor Phase 1 — implementation complete, 84 examples pass. gcc_account defined in SettlementCore concern. | Confirms all settlements have a unified gcc_account via SettlementCore. This is the financial foundation for any pricing/economy work. extraction_service, operational_manager, procurement_service all use settlement.gcc_account. |
| `2026-08-16-SYNTHESIS-REPORT-MARKET-FEE-COMMIT.md` (summary) | PRIOR ART | Retroactive review of 7db7566c (market-fee-hold branch). Found OrbitalSettlement SettlementFees was reverted. | **HIGH VALUE**: Documents the exact state of per-location fee system. SettlementFees concern exists on BaseSettlement but NOT on OrbitalSettlement. LogisticsCoordinator and UniversalDockingService assume all settlements have fee methods — silent zero fees for orbital is a bug. |
| `2026-08-24-ESCALATION-SERVICE-DESIGN-CLARIFICATIONS.md` (summary) | PRIOR ART | Escalation service design clarifications — AI Manager prioritization: local harvest first, imports last. Material classification by harvesting tier. | **HIGH VALUE**: Confirms the economic priority order that any new pricing system must respect: 1) Atmospheric gases (trivial), 2) Regolith scooping, 3) Mining+ISRU, 4) Imports. CO2 on Mars is trivially harvestable. AI Manager cannot bootstrap without local extraction. |
| **NEEDS_REVIEW / STATUS** | | | |
| `projects/galaxy_game/NEEDS_REVIEW.md` | N/A | Contains economy/market entries — keyword matches found but content not fully read (only grep output available). | Needs separate review for specific economy-related entries. |
| `projects/galaxy_game/status.md` | N/A | Contains economy/market entries — keyword matches found but content not fully read. | Needs separate review for specific economy-related entries. |

---

## Key Findings for New Market/Economy Design

### 1. Existing Pricing Infrastructure (DO NOT REBUILD)
- **NpcPriceCalculator** (`app/services/market/npc_price_calculator.rb`): Active pricing oracle with cost_based_bid/ask, market_based_bid/ask, EAP ceiling via Tier1PriceModeler
- **Settlements::CostAnalyzer** (`app/services/settlements/cost_analyzer.rb`): Local vs import cost comparison — already working
- **SettlementFees concern**: Per-location fee system implemented (unpushed branch) with broker/transaction fee types
- **GCC Account**: Unified via SettlementCore concern on all settlements

### 2. Known Gaps (PRIOR ART for New Design)
- **Raw resource extraction break-even** (task `2026-06-21-HIGH-ARCHITECTURE-RAW-RESOURCE-EXTRACTION-PRICING.md`): NpcPriceCalculator uses Earth import cost as floor for ALL resources. Harvested raw materials need `(fuel + depreciation + energy + risk) / total_kg` as the floor instead. This is the most directly actionable prior art.
- **Buy vs physical transfer split** (task `2026-07-29-HIGH-RESEARCH-ORBITAL-CARGO-LOGISTICS-AND-MARKET-LOCATION.md`): Marketplace ownership on structures was killed as scope creep. The real gap is that buy = ownership change only, but loading = physical transfer gated by dock-location for orbital settlements.
- **MarketStabilizationService missing method**: `calculate_minimum_threshold` called but never defined — critical runtime bug blocking market intervention logic.

### 3. Economic Priority Order (from Escalation Service Design)
1. Atmospheric gases → trivial intake (no ISRU gate)
2. Regolith harvesting → simple scooping
3. Mining + ISRU processing → water ice in PSRs requires mine setup
4. Imports → only when body genuinely cannot produce

### 4. Dead/Superseded Items to Ignore
- Marketplace-on-structure polymorphic ownership (killed as scope creep)
- Universal market fee defaults (superseded by per-location approach)
- Material storage classification (resolved by surface_storage system)
- ProcurementService.can_produce_locally? wiring (already completed)
- Raw resource extraction pricing v1 (superseded by v2 in phase14)

### 5. Files That Need Separate Review
- `projects/galaxy_game/NEEDS_REVIEW.md` — economy/market entries not fully read (grep only)
- `projects/galaxy_game/status.md` — economy/market entries not fully read (grep only)
- These should be reviewed for specific economy-related items before design work begins.

---

## Files NOT Economy-Relevant (from grep, filtered out)
The following matched keywords but are unrelated to market/economy pricing:
- Blueprint creation tasks (epoxy_resin, fabrication_plant, graphite) — matched "cost" in manufacturing context but are data/schema tasks
- Phase structure documentation files
- Asset UI catalog tasks
- General documentation cleanup tasks
- Biome/terrain rendering tasks
- Power/battery research (unrelated to GCC economics)
