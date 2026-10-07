## B1 — Map Asset Registry to Visual Definition

### 1. What the Asset Registry Owns

**Evidence from VISUAL_CONTRACT.md v1.0:**

| Artifact | Domain | Role |
|----------|--------|------|
| **Asset Registry** | Canonical game data | Central registry mapping `asset_id` to all associated artifacts. The single source of truth for asset existence and identity. |
| **Blueprint** | Canonical game data | Defines physical properties, materials, production, cost, deployment. **Does NOT carry visual fields.** |
| **Operational Data** | Canonical game data | Runtime operational properties (power, payload, range, autonomy). Referenced by blueprint via `operational_data_reference`. |
| **Visual Definition** | Development-time visual artifact | Defines visual appearance: silhouette, color profile, material profiles, recognition features, render profiles, camera profiles, complexity levels, design constraints. |
| **Visual Profile** | Development-time visual artifact | Visual composition rules: color schemes, animation profiles, complexity-level variants, shared components across asset families. |
| **Render Template** | Development-time visual artifact | Output formats and rendering parameters: sprite sheet layout, catalog render angles, engineering render styles, blueprint view specifications. |

**What the Asset Registry OWNS:**

1. **`asset_id` as primary lookup key** — Every asset has a single unique `asset_id`. The registry stores this as its primary key.
2. **Asset existence and identity** — The registry is the single source of truth for whether an asset exists and what its canonical identity is.
3. **`asset_id → artifact path resolution** — Development-time orchestration resolves which Visual Definition, Visual Profile, and Render Template apply for a given `asset_id`. This resolution is stored in the Asset Registry/orchestration layer.
4. **`asset_id → visual_profile_id` association** — Per VISUAL_CONTRACT.md Decision (2026-09-12): "Development-time Asset Registry/orchestration owns the `asset_id → visual_profile_id` association." This is stored exclusively in the Asset Registry/orchestration layer, NOT in Blueprint, Visual Definition, Operational Data, or Render Template.
5. **Canonical asset metadata** — The registry stores canonical game data (Blueprint + Operational Data) that feeds into PromptCompiler as already-resolved inputs.

### 2. What the Asset Registry Does NOT Own

**Evidence from VISUAL_CONTRACT.md v1.0 and DECISIONS.md:**

1. **Visual Profile or Visual Definition content** — The registry does NOT define visual appearance rules, color profiles, animation profiles, or render specifications. These are owned by Visual Definition and Visual Profile artifacts.
2. **Blueprint content** — The registry does NOT own the physical properties, materials, production data, cost data, or deployment characteristics defined in Blueprints.
3. **Operational Data content** — The registry does NOT own runtime operational properties (power consumption, payload, range, autonomy).
4. **Render Template content** — The registry does NOT define sprite sheet layouts, catalog render angles, engineering render styles, or blueprint view specifications.
5. **Repository search by `asset_id`** — "No artifact searches the repository by `asset_id`." The registry resolves paths via orchestration; it does not perform filesystem discovery.
6. **Visual Profile resolution in PromptCompiler** — Visual Profile resolution is handled by development-time orchestration, NOT by PromptCompiler's public API. No `visual_profile_path:` argument is added to PromptCompiler.compile.
7. **Runtime game logic** — "Asset-generation tooling is development-time infrastructure outside the Rails runtime."

### 3. How asset_id Maps to Blueprint, Visual Definition, Visual Profile, Render Template, and Representations

**Evidence from VISUAL_CONTRACT.md v1.0:**

```
Canonical Game Data (Runtime)          Development-Time Visual Artifacts
─────────────────────────────          ───────────────────────────────────
Asset Registry                         Visual Definition
Blueprint                              Visual Profile
Operational Data                       Render Template
                                       PromptCompiler (consumes resolved inputs)
