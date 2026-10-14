---
status: backlog
resume: false
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as their startup contract. Every word matters.

**One allowed substitution:** before dispatch, replace `<agent-tasks>` with the absolute path of the agent-tasks clone on the machine that will run the task (run `pwd` from the repo root). Fill `[project]`, `[SUBFOLDER]` and `[FILENAME]` as usual. Change nothing else.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: <agent-tasks>/projects/galaxy_game/tasks/backlog/asset-ui/2026-10-13-HIGH-BUGFIX-CATALOG-INDEX-IMAGE-RESOLUTION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/asset-ui/2026-10-13-HIGH-BUGFIX-CATALOG-INDEX-IMAGE-RESOLUTION.md \
         projects/galaxy_game/tasks/active/2026-10-13-HIGH-BUGFIX-CATALOG-INDEX-IMAGE-RESOLUTION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start implementation until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-10-13-HIGH-BUGFIX-CATALOG-INDEX-IMAGE-RESOLUTION.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and acceptance criteria.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT:** Do not modify or abbreviate the text above. Copy it exactly as-is when dispatching this task to an agent. This is the startup contract — every element is required. Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below. The dispatch interface above is ONLY the bootstrap instructions.

---

## Problem Statement

The admin catalog index page (`/admin/catalog`) displays broken images for assets whose actual catalog render files do not exist at the path derived by `CatalogService#build_entry`. This was identified as a critical finding in A2's research and confirmed unresolved in the 2026-10-13 verification summary.

**Source**: A2 synthesis (`summaries/2026-10-06-RESEARCH-ASSET-UI-A2-static-asset-storage.md`) section 4 — "The Mismatch" table showing CatalogService expects `app/data/images/blueprints/crafts/ground/regolith_harvester_rover.png` but actual images live at `data/images/catalog/crafts/ground/rh400_concept.png`.

**Verification**: 2026-10-13 verification summary (`summaries/2026-10-13-A2-VERIFICATION-B1-B2-COMPLETION-CLAIMS.md`) section 2 — "Catalog index page images: ❌ Unresolved — still uses old .json → .png derivation."

---

## Prerequisite: Deterministic Identity Mapping (Revision 1)

**Before any implementation, the identity mapping between blueprint IDs and AssetRegistry asset_ids MUST be established by code evidence.**

The mapping must satisfy all of the following:
- **Deterministic**: One-to-one or one-to-many lookup with a single unambiguous result. No filename inference, no fuzzy matching, no first-match selection.
- **Code-supported**: The mapping must exist in registered registry entries (via `register_asset()`) or in an existing data structure. If no deterministic mapping exists in the current codebase, this is a blocker — not a gap to work around.
- **Prohibited methods**: Filename inference (e.g., deriving `VEHICLE_HARVESTER_ROVER_RH400` from `regolith_harvester_rover` via string transformation), fuzzy matching across registry entries, selecting the "closest" match, and any RH-400-specific hardcoded mapping in general-purpose resolution logic.

**Evidence to provide before implementation**: Show the exact code path where `blueprint_id` → `asset_id` is resolved. If it requires iterating all registry entries and comparing `:blueprint_id`, document that as the mechanism — do not invent a new one.

---

## Current Behavior

`CatalogService#build_entry` (line ~220 in `galaxy_game/app/services/catalog_service.rb`) derives image paths using blueprint-relative path substitution:

```ruby
thumbnail_path: relative.to_s.gsub('.json', '.png')
# e.g., "blueprints/crafts/ground/regolith_harvester_rover.png"
```

The `image_exists?` method (line ~245) checks at:
```ruby
img_path = base_path.join('images', relative_path.to_s.gsub('.json', '.png'))
# = app/data/images/blueprints/crafts/ground/regolith_harvester_rover.png
```

This path does not exist for any asset whose images are stored under `data/images/catalog/...` (the actual catalog storage location established by A2). The result: the index view's `image_tag "/images/#{thumbnail_path}"` renders 404s.

---

## Desired Behavior (Revision 3)

### Resolution order (strict, no shortcuts):

1. **Registry-first**: Query AssetRegistry for the asset. If a registered entry exists with `representations['catalog_render']['files']` containing at least one file path:
   - Validate each path against the approved catalog root (`GalaxyGame::Paths::JSON_DATA.join('images', 'catalog')`). Reject any path that does not start with this root (path traversal protection).
   - Use the deterministic primary-candidate selection rule established in Phase 1 and approved by the human.
   - If multiple catalog_render files exist but no approved primary rule determines which one to use, treat the representation as unusable for this task. Report that as a prerequisite gap in Phase 1 synthesis and stop before implementation. Do not select the first file, closest match, or any unspecified candidate.
   - If exactly one validated path exists (or the primary-candidate rule selects one), verify it exists on disk. If it exists, use it as `thumbnail_path`.
   - If no validated path exists on disk, proceed to step 2.

