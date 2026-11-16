# Architectural Overview & Planning Summary: Galaxy Game Economic Subsystem (`economy/`)

**Date:** 2026-09-07  
**Status:** Planning & Review Stage  
**Scope:** Transitioning from Earth Anchor Price (EAP) bootstrap to Total Cost of Ownership (TCO) & Amortized CapEx-Plus-Energy Standards for local extraction and manufacturing.

---

## 1. Executive Summary & Context
In *Galaxy Game*, early economic bootstrapping relied heavily on an Earth Anchor Price (EAP) model (`Tier1PriceModeler`), which uses Earth spot prices plus freight tariffs as a baseline pricing floor. While this is an effective bootstrap mechanism for Earth-connected nodes like Luna, it breaks down for distant celestial bodies (Mars, Venus, outer stations) where physical import is economically unviable and local In-Situ Resource Utilization (ISRU) must take over.

This planning document establishes the conceptual framework and review roadmap for migrating local bulk commodity pricing from Earth-import anchors to a sustainable **Amortized CapEx & Energy-Standard TCO model**, while establishing a dedicated `economy/` subsystem to manage production floors, asset depreciation, and trade logistics.

---

## 2. Core Economic Principles
1. **Retain EAP for High-Tech & Early Bootstrap:** Complex machinery, electronics, and early-stage bootstrapping (e.g., Luna) continue to leverage Earth import pricing (`Earth Base Price + Interplanetary Freight Tariff`).
2. **Local ISRU Extraction Floor:** Bulk consumables (LOX, water, $CO_2$) and locally harvested materials must be priced based on local extraction costs rather than Earth import floors, utilizing the extraction break-even formula:
   $$	ext{Floor} = rac{	ext{Fuel} + 	ext{Depreciation} + 	ext{Energy} + 	ext{Risk}}{	ext{Total KG}}$$
3. **Amortized CapEx Recovery:** The initial cost of machinery and its transport tariff to a settlement node are treated as capital debt, amortized over the expected lifetime output of the equipment. As cumulative production scales, unit costs naturally follow a deflationary curve.
4. **Immediate Ledger vs. Physical Logistics:** Ownership changes (buying/selling) occur instantly via ledgers, while physical cargo movement remains strictly gated by dock locations, storage classifications, and shuttle transport logistics.

---

## 3. Key Findings & Prior Art from Subsystem Audits
* **`Tier1PriceModeler`:** Confirmed as live, active code computing EAP per material (`app/services/tier1_price_modeler.rb`), invoked by `market_price_service.rb` and `npc_price_calculator.rb`.
* **`Settlements::CostAnalyzer`:** Fully implemented (`f0c3b8ca`), handling local vs. import cost comparisons and integrated into shortage detection pipelines (`ImportRequestGenerator`).
* **`NpcPriceCalculator.cost_based_bid`:** Currently enforces Earth import cost as a price floor for all resources. This is the primary target for refactoring to incorporate local extraction break-even floors.
* **Per-Location Fee Parity:** Recent work introduced `SettlementFees` to manage broker and transaction fee types per location. A discovered parity bug notes that `OrbitalSettlement` currently lacks this concern (unlike `BaseSettlement`), which must be resolved to prevent silent zero-fee calculations in docking and logistics services.

---

## 4. Subsystem Naming & Architecture
* **Directory Designation:** All future economic engineering, pricing service objects, asset CapEx amortization logic, and manufacturing COGS integration will be housed under **`app/services/economy/`** (or mirrored in backlog folders as `backlog/economy/`).
* **Rationale:** Naming the line of work `economy/` rather than `market/` future-proofs the codebase, distinguishing macroeconomic production and CapEx loops from simple exchange-order books.

---

## 5. Proposed Implementation Roadmap (Post-Review)
1. **Phase 1 — Parity Fix:** Restore `SettlementFees` concern parity on `OrbitalSettlement` to ensure consistent fee handling across surface and orbital nodes.
2. **Phase 2 — Pricing Floor Refactor:** Update `NpcPriceCalculator.cost_based_bid` to branch between high-tech imports (retaining EAP) and local bulk/ISRU resources (applying the CapEx/extraction break-even formula).
3. **Phase 3 — COGS Integration:** Wire up the orphaned `Manufacturing::CostCalculator` service to bridge settlement production costs directly into market pricing ledgers.
