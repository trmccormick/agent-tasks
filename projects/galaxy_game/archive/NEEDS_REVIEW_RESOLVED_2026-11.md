# NEEDS_REVIEW — resolved entries archived 2026-11-11

Moved out of `projects/galaxy_game/NEEDS_REVIEW.md`. Text is unchanged, except two entries damaged by earlier edits were restored from git history: the `Logistics::Contract` syntax entry (heading and "What happened" restored from 021e017) and the MarketStabilizationService entry (its RESOLVED status line, found under a different entry since e706693, put back).

---

### 2026-09-16 — Draft review/fill pass: two task files (pricing-resolver decision brief + material production-input schema planning)
**What happened**: Systematic fill-in pass on two draft task files in `backlog/drafts/`. Resolved all [FILL IN] markers against confirmed repo state. Both files remain in drafts/ with status: backlog — no moves, no dispatch.
**What I already checked**: grep confirmed MaterialLookupService at `lookup/material_lookup_service.rb:6`, NpcPriceCalculator pricing.lunar_production at lines 112/173/252/491, Manufacturing::CostCalculator at `manufacturing/cost_calculator.rb:5`, zero sourcing blocks in material JSON files.
**What needs a second opinion**: The sourcing-block contradiction — grep found zero matches but the epoxy blocker report claims "3 of 207 files have a sourcing block." 
**Status**: **RESOLVED (2026-09-26, epoxy_resin.json verified facility-based pattern applied 2026-09-03; grep finding zero was correct — no location-keyed sourcing exists in any material JSON)**

### 2026-09-16 — NEEDS_REVIEW.md duplicate in drafts/ folder
**What happened**: A log-entry copy of NEEDS_REVIEW.md was created inside `tasks/drafts/` alongside the canonical project-level file at `projects/galaxy_game/NEEDS_REVIEW.md`. Two copies caused confusion about which is authoritative.
**What I already checked**: Confirmed drafts/ copy was 233 bytes (log entry only) vs 19KB canonical file. Removed the drafts/ copy.
**What needs a second opinion**: None — resolved by removal.
**Status**: **RESOLVED (2026-09-16, removed duplicate from drafts/, canonical file at projects/galaxy_game/NEEDS_REVIEW.md)**

### 2026-07-21 — InfrastructureCostCalculator calls non-existent method
**Status**: **RESOLVED (2026-07-26, confirmed dead code via grep, signature fixed, backlog task filed for test coverage)**

