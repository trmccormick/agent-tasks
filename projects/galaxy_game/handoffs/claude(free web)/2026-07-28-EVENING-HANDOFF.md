Session Handoff — Galaxy Game — 2026-07-28 (evening)

## Closed this session

1. **TerraSim vs UI biome-classification investigation drafted and filed** — NEW TASK, not yet started.
   Follow-up to the 2026-07-17 elevation-in-biome-classification research (which found `surface_view.js` computes biome client-side from raw lat/elevation, with no elevation adjustment). Review surfaced a deeper architectural question: is the client independently recomputing biome, or is it meant to render whatever TerraSim already decided? Filed as a trace-only research task (no code changes) to `backlog/research/2026-07-28-HIGH-RESEARCH-TERRASIM-UI-BIOME-DATAFLOW.md`. Explicitly blocks any future elevation-implementation task until it resolves which of two scenarios applies (duplicate logic vs. TerraSim never exported biome at all). **Correction note**: `backlog/` uses phase folders (`current`, `design`, `deferred-cleanup`, `drafts`, `procedural_generation`, `research`, `superseded`, `ui`), not month-dated folders — earlier draft of this task had a wrong `backlog/2026-07/` path, corrected before handoff.

2. **Elevation-in-biome-classification research task (2026-07-17) closed properly.**
   Was left stranded in `active/` after completion — moved to `completed/2026-07/` this session, verified single copy via `find`.

3. **Visual Profile `precision_industrial_v1` and Production Asset Render Template — both locked at v1.1/v1.0.**
   Two rounds of ChatGPT collaborative review. Final state:
   - Visual Profile v1.1: added Status/Version header, Purpose section, PromptBuilder Contract, Evolution Policy, merged Consistency+Family Relationship rules, softened "Construction Method" to "Typical Manufacturing Method" (descriptive not prescriptive), replaced stale "open items" (isometric assumption) with a **RESOLVED** section citing the actual top-down/no-directional-sprite findings from 2026-07-27. Reference list kept to RH-400 only — did not add hypothetical units.
   - Production Asset Render Template v1.0 (renamed from Sprite Render Template): top-down orthographic, transparent background, 1024×1024 PNG, normalized framing (~75% canvas regardless of physical size — blueprint JSON remains sole source of truth for real-world scale, framing is explicitly not a scale indicator), Visual Profile Contract locking STYLE/MANUFACTURING/MATERIALS to profile inheritance, and a Template Evolution Policy explicitly scoping "fix the template, not the asset" to render behavior only — does not authorize changing locked Visual Profile attributes.
   - Deliberately deferred: ChatGPT's proposed Render Profile / Material Profile system (separate reusable specs for camera/lighting vs. material appearance) — good idea, not adopted until there's a real second use case, per the project's standing "don't design for hypothetical use cases" principle.
   - RH-400 Asset Family sequencing agreed: Catalog Render done; Production Asset next (everything else in the family derives from/validates against it); working rule — if the Production Asset doesn't extract cleanly, fix goes into the template, not the asset.

4. **Design docs relocated from agent-tasks to galaxyGame repo — DONE, verified clean on both ends.**
   All 8 canonical design docs (VISUAL_PHILOSOPHY.md, DESIGN_SYSTEM_SUMMARY.md, DESIGN_SYSTEM_ARCHITECTURE.md, VISUAL_DEFINITION_TEMPLATE.md, ASSET_GENERATION_ARCHITECTURE.md, UNIFIED_ASSET_CATALOG_ARCHITECTURE.md, DESIGN_RESEARCH_INDEX.md, EXISTING_ASSET_AUDIT.md) moved from `agent-tasks/projects/galaxy_game/design/` to `galaxyGame/docs/reference/asset-generation/`, cross-repo (no shared git history, deletion/addition committed separately with provenance notes in each message). Both new Visual Profile / Render Template docs landed in the same folder. agent-tasks side: `git status` confirms zero trace of `design/` remaining, deletion committed (`200acf5`). galaxyGame side: **the actual add-commit for the 10 relocated/new docs was drafted but not yet confirmed run** — check `git status` in galaxyGame for `docs/reference/asset-generation/` before assuming this is committed.

