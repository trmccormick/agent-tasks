# C3b adjustment plan (replaces Haiku's local C3 fix)

Evidence labels: VERIFIED = read in repo @ galaxyGame b816d8e; REPORTED = from the Qwen/Haiku log only.

## Why Haiku's fix should not be kept
- VERIFIED: `ASSETS_PATH = JSON_DATA/images`, so `/api/assets/*path` is relative to `images/`.
  The correct URL is `/api/assets/catalog/crafts/ground/rh400_concept.png`.
  Haiku's route used `images/catalog/...` relative to JSON_DATA (a different base) and a NEW route.
- VERIFIED: `/api/assets/*path` already exists, has a realpath traversal check, and uses send_file.
- VERIFIED: `find_representation_files('catalog_render')` globs `*concept*`, `*regolith*`, `*catalog*`;
  `resolved_catalog_render_path` returns `files.first`. REPORTED: two RH-400 files exist
  (rh400_concept.png, rh400_regolith_harvesting_rover.png). C3b Gotcha 2 / Step 1 says STOP and ask
  when more than one candidate exists. Haiku never raised this.
- VERIFIED: visual definition lives at repo-root `docs/reference/asset-generation/visual_definitions/`;
  compose mounts only `./galaxy_game` (not `./docs`) -> registry cannot see it in the container.
  Haiku's hardcoded `assemble_rh400_entry()` hides that mount gap.

## Decision needed from Tracy (one)
Which RH-400 file is the catalog render?  (a) rh400_concept.png  (b) rh400_regolith_harvesting_rover.png
Until answered, the code should not rely on glob order.

## Proposed minimal correction (after the decision)
1. Revert Haiku's galaxyGame commit with `git revert <sha>` (a new revert commit; no reset, no stash).
   Check `git show --stat <sha>` first: if `git add -A` swept in tools/diag_*.rb or unrelated files,
   `git revert` will remove them from tracking too; confirm that is wanted.
2. Container visibility of the visual definition (separate from the image URL; pick one, Tracy decides):
   - add `- ./docs:/home/galaxy_game/docs` to docker-compose.dev.yml, or
   - leave as is and note that the registry resolves nothing in-container (the controller RH400 constants
     fallback from C3 already covers the catalog page).
3. Image URL fix, controller only (no registry/service change, no new route):
   in `Admin::CatalogController#catalog_data_for_entry`, after `catalog_service.catalog_data(asset_id)`,
   convert absolute filesystem paths to `/api/assets/<path relative to ASSETS_PATH>` using
   `Pathname#relative_path_from(GalaxyGame::Paths::ASSETS_PATH)`; return nil if the path is outside
   ASSETS_PATH; keep nil as nil.
4. Regression test (no stub of catalog_data): create a temp file under ASSETS_PATH/catalog/..., call the
   real controller/service, assert `src` starts with `/api/assets/catalog/`, then `get` that URL and
   expect 200. Log RSpec per SESSION_GUIDANCE (redirect to file, no `tail`).
5. Commit with explicit paths only: `git commit -m "..." -- <paths>`; no `git add -A`.
6. Status: one Session Log entry in agent-tasks status.md with VERIFIED/REPORTED labels; move C3b
   backlog -> active -> completed; confirm one copy.

## Cleanup (separate, after approval)
- Remove hidden `app/data/.diag_rh400_v2.rb` (json-data mount) and untracked tools/diag_rh400*.rb.
- The `docs/new_agent/...` status commit in galaxyGame: check whether it tracked a symlink target.
