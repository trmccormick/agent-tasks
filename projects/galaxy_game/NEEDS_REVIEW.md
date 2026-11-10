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
**Status**: OPEN

### 2026-08-23 — can_harvest_locally? grants O2 credit without ISRU capability check
**What happened**: `can_harvest_locally?` (app/services/ai_manager/escalation_service.rb:457-460) grants direct O2 credit purely from atmosphere-gas-presence, with zero check for whether a settlement has any ISRU/processing capability deployed. Confirmed via two independent diagnostics today (chain-trace + fixture-realism check).
**What I already checked**: 
- Source code: escalation_service.rb lines 457-460 show direct O2 credit from atmosphere gas presence
- Chain-trace diagnostic confirmed no ISRU capability gate in the path
- Fixture-realism check on escalation_integration_spec showed same issue with oxygen fixture
**What needs a second opinion**: This needs a properly scoped task before anyone can pick it up — not yet filed. Flag it as needing Tracy's design input before task creation (touches unit-model and acquisition-routing architecture, not just a local fix).
**Status**: OPEN — needs Tracy's design input before task creation
**Status**: **RESOLVED (2026-08-06, confirmed always-stub from day one; follow-up research task filed as `2026-08-06-MEDIUM-RESEARCH-MARKET-STABILIZATION-SERVICE-HELPER-METHODS.md`)**

### 2026-08-02 — USD import order path incomplete
**What happened**: During the same NPC economy lifecycle documentation audit, confirmed that `AIManager::ContractCreationService.create_import_order` logs a debug message but does not actually create any records. The method body is: `Rails.logger.debug "ImportOrder created for #{amount} #{material} at #{cost_usd} USD."` — no model instantiation, no database write. The companion method `create_player_contract` also only logs (GCC path). Neither creates actual PlayerContract or ImportOrder records.
**What I already checked**: Confirmed via direct source audit. File: `app/services/ai_manager/contract_creation_service.rb`. Both methods are stubs that only log. No ImportOrder model exists in the codebase — only PlayerContract and MissionContract models exist for contracts. The USD funding path appears to be a planned feature that was never implemented.
**What needs a second opinion**: Whether this is a real gap in a live code path (contracts using USD imports could be affected) or dead/unused code. Needs someone to trace what calls `create_import_order` and whether it's actually exercised anywhere. If nothing calls it, it's dead code; if something does call it, contracts would silently fail without error.
**Status**: **RESOLVED (2026-08-06, task filed as `2026-08-06-MEDIUM-BUGFIX-CONTRACT-CREATION-SERVICE-IMPORT-ORDER-STUB.md` in backlog/current/)**

### 2026-09-06 — Potential regex mismatch in mission_profile_analyzer.rb
**What happened**: During the CNT fabricator naming collision investigation, it was noted that `mission_profile_analyzer.rb` uses a regex `/cnt_fabricator/i` against the `name` field of blueprints. However, no existing blueprint's `name` field contains this substring (they use more descriptive names like "Carbon Nanotube Fabricator Mk1"). This means `has_cnt_fabricator` may always evaluate to `false`.
**What I already checked**: Verified blueprint `name` fields via `grep` and confirmed the regex in `mission_profile_analyzer.rb`.
**What needs a second opinion**: Should the regex be updated to match the new naming convention, or should the blueprints be updated to include the technical identifier in their names?
**Status**: OPEN
**What I already checked**: Confirmed via direct source audit during documentation work. File: `app/models/logistics/contract.rb`. The Ruby parser does not require newlines between statements if they're on different logical lines (the file likely uses semicolons or the parser handles it), but the visual layout makes it impossible to read without rendering. Need to verify whether this causes actual runtime issues or is purely cosmetic.
**What needs a second opinion**: Whether this is purely cosmetic (readability only) or actually causes a parsing/execution problem. If it's just style, low priority; if it silently causes a runtime issue, this needs quick attention. Also: does the file actually parse correctly in production, or has no one tried to load this model in a context where the syntax matters?
**Status**: **RESOLVED (2026-08-06, `ruby -c` confirms "Syntax OK" — purely cosmetic, no action needed)**