2. **Validated fallback**: When registry is unavailable or has no usable manifest data:
   - Use the existing blueprint-relative `.json → .png` derivation.
   - Verify the derived path exists on disk via `image_exists?`.
   - If it exists, use it as `thumbnail_path`.
   - If it does not exist, proceed to step 3.

3. **Explicit no-image**: When neither registry nor fallback yields an existing file:
   - Set `thumbnail_path` to `nil`.
   - Set `has_image` to `false`.
   - Do NOT produce a broken URL or placeholder image element.

### Safe serving requirements:

The generated thumbnail URL must use the existing safe catalog image serving route, not a raw filesystem path exposed to the browser. The implementation must preserve separation between validated filesystem paths used internally and the URL presented to the view. Path validation must enforce containment within the approved canonical catalog root, including protection against path traversal and symlink escape. HTTP verification must confirm that the generated URL serves the expected catalog image.

### Distinguishing missing data from architectural blockers:

- **Ordinary missing data**: A single asset's catalog render file is absent (e.g., the file was never generated, or it exists under a different name). This is handled by step 3 — nil thumbnail_path, graceful view rendering.
- **Architectural runtime blocker**: The registry has no entries for an entire category of assets because development-time orchestration has not populated them, OR the approved catalog root mount is unavailable at runtime. This requires explicit documentation in the synthesis report and a human decision — it is NOT silently swallowed by fallback.

---

## Integration Points

| File | Role | What to inspect/modify |
|------|------|----------------------|
| `galaxy_game/app/services/catalog_service.rb` | Primary — `build_entry` method (~line 220) and `image_exists?` (~line 245) | Add registry-first resolution; preserve fallback |
| `galaxy_game/app/services/asset_registry.rb` | Registry lookup — `resolve(asset_id)` and `representation_manifest(asset_id)` | Understand what data is available at index time |
| `galaxy_game/app/views/admin/catalog/index.html.erb` | View rendering — image_tag calls | Handle nil thumbnail_path gracefully |
| `galaxy_game/spec/services/catalog_service_spec.rb` | Tests — current catalog_service test count (run `bundle exec rspec galaxy_game/spec/services/catalog_service_spec.rb --dry-run --format documentation` to get actual count before starting) | Add focused tests for registry-first resolution, fallback, and no-image; ensure all existing examples still pass |

---

## Implementation Guidance

### Step 1: Understand the registry entry available at index time

Before modifying `build_entry`, inspect the AssetRegistry metadata that is available under the existing B2 contract at catalog index-rendering time. Determine whether the runtime architecture provides the registry metadata needed for the craft ground category.

Do not invoke, extend, or rely on development-time registry discovery or orchestration as part of runtime catalog rendering. If the required runtime metadata is unavailable, document that as a potential prerequisite or architectural blocker in Phase 1 synthesis and stop for human review.

### Step 2: Apply deterministic identity mapping and registry-first resolution

In `build_entry`, after computing `relative` and before setting `thumbnail_path`:

Use only the deterministic blueprint-to-asset_id mapping established and documented in the approved Phase 1 synthesis. If Phase 1 finds no existing deterministic mapping, stop. Do not implement registry-first resolution or invent an alternative lookup. If the approved mapping resolves to an entry with a usable catalog_render representation, apply the validated path-selection procedure in Desired Behavior. Otherwise, use the approved fallback procedure.

**Key constraint**: Do NOT hardcode RH-400-specific logic. The resolution must be generic — any registered asset with a catalog_render representation should benefit.

### Step 3: Update `image_exists?` to use the same resolution path

The `image_exists?` method should check the same sources as `thumbnail_path` assignment:
1. If thumbnail_path came from registry manifest, verify that file exists on disk.
2. If thumbnail_path came from fallback derivation, use the existing `.exist?` check.

### Step 4: Update the index view to handle nil thumbnail_path

In `admin/catalog/index.html.erb`, where `image_tag "/images/#{entry[:thumbnail_path]}"` is called:
- Add a conditional: only render the image tag when `thumbnail_path` is present.
- When nil, render an empty alt or placeholder div (no broken image element).

**Revision 2 — Serving mechanism**: Do NOT create new routes, new serving actions, or new URL conventions. Reuse the existing safe catalog image-serving mechanism established by C3 (`catalog_image` controller action at `admin/catalog_image_path(path:)`). Distinguish clearly:
- **Filesystem paths** (from registry manifest): absolute paths like `/Users/tam0013/.../app/data/images/catalog/crafts/ground/rh400_concept.png` — these are NOT browser URLs.
- **Browser URLs** (for `image_tag`): relative paths like `/admin/catalog/image/images/catalog/crafts/ground/rh400_concept.png` — these must pass the controller's approved-root validation (`file_path.to_s.start_with?(catalog_dir.to_s)`).

