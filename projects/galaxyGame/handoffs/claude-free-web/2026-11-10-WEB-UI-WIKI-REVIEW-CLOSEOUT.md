# Handoff: Claude web session, 2026-11-10
Agent folder: `claude-free-web`. Project: galaxy_game (work spans galaxyGame, galaxy-game-ui, agent-tasks). No task file; session scope below.
Session stopped at the usage limit (not finished; a new web session continues). Evidence labels: VERIFIED = read or run this session; REPORTED = stated by Tracy or shown in his terminal output; UNVERIFIED = recalled from an earlier read, not re-checked.

## Scope
1. Session start: read `WEB_AGENT_SESSION_START.md`, then `NEEDS_REVIEW.md`, `status.md` header and the latest handoff.
2. UI architecture direction (JS frontend, Rails API, JSON reader) and corrections to the ChatGPT planning prompt (tasks A to F).
3. Independent review of galaxy-game-ui branch `docs/initial-ui-wiki`.
4. Review of Tracy's uncommitted galaxyGame wiki files; a corrected `02_player_view_architecture.md` was delivered as one finished file.

## Commits
- galaxyGame (local, NOT pushed, REPORTED by terminal output): `fde4e0ce` (Code Alignment banners on four pages, `docs/wiki/README.md` row fix), `f382b4ab` (`ui/02_player_view_architecture.md` new, `ui/README.md` row). `origin/main` was still `fe194898` when last checked (VERIFIED).
- galaxy-game-ui: `9647955` on `docs/initial-ui-wiki`, written by Qwen, pushed by Tracy (REPORTED, remote tip moved from `5ffe1eb`). Claude made no commits.
- agent-tasks: none by this session.

## Tracy's decisions (REPORTED)
- The prototype gets its own JSON reader. First step is a port of the main app's lookup services (`Lookup::*`). Recommended first slice (a recommendation, not a decision): `BaseLookupService`, the paths initializer, `BlueprintLookupService`, `UnitLookupService`.
- Longer term: merge into the prototype UI app as the JS is built out; consolidate game information into `docs/wiki` and slim the tasks. Tasks exist to preserve design intent across limited session time, so they are not retired.
- The five-view split (Planetary, Surface, Settlement, Space, Galaxy) and the navigation principles are ESTABLISHED INTENT, decided a few days before and recorded 2026-11-09 in `docs/wiki/ui/02_player_view_architecture.md`. Layout, view internals and transitions stay DIRECTION or OPEN.
- UI design intent lives in galaxyGame `docs/wiki/ui/` (established 2026-11-04); the prototype repo is an implementation workspace.
- Temporary files go in one folder named `scratch`, reused every time.

## Findings (VERIFIED by reading, nothing run)
- galaxyGame has no single "JSON reader". Candidates: `GalaxyGame::Paths::JSON_DATA` (a path constant, honors `GALAXY_JSON_DATA_PATH`), the 14 `Lookup::*` services (about 2,600 lines, mostly plain file reading, class-level cache), `CatalogService`, `AssetRegistry`. `BaseLookupService` hardcodes `Rails.root/app/data` and ignores `GALAXY_JSON_DATA_PATH`.
- Prototype (galaxy-game-ui, nested `galaxy-game-ui/` Rails app): Rails 8.1, Propshaft, importmap-rails, Turbo, Stimulus, Tailwind, Postgres; no API namespace; dashboard uses `MockDataHelper`; no access to `data/json-data`.
- galaxy-game-ui `docs/initial-ui-wiki` was reviewed in three rounds and the corrections are on the remote at `9647955` (audit report linked, Stage A marked partial, manifest links and EVE wording fixed, four OPEN decisions recorded in the roadmap: navigation labels, API response/error conventions, auth and CSRF, test commands). Review closed.
- galaxy-game-ui `main` (at `390eedc` when checked) has its own separate wiki pages (front door, `future_work.md`, adaptive-AI notes); `docs/initial-ui-wiki` is NOT merged into it. Two wiki lines have diverged.
- galaxyGame README row fix was a real bug (literal `\n` joined two table rows). The four banners match the 29 existing Code Alignment banners.
- `docs/wiki/ui/02_player_view_architecture.md` needed: broken link path (`../../architecture/three_layer_views.md`), stricter labels, an Admin Console separation sentence, implementation-state lines (Planetary and Settlement are specification only). All applied in the committed version.
- Risk for a port: the main repo's full-suite baseline (status.md 2026-09-30, REPORTED, may be stale) was 143 failures in 4,764 examples, so "specs pass" is not proof for a port.

## Corrections to my earlier statements
- I said VISUAL ONLY was missing from the evidence vocabulary; it is defined at galaxy-game-ui `docs/wiki/README.md` line 257.
- My first link check missed the nested screenshot manifests; the second round caught them.
- `rm ~/scratch/*` was too broad (it skipped the directory `hyku-720-testing`, which is not this session's and was left untouched). Name exact files from now on.

## Open items and next action
1. Tracy: push the two galaxyGame commits when ready.
2. Reconcile galaxy-game-ui `docs/initial-ui-wiki` with `main`'s separate wiki pages, and decide which wiki holds what (galaxyGame `docs/wiki/ui/` versus galaxy-game-ui `docs/wiki/`). Owner: Tracy, then the next web session.
3. Review the `galaxy_game/NEEDS_REVIEW.md` entry asking whether keeping documentation current should be a general workflow rule (short; read against `GUARDRAILS.md` and the session-start docs). Not started.
4. Review the `NEEDS_REVIEW.md` game clock and simulation loop entry. UNVERIFIED: it cites a code snapshot and `docs/wiki/engineering/game_time_and_simulation_loop.md`; confirm both exist first. Needs a code read of galaxyGame; a fresh session suits it.
5. Unresolved design questions for Tracy: prototype as the new app or as front end and API only; copy versus shared reader code; which path source wins; keep or reset the lookup cache; how the prototype reaches `data/json-data`.
6. ChatGPT's answers and task drafts (A to F) were not seen this session.
7. Still open from the 2026-11-07 handoff, not touched this session: Qwen results, the tug and slag NEEDS_REVIEW draft, the Mars-moon options wiki page, the workflow-review pass, the wiki lint errors.

## Blockers
None. Claude cannot write to the repo; this file and the status.md entry must be saved and committed by Tracy. `~/scratch/hyku-720-testing` is not this session's.