```

**Mapping table:**

| Registry Concept | Visual Definition Field | Mapping Type |
|-----------------|------------------------|--------------|
| `asset_id` (registry primary key) | `visual_definition.asset_id` (REQUIRED) | **direct** — shared identity across all artifacts |
| Asset existence | `visual_definition.asset_family` (REQUIRED) | **direct** — registry stores asset_family as metadata; Visual Definition defines valid values: `resource`, `component`, `assembly`, `equipment`, `unit`, `structure`, `vehicle`, `organization` |
| Blueprint physical properties | N/A (Blueprint is separate artifact) | **separate** — Blueprint does NOT carry visual fields per VISUAL_CONTRACT.md |
| Operational Data reference | N/A (Operational Data is separate artifact) | **separate** — referenced by blueprint via `operational_data_reference` |
| Visual appearance rules | `visual_definition.*` fields (silhouette, color_profile, material_profiles, recognition_features, technology_level, manufacturing_style, etc.) | **direct** — registry stores path reference; Visual Definition owns content |
| Visual composition rules | `visual_definition.animation_profile`, `visual_definition.color_profile`, `visual_definition.shared_components` | **via orchestration** — registry resolves `asset_id → visual_profile_id`; ProfileResolutionEngine supplies profile_attributes to PromptCompiler internally |
| Output format specs | `visual_definition.render_profiles` (array of strings) | **direct** — registry stores path reference; Render Template owns content |

**RH-400 concrete example:**

| Concept | Value | Evidence |
|---------|-------|----------|
| Blueprint ID | `regolith_harvester_rover` | Blueprint JSON file name |
| Visual Definition asset_id | `VEHICLE_HARVESTER_ROVER_RH400` | VEHICLE_HARVESTER_ROVER_RH400.json embedded JSON |
| File prefix | `rh400_` | Existing image files: `rh400_concept.png`, `rh400_regolith_harvesting_rover.png`, `rh400_sprite_test.png` |
| asset_family | `vehicle` | Visual Definition field (proposed, "not yet formalized by the Icon Bible") |
| component_class | `harvester` | Visual Definition field |

**Identifier mismatch:** The three identifiers (`regolith_harvester_rover`, `VEHICLE_HARVESTER_ROVER_RH400`, `rh400_`) are all valid but inconsistent. The registry must handle this without renaming files or changing IDs. The mapping is:
- Blueprint ID → `regolith_harvester_rover` (canonical game data)
- Visual Definition asset_id → `VEHICLE_HARVESTER_ROVER_RH400` (development-time visual artifact)
- File prefix → `rh400_` (filesystem naming convention, abbreviated form of the canonical ID)

### 4. How Representation Metadata Should Be Modeled at the Architecture/Contract Level

**Evidence from A3 findings:** RH-400 Visual Definition specifies six render profiles but only three files exist. No explicit representation metadata/manifest exists in the repository.

**Proposed architecture-level modeling:**

| Representation | Visual Definition Field | File Evidence | Status |
|---------------|------------------------|--------------|--------|
| `inventory_icon` | `render_profiles: ["inventory_icon"]` | None found | MISSING |
| `catalog_render` | `render_profiles: ["catalog_render"]` | `rh400_concept.png`, `rh400_regolith_harvesting_rover.png` (2 files) | PARTIAL |
| `engineering_render` | `render_profiles: ["engineering_render"]` | None found | MISSING |
| `blueprint` | `render_profiles: ["blueprint"]` | None found | MISSING |
| `exploded_view` | `render_profiles: ["exploded_view"]` | None found | MISSING |
| `sprite_sheet` | `render_profiles: ["sprite_sheet"]` | `rh400_sprite_test.png` (experimental) | PARTIAL (test only) |

**Architecture-level representation metadata:**

The registry should store a **representation manifest** at the architecture level — not as file paths, but as a structured list of:
- `render_profile` name (from Visual Definition's `render_profiles` field)
- `status`: `exists`, `missing`, `experimental`, `planned`
- `complexity_levels`: which L0-L5 levels are available for this profile
- `notes`: any relevant context (e.g., "experimental", "pre-Icon Bible")

This manifest is **development-time metadata** stored in the Asset Registry/orchestration layer. It does NOT modify Visual Definition content or create runtime dependencies.

### 5. How Missing Representations Are Represented Without Inventing Files

**Evidence from A3 findings:** RH-400 has six planned render profiles but only three files (two catalog renders, one experimental sprite test). No representation manifest exists.

**Proposed approach:**

| Approach | Description | Why |
|----------|-------------|-----|
| `status: missing` in representation manifest | Registry records that a render profile is defined in Visual Definition but no file exists | Honest about absence without inventing content |
| `status: experimental` for test assets | `rh400_sprite_test.png` is explicitly marked as experimental/test, not production | Prevents accidental promotion of test assets |
| `complexity_levels: []` for missing profiles | Empty array indicates no complexity levels are available | Clear signal that generation is needed |
| No default/fallback values invented | Registry does NOT invent placeholder files or assume content exists | Preserves architectural integrity |

**Key principle:** The registry records what IS and what IS NOT. It does not fabricate what should be. Missing representations are a development-time gap to be addressed by asset generation, not by the registry itself.

### 6. How the Registry/Orchestration Layer Interacts with PromptCompiler

**Evidence from VISUAL_CONTRACT.md v1.0:**

```ruby
PromptCompiler.compile(
  asset_id:,
  blueprint_path:,
  operational_data_path:,
  visual_definition_path:,
  render_template_path:
)
```

**Interaction contract:**

| Layer | Responsibility |
|-------|---------------|
| **Asset Registry/orchestration** | Resolves `asset_id → blueprint_path`, `asset_id → operational_data_path`, `asset_id → visual_definition_path`, `asset_id → render_template_path`. Also resolves `asset_id → visual_profile_id` via ProfileResolutionEngine. Supplies resolved `profile_attributes` to PromptCompiler's internal composition boundary. |
| **PromptCompiler** | Consumes already-resolved paths and profile attributes. Composes image prompt from profile attributes, visual definition data, blueprint data, and operational data. Does NOT search repository by `asset_id`. Public interface unchanged. |

**Critical constraint:** "PromptCompiler consumes already-resolved inputs. It does not search the repository by `asset_id`." The registry's role is path resolution; PromptCompiler's role is prompt composition. These are strictly separated.

### 7. How the Architecture Handles Existing Identifier/Naming Mismatches

**Evidence from A3 findings:** RH-400 has three distinct identifiers:
- Blueprint ID: `regolith_harvester_rover` (canonical game data)
- Visual Definition asset_id: `VEHICLE_HARVESTER_ROVER_RH400` (development-time visual artifact, Icon Bible format `[CATEGORY]_[TYPE]_[NAME]_[VARIANT]`)
- File prefix: `rh400_` (abbreviated filesystem naming, matches neither canonical ID)

**Proposed handling:**

| Identifier | Domain | Registry Treatment |
|-----------|--------|-------------------|
| `regolith_harvester_rover` | Blueprint (canonical game data) | Stored as blueprint_id in registry; used for runtime lookup |
| `VEHICLE_HARVESTER_ROVER_RH400` | Visual Definition (development-time) | Stored as asset_id in registry; shared identity across all artifacts |
| `rh400_` | Filesystem naming convention | Stored as file_prefix in registry metadata; used for filesystem path construction |

**Registry data structure (architecture-level, not code):**

```json
{
  "asset_id": "VEHICLE_HARVESTER_ROVER_RH400",
  "blueprint_id": "regolith_harvester_rover",
  "file_prefix": "rh400_",
  "asset_family": "vehicle",
  "component_class": "harvester",
  "representations": {
    "inventory_icon": { "status": "missing" },
    "catalog_render": { "status": "exists", "files": ["rh400_concept.png", "rh400_regolith_harvesting_rover.png"] },
    "engineering_render": { "status": "missing" },
    "blueprint": { "status": "missing" },
    "exploded_view": { "status": "missing" },
    "sprite_sheet": { "status": "experimental", "files": ["rh400_sprite_test.png"] }
  },
  "visual_profile_id": null,
  "blueprint_path": "app/data/blueprints/crafts/ground/regolith_harvester_rover.json",
  "operational_data_path": "app/data/operational_data/crafts/ground/regolith_harvester_rover.json"
}
```

**Key principle:** The registry records all three identifiers without renaming files or changing IDs. It maps them explicitly so downstream consumers understand the relationship.

### 8. Whether/How Filesystem Paths Belong in Registry Data

**Evidence from A4 findings:** CatalogService derives broken image paths from blueprint paths (`.json → .png` substitution). This is a runtime Rails concern that should not exist.

**Proposed approach:**

| Path Type | Stored in Registry? | Why |
|-----------|-------------------|-----|
| Blueprint path (`app/data/blueprints/...`) | YES | Canonical game data; needed by PromptCompiler and catalog consumers |
| Operational Data path (`app/data/operational_data/...`) | YES | Canonical game data; needed by PromptCompiler |
| Visual Definition path (`docs/reference/asset-generation/visual_definitions/...`) | YES (development-time only) | Development-time artifact; needed by PromptCompiler |
| Render Template path (`docs/reference/asset-generation/render_templates/...`) | YES (development-time only) | Development-time artifact; needed by PromptCompiler |
| Image file paths (`data/images/catalog/...`, `data/images/sprites/...`) | NO (not as primary data) | These are OUTPUTS of asset generation, not inputs. Registry stores representation manifest with status, not raw file paths. Filesystem discovery is development-time orchestration responsibility. |

**Key principle:** The registry stores path references for INPUT artifacts (Blueprint, Operational Data, Visual Definition, Render Template) that PromptCompiler consumes. It does NOT store output image file paths as primary data — those are discovered by development-time orchestration from the representation manifest.

### 9. How Catalog and Surface Consumers Should Obtain Resolved Asset Information

**Evidence from A4 findings:** CatalogService has no Visual Definition lookup path. Current image path derivation is broken (`.json → .png` substitution). Catalog pipeline doesn't consume icon data at all.

**Evidence from A5 findings:** SurfaceView.js `KNOWN_ASSETS` is empty Set. Unit-layer grid data exists but does not resolve unit IDs to sprites. No sprite sheets, animation frames, or damage states are modeled/consumed.

**Proposed separation:**

| Consumer | How It Gets Asset Information | Domain |
|----------|------------------------------|--------|
| **Catalog presentation (B2)** | Development-time orchestration resolves `asset_id → catalog_render path` and supplies resolved paths to catalog UI via API or pre-rendered data. CatalogService continues loading Blueprint + Operational Data; Visual Definition lookup is added as a development-time enrichment step, NOT a runtime Rails dependency. | Development-time → Runtime bridge (explicit boundary) |
| **Surface renderer (B3)** | Development-time orchestration resolves `asset_id → surface sprite path` and supplies resolved paths to the game via asset bundles or preload manifests. SurfaceView.js loads sprites from resolved paths; KNOWN_ASSETS is populated by orchestration, NOT by runtime Rails discovery. | Development-time → Runtime bridge (explicit boundary) |
| **PromptCompiler** | Receives already-resolved paths as keyword arguments. Does not search repository. | Development-time only |

**Critical boundary:** "Asset-generation tooling is development-time infrastructure outside the Rails runtime." Catalog and surface consumers obtain resolved asset information through:
1. **Pre-computed manifests** — Development-time orchestration generates JSON manifests mapping `asset_id → resolved paths`. These manifests are bundled with the game or served via API.
2. **Explicit path resolution** — No runtime Rails code performs repository search by `asset_id`. All path resolution happens in development-time tooling.

### 10. Boundaries Needed to Prevent Blueprint from Acquiring Visual Profile/Visual Definition Ownership

**Evidence from VISUAL_CONTRACT.md v1.0:** "Blueprints do NOT carry `visual_profile` or `visual_definition` fields. The link between canonical game data and visual artifacts is `asset_id`, resolved by development-time orchestration."

**Proposed boundaries:**

| Boundary | Enforcement Mechanism |
|----------|---------------------|
| **Blueprint schema constraint** | Blueprint JSON schema explicitly excludes `visual_profile` and `visual_definition` fields. Any addition requires VISUAL_CONTRACT.md amendment. |
| **Registry ownership** | The registry owns `asset_id → visual_profile_id` association, NOT the Blueprint. This is stored exclusively in the Asset Registry/orchestration layer. |
| **PromptCompiler constraint** | PromptCompiler receives `profile_attributes` from orchestration's internal composition boundary, NOT from Blueprint. Public interface unchanged. |
| **Development-time vs runtime separation** | Visual Profile/Visual Definition are development-time artifacts. Runtime Rails code does not reference them directly. |
| **asset_id as shared identity** | All artifacts share `asset_id` as the only cross-domain key. No artifact carries another artifact's content or path as a field. |

---

### Repository Evidence Summary

| Evidence Source | Finding |
|----------------|---------|
| VISUAL_CONTRACT.md v1.0 | Authoritative architecture: asset_id is shared identity; Blueprint does NOT own visual fields; PromptCompiler interface is fixed at 5 keyword args |
| A1 synthesis | Asset Registry reality check confirms registry stores asset_id as primary key; no implementation exists yet |
| A2 findings | Static asset storage: images are in `data/images/catalog/`, `data/images/sprites/`; no manifest or registry mapping exists |
| A3 findings | RH-400 has 3 identifiers (blueprint ID, asset_id, file prefix); 6 planned render profiles but only 3 files exist |
| A4 findings | CatalogService derives broken paths; no Visual Definition lookup; catalog pipeline doesn't consume icons |
| A5 findings | SurfaceView.js KNOWN_ASSETS is empty; no functional unit sprite loading; terrain PNG pattern exists but not reusable for units |
| A6 findings | Icon Bible file missing but non-blocking; catalog-relevant rules embedded in VISUAL_DEFINITION_TEMPLATE.md |

### Proposed Architectural Contract

**Asset Registry data model (architecture-level):**

```
AssetRegistryEntry {
  // Canonical identity
  asset_id: string (REQUIRED, shared across all artifacts)
  blueprint_id: string (REQUIRED, canonical game data identifier)
  
  // Asset metadata
  asset_family: string (REQUIRED, from Visual Definition valid values)
  component_class: string (REQUIRED for components/units/structures/vehicles)
  file_prefix: string (OPTIONAL, filesystem naming convention)
  
  // Development-time artifact paths (INPUTS to PromptCompiler)
  blueprint_path: Path (REQUIRED)
  operational_data_path: Path (REQUIRED for units/structures/vehicles)
  visual_definition_path: Path (REQUIRED)
  render_template_path: Path (REQUIRED)
  
  // Visual Profile resolution (development-time orchestration)
  visual_profile_id: string | null (resolved by orchestration, stored in registry)
  
  // Representation manifest (development-time metadata)
  representations: {
    [render_profile_name]: {
      status: "exists" | "missing" | "experimental" | "planned"
      files?: string[]
      complexity_levels?: integer[]
      notes?: string
    }
  }
  
  // Runtime bridge (pre-computed manifests)
  catalog_manifest_path: Path | null (development-time generated, served to runtime)
  surface_manifest_path: Path | null (development-time generated, bundled with game)
}
```

### Ownership Boundaries

| Artifact | Owned By | Registry Role |
|----------|----------|--------------|
| `asset_id` | Shared identity (all artifacts) | Primary lookup key |
| Blueprint content | Blueprint artifact | Stores path reference only |
| Operational Data content | Operational Data artifact | Stores path reference only |
| Visual Definition content | Visual Definition artifact | Stores path reference only |
| Visual Profile content | Visual Profile artifact | Stores `visual_profile_id` association; ProfileResolutionEngine resolves to attributes |
| Render Template content | Render Template artifact | Stores path reference only |
| Representation manifest | Asset Registry/orchestration layer | **Registry owns this** — development-time metadata about which representations exist/are missing |
| Image output files | Asset generation outputs | NOT stored in registry as primary data; discovered via representation manifest |

### Unresolved Questions

1. **Visual Profile format** — Visual Profile is locked/out-of-scope per task constraints. Its file format and storage location are not defined in this architecture.
2. **Render Template format** — Render Template is locked/out-of-scope per task constraints. Its file format and storage location are not defined in this architecture.
3. **Migration of existing files** — VISUAL_CONTRACT.md v1.0 notes VEHICLE_HARVESTER_ROVER_RH400.json violates format contract (`.json` extension but contains Markdown). Migration strategy is a separate task.
4. **Runtime manifest delivery mechanism** — How catalog_manifest_path and surface_manifest_path are delivered to runtime consumers (API endpoint? bundled JSON? pre-rendered data?) is an implementation detail for C-series.

### Implementation Implications for C-Series

| C-Task | B1 Dependency |
|--------|--------------|
| **C1** — Implement Asset Registry/orchestration | Direct dependency: C1 implements the architecture defined in B1 |
| **C2** — Implement catalog presentation | Depends on B1's representation manifest and catalog_manifest_path |
| **C3** — Implement surface asset loading | Depends on B1's surface_manifest_path and representation status |
| **C4** — Implement Visual Profile resolution | Depends on B1's `visual_profile_id` association and ProfileResolutionEngine contract |
| **C5** — Implement surface rendering integration | Depends on B1's surface_manifest_path and sprite path resolution |

---

**B1 ARCHITECTURE COMPLETE.**

