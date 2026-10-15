---
status: backlog
resume: false
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
---

## Review status note

This task remains in backlog. All readiness checklist boxes must remain unchecked until the human has verified the task paths and dispatch interface, reviewed the Phase 1 scope, and explicitly determined that the task is ready. No implementation is authorized by this document alone. Phase 1 must be dispatched only after the readiness checklist is completed; Phase 2 requires a separate explicit human approval of the saved Phase 1 synthesis.

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

## Prerequisite: Deterministic Identity Mapping

**Before any implementation, Phase 1 must establish the existing, code-supported mapping from a catalog blueprint identity to the canonical AssetRegistry `asset_id`.**

The mapping must satisfy all of the following:

- **Deterministic:** The same blueprint identity must resolve to one unambiguous registry asset. A one-to-many relationship is acceptable only if the existing contract explicitly defines how to select the intended asset.
- **Existing and code-supported:** The mapping mechanism must already exist in the current codebase or in an established data structure used by the current application. This task must not introduce a new mapping mechanism.
- **No inferred identity:** Do not infer `asset_id` from filenames, normalize strings to manufacture an identifier, use fuzzy matching, select the closest match, or use the first registry entry that appears to match.
- **No asset-specific exception:** Do not introduce an RH-400-specific hardcoded mapping in general-purpose catalog resolution.

**Required Phase 1 evidence:** Identify the exact code path, data structure, and relevant methods that resolve the catalog blueprint identity to `asset_id`. If the existing code iterates registry entries and compares `blueprint_id`, document the existing implementation and demonstrate why its result is deterministic. Do not add or extend that lookup during Phase 1.

If no existing deterministic mapping can be established from code evidence, record the missing mapping as a blocker and stop. Do not proceed to Phase 2 until the human reviews and explicitly approves a resolution.

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

## Desired Behavior

### Resolution order (strict, no shortcuts)

1. **Registry-first:** Resolve the asset using the existing deterministic identity mapping approved in Phase 1. Inspect `representations['catalog_render']['files']` only through the established manifest contract.

   - Validate each candidate against the approved canonical catalog root and verify containment using the existing approved path-validation approach. Reject candidates that escape the root, including through traversal or symlinks.
   - If exactly one valid candidate remains, verify that it is a regular file and use it only if it can be safely served through the approved existing image-serving route.
   - If multiple valid candidates remain, use one only if the existing manifest contract or an already-established, documented selection rule identifies the primary candidate unambiguously.
   - Do not choose the first array element, sort candidates to manufacture a preference, infer preference from filenames, choose the largest image, or introduce a new selection heuristic in this task.
   - If multiple candidates exist and no established rule identifies a primary candidate, treat the representation as unusable for this task. Document the unresolved selection rule in the Phase 1 synthesis and stop for human review.
   - If the selected candidate is absent, is not a regular file, fails containment validation, or cannot be safely served, do not emit its path as a thumbnail. Proceed to the validated fallback only if Phase 1 establishes that fallback as safe and compatible with the runtime contract; otherwise, stop for human review.

2. **Validated fallback:** Use the existing blueprint-relative `.json → .png` derivation only when Phase 1 establishes that the current runtime contract permits fallback for the specific condition encountered.

   - If an individual registered asset has no usable catalog render and the approved Phase 1 findings establish the fallback as compatible with the runtime contract, verify that the derived path is safely contained within its approved root and that the file exists.
   - If a successfully resolved asset has no catalog render and the approved fallback file exists, use it only through the approved safe-serving mechanism.
   - If the registry is unavailable, empty for the relevant category, or missing required runtime metadata, do not silently treat that state as an ordinary fallback case. Follow the Phase 1 findings and stop for human review if the runtime metadata contract is unresolved.
   - If an approved fallback is unavailable or invalid, proceed to the explicit no-image behavior only where Phase 1 has established that the condition is ordinary missing image data rather than an architectural blocker.

