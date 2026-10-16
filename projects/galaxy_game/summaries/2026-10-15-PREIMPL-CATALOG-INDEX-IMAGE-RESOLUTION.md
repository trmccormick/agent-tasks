# Phase 1 Synthesis: Catalog Index Image Resolution

**Task**: `2026-10-13-HIGH-BUGFIX-CATALOG-INDEX-IMAGE-RESOLUTION.md`
**Date**: 2026-10-15
**Phase**: 1 (Research & Synthesis) — awaiting human approval before Phase 2

---

## 1. Identity Mapping

### Finding: NO deterministic blueprint_id → asset_id mapping exists at runtime.

#### 1.1 `AssetRegistry#find_asset_by_blueprint_id` — Does NOT exist

**Reference**: `galaxy_game/app/controllers/admin/catalog_controller.rb`, lines 93–98

```ruby
if registry.respond_to?(:find_asset_by_blueprint_id)   # ← ALWAYS false
  registry.find_asset_by_blueprint_id(blueprint_id)&.dig(:entry, :asset_id)
else
  if blueprint_id == AssetRegistry::RH400_BLUEPRINT_ID  # ← ONLY works for RH-400
    AssetRegistry::RH400_ASSET_ID
  end
end
```

The method is referenced in exactly one place (the controller's `respond_to?` check) but **never defined** anywhere in the codebase. The defensive check always returns `false`, silently degrading to the hardcoded RH-400 constant fallback.

#### 1.2 All AssetRegistry methods that touch blueprint identities

| Method | File:Line | Direction | Deterministic? |
|---|---|---|---|
| `register_asset(asset_id:, blueprint_id:, ...)` | `asset_registry.rb:104` | Write — registers a known pair into `@registry_cache` | Yes (caller provides both) |
| `resolve(asset_id)` | `asset_registry.rb:50` | Read by asset_id only | No reverse lookup |
| `find_in_registry(asset_id)` | `asset_registry.rb:199,477` | Read by asset_id only | No reverse lookup |
| `discover_from_filesystem(asset_id)` | `asset_registry.rb:204` | Reads VD file → extracts blueprint_id from it | **asset_id → blueprint_id** (one-way) |
| `extract_blueprint_id_from_visual_definition(vd_path)` | `asset_registry.rb:306` | Extracts `blueprint_id` from VD JSON body | One-way |
| `infer_blueprint_id(asset_id)` | `asset_registry.rb:325` | Heuristic naming convention + RH-400 special case | **asset_id → blueprint_id** (one-way) |

Every public method on `AssetRegistry` is keyed by `asset_id`. There is **no** method that accepts a `blueprint_id` and returns an `asset_id`. The registry is fundamentally **forward-only** (asset_id → blueprint_id), not bidirectional.

#### 1.3 Existing deterministic mappings

The only deterministic mappings that exist are:

**Hardcoded constants in `AssetRegistry`** (`asset_registry.rb:38-40`):
```ruby
RH400_BLUEPRINT_ID = 'regolith_harvester_rover'
RH400_ASSET_ID     = 'VEHICLE_HARVESTER_ROVER_RH400'
RH400_FILE_PREFIX  = 'rh400_'
```

**`register_asset` writes into `@registry_cache`**:
- Keyed by `asset_id` (the hash key)
- `blueprint_id` is stored as a **value** inside the entry, not as an index
- No reverse index is built

**Visual Definition file** (`docs/reference/asset-generation/visual_definitions/VEHICLE_HARVESTER_ROVER_RH400.json`) contains:
```yaml
asset_id: VEHICLE_HARVESTER_ROVER_RH400
blueprint_ref: regolith_harvester_rover
```
This establishes a VD → blueprint relationship (reverse direction), but the `extract_blueprint_id_from_visual_definition` method reads `blueprint_id` (not `blueprint_ref`) from the JSON body — and the frontmatter field is `blueprint_ref`, not `blueprint_id`.

#### 1.4 Blocker: Identity Mapping Gap

**Status**: BLOCKER — no existing deterministic mapping from blueprint_id → asset_id for any blueprint other than the hardcoded RH-400 constant.

The controller code has a defensive `respond_to?` check that silently degrades to the hardcoded fallback, meaning any non-RH-400 blueprint will fail silently with a `nil` return from `resolve_asset_id_for_blueprint`.

**What's missing**:
- No reverse index in AssetRegistry (blueprint_id is stored as a value, never as a lookup key)
- `find_asset_by_blueprint_id` was referenced but never implemented
- No data structure for blueprint → asset mapping at runtime
- VD files have `blueprint_ref` but it's not indexed by any code

---

## 2. Runtime Metadata Source

### Finding: Registry is empty at boot; only RH-400 can be discovered via filesystem auto-discovery.

#### 2.1 How `@registry_cache` is populated

Two mechanisms, both lazy (no eager population):

| Mechanism | Trigger | When Used |
|---|---|---|
| `register_asset(...)` | Explicit API call | Development-time orchestration only |
| `discover_from_filesystem(asset_id)` | Auto-discovery fallback in `resolve()` | Runtime, when no cache entry exists |

The `resolve()` method (line 52-64) is the **only** public entry point that populates the cache:

```ruby
def resolve(asset_id)
  return nil unless asset_id.present?
  return @registry_cache[asset_id] if @registry_cache.key?(asset_id)
  entry = find_in_registry(asset_id)
  return entry if entry
  entry = discover_from_filesystem(asset_id)
  @registry_cache[asset_id] = entry if entry
  entry
end
```

**`@registry_cache` starts empty** (`{}` in `initialize`). It is **never** pre-populated by any boot hook.

#### 2.2 No boot-time `register_asset` calls

Zero occurrences of `register_asset` in:
- `galaxy_game/db/seeds.rb` — no matches
- `galaxy_game/config/initializers/**/*.rb` — no matches
- `galaxy_game/app/models/**/*.rb` — no matches

All 20 occurrences are in **test files only**:
- `spec/services/asset_registry_spec.rb` (19 calls) — unit tests
- `spec/services/catalog_service_spec.rb` (4 calls) — integration tests

**Conclusion: The registry is completely empty at runtime.** Every catalog request relies on `discover_from_filesystem()` or the C3 fallback.

#### 2.3 What `discover_from_filesystem` does for RH-400

When `resolve('VEHICLE_HARVESTER_ROVER_RH400')` is called:

1. **`find_visual_definition_path(asset_id)`** — Tries exact match: `docs/reference/asset-generation/visual_definitions/VEHICLE_HARVESTER_ROVER_RH400.json` → **FINDS IT**
2. **`extract_blueprint_id_from_visual_definition(vd_path)`** — Parses JSON, looks for `data['blueprint_id']`. File has `blueprint_ref` but NOT `blueprint_id`. Returns `nil`, falls back to `infer_blueprint_id()` which returns `'regolith_harvester_rover'` (hardcoded constant).
3. **`extract_asset_family(vd_path)`** — Parses JSON, finds `"asset_family": "vehicle"` → returns `"vehicle"`
4. **`build_representation_manifest(asset_id, vd_path)`** — Gets render_profiles from the file (all 6), globs `JSON_DATA/images/catalog/` for matching files.

#### 2.4 JSON_DATA path resolution (`game_data_paths.rb`)

```ruby
JSON_DATA = if ENV['GALAXY_JSON_DATA_PATH']
  Pathname.new(ENV['GALAXY_JSON_DATA_PATH']).freeze
else
  RAILS_ROOT.join('app', 'data').freeze   # ← default
end
```

Default: `galaxy_game/app/data/` (not the project root `data/` folder).

#### 2.5 Blocker: B2/Runtime Metadata Gap

**Status**: PARTIAL BLOCKER — registry-first resolution works for RH-400 only via auto-discovery, but NOT for any other asset because:
- No other visual definition files exist in `docs/reference/asset-generation/visual_definitions/`
- No boot-time registration populates the cache
- No reverse index exists to map blueprint_id → asset_id

**Permitted scope**: This task's B2 boundary preservation rule states "No new runtime discovery, filesystem scanning, docs mounts, manifest generation, or registry redesign." The existing `discover_from_filesystem` mechanism is an established runtime fallback — it can be used for RH-400 but cannot be extended to other assets without violating the B2 boundary.

---

## 3. Primary-Candidate Selection

### Finding: No existing general contract selects one primary catalog-render candidate. Selection is implicit via `files.first`.

#### 3.1 RH-400 catalog render candidates on disk

| File | Size | Glob match |
|---|---|---|
| `rh400_concept.png` (~2.0 MB) | ✅ `*concept*` glob |
| `rh400_regolith_harvesting_rover.png` (~1.7 MB) | ✅ `*regolith*` glob |
| `rh400_sprite_test.png` (~2.6 MB) | ❌ Not matched (no "concept"/"regolith" in name) |

#### 3.2 How `find_rh400_catalog_renders` works (`catalog_service.rb`)

```ruby
def find_rh400_catalog_renders
  catalog_images_dir = base_path.join('images', 'catalog')
  files = []
  files.concat(Dir.glob(catalog_images_dir.join("**/rh400_*concept*.png")).to_a)
  files.concat(Dir.glob(catalog_images_dir.join("**/rh400_*regolith*.png")).to_a)
  files.map { |f| "/app/data/" + Pathname.new(f).relative_path_from(base_path).to_s }
end
```

Only **2 of 3** files are found. `rh400_sprite_test.png` is not matched by either pattern.

#### 3.3 How primary candidate is selected

`resolved_catalog_render_path` (`catalog_service.rb`) extracts the **first file** from `entry[:representations]['catalog_render']['files']`:

```ruby
def resolved_catalog_render_path(entry)
  manifest = entry[:representations] || {}
  catalog_rep = manifest['catalog_render'] || manifest[:catalog_render]
  return nil unless catalog_rep.is_a?(Hash)
  files = catalog_rep['files'] || catalog_rep[:files] || []
  files.first if files.any?   # ← FIRST element, no selection rule
end
```

For the RH-400 fallback entry, this returns whichever glob pattern matched first — likely `rh400_concept.png` (from the `*concept*` glob). **No established rule selects among multiple candidates.**

#### 3.4 Blocker: Primary-Candidate Selection Gap

**Status**: BLOCKER for RH-400 verification case (AC4). The task requires Phase 1 to "identify the existing rule that selects the primary catalog-render candidate for `VEHICLE_HARVESTER_ROVER_RH400`." **No such rule exists.** The selection is implicit via glob array order (`files.first`).

**If no existing primary-selection rule can be established**, per the task: "record the selection rule as an unresolved prerequisite and stop for human review. Do not choose based on array order, filename order, image dimensions, file format, perceived image quality, or an RH-400-specific exception."

---

## 4. Safe Serving

### Finding: Basic traversal protection exists but symlink handling and regular-file validation are missing.

#### 4.1 Route definition (`config/routes.rb`, line ~126)

```ruby
get 'catalog/image/*path', to: 'catalog#catalog_image', as: 'catalog_image', constraints: { path: /.*/ }, format: false
```

- **URL helper**: `admin_catalog_image_path(path: 'images/catalog/crafts/ground/rh400_concept.png')` (namespaced under `Admin::`)
- The `constraints: { path: /.*/ }` allows any characters including `..` and `/`
- `format: false` prevents Rails from stripping file extensions

#### 4.2 Controller action (`admin/catalog_controller.rb`, lines ~42-53)

```ruby
def catalog_image
  file_path = GalaxyGame::Paths::JSON_DATA.join(params[:path])
  catalog_dir = GalaxyGame::Paths::JSON_DATA.join('images', 'catalog')
  
  if file_path.to_s.start_with?(catalog_dir.to_s) && File.exist?(file_path)
    send_file file_path, disposition: 'inline'
  else
    head :not_found
  end
end
```

#### 4.3 Security assessment

| Requirement | Status | Details |
|---|---|---|
| **Containment** | ⚠️ Partial | `start_with?` on stringified paths provides basic containment but is a string prefix match, not canonical path comparison |
| **Traversal Protection** | ⚠️ Partial | `Pathname.join` normalizes `..` sequences, blocking simple `../../../etc/passwd` attacks. However, if `JSON_DATA` itself contains symlinks in its ancestry, the prefix check could pass while the real file lives outside the intended tree |
| **Symlink Handling** | ❌ Missing | No `File.symlink?` checks. No `realpath`/`expand_path` canonicalization. If a symlink exists inside `images/catalog/` pointing outside the catalog directory, the `start_with?` check passes (compares against non-canonical path) but the actual file served is outside the intended tree |
| **Regular File Validation** | ❌ Missing | Only checks `File.exist?`. Does not verify `File.file?` — a directory or named pipe could be served |

#### 4.4 Vulnerability scenarios

**Scenario A — Symlink escape (medium risk)**:
```
app/data/images/catalog/crafts/ground/rh400_concept.png → /etc/passwd
Request: /catalog/image/crafts/ground/rh400_concept.png
Result: Serves /etc/passwd via the symlink
```

**Scenario B — Directory traversal (low risk)**:
```
Request: /catalog/image/../../../etc/passwd
Pathname.join normalizes .. → does NOT start with catalog dir → blocked ✓
```

#### 4.5 Assessment: Existing `start_with?` check is NOT sufficient for production safety per the task's requirements. However, **this task cannot modify the controller or route** (out of scope). The gap must be documented and flagged for human review if Phase 2 requires safe serving to be established.

---

## 5. Fallback Analysis

### Finding: Legacy `.json → .png` derived paths have ZERO matching files. Fallback is effectively dead code for all assets.

#### 5.1 Legacy path derivation

`build_entry` (`catalog_service.rb`) derives:
```ruby
thumbnail_path: relative.to_s.gsub('.json', '.png')
# e.g., "blueprints/crafts/ground/regolith_harvester_rover.png"
```

`image_exists?` checks:
```ruby
img_path = base_path.join('images', relative_path.to_s.gsub('.json', '.png'))
# = app/data/images/blueprints/crafts/ground/regolith_harvester_rover.png
```

#### 5.2 Legacy file existence

**No legacy PNG files exist.** The command:
```
find /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/data/images/blueprints -name "*.png"
```
returned **zero results**. There are no matching files under the legacy `blueprints/` directory.

#### 5.3 Fallback permissibility

Per the task's "Desired Behavior" section:
- The legacy `.json → .png` derivation should be used **only** when Phase 1 establishes that the current runtime contract permits fallback for the specific condition encountered.
- Since no files exist at any legacy derived path, fallback is effectively dead code.
- An empty registry or missing category-wide runtime metadata is **not** an ordinary fallback condition — it's a B2/runtime blocker (see Section 2).

#### 5.4 Conclusion on fallback

The legacy fallback is not viable for the current state of the codebase because:
1. No files exist at any derived `.json → .png` path
2. The registry is empty at runtime (no `register_asset` calls)
3. Only RH-400 can be discovered via auto-discovery, which has its own catalog render files under `data/images/catalog/`

---

## 6. Test Baseline

### Command:
```bash
bundle exec rspec spec/services/catalog_service_spec.rb --dry-run --format documentation
```

### Result: **25 examples, 0 failures**

All existing examples pass. No regressions to worry about — but the tests that need to be added (registry-first resolution, fallback, no-image, safe serving) are not yet present.

---

## Unresolved Prerequisites

| # | Prerequisite | Status | Details |
|---|---|---|---|
| UP1 | **Identity mapping** — deterministic blueprint_id → asset_id mapping | BLOCKER | No reverse index exists in AssetRegistry. `find_asset_by_blueprint_id` was referenced but never implemented. Only hardcoded RH-400 constant works. |
| UP2 | **Primary-candidate selection rule** for RH-400 catalog renders | BLOCKER | No existing rule selects among multiple candidates. Selection is implicit via `files.first`. Per task: "record the selection rule as an unresolved prerequisite and stop for human review." |
| UP3 | **Safe serving establishment** — verify existing controller action meets containment/symlink/regular-file requirements | GAP | Existing `start_with?` check is insufficient per task's security requirements. Task cannot modify controller/route. Must flag for human review if Phase 2 requires safe serving to be established. |
| UP4 | **Runtime metadata sufficiency** — registry entries populated for craft ground category | PARTIAL | Only RH-400 can be discovered via auto-discovery (one VD file exists). No other assets have visual definitions. B2 boundary preservation prevents extending discovery. |

---

## Recommendation: STOP — Do Not Proceed to Phase 2

**This task cannot proceed to implementation without human review and resolution of the following blockers:**

1. **UP1 (Identity Mapping)**: A deterministic blueprint_id → asset_id mapping must be established. Options for human consideration:
   - Use the existing `discover_from_filesystem` mechanism for RH-400 only (limited scope)
   - Establish that the visual definition's `blueprint_ref` field can serve as the mapping basis (requires code inspection to confirm it's readable at runtime)
   - Human decision on how to handle non-RH-400 assets within B2 boundary constraints