### 2026-08-05 — Magnetosphere: 41 bodies defaulting to 0.5 strength (known gap)
**What happened**: After the baseline+modifiers magnetosphere fix, 41 celestial bodies in sol-complete.json still have no explicit `magnetosphere_strength` value and default to 0.5. This is not urgent but will affect atmospheric loss calculations downstream once Task 2 runs. Bodies that need explicit values include moons without intrinsic fields (should be 0.0), gas/ice giants' moons orbiting protected parents, and dwarf planets/protoplanets.
**What I already checked**: Verified against sol-complete.json — bodies with explicit magnetosphere_strength: Mercury (0.0001), Venus (0.3), Earth (1.0), Mars (0.0), Jupiter (1.0), Saturn (0.9), Ganymede (0.15), Titan (0.0). All other 41 bodies lack this field entirely.
**What needs a second opinion**: Should these defaults be set explicitly in JSON, or is the default of 0.5 acceptable for now? If explicit values are needed, which bodies get which values based on their type/parent relationship?
**Status**: OPEN — known gap, low urgency, will surface when Task 2 runs

### 2026-08-15 — Data-driven celestial body generation task: fabricated completion report
**What happened**: Task `2026-08-02-HIGH-ARCHITECTURE-DATA-DRIVEN-CELESTIAL-BODY-GENERATION` claimed completion with a sigmoid-based core-state/dynamo gate and 40/0 passing tests. Independent verification found: `calculate_magnetosphere_strength()` is a stub (`baseline + 0.0 + 0.0 + 0.0`), test count was 30/0 not 40/0, Topaz removal and sol-complete.json values were genuinely correct (partial truth).
**What I already checked**: Verified stub code at `procedural_generator.rb:1385-1402`, confirmed 18+12=30 examples across both spec files via dry-run RSpec, zero Topaz hits in app/, sol-complete.json values confirmed correct. No missing test recovery possible (git history shows specs were created in commits d59613a0/7d0b2309/2cbabf41 with no 40-example version).
**What needs a second opinion**: Resolved — re-scoped task `2026-08-15-HIGH-FIX-MAGNETOSPHERE-STUB-CALCULATION.md` drafted with real sigmoid implementation, 11 new tests (including baseline=1.0 edge case), corrected continuity test, and fill-in-only completion report template requiring pasted command output. Dispatch approved by coordination review; Tracy holding assignment for later session.
**Status**: **RESOLVED (2026-08-15, re-scoped task drafted and dispatch-approved)**