5. **Composite-sheet sprite extraction retirement — content update drafted for Qwen, not yet confirmed run.**
   Old `unit_sprites/sprite_00–15.png` and related settlement/atlas files deleted manually by Tracy (galaxyGame commit `f742a430`) — scope expanded beyond the original task's `unit_sprites/`-only listing to include `settlement_atlas.json`, `settlement_sprites*`, `settlement_terrain.png`, `source_preview.jpg`, `galaxy_regional_atlas.json`, `settlement_tileset.json`. Tracy inspected and confirmed disposable before deleting (Time Machine + git history both retain them). The two JSON files were extraction-guide metadata for the now-deleted sprite sheet, not standalone data — deleted as one unit with the images they described, not independently assessed. Deleted rather than archived (deviates from the task's original Gotcha 1 guidance to archive, not delete) — acceptable given full backup coverage. **A Qwen command was drafted this session to update the task's Completion Report with these details and move it `active/` → `completed/2026-07/` — confirm this actually ran before next session.**

6. **`InfrastructureCostCalculator` fix confirmed safe and committed (or ready to commit).**
   Diff verified matches the known audit fix: `can_produce?(destination, ...)` → `can_produce_locally?(material.chemical_formula)`. Initially flagged as fully dead code (zero callers in `app/`/`spec/`) — corrected after Tracy found a manual script `galaxy_game/test_realistic_costs.rb` that does reference the class. Investigated: single commit `90ba2a25`, dated 2026-01-31, swept in under an unrelated "diameter-based grid sizing for terrain generation" commit message, never touched since — confirmed one-off/dormant, doesn't change the practical dead-code conclusion. **A one-line addition documenting this was drafted for the open backlog task `2026-07-26-LOW-BUGFIX-INFRASTRUCTURE-COST-CALCULATOR-DEAD-CODE.md` — confirm Qwen ran it.** The commit for the calculator fix itself: confirm `git log` shows it landed (command was given, not confirmed run before session ended).

## Still open, not started

- **`gas_spec.rb`** — modified, diff never reviewed this session. Next thing to look at.
- **`CELESTIAL_BODY_DATA_CONVENTIONS.md`** — modified, diff never reviewed this session.
- **RH-400 Production Asset generation** — the actual next asset-pipeline deliverable, now that both canonical docs are locked. Nothing generated yet.
- **Manufacturing::Service duplicate** — two follow-up tasks still sitting untouched in backlog (git-blame investigation + broader manufacturing-chain-vs-docs research), carried forward from 2026-07-26.
- **Visual Definition v1.0 for RH-400** — task file ready (`2026-07-26-MEDIUM-DATA-VISUAL-DEFINITION-V1-RH400.md`), not started. Note: may now be partly superseded/informed by the newly-locked Visual Profile doc — worth a quick read-through of that task file next session to check it still matches current decisions before dispatching it as-is.
- **Manufacturing origin vs. tech tier visual design question** — still informal, not written into any doc (carried forward from 2026-07-26).
- **Gameplay Loops Overview** and **slow `base_satellite_spec.rb` investigation** — both still untouched in backlog, carried forward across multiple sessions now.
- **Luna baseline re-verification** — queued since 2026-07-26 (`luna_mission:execute` fresh 17/17 after the postgres healthcheck stack reset, then `luna:simulate_operations` tick-by-tick read-through). Not done this session — carried forward again.

## New standing facts from this session

- `backlog/` folder structure is phase-based (`current`, `design`, `deferred-cleanup`, `drafts`, `procedural_generation`, `research`, `superseded`, `ui`), not month-dated. Filed to memory to avoid repeating the wrong-path mistake.
- ChatGPT session time for asset-generation work is a limited resource — plan the highest-value thing to do before the session starts, don't design speculative future systems live (Render/Material Profiles were correctly deferred for this reason).
- Cross-repo doc moves (agent-tasks ↔ galaxyGame) don't preserve git history — treat as delete-in-one/add-in-other with a provenance note in both commit messages, not a `git mv`.

## Next session, concretely

1. Confirm the two drafted-but-unconfirmed Qwen actions actually ran (sprite-extraction task completion, cost-calculator backlog note) — check `git log`/`git status` in both repos rather than assuming.
2. Confirm the galaxyGame commit for the 10 asset-generation docs landed.
3. Review `gas_spec.rb` and `CELESTIAL_BODY_DATA_CONVENTIONS.md` diffs — unstarted from this session's cleanup batch.
4. Move to actual RH-400 Production Asset generation under the now-locked template — this is the real next deliverable, everything else this session was groundwork for it.