Any new route or serving convention requires separate scope approval.

### Step 5: Write tests (Revision 5 — focused service and HTTP/view coverage)

Run the current test count first: `bundle exec rspec galaxy_game/spec/services/catalog_service_spec.rb --dry-run --format documentation` and record the actual number. Use that number in AC5 below.

Add at least these focused test cases to `catalog_service_spec.rb`:
1. **Registry path present** — verifies thumbnail_path comes from manifest, not derivation.
2. **Registry unavailable fallback** — verifies that when the registry cache is empty, the implementer first documents whether the runtime architecture is expected to provide registry metadata (and reports any gap as a blocker for human review) before falling back to `.json → .png` derivation.
3. **No image available** — verifies `thumbnail_path` is nil and `has_image` is false when neither source yields an existing file.
4. **Unsafe path rejection** — verifies paths outside the approved catalog root are rejected (HTTP-level test via the `catalog_image` controller action).
5. **No-image rendering** — verifies the index view renders gracefully with nil thumbnail_path (no broken image element).

All existing examples must still pass. Record the actual pre-change count and post-change count in the synthesis report.

---

## Acceptance Criteria

- [ ] **AC1: Registry path present** — When AssetRegistry has a registered entry with `representations['catalog_render']['files']` containing an existing file, `build_entry` returns that file's path as `thumbnail_path` and `has_image` is true.
- [ ] **AC2: Registry unavailable fallback** — When AssetRegistry cache is empty (no registered entries), `build_entry` falls back to the existing blueprint-relative `.json → .png` derivation. The implementer must first document whether the runtime architecture is expected to provide registry metadata for catalog index rendering; if it does not, this is a prerequisite/architectural blocker reported for human review, NOT silently accepted as acceptable behavior.
- [ ] **AC3: Missing/no-image behavior** — When neither registry nor fallback yields an existing file, `thumbnail_path` is nil and `has_image` is false. The index view does not render a broken image element.
- [ ] **AC4: RH-400 verification** — For the craft ground category (where RH-400 lives), at least one entry's thumbnail resolves via registry manifest path to an existing file under `data/images/catalog/...`.
- [ ] **AC5: Existing tests pass** — All existing `catalog_service_spec.rb` examples still pass. Record actual pre-change and post-change counts in the synthesis report. No regressions in catalog index or detail view behavior.
- [ ] **AC6: HTTP and view verification** — The generated catalog thumbnail URL is verified through an HTTP-level request or equivalent controller/request test. The test confirms that the URL uses the safe catalog image serving route and serves the expected image. The view uses the generated URL rather than a raw filesystem path.

---

## RH-400 Verification Case

Use the craft ground category as the initial verification case. The following assets exist under `data/images/catalog/crafts/ground/`:
- `rh400_concept.png` (RGB, 1402×1122) — catalog render
- `rh400_regolith_harvesting_rover.png` (RGB, 1536×1024) — catalog render alternate
- `rh400_sprite_test.png` (RGBA, 1536×1024) — sprite test candidate

Verify that the primary catalog-render candidate for `VEHICLE_HARVESTER_ROVER_RH400` resolves as a thumbnail_path when the registry has an entry, using the deterministic primary-candidate rule established in Phase 1. If no approved primary rule exists, this is a prerequisite gap — do not select among candidates. Do NOT add RH-400-specific hardcoded paths to the general resolution logic.

---

## Out of Scope

