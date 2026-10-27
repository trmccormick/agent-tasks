Haiku: this is a correction pass on your C3 image-path work. Phase 1 is READ-ONLY. Do not edit, stage, commit, revert, stash, reset or push anything. Run one command at a time and paste the output. Do not use docker cp; ./galaxy_game, ./data/json-data and ./data/images are volume-mounted (see docker-compose.dev.yml).

Why: the C3b task in agent-tasks (projects/galaxy_game/tasks/backlog/asset-ui/2026-10-11-MEDIUM-BUG-FIX-C3B-RH400-CATALOG-IMAGE-URL.md) set limits your last commit went past: no hardcoded RH-400 mapping, no new route, no choosing between render candidates, no `git add -A`. Read that task's Gotchas and Stop Conditions first.

Report these items, with evidence, in this order:

1. `git log --oneline -5` and `git show --stat HEAD` in galaxyGame. List every file in your commit(s). Say which ones were not part of the fix (tools/diag_*.rb, anything under docs/new_agent, etc.).
2. `git status --short` in galaxyGame. List what is untracked or modified now.
3. Whether the docs/new_agent/... status commit tracked a symlink target. Run `git ls-files -s docs/new_agent` and report the mode (120000 means symlink).
4. The exact files in `images/catalog` matching rh400, with their paths relative to GalaxyGame::Paths::ASSETS_PATH. Note ASSETS_PATH is JSON_DATA/images, so the relative path starts with `catalog/`, not `images/catalog/`.
5. The URL the existing route `/api/assets/*path` would need for each candidate. Do not add a route.
6. Whether your `/admin/catalog/image/*path` route used the same base as `/api/assets`. (Expected: it did not.)
7. Where `docs/reference/asset-generation/visual_definitions` is visible inside the container (`docker exec web ls /home/galaxy_game/docs 2>&1`). Report only what you see.

Then STOP and write: "Phase 1 complete. Two candidates found: <names>. Waiting for Tracy to choose the catalog render and approve phase 2." Do not pick a render. Do not start phase 2.

PHASE 2 (only after Tracy replies with the render and approval; do not start before):
- `git revert <your fix commit sha>` as a new commit. No reset, no stash. Confirm with `git show --stat` that only your files were reverted.
- In Admin::CatalogController#catalog_data_for_entry only, convert the absolute path from the contract to `/api/assets/<path relative to ASSETS_PATH>` using Pathname#relative_path_from. Return nil if the path is outside ASSETS_PATH. No change to CatalogService or AssetRegistry, no new route, no RH-400 hardcoding.
- Add a regression test that does not stub catalog_data: assert the rendered src starts with `/api/assets/catalog/` and that a GET to that URL returns 200.
- Run the spec per projects/galaxy_game/SESSION_GUIDANCE.md: redirect output to a log file, no `tail`, check the exit status.
- Commit with explicit paths: `git commit -m "..." -- <paths>`. Never `git add -A`.
- Report evidence, then stop. Status/closeout is done later, per SESSION_CLOSEOUT.md.