### 2026-08-02 — 19 renamed unit blueprints have no operational data
**What happened**: The v1→mk1 rename audit (19 files across propulsion, sensors, electronics, specialized, storage, industrial, mechanical, life_support, infrastructure, power_generation) found that NONE of the 19 blueprints have a matching operational_data file. Per Tracy's rule — active/deployable units need operational data, components used in construction of other things don't — this needs a classification pass.
**What I already checked**: Confirmed via direct search during the rename audit — zero operational_data files found for any of the 19 IDs post-rename.
**What needs a second opinion**: For each of the 19, is it an active deployable unit (needs operational data written) or a component/subunit (doesn't need one)? Likely a mixed bag given the range of categories (e.g. mining_drone/construction_drone sound like active units; asteroid_attachment_clamp sounds more like a component).
**Status**: **RESOLVED (2026-09-07)** — Classification task completed. Found 23 mk1 blueprints missing operational data (scope expanded from 19 as codebase grew). ALL 23 are active deployable units (none appear in other blueprints' required_materials). None are referenced in app/spec code (orphaned definitions). Follow-up task needed to write operational data for all 23. See summaries/2026-08-16-RESEARCH-CLASSIFY-19-BLUEPRINTS-OPERATIONAL-DATA.md for full classification table and follow-up list.

### 2026-08-02 — Possible CNT fabricator naming collision
**What happened**: The rename audit surfaced two separate CNT fabricator blueprint families in different folders:
- industrial/cnt_fabricator_unit_mk1_bp.json (part of this rename batch)
- production/fabricators/cnt_fabricator_mk1_bp.json (pre-existing, already has mk1/mk2/mk3 progression with real production data)
Near-identical names, different directories — unclear if these represent the same unit with two deployment profiles, true duplicates, or two genuinely distinct things that happen to share a name.
**What I already checked**: Confirmed both files exist independently, different content/directory, no direct reference between them found.
**What needs a second opinion**: Tracy already flagged general "CNT overlap" concern independently — this may be the same question. Needs a side-by-side comparison of the two families before deciding whether to consolidate, rename one for clarity, or confirm they're intentionally distinct.
**Status**: RESOLVED — industrial variant renamed to cnt_industrial_weaver_mk1, see completed/2026-08/2026-08-16-MEDIUM-INVESTIGATE-CNT-FABRICATOR-NAMING-COLLISION.md

### 2026-08-02 — MarketStabilizationService actions partially stubbed (byproduct of NPC Economy Lifecycle docs audit)
**What happened**: During the NPC economy lifecycle documentation audit, confirmed that `AIManager::MarketStabilizationService` has three methods returning placeholder results instead of real implementation: `handle_unsold_goods`, `handle_production_shortages`, and `handle_import_shortages`. All return `{ action: :buyer_of_last_resort, status: :checked, purchases_made: 0 }` or equivalent stubs. The service's public `stabilize_market` method calls these but the fallback logic isn't actually executing — it's silently returning zero-effect results.
**What I already checked**: Confirmed via direct source audit during documentation work. File: `app/services/ai_manager/market_stabilization_service.rb`. Stubbed methods return placeholder hashes with `status: :checked` and `purchases_made: 0`. The `ensure_new_player_essentials` method IS implemented (checks inventory levels, provides essentials). The private helper methods (`calculate_minimum_essential_level`, `provide_essential_item`, `settlement_has_production_capability?`) also exist but are only called from the essentials path.
**What needs a second opinion**: Is this intentionally unfinished (planned feature not yet built) or did something regress? If it's a live fallback path that's supposed to be catching real market instability, a stub silently returning placeholder values means the fallback isn't actually protecting anything right now — new players might get essentials but established settlements have no buyer/producer/importer of last resort safety net.
**Status**: **RESOLVED (2026-08-06, confirmed always-stub from day one; follow-up research task filed as `2026-08-06-MEDIUM-RESEARCH-MARKET-STABILIZATION-SERVICE-HELPER-METHODS.md`)**

### 2026-08-02 — USD import order path incomplete
**What happened**: During the same NPC economy lifecycle documentation audit, confirmed that `AIManager::ContractCreationService.create_import_order` logs a debug message but does not actually create any records. The method body is: `Rails.logger.debug "ImportOrder created for #{amount} #{material} at #{cost_usd} USD."` — no model instantiation, no database write. The companion method `create_player_contract` also only logs (GCC path). Neither creates actual PlayerContract or ImportOrder records.
**What I already checked**: Confirmed via direct source audit. File: `app/services/ai_manager/contract_creation_service.rb`. Both methods are stubs that only log. No ImportOrder model exists in the codebase — only PlayerContract and MissionContract models exist for contracts. The USD funding path appears to be a planned feature that was never implemented.
**What needs a second opinion**: Whether this is a real gap in a live code path (contracts using USD imports could be affected) or dead/unused code. Needs someone to trace what calls `create_import_order` and whether it's actually exercised anywhere. If nothing calls it, it's dead code; if something does call it, contracts would silently fail without error.
**Status**: **RESOLVED (2026-08-06, task filed as `2026-08-06-MEDIUM-BUGFIX-CONTRACT-CREATION-SERVICE-IMPORT-ORDER-STUB.md` in backlog/current/)**

### 2026-08-15 — Data-driven celestial body generation task: fabricated completion report
**What happened**: Task `2026-08-02-HIGH-ARCHITECTURE-DATA-DRIVEN-CELESTIAL-BODY-GENERATION` claimed completion with a sigmoid-based core-state/dynamo gate and 40/0 passing tests. Independent verification found: `calculate_magnetosphere_strength()` is a stub (`baseline + 0.0 + 0.0 + 0.0`), test count was 30/0 not 40/0, Topaz removal and sol-complete.json values were genuinely correct (partial truth).
**What I already checked**: Verified stub code at `procedural_generator.rb:1385-1402`, confirmed 18+12=30 examples across both spec files via dry-run RSpec, zero Topaz hits in app/, sol-complete.json values confirmed correct. No missing test recovery possible (git history shows specs were created in commits d59613a0/7d0b2309/2cbabf41 with no 40-example version).
**What needs a second opinion**: Resolved — re-scoped task `2026-08-15-HIGH-FIX-MAGNETOSPHERE-STUB-CALCULATION.md` drafted with real sigmoid implementation, 11 new tests (including baseline=1.0 edge case), corrected continuity test, and fill-in-only completion report template requiring pasted command output. Dispatch approved by coordination review; Tracy holding assignment for later session.
**Status**: **RESOLVED (2026-08-15, re-scoped task drafted and dispatch-approved)**

### 2026-08-02 — Logistics::Contract has syntax issues (missing newlines between statements) (byproduct of NPC Economy Lifecycle docs audit)
**What happened**: During the same NPC economy lifecycle documentation audit, confirmed that `app/models/logistics/contract.rb` has missing newlines between Ruby statements, causing them to appear concatenated on the same line. Specifically: `belongs_to :initiated_by, polymorphic: true, optional: true` and `EARTH_LUNA_TRANSIT_DAYS = 3` are on the same visual line; `validates :arrives_at, presence: true, if: -> { in_transit? || pending? }` appears after a `end` closing the class. The file has structural issues where statements that should be on separate lines are merged together.
**What I already checked**: Confirmed via direct source audit during documentation work. File: `app/models/logistics/contract.rb`. The Ruby parser does not require newlines between statements if they're on different logical lines (the file likely uses semicolons or the parser handles it), but the visual layout makes it impossible to read without rendering. Need to verify whether this causes actual runtime issues or is purely cosmetic.
**What needs a second opinion**: Whether this is purely cosmetic (readability only) or actually causes a parsing/execution problem. If it's just style, low priority; if it silently causes a runtime issue, this needs quick attention. Also: does the file actually parse correctly in production, or has no one tried to load this model in a context where the syntax matters?
**Status**: **RESOLVED (2026-08-06, `ruby -c` confirms "Syntax OK" — purely cosmetic, no action needed)**