- Naming convention redesign (A2 finding #2 — no follow-up task filed)
- Multi-representation consumer-context selection (RGB vs RGBA, UI display vs surface rendering)
- Dispatching C3b or re-dispatching C2/C3
- Pushing or accepting C3 work
- Changes to B1 (`asset_registry.rb` class structure), B2 contract fields, or either C2 file
- Transit engine topology or any transit-related work
- **Revision 4 — B2 boundary preservation**: No new runtime discovery, filesystem scanning, docs mounts, manifest generation, or registry redesign. The development-time/runtime boundary established by B2 is fixed. This task operates within the existing boundary: AssetRegistry is a read-only data source at runtime; no orchestration or file-generation logic belongs here.
- Permitted implementation files:
  - `app/services/catalog_service.rb`
  - `app/views/admin/catalog/index.html.erb`
  - `spec/services/catalog_service_spec.rb`
  - Existing or newly created controller/request/view specs needed to verify HTTP serving and view behavior.
  - `app/models/asset_registry.rb` and related registry code may be inspected read-only, but must not be modified in this task.
- If HTTP or view verification requires modifying a controller, route, or other application file, stop at the end of Phase 1 and request explicit human approval for that narrowly scoped change.

---

## Architecture Gotchas

1. **Registry may be empty at index time**: AssetRegistry entries are populated by `register_asset()` (development-time orchestration) or `discover_from_filesystem()` (auto-discovery from visual definition files). If neither has run for a given asset, the registry cache is empty.

   **An empty registry is NOT automatically acceptable.** The implementer must document whether the existing runtime architecture is expected to provide registry metadata for catalog index rendering. If it does not, that is a potential prerequisite/architectural blocker and must be reported for human review rather than silently accepted. The legacy fallback remains a compatibility path, not a substitute for resolving the registry/runtime contract.
2. **Blueprint and registry identities differ**: The catalog entry's `:id` may be a blueprint-relative identifier, while AssetRegistry uses a canonical `asset_id`. The implementation must use the deterministic mapping evidenced in Phase 1 and explicitly approved by the human. Do not introduce lookup alternatives, infer IDs, or iterate entries speculatively. If no suitable mapping exists, stop for human review.
3. **Registry instantiation cost**: Creating `AssetRegistry.new` inside `build_entry` (called for every entry in the catalog) could be expensive. Consider caching or lazy initialization.
4. **Path format mismatch**: Registry manifest files are absolute filesystem paths; the view expects relative paths suitable for `image_tag`. Convert appropriately.

---

## Synthesis Reports (Revision 6 — Two-phase separation)

### Phase 1: Pre-Implementation Synthesis (REQUIRED before any code changes)

Save as `summaries/YYYY-MM-DD-PREIMPL-CATALOG-INDEX-IMAGE-RESOLUTION.md`. This is a **planning artifact**, not a completion report. It must evidence the following with code references, not assertions:

1. **Identity mapping**: Show the exact code path where `blueprint_id` → `asset_id` is resolved. If no deterministic mapping exists in current code, document this as a blocker and stop.
2. **Runtime metadata source**: Confirm, with code evidence, which registry metadata is available under the existing B2 contract at catalog index-rendering time for the craft ground category. Do not invoke or extend development-time discovery or orchestration to populate runtime rendering metadata. If the required runtime metadata is unavailable, document the prerequisite/architectural blocker and stop for human review.
3. **Safe serving**: Document the existing catalog image controller action's path-containment behavior and URL-generation behavior. Confirm whether its existing validation is sufficient for the task's approved canonical catalog root, traversal/symlink protection, and filesystem-path versus browser-URL separation. Do not modify the controller in Phase 1. If the existing behavior is insufficient, document the required narrowly scoped change and stop for human approval before Phase 2.
4. **Fallback analysis**: Document which assets in the current codebase have files at the derived `.json → .png` path (if any), and which rely on registry manifest paths.
5. **Proposed tests**: List the exact test names and file locations for new tests, with brief descriptions of what each verifies.
6. **Unresolved prerequisites**: Any missing data, mount issues, or orchestration gaps that block implementation.

**Human approval gate**: Do NOT proceed to code changes until this synthesis is reviewed and approved by the human. The synthesis must be saved as a file — chat summaries do not count.

### Phase 2: Completion Report (after implementation)

Save as `summaries/YYYY-MM-DD-BUGFIX-CATALOG-INDEX-IMAGE-RESOLUTION.md`. This is the **execution artifact** that follows the pre-implementation synthesis. Include:

1. **What was changed** — file, method, line numbers
2. **Resolution path logic** — exact flow: registry lookup → manifest extraction → fallback derivation → nil handling
3. **Test results** — actual pre-change and post-change spec counts (not a fixed number)
4. **RH-400 verification** — which craft ground entries resolved, via which path
5. **Edge cases handled** — empty registry, missing manifest files, path traversal safety
6. **Known limitations** — any gaps or deferred decisions

### What changed from the original template:
- The single "Synthesis Report Template" section is replaced with two distinct phases.
- Phase 1 (pre-implementation) requires code-evidenced planning before any changes.
- Phase 2 (completion) follows implementation and references Phase 1 findings.
- A human approval pause between Phase 1 and Phase 2 is mandatory.

---

## Dependencies

- **Prerequisite**: B1 (`asset_registry.rb`) implemented and committed as `4361fbc3`
- **Prerequisite**: C2/C3 (`catalog_data`, RH-400 fallback) implemented and committed as `d614e4fa` / `d7894ca3`
- **Blocks**: None — this task unblocks the catalog index page from showing broken images