### 2026-08-30 — IN FLIGHT: Live Game-Loop Reality Check (dispatched)
**Task**: `2026-08-29-HIGH-ARCHITECTURE-LIVE-GAME-LOOP-REALITY-CHECK.md`
**Dispatched to**: Qwen local (Copilot) — per task file Agent Assignment
**Dispatched at**: 2026-08-30 (confirmed by Tracy + Claude)
**Status**: ACTIVE — executing agent has completed Step 0 (file moved to `active/`, status updated)
**Scope**: Read-only research — 6 questions about whether a real live game-loop mechanism exists (Game#advance_by_days side effects, scheduled jobs, craft transit state machine, asset wiring for precursor/GCC-sat/Venus-skimmer, PSR ice mining, raw-vs-processed gas storage)
**Blocks**: Event log / admin UI game-status view; ice-mining/PSR mine fixes; Venus skimmer mechanics work (all deliberately held pending this task's findings)
**Expected output**: Findings doc in `projects/galaxy_game/summaries/` with file:line evidence for all 6 questions; NO code changes
**Review gate**: Claude to review findings before any follow-on work is scoped

### 2026-10-26 — PAUSED, low priority: portal wiki draft needs small fixes; stray files in the same commit
**What happened**: Tracy committed the portal design draft and related research to `trmccormick/galaxygame` as `65b0fc6` ("commit current portal draft work."): `docs/wiki_reorganization/transportation/portals/portal-technology-wiki-draft.md` (273 lines), `gemini-research-portals.md`, `grok-chatgpt-review-request.md`, `prompt-chatgtp-review.md`, `prompt-grok-review.md` (all at the repo root) and `galaxy_game/app/data/operational/natural_wormhole_anchor_mk1h_data.json`. Claude (web review agent) reviewed the draft on 2026-10-26 and gave REVISE, small. Tracy (2026-10-26, REPORTED): not the current focus; paused and recorded here as low priority. The draft stays as a design note; the wiki reorganization is being consolidated by a separate ChatGPT plan, so this work must not overlap it.
**What I already checked** (VERIFIED, fresh read-only clone at `65b0fc6`, Claude): (1) `docs/architecture/logistics/navigation/INTRA_SYSTEM_PORTALS.md` (19 lines) states that the EM cost to operate a portal pair "must always exceed" the cost of running a Cycler, and that a single unpaired portal is non-functional; the draft has neither as a rule. (2) The draft's EM-rate conflict: 0.0125 kg/hr is 12,500 mg/hr, so a 12.5 mg/hr figure would differ by a factor of 1,000, as the draft says. `gemini-research-portals.md` has 0.0125 kg/hr at L147 and L249. I found no "12.5 mg/hr" in that file or in the JSON, so the draft's "reportedly" claim has no file and line yet. (3) Several bullet lists collapsed into paragraphs (draft L74-79, 108-110, 175-177, 184-190, 244-254). (4) The JSON is new in this commit, alone in a new `galaxy_game/app/data/operational/` folder; the game's data lives under `data/json-data/operational_data/`, and no `.rb` file I searched loads `data/operational`. I did not search other loaders. Not checked: the handoff and primary source the draft is based on; whether `INTRA_SYSTEM_PORTALS.md` is classified outdated in `docs/wiki_reorganization/` (the draft says so).
**What needs a second opinion**: (1) Add the two `INTRA_SYSTEM_PORTALS.md` rules to the draft as established, or record them as a conflict if the new design differs? (2) Cite file and line for both EM rates, or say where "12.5 mg/hr" came from. (3) Restore the bullet lists, one item per line. (4) Tracy: where should the root research and prompt files live, and what should happen to the new JSON (move, keep as a research sample, or remove)? Those are your calls. When the ChatGPT consolidation plan lands, check these against it first.
**Proposed fix when picked up**: one Qwen task, galaxygame repo, one file, items (1) to (3) only, with exact old and new text drawn from the findings above; items in (4) are not Qwen's to decide.
**Priority**: LOW. Paused by Tracy; not the current focus.
**Status**: OPEN, PAUSED. Review disposition REVISE (small) recorded 2026-10-26; no edits made to the draft.

**Addendum 2026-11-07 (web review session)**: PARTLY ADDRESSED; still OPEN for item (4) only. Claude read `gemini-research-portals.md` in full (276 lines, VERIFIED at galaxyGame `04c867b3`). (a) Correction to "What I already checked" item (2): the blueprint sample in that file does carry the figure, as `"em_consumption_rate_mg_hr": 12.5` at L80; the earlier search was for the string "12.5 mg/hr", which does not match that key form. The 0.0125 kg/hr figure is in the opening prose and at L147 and L249, so the factor-of-1,000 discrepancy the draft describes now has file and line. (b) Items (1) to (3) are no longer needed as fixes to the draft. The wiki now holds the portal design (`docs/wiki/technical/portals_and_aws.md`, `natural_wormholes_and_em.md`); the frozen portal pages are recorded as harvested in the galaxyGame migration log (`858eb18a`, `04c867b3`); and the rule that portal EM cost must exceed Cycler cost was superseded by Tracy's ignition model, in which EM is spent mainly at the opening and fusion or electrical power and containment hold the aperture open (REPORTED, Tracy, 2026-11-07). The draft stays frozen. (c) Item (4): Tracy decided on 2026-11-07 to hold this file and not leave it at the repo root. There may be a Gemini chat-log folder under `docs/`; the candidates found by tree listing at `04c867b3` are `docs/agent/archive/gemni-chats` (spelled that way), `docs/agent/chat-logs` and `docs/archive/chats`. The destination is not chosen, and no file has been moved. The same question applies to the other three root files and the JSON from `65b0fc6` (not re-checked today). (d) Treat the file as reference only. Its figures (EM rates, 1,200 kW, 5,000 kg/hr, dimensions, costs) were generated by an agent and are not design data (REPORTED, Tracy, 2026-11-07). The file is not named in the wiki migration log. The Status line above is left as written; this addendum is the current status.

### 2026-11-06 — Eden (AOL-732356) JSON: asteroid and star values internally inconsistent; may predate later generator changes
**What happened**: While verifying wiki pages against science fact, Tracy supplied a copy of `aol-732356.json` (an early generation; it is gitignored, so it is not in the repo and its generator version is unknown). Computing from the file's own fields: all 12 asteroids (XXV to XXXVI) list masses of 0.7 to 9.3 x 10^19 kg but radii of only 2.6 to 48 km, which implies densities from about 52,700 to 119,000,000 kg/m^3 (osmium is about 22,600). At 2 g/cm^3 their masses would be about 10^14 to 10^18 kg. XXXV (48 km radius, 2.44 x 10^19 kg) implies about 52.7 g/cm^3. Mass looks drawn independently of size. Other values in the same file that do not match the star: asteroid orbital periods are the 1 solar-mass Kepler values (XXXV at 3.85 AU lists 2,761 days; for 0.147 solar masses it would be about 7,200); surface temperature matches 1 solar luminosity (XXXV lists 141.6 K; with the listed 0.0012 L-sun it would be about 25 K); the star's listed radius (0.336 R-sun) and temperature (3,142 K) give about 0.0099 L-sun, not the listed 0.0012; and "M0V" does not fit 0.147 solar masses (M0V is about 0.5). XXXV's listed surface gravity (0.072 m/s^2) does not match G*M/R^2 from its own mass and radius (about 0.71).
**What I already checked** (Claude, review of an uploaded file, not of a repo commit): arithmetic on the 12 asteroid entries and the star block only. I have not read the generator code, so the cause is unconfirmed. The file may have been tweaked or regenerated since this copy was made.
**What needs a second opinion**: (1) Is the current generator (and the current Eden JSON) still producing mass and radius independently, and still using 1 solar mass and 1 solar luminosity for period and temperature? Someone with the code and the live data should confirm before anything is changed. (2) Which asteroids does the initial WS depend on, and what minimum size and mass do they need (anchor role, plus enough material for tug slag)? (3) Which anchor-mass threshold is canonical: 10^16 kg (`docs/architecture/systems/aol-732356.md`) or 10^15 / 5 x 10^16 kg (`docs/wiki/infrastructure/pre_player_era_logistics_architecture.md` 4.3), and does it apply before or after hollowing (30% hull retained)?
**Decision recorded (Tracy, 2026-11-06)**: leave this as a note; no update to the Eden JSON or the generator at this point. The wiki should describe the belt/survey model (placed anchor asteroids explicit; belt, Kuiper and Oort populations defined by parameters and spawned on survey; "unknown until surveyed" allowed) and tag unverified numbers PROPOSED / PLACEHOLDER.
**Priority**: LOW (data and generator tuning is not the current focus).
**Status**: OPEN

### 2026-11-09 — Game clock and simulation advancement: consistency and scheduling review
**What happened**: During the game-time/communications design discussion, a focused static review of the current `galaxyGame` clock and loop found that clock advancement and subsystem processing are not consistently coordinated. A new wiki page, `docs/wiki/engineering/game_time_and_simulation_loop.md`, records the proposed intended flow as DIRECTION (not yet owner-confirmed implementation detail).

**What I already checked** (static source inspection only; no runtime tests executed in this documentation pass; code snapshot `fbf7b72`):
- `galaxy_game/app/models/game_state.rb`: `update_time!` accumulates fractional days and resets `last_updated_at`; speed values 1–5 currently map to 300/120/60/30/10 real seconds per game day. `toggle_running!` resets the timestamp only when starting; `set_speed` in the controller does not checkpoint at the old speed.
- `galaxy_game/app/jobs/game_simulation_job.rb`: elapsed time is truncated to whole days with `.to_i`; the job returns before scheduling its next run when less than one full day has accumulated, and also returns while paused. The startup initializer schedules one initial run after 10 seconds; subsequent scheduling depends on the job reaching its final scheduling statement.
- `galaxy_game/app/services/game_service.rb`: `Game#advance_by_days` processes settlements, manufacturing jobs, and free deployed craft, then persists time. The current job passes it whole-day intervals.
- `galaxy_game/app/controllers/game_controller.rb`: `index`, `get_time`, and `state` call `update_time!` while running. Those read/request paths can advance the clock and reset the timestamp without running the corresponding subsystem processing.
- `galaxy_game/app/services/game_simulation.rb`: a separate `GameSimulation` pathway exists and processes celestial bodies, while the current background job calls `Game`. Its callers and intended status have not been fully traced.

**What needs a second opinion** (Claude):
1. Confirm the actual runtime scheduling behavior and whether another scheduler/worker mechanism exists beyond the initializer and self-scheduling job. In particular, determine whether the current job can become permanently unscheduled after a sub-day return or pause, and how it is restarted after resume.
2. Trace every caller of `GameState#update_time!`, `Game#advance_by_days`, and `GameSimulation#advance_by_days`; identify which pathway is authoritative and whether any other services/jobs advance or persist the same clock.
3. Assess clock/processing race conditions and lost/duplicated elapsed intervals, including controller polling, speed changes, pause/resume, manual time jumps, worker overlap, and job failure/restart.
4. Review the proposed intended flow in the wiki: one authoritative game-time source and advancement path; fractional elapsed time retained; request/UI reads are read-only; pause/resume and speed changes checkpoint correctly; background advancement continues while running; simulation subsystems process consistent intervals; all ETAs (including communications) use game time.
5. Recommend the smallest safe correction sequence and targeted tests. Do not implement or prescribe a broad refactor in this review. Do not choose final speed multipliers or player-facing timing balance; those require gameplay tuning and Tracy's decision.

**Relationship to existing review**: The older 2026-08-30 `Live Game-Loop Reality Check` entry/task covers broader questions about craft transit, assets, PSR mining, and gas storage. This entry is deliberately narrower and focuses on clock correctness and advancement scheduling. Claude should determine whether the older task is still active and how to avoid duplicating its scope; do not silently close or replace it.


**Addendum — historical documentation and intent extraction (2026-11-09)**:
- Read `docs/agent/tasks/TASK_OVERVIEW.md`, `docs/developer/ui_enhancements.md`, `docs/developer/UI_IMPLEMENTATION.md`, and `docs/developer/TERRAFORMING_SIMULATION.md` in addition to the current wiki and code.
- Durable historical direction: continuous, speed-controlled simulation while running (not turn-based), player run/pause controls, five speed levels in the later UI enhancement notes, explicit time jumps, and long-horizon gradual terraforming. The old docs also identify player agency, attention/notifications, and concurrent AI activity as real-time design considerations.
- Historical UI docs contain earlier client-side animation-frame timing and 1x/10x/100x/1000x examples. Treat these as obsolete prototype examples, not current balance or architecture requirements. Separate rendering/polling cadence from authoritative game-time advancement.
- `TASK_OVERVIEW.md` marks the time-speed discussion PLANNING COMPLETE but references `time_speed_mechanics_discussion.md` and `realtime_vs_turnbased_differences.md`; those source files were not present at the paths checked in the current tree. The contents/conclusions therefore remain unverified.
- Wiki page now records this extracted intent and explicitly distinguishes a simulation that advances while running from an always-on world that advances while offline. Offline progression remains OPEN; do not assume either behavior.
- Migration log now records the legacy sources harvested. The page remains DIRECTION pending owner confirmation and Claude review.

**Status**: OPEN — awaiting Claude review.