3. **Explicit no-image:** When neither registry nor approved fallback yields a usable file and Phase 1 establishes that this is ordinary missing image data:
   - Set `thumbnail_path` to `nil`.
   - Set `has_image` to `false`.
   - Do not produce a broken URL or placeholder image element.

### Safe serving requirements

The generated thumbnail URL must use the existing catalog image-serving route and URL helper, not a raw filesystem path exposed to the browser. Filesystem paths and browser URLs must remain distinct throughout resolution and view rendering.

Before implementation, Phase 1 must establish through code inspection whether the existing serving action actually enforces containment within the approved canonical catalog root, including protection against path traversal and symlink escape. Do not assume that an existing route or string-prefix check is sufficient.

The selected file must be a regular file contained within the approved canonical root. An HTTP-level request or equivalent controller/request integration test must verify that the generated browser URL serves the expected file and that traversal and out-of-root requests are rejected.

If safe serving cannot be established without modifying the controller, routes, or another unapproved application file, stop for human review before Phase 2. No such modification is authorized by this task.

### Distinguishing missing data from architectural blockers

- **Ordinary missing data:** A single asset's catalog render file is absent, and Phase 1 establishes that the current runtime contract permits graceful no-image behavior for this condition.
- **Architectural runtime blocker:** The registry has no entries for an entire category of assets because the existing runtime does not provide the required metadata, or the approved catalog root is unavailable at runtime. This requires explicit documentation in the synthesis report and a human decision; it must not be silently swallowed by fallback.

## Integration Points

| File | Role | What to inspect/modify |
|------|------|----------------------|
| `galaxy_game/app/services/catalog_service.rb` | Primary — `build_entry` method (~line 220) and `image_exists?` (~line 245) | Add registry-first resolution; preserve fallback |
| `galaxy_game/app/services/asset_registry.rb` | Registry lookup — `resolve(asset_id)` and `representation_manifest(asset_id)` | Understand what data is available at index time |
| `galaxy_game/app/views/admin/catalog/index.html.erb` | View rendering — image_tag calls | Handle nil thumbnail_path gracefully |
| `galaxy_game/spec/services/catalog_service_spec.rb` | Tests — current catalog_service test count (run `bundle exec rspec galaxy_game/spec/services/catalog_service_spec.rb --dry-run --format documentation` to get actual count before starting) | Add focused tests for registry-first resolution, fallback, and no-image; ensure all existing examples still pass |

---

## Implementation Guidance

### Step 1: Establish the existing runtime metadata source

Before modifying `build_entry`, inspect the current AssetRegistry and catalog-service contracts to identify the metadata actually available during an ordinary catalog index request.

Determine whether an existing runtime-accessible registry or pre-resolved manifest source provides the asset identity and `catalog_render` metadata required for the craft ground category. Cite the code path and data source that make this metadata available at request time.

Treat `register_asset()` and `discover_from_filesystem()` only as implementation details to inspect. Do not invoke, add, extend, or depend on development-time registration, filesystem discovery, Visual Definition documentation mounts, or orchestration to populate runtime catalog metadata.

If the existing runtime architecture does not provide the required metadata, record a runtime metadata blocker in the Phase 1 synthesis and stop for human review. Do not solve the blocker by adding runtime discovery, changing B2, or introducing a new manifest-generation mechanism in this task.

### Step 2: Apply the approved identity mapping and registry-first resolution

Before implementing this step, use the mapping established and approved in Phase 1.

1. Resolve the catalog entry to an AssetRegistry `asset_id` using only the existing deterministic mapping documented in the approved synthesis.
2. If no existing deterministic mapping is established, stop. Do not implement registry-first resolution or introduce an alternative lookup.
3. If the mapping resolves to a registered entry with a usable `catalog_render` representation, apply the deterministic primary-candidate rule and safe path-validation procedure specified in Desired Behavior.
4. If the mapping resolves successfully but the individual entry has no usable catalog render, use the validated legacy fallback only if Phase 1 establishes that the fallback is safe and compatible with the current runtime contract.
5. If the registry is unavailable, empty for the relevant category, or missing required runtime metadata, follow the Phase 1 finding. Do not silently treat a category-wide runtime metadata gap as ordinary missing image data.

