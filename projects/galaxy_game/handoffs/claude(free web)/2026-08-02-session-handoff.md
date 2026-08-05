# Session Handoff — 2026-08-02

## Resolved & Closed

- **Spin-Gravity Core Mk1** — blueprint, operational data, subsurface specs, and full tech-tree chain all implemented (`gravitational_engineering` → unlocks the unit; new `magnetic_systems` and `precision_manufacturing` tech entries created). Task closed, status.md updated.
- **Sprite/asset pipeline incident** — real terrain (45) and biome (13) sprites restored from Time Machine; root cause fixed (docker-compose mount moved from `public/assets` to `app/data/images`, matching the maps/tilesets/geotiff convention); new `Api::AssetsController` serves images with path-traversal protection. Sprite Tiles task closed honestly.
- **19-file v1→mk1 rename** — completed cleanly; one force-add mistake into gitignored `data/` caught and corrected via `git reset --mixed`.
- **Settlement Tiles false-completion** — confirmed already resolved from a prior session; completion report text filled in.
- **NPC Economy Lifecycle docs** — two docs written; three byproduct findings filed to `NEEDS_REVIEW.md` (MarketStabilizationService stubs, missing ImportOrder model, Logistics::Contract syntax issue).
- **Blueprint naming cleanup** — PVE, CAR-300, AeroFab, VSI all correctly relocated/renamed; a 139-file audit confirmed the cleanup held.
- **Propellant consumption research** — closed properly with sourced data (Raptor/RS-25/RD-180).
- **Colony/Settlement hierarchy diagram** — produced, confirms Colony↔Settlement is a parallel governance relationship, not a strict tree.
- **Active-folder audit** — two false-signal tasks found and corrected: CIV4 task and Gameplay Loops Overview were both dispatched to `active/` while genuinely blocked; both moved back to `backlog/current/`.
- **Guardrails updated** (`CLAUDE_SESSION_START.md`, `GUARDRAILS.md`, both planning-agent docs): never `git add -f` anything under `data/` (hit twice this week, different file types); a task's stated blockers are a claim to verify live, never trust by age or filing location.

## CIV4 Surface View Gameplay — re-scoped, back in backlog/current/

Earlier "need new City/Improvement/Yield models" finding was wrong — vocabulary mismatch, not a real gap. Corrected: City = Settlement+Colony (exists), farms = Structures (exist), mines = geological features/Units (exist). Real remaining blocker: exposing existing Structure/Unit/Feature per-tile data through `terrain_data_builder.rb` — a data-export task, smaller than originally scoped. Roads genuinely don't exist anywhere — open design question, not a task. Lat/long-to-grid mapping (`BaseFeature#coordinates` vs. tile row/col) confirmed not needed yet at this stage of development.

## Open threads queued for a Gemini design session

- **AeroFab CNC Module / VSI stubs** — placeholder blueprints (1h/1GCC), need real specs or explicit sign-off as component templates. Backlog task filed.
- **Lava tube outpost** — material costs, production times, blueprint mapping still needed.
- **Terraforming/magnetosphere design** — full concept worked out tonight: L1 magnetic shield as AI-Manager-maintained infrastructure (not magic), built from Earth+Luna materials, gated before cycler launch. Mechanism confirmed as *stopping atmospheric sputtering loss* (modern research, post-1990s-applet) rather than actively pumping gas in — existing volatile-release/replenishment machinery (`Geosphere#calculate_volatile_release`, `VolatilePhaseTransitionService`) already runs generically for every world and is wired into the simulation pipeline; the missing piece is specifically the atmospheric-loss term, which doesn't exist anywhere yet. Local `stored_volatiles` reserves are deliberately insufficient by design — imports are the intended resource sink, alongside AI-Manager background terraforming. Real bug found as a side effect: `venus_mars_pipeline.rake` passes an invalid kwarg to `TerraSim::Simulator.new()` and would crash on first run — worth a small standalone fix, separate from the design work.
- **Original Java galaxyGame repo** (`trmccormick/galaxyGame`, `original-incomplete-java-version` branch) — confirmed to exist (an earlier full game attempt, not just the terraforming applet), not yet reviewed. Worth a Qwen clone-and-compare if the terraforming lineage question ever needs a third data point.

## Not urgent, still open

- `item.rb` safe-navigation change (broad blast radius on `Item.create!`) — flagged from the cross-session summary, still worth a look.
- Placeholder sprite generator scripts (`generate_*_sprites.py`) — keep or delete, low stakes.
