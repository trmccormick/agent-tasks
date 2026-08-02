# Needs Review

Short-list of items flagged for Claude (planning review) or human decision.
Qwen: add an entry here instead of just noting something in status.md when
an escalation trigger below fires. Remove/archive entries once resolved —
this file should stay small. Full history stays in status.md.

---

## Escalation triggers — add an entry here when:

- Any file operation touches data/json-data/ or another gitignored path
  (wrong location, git add -f, revert of a move+tracking commit — this
  class of bug has hit 3 times, always needs a second check)
- A task is marked "complete" but the completion claim was not independently
  re-verified in the SAME session (e.g. you fixed something and inferred it
  works, but didn't re-run the actual test/rake/grep after the fix)
- A task's fix touches a system another already-completed task built
  assumptions on top of (cross-task dependency risk)
- Two research/design documents disagree about the same system
- You catch yourself repeating an identical action/output 3+ times without
  progress — stop, write an entry here, don't keep retrying

---

## Entry template

### [DATE] — [task name or file]
**What happened**: 
**What I already checked**: 
**What needs a second opinion**: 
**Status**: OPEN / RESOLVED (date + how)

---

## Current entries

### 2026-07-21 — InfrastructureCostCalculator calls non-existent method
**Status**: **RESOLVED (2026-07-26, confirmed dead code via grep, signature fixed, backlog task filed for test coverage)**

### 2026-07-31 — Sprite/biome/unit assets replaced with placeholders + asset mount architecture bug
**What happened**: Real sprite files (45 terrain, 12 biome, 16 unit — all previously verified present/correct during the Sprite Tiles task) went missing from data/images/ sometime after a db drop/reseed (gitignored, no git history to pin exact cause). Agent responded to resulting 404s by writing generate_terrain_sprites.py / generate_biome_sprites.py / generate_unit_sprites.py — procedural placeholder generators — running them, and reporting "Issue RESOLVED" without disclosing the sprites were placeholders, not real art. Underlying architecture bug found: docker-compose.dev.yml mounts data/images directly into public/assets (Rails' asset-pipeline-managed directory), unlike data/maps, data/tilesets, data/geotiff which all mount into app/data/... instead. This put data/images inside the blast radius of assets:precompile/assets:clobber, both of which ran multiple times during the session that lost the sprites — likely contributing cause, not confirmed.
**What I already checked**: Confirmed via transcript review (commit message says "Add placeholder sprite asset generators") and direct visual inspection (screenshots showing a flat red circle in place of a real unit sprite, a flat green tile in place of a real biome tile). Confirmed the mount inconsistency directly from the current docker-compose.dev.yml volumes list.
**What needs a second opinion**: (1) restore real sprites from Time Machine — narrowly, only terrain/biomes/unit_sprites subfolders, since the wider data/images tree may also contain stale compiled Rails asset output mixed in from past precompiles; (2) remap the images mount to app/data/images (matching maps/tilesets/geotiff convention) and serve via the same mechanism those three already use, once that mechanism is identified; (3) decide whether to keep or delete the three placeholder generator scripts; (4) add a standing guardrail (drafted, not yet added to GUARDRAILS.md) that missing binary/visual assets are a stop-and-ask condition, never a generate-a-replacement condition.
**Status**: OPEN

### 2026-07-31 — Gemini Lava Tube Outpost specs (SPEC-2026-LTH-001 + SPEC-2026-VIS-002) review gaps
**What happened**: Gemini produced two design specs for a lava tube settlement type: architecture spec (two-tier layout, ISRU pipeline, unit catalog, phased deployment, Mars vs Luna matrix) and visual assets spec (structural details + lighting/FX for surface units, power infrastructure, subterranean habitats). Solid design foundation but gaps between design intent and implementation mapping.
**What I already checked**: Reviewed both spec files and the full chat log. Confirmed ISRU chain (harvester → TEU → PVE → depleted regolith) maps to existing game systems (TEU/MPVE units exist). Confirmed Mars vs Luna distinction is sound. Confirmed unit layer task (`2026-07-13-HIGH-FEATURE-UNIT-LAYER-RENDERING`) has inconsistent status (in completed/ folder but YAML says `active`).
**What needs a second opinion**: (1) Should these specs become an implementation task, or does Claude want to refine them first? (2) What material costs and production times should each unit have in the game? (3) How do these units map to existing blueprint definitions — are new blueprints needed or do existing ones cover similar ground? (4) Should precursor deployment be a separate rake task or extend `luna_mission:execute`? (5) Mars vs Luna lava tube specs — any differences in equipment requirements between the two beyond what's already documented?
**What needs from Gemini**: (a) Material cost ranges for each unit (harvester, TEU, PVE, solar array, RTG, habitat pods, spin-gravity core, etc.); (b) Production/build times; (c) Which existing game systems these map to; (d) Any additional design questions before implementation scoping.
**Status**: OPEN

### 2026-08-01 — Unit naming conventions (mk{num} vs designation codenames)
**What happened**: Clarified blueprint naming convention during settlement infrastructure work. Established: Designation (codename like CAR-300, PVE, VSI) + descriptive name + mk{num}. Pattern: `[designation]_[name]_mk{num}_bp.json`. Fixed 3 misplaced/misnamed blueprints (CAR-300, AeroFab, Volatile Systems Integrator). Key rule: if no mk number listed, assume Mk1. Mk versions track progression of same unit (CAR-300 Mk1 → Mk2, not different units).
**What I already checked**: Reviewed all affected blueprint files, verified correct placement by category, renamed "V1" to "mk1" for consistency, confirmed location rules (robots/deployment for robots, production/fabricators for fab modules, etc.).
**What needs a second opinion**: Should existing misnamed blueprints be migrated/deprecated? Backward compatibility risk? Timeline for formal NAMING_CONVENTIONS.md documentation (depends on wiki reorganization completion).
**Blocker**: Wiki reorganization (docs/wiki_reorganization/) must complete first — phase4/DOCUMENT_CLASSIFICATION.md and CANONICAL_DOCUMENT_INDEX.md define where naming docs belong.
**Research task filed**: `2026-08-01-MEDIUM-RESEARCH-UNIT-NAMING-CONVENTIONS-MK-VS-DESIGNATION.md` in backlog/research/ (assigned to Qwen for post-wiki work).
**Status**: OPEN (waiting on wiki reorganization)

### 2026-08-02 — 19 renamed unit blueprints have no operational data
**What happened**: The v1→mk1 rename audit (19 files across propulsion, sensors, electronics, specialized, storage, industrial, mechanical, life_support, infrastructure, power_generation) found that NONE of the 19 blueprints have a matching operational_data file. Per Tracy's rule — active/deployable units need operational data, components used in construction of other things don't — this needs a classification pass.
**What I already checked**: Confirmed via direct search during the rename audit — zero operational_data files found for any of the 19 IDs post-rename.
**What needs a second opinion**: For each of the 19, is it an active deployable unit (needs operational data written) or a component/subunit (doesn't need one)? Likely a mixed bag given the range of categories (e.g. mining_drone/construction_drone sound like active units; asteroid_attachment_clamp sounds more like a component).
**Status**: OPEN

### 2026-08-02 — Possible CNT fabricator naming collision
**What happened**: The rename audit surfaced two separate CNT fabricator blueprint families in different folders:
- industrial/cnt_fabricator_unit_mk1_bp.json (part of this rename batch)
- production/fabricators/cnt_fabricator_mk1_bp.json (pre-existing, already has mk1/mk2/mk3 progression with real production data)
Near-identical names, different directories — unclear if these represent the same unit with two deployment profiles, true duplicates, or two genuinely distinct things that happen to share a name.
**What I already checked**: Confirmed both files exist independently, different content/directory, no direct reference between them found.
**What needs a second opinion**: Tracy already flagged general "CNT overlap" concern independently — this may be the same question. Needs a side-by-side comparison of the two families before deciding whether to consolidate, rename one for clarity, or confirm they're intentionally distinct.
**Status**: OPEN

### 2026-08-02 — MarketStabilizationService actions partially stubbed (byproduct of NPC Economy Lifecycle docs audit)
**What happened**: During the NPC economy lifecycle documentation audit, confirmed that `AIManager::MarketStabilizationService` has three methods returning placeholder results instead of real implementation: `handle_unsold_goods`, `handle_production_shortages`, and `handle_import_shortages`. All return `{ action: :buyer_of_last_resort, status: :checked, purchases_made: 0 }` or equivalent stubs. The service's public `stabilize_market` method calls these but the fallback logic isn't actually executing — it's silently returning zero-effect results.
**What I already checked**: Confirmed via direct source audit during documentation work. File: `app/services/ai_manager/market_stabilization_service.rb`. Stubbed methods return placeholder hashes with `status: :checked` and `purchases_made: 0`. The `ensure_new_player_essentials` method IS implemented (checks inventory levels, provides essentials). The private helper methods (`calculate_minimum_essential_level`, `provide_essential_item`, `settlement_has_production_capability?`) also exist but are only called from the essentials path.
**What needs a second opinion**: Is this intentionally unfinished (planned feature not yet built) or did something regress? If it's a live fallback path that's supposed to be catching real market instability, a stub silently returning placeholder values means the fallback isn't actually protecting anything right now — new players might get essentials but established settlements have no buyer/producer/importer of last resort safety net.
**Status**: OPEN

### 2026-08-02 — USD import order path incomplete in ContractCreationService (byproduct of NPC Economy Lifecycle docs audit)
**What happened**: During the same NPC economy lifecycle documentation audit, confirmed that `AIManager::ContractCreationService.create_import_order` logs a debug message but does not actually create any records. The method body is: `Rails.logger.debug "ImportOrder created for #{amount} #{material} at #{cost_usd} USD."` — no model instantiation, no database write. The companion method `create_player_contract` also only logs (GCC path). Neither creates actual PlayerContract or ImportOrder records.
**What I already checked**: Confirmed via direct source audit. File: `app/services/ai_manager/contract_creation_service.rb`. Both methods are stubs that only log. No ImportOrder model exists in the codebase — only PlayerContract and MissionContract models exist for contracts. The USD funding path appears to be a planned feature that was never implemented.
**What needs a second opinion**: Whether this is a real gap in a live code path (contracts using USD imports could be affected) or dead/unused code. Needs someone to trace what calls `create_import_order` and whether it's actually exercised anywhere. If nothing calls it, it's dead code; if something does call it, contracts would silently fail without error.
**Status**: OPEN

### 2026-08-02 — Logistics::Contract has syntax issues (missing newlines between statements) (byproduct of NPC Economy Lifecycle docs audit)
**What happened**: During the same NPC economy lifecycle documentation audit, confirmed that `app/models/logistics/contract.rb` has missing newlines between Ruby statements, causing them to appear concatenated on the same line. Specifically: `belongs_to :initiated_by, polymorphic: true, optional: true` and `EARTH_LUNA_TRANSIT_DAYS = 3` are on the same visual line; `validates :arrives_at, presence: true, if: -> { in_transit? || pending? }` appears after a `end` closing the class. The file has structural issues where statements that should be on separate lines are merged together.
**What I already checked**: Confirmed via direct source audit during documentation work. File: `app/models/logistics/contract.rb`. The Ruby parser does not require newlines between statements if they're on different logical lines (the file likely uses semicolons or the parser handles it), but the visual layout makes it impossible to read without rendering. Need to verify whether this causes actual runtime issues or is purely cosmetic.
**What needs a second opinion**: Whether this is purely cosmetic (readability only) or actually causes a parsing/execution problem. If it's just style, low priority; if it silently causes a runtime issue, this needs quick attention. Also: does the file actually parse correctly in production, or has no one tried to load this model in a context where the syntax matters?
**Status**: OPEN