**Key constraint:** Do not hardcode RH-400-specific logic. Any registry-first resolution implemented by this task must be generic for assets supported by the approved mapping and runtime contract.

### Step 3: Keep image-existence checks consistent with the approved resolution contract

After Phase 1 has been reviewed and explicitly approved, update `image_exists?` only as needed to support the approved resolution path.

The implementation must not treat a file as a usable thumbnail solely because it exists on disk. For a registry candidate, it must use the approved identity mapping, primary-candidate rule, canonical-root containment validation, and safe-serving route established in Phase 1. For the legacy fallback, it must use the path and validation behavior approved in Phase 1.

If Phase 1 identifies that either path requires an unapproved runtime, registry, controller, or route change, do not implement that path. Stop and request human approval for the narrowly scoped change.

### Step 4: Update the index view to handle nil `thumbnail_path`

In `admin/catalog/index.html.erb`:

- Render the image element only when `thumbnail_path` is present and a usable browser URL has been generated through the approved existing route helper.
- When `thumbnail_path` is nil, render no broken image element. A placeholder may be used only if the existing view design already defines one; do not add a new placeholder convention as part of this task.
- Never pass an absolute filesystem path directly to `image_tag`.

### Serving mechanism and path safety

Do not create new routes, serving actions, or URL conventions. Inspect and, only if proven safe, reuse the existing `catalog_image` controller action and its established URL helper, `admin/catalog_image_path(path:)`.

Keep filesystem paths and browser URLs separate:

- **Filesystem paths:** Paths obtained from the registry manifest or fallback resolution. These are internal values and must never be emitted directly into the HTML as image URLs.
- **Browser URLs:** URLs generated through the existing approved route and URL helper. The view must render this generated URL, not an absolute filesystem path and not a manually constructed route string.

Before implementation, verify that the existing controller resolves requested paths against the canonical approved catalog root and prevents both path traversal and symlink escape. A string-prefix comparison such as `file_path.to_s.start_with?(catalog_dir.to_s)` is not sufficient evidence of containment by itself.

The approved root must be identified from the existing application configuration and resolved to its canonical filesystem location. Phase 1 must document how the controller validates the requested path, handles symlinks, verifies that the resolved file remains inside the approved root, and generates the browser response.

The selected file must be a regular file inside the approved root and must be safely served by the existing action. Verify this through an HTTP-level request or equivalent controller/request integration test that exercises the actual serving path.

If the existing serving mechanism does not meet these requirements, document the exact gap and stop before Phase 2. Do not modify controllers, routes, or serving behavior under this task without separate explicit human approval.

Any new route or serving convention requires separate scope approval.

### Step 5: Add focused service, view, and HTTP/request tests

Before implementation, run the existing catalog service spec-count command and record the actual baseline count. Phase 1 must name the exact files and test examples proposed for each layer.

Add the following tests in Phase 2:

**Service specs — `galaxy_game/spec/services/catalog_service_spec.rb`**
1. **Registry path present:** With a registered entry and one approved, existing catalog-render file, verify that `thumbnail_path` is derived from the approved manifest path rather than the legacy derivation.
2. **Approved fallback:** For an individual asset with no usable catalog render, verify the legacy fallback only if Phase 1 established that fallback as permitted under the existing runtime contract.
3. **No image available:** When the registry entry and approved fallback both lack a usable file, verify that `thumbnail_path` is `nil` and `has_image` is `false`, provided Phase 1 established that this is ordinary missing image data rather than an architectural blocker.
4. **Unsafe manifest path:** Verify that a manifest candidate that escapes the approved canonical catalog root is rejected before it can become a thumbnail URL.

**View spec — the exact existing or newly approved view-spec path named in Phase 1**
5. **No-image rendering:** Verify that an entry with `thumbnail_path == nil` renders no broken `<img>` element for that entry.