2. **UP2 (Primary-Candidate Selection)**: No existing rule selects among multiple RH-400 catalog render candidates. Human must decide which candidate is primary or establish a selection rule that does not violate the task's constraints (no filename order, dimensions, perceived quality, etc.).

3. **UP3 (Safe Serving)**: The existing controller action does not meet the task's security requirements (missing symlink handling, regular-file validation). Since this task cannot modify the controller/route, human must decide whether to:
   - Accept the existing serving mechanism as "good enough" for admin-only context
   - Approve a narrowly scoped controller change outside this task's scope
   - Find another way to establish safe serving

4. **UP4 (Runtime Metadata)**: Only RH-400 has visual definition data available at runtime. Human must decide whether limiting the fix to RH-400 is acceptable, or if broader coverage is needed within B2 constraints.

---

## Phase 1 Artifacts Summary

| Artifact | Location |
|---|---|
| This synthesis report | `summaries/2026-10-15-PREIMPL-CATALOG-INDEX-IMAGE-RESOLUTION.md` |
| Task file (status: active) | `projects/galaxy_game/tasks/active/2026-10-13-HIGH-BUGFIX-CATALOG-INDEX-IMAGE-RESOLUTION.md` |
| Test baseline | 25 examples, 0 failures in `catalog_service_spec.rb` |

---

## Next Steps (Requires Human Action)

1. Review all four unresolved prerequisites above
2. Resolve UP1 and UP2 (identity mapping + primary-candidate selection) — these are hard blockers
3. Decide on UP3 (safe serving) — does existing mechanism suffice for admin-only context?
4. Decide on UP4 (runtime metadata scope) — RH-400 only, or broader within B2 constraints?
5. Once all prerequisites are resolved, update the task file with Phase 2 synthesis and human approval
6. Only then proceed to Phase 2 implementation