**Controller/request spec — the exact existing or newly approved spec path named in Phase 1**
6. **Safe image serving:** Request a valid generated catalog thumbnail URL and verify that the existing route serves the expected image.
7. **Unsafe image serving:** Verify that traversal, out-of-root paths, and a symlink escape are rejected by the existing serving action.

The HTTP/request tests must exercise the actual route and controller path, not only a mocked `CatalogService` result. If the existing serving action cannot pass these tests without an unapproved production-code change, stop and request separate human approval.

All existing examples must still pass. Record the actual pre-change and post-change spec counts in the completion report. Do not interpret test-count recording or mocked unit tests as proof that the production serving path is safe.

---

## Acceptance Criteria

- [ ] **AC1: Approved registry path** — After Phase 1 approval establishes the deterministic identity mapping, primary-candidate rule, runtime metadata source, and safe-serving contract, a registered entry with one approved, existing catalog-render file resolves to that file as the internal `thumbnail_path`; `has_image` is true only when the file is usable through the approved serving route.
- [ ] **AC2: Validated fallback** — The legacy blueprint-relative `.json → .png` derivation is used only for conditions Phase 1 establishes as eligible for fallback under the existing runtime contract. An empty registry or missing category-wide runtime metadata is not silently treated as an ordinary fallback condition.
- [ ] **AC3: Missing/no-image behavior** — When Phase 1 establishes that neither the registry candidate nor the approved fallback yields a usable file and the condition is ordinary missing image data, `thumbnail_path` is `nil`, `has_image` is `false`, and the index view emits no broken image element.
- [ ] **AC4: RH-400 verification** — The Phase 1 synthesis identifies the existing rule that selects the primary catalog-render candidate for `VEHICLE_HARVESTER_ROVER_RH400`. After approval and implementation, the selected candidate resolves through the approved registry path to an existing, safely served file under `data/images/catalog/...`. If no existing primary-selection rule exists, stop for human review instead of choosing a candidate.
- [ ] **AC5: Existing tests pass** — All existing `catalog_service_spec.rb` examples pass. Record actual pre-change and post-change example counts and results in the Phase 1 baseline and Phase 2 completion report. Verify there are no regressions in catalog index or detail view behavior.
- [ ] **AC6: HTTP, path-containment, and view verification** — An integration-level controller/request test exercises the existing catalog image-serving route with a valid catalog image URL and confirms that the expected image is served. The same test layer verifies rejection of traversal and out-of-root paths and, where the test environment supports symlink creation, a symlink pointing outside the approved canonical catalog root. A view test confirms that the index renders the generated route URL rather than an absolute filesystem path and emits no image element for an entry with `thumbnail_path == nil`. These checks must exercise the actual route/controller and rendered view, not only mocked service results. If they cannot be satisfied without an unapproved production-code change, stop for human review.

## RH-400 Verification Case

Use the craft ground category as the initial verification case. The following assets are identified in the task's source evidence under `data/images/catalog/crafts/ground/`:

- `rh400_concept.png` (RGB, 1402×1122) — catalog render candidate
- `rh400_regolith_harvesting_rover.png` (RGB, 1536×1024) — alternate catalog render candidate
- `rh400_sprite_test.png` (RGBA, 1536×1024) — sprite test candidate

Phase 1 must verify these paths and determine whether the existing manifest contract or an already-established, documented selection rule identifies the primary catalog render for `VEHICLE_HARVESTER_ROVER_RH400`. The synthesis must cite the exact code or data structure establishing that rule and identify the candidate it selects.

If the contract does not identify a primary candidate, record the selection rule as an unresolved prerequisite and stop for human review. Do not choose based on array order, filename order, image dimensions, file format, perceived image quality, or an RH-400-specific exception. Do not add a new primary-selection rule as part of this task.

## Out of Scope

- Naming convention redesign (A2 finding #2 — no follow-up task filed)
- Multi-representation consumer-context selection (RGB vs RGBA, UI display vs surface rendering)
- Dispatching C3b or re-dispatching C2/C3
- Pushing or accepting C3 work
- Changes to B1 (`asset_registry.rb` class structure), B2 contract fields, or either C2 file
- Transit engine topology or any transit-related work
- **B2 boundary preservation:** No new runtime discovery, filesystem scanning, docs mounts, manifest generation, or registry redesign. The development-time/runtime boundary established by B2 is fixed. This task operates within the existing boundary: AssetRegistry is a read-only data source at runtime; no orchestration or file-generation logic belongs here.

### Permitted implementation files and scope boundary

Permitted production-code changes:
- `galaxy_game/app/services/catalog_service.rb`
- `galaxy_game/app/views/admin/catalog/index.html.erb`

Permitted test changes:
- `galaxy_game/spec/services/catalog_service_spec.rb`
- A narrowly scoped controller, request, or view spec that verifies the existing catalog image-serving route and index-view behavior. If a new test file is needed, Phase 1 must name its exact path and intended assertions in the synthesis report.

Read-only inspection:
- `galaxy_game/app/services/asset_registry.rb`
- The existing catalog image controller and route declaration.
- Existing registry, controller/request, and view tests or fixtures needed to establish the current contracts.

No other production-code changes are authorized by this task. In particular, do not modify controllers, routes, AssetRegistry contracts, runtime discovery, Visual Definition mounts, manifest-generation behavior, or unrelated application code.

If Phase 1 finds that the existing serving action cannot meet the required path-containment or URL behavior without a controller, route, or other unapproved production-code change, stop before Phase 2 and request explicit human approval for that narrowly scoped change. Adding an approved, test-only spec file does not authorize changes to the production code that it tests.

## Architecture Gotchas

1. **Runtime registry availability is an explicit prerequisite:** Development-time registration and Visual Definition filesystem discovery do not, by themselves, establish that populated registry metadata is available during a normal Rails catalog request. Phase 1 must identify the actual runtime-accessible registry or pre-resolved manifest source and demonstrate how it is populated and read under the existing B2 contract.

   Do not invoke or extend `register_asset()`, `discover_from_filesystem()`, filesystem discovery, or development-time orchestration to fill runtime metadata gaps. An empty registry or missing category-wide metadata must be reported as a blocker unless existing code evidence establishes that the absence is an ordinary per-asset missing representation covered by the approved fallback.

2. **Blueprint and registry identities differ:** A catalog entry's `:id` may be a blueprint-relative identifier, while AssetRegistry uses a canonical `asset_id`. The implementation must use only the existing deterministic mapping evidenced in Phase 1 and explicitly approved by the human. Inspecting an existing registry iteration is permitted; introducing a new iteration or lookup strategy to manufacture a mapping is not. Do not infer IDs, perform fuzzy matching, or select the first or closest candidate. If no suitable existing mapping is established, stop for human review.

3. **Registry instantiation cost:** Creating `AssetRegistry.new` inside `build_entry` (called for every entry in the catalog) could be expensive. Inspect the existing initialization and caching behavior before proposing any change. Do not redesign registry initialization as part of this task without separate approval.

4. **Path format mismatch:** Registry manifest files may be absolute filesystem paths, while the view requires a browser URL. Use the approved existing route helper to produce the browser URL. Never expose a raw filesystem path to the view as an image URL.

## Synthesis Reports (Two-phase separation)

### Phase 1: Pre-Implementation Synthesis (REQUIRED before any code changes)

Save the report as `summaries/YYYY-MM-DD-PREIMPL-CATALOG-INDEX-IMAGE-RESOLUTION.md`.

This is a planning artifact, not a completion report. Phase 1 is inspection and evidence collection only. Do not modify application code, tests, routes, registry contracts, runtime configuration, asset files, or task scope during this phase.

The report must include the following, with exact repository paths and relevant methods, code excerpts, or line references. Assertions without supporting code evidence are insufficient.

1. **Identity mapping:** Show the existing code path or data structure that resolves the catalog blueprint identity to a canonical `asset_id`. State whether the mapping is deterministic and why. If no existing deterministic mapping is established, record a blocker and stop.
2. **Runtime metadata source:** Identify the existing runtime-accessible registry or pre-resolved manifest source used during a normal catalog request. Show how the relevant craft-ground metadata becomes available under the existing B2 contract. If it is unavailable or depends on development-time discovery, record a blocker and stop.
3. **Safe serving:** Inspect the existing catalog image-serving controller action and URL helper. Document request-path resolution, canonical-root containment, traversal protection, symlink handling, regular-file validation, and browser URL generation with code references. Do not modify the controller or route. If safe serving cannot be established, record the exact gap and stop for separate human approval.
4. **Primary-candidate selection:** Identify the existing manifest contract or established rule that selects one primary `catalog_render` file. Show how the rule resolves the available RH-400 candidates. If no existing rule selects one unambiguously, record a blocker and stop.
5. **Fallback analysis:** Identify the legacy derived image path, the code that checks it, and the assets for which the derived path actually exists. Establish whether the existing runtime contract permits fallback for each relevant failure condition. Do not assume that an empty registry is ordinary missing image data.
6. **Test plan:** List the exact test files and proposed example names for service, view, and HTTP/request coverage. State what each test proves and identify any test-layer or production-code change that would require additional approval.
7. **Read-only test baseline:** During Phase 1, record the result and actual example count for:

   `bundle exec rspec galaxy_game/spec/services/catalog_service_spec.rb --dry-run --format documentation`

   Document failures that prevent reliable baseline measurement without attempting unrelated repairs.
8. **Unresolved prerequisites and recommendation:** List every unresolved mapping, runtime metadata, primary-selection, safe-serving, fallback, and test-scope question. Give an explicit recommendation to proceed or stop.

**Mandatory stop condition:** If any required prerequisite cannot be established from existing code and data, do not invent a mapping, selection rule, fallback exception, runtime metadata source, route, or security workaround. Record the exact evidence gap and stop for human review.

**Human approval gate:** Save the synthesis report as a file and wait for explicit human review and approval before starting Phase 2. A chat summary, a proposed implementation, or a completed checklist does not substitute for approval of the saved synthesis report.

### Phase 2: Completion Report (after implementation)

Save the report as `summaries/YYYY-MM-DD-BUGFIX-CATALOG-INDEX-IMAGE-RESOLUTION.md`. This execution artifact must reference the approved Phase 1 synthesis and document the work actually performed.

Include:

1. **Approved plan:** Identify the Phase 1 synthesis report and summarize any approved changes to its plan or scope.
2. **Changes made:** List each changed file, method, and relevant line range. Confirm that no unapproved production files or unrelated systems were changed.
3. **Resolution path:** Document the actual registry lookup, identity mapping, manifest extraction, primary-candidate selection, approved fallback, and nil-handling flow.
4. **Runtime behavior:** Explain how the implementation uses the runtime metadata source established in Phase 1 without introducing development-time discovery, filesystem scanning, documentation mounts, or runtime manifest generation.
5. **Test results:** Record the actual pre-change and post-change example counts and test results, including any failures.
6. **HTTP and view verification:** Identify the exact request/controller and view specs executed. Record the valid-image serving result, unsafe-path rejection results, symlink containment result where supported, and no-image rendering result.
7. **RH-400 verification:** State which approved catalog-render candidate was selected, cite the selection rule, and record the path used to resolve and safely serve it.
8. **Edge cases:** Document empty or unavailable registry behavior, missing manifest files, rejected paths, failed serving, and fallback/no-image behavior.
9. **Known limitations:** Record unresolved issues, deviations from the approved synthesis, or any additional changes that required separate approval.

Do not claim acceptance of a criterion that was not verified by the corresponding test or repository evidence. If implementation reveals a prerequisite or scope change not covered by the approved Phase 1 synthesis, stop and obtain human approval rather than silently expanding the task.

## Dependencies

- **Prerequisite**: B1 (`asset_registry.rb`) implemented and committed as `4361fbc3`
- **Prerequisite**: C2/C3 (`catalog_data`, RH-400 fallback) implemented and committed as `d614e4fa` / `d7894ca3`
- **Blocks**: None — this task unblocks the catalog index page from showing broken images
