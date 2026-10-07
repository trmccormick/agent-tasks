## B2 — Define Catalog Presentation Contract

### 1. What Data the Catalog Presentation Layer Needs to Identify an Asset

**Evidence from A4 findings:** CatalogService loads blueprint + operational_data from `app/data/`. There is no Visual Definition lookup path. Current image path derivation is broken (`.json → .png` substitution). Actual catalog images are not referenced. Multiple representations are unmodeled.

**Evidence from VISUAL_CONTRACT.md v1.0:** `asset_id` is the shared canonical identity across all artifact types. Every asset has a single unique `asset_id`. The registry stores `asset_id` as the primary lookup key.

**Required identification data for catalog presentation:**

| Field | Source | Required? | Notes |
|-------|--------|-----------|-------|
| `asset_id` | Visual Definition (REQUIRED field) | **YES** | Shared canonical identity across all artifacts; primary catalog identifier |
| `blueprint_id` | Blueprint JSON | **YES** | Canonical game data identifier; used for runtime lookup |
| `asset_family` | Visual Definition (REQUIRED field) | **YES** | Determines which documents are relevant: `resource`, `component`, `assembly`, `equipment`, `unit`, `structure`, `vehicle`, `organization` |
| `component_class` | Visual Definition (REQUIRED for components/units/structures/vehicles) | **YES** | What type of thing within its family: `structural`, `mechanical`, `extractor`, `rover`, etc. |
| Blueprint data | Blueprint JSON (loaded by CatalogService) | **YES** | Physical properties, materials, production data, cost, deployment characteristics |
| Operational Data | Operational Data JSON (loaded by CatalogService for units/structures/vehicles) | **Conditional** | Only for Units/Structures/Vehicles. NOT for Resources/Components |

### 2. How Canonical Asset Identity Should Be Represented

**Evidence from VISUAL_CONTRACT.md v1.0:** "`asset_id` is a shared identity, not a file path." The registry stores `asset_id` as the primary lookup key across all artifact types.

**Evidence from A3 findings:** RH-400 has three distinct identifiers:
- Blueprint ID: `regolith_harvester_rover` (canonical game data)
- Visual Definition asset_id: `VEHICLE_HARVESTER_ROVER_RH400` (development-time visual artifact, Icon Bible format `[CATEGORY]_[TYPE]_[NAME]_[VARIANT]`)
- File prefix: `rh400_` (abbreviated filesystem naming)

**Proposed catalog identity representation:**

| Field | Value | Source |
|-------|-------|--------|
| `asset_id` | `VEHICLE_HARVESTER_ROVER_RH400` | Visual Definition — shared canonical identity |
| `blueprint_id` | `regolith_harvester_rover` | Blueprint JSON — canonical game data identifier |
| `display_name` | "RH-400 Regolith Harvesting Rover" | Derived from blueprint/visual definition prose (not a structured field) |

**Key principle:** The catalog presentation layer uses `asset_id` as the primary identifier. It does NOT use file paths, blueprint IDs, or file prefixes as identifiers. These are stored as metadata for reference but `asset_id` is the canonical key.

### 3. How Catalog Representation(s) Should Be Distinguished from Engineering, Blueprint, Sprite, Exploded, etc.

**Evidence from A3 findings:** RH-400 Visual Definition specifies six render profiles:
1. `inventory_icon` — L0-L1 silhouette icon for inventory slots (MISSING)
2. `catalog_render` — White background, two isometric views, no annotations (PARTIAL — 2 files exist)
3. `engineering_render` — Callouts, dimensions, materials, part numbers (MISSING)
4. `blueprint` — Section cuts, exploded diagrams, internal routing (MISSING)
5. `exploded_view` — Disassembled components with connection lines (MISSING)
6. `sprite_sheet` — All complexity levels L0-L5 in single sheet (PARTIAL — experimental only)

**Evidence from A4 findings:** CatalogService currently derives broken image paths. No representation metadata exists.

**Proposed catalog presentation contract for representations:**

| Representation | Role | Required? | Technical Requirements |
|---------------|------|-----------|----------------------|
| `catalog_render` | Primary catalog display asset | **YES** (for all assets) | White background, two isometric views, no annotations. Format: PNG. Background: opaque white. Dimensions: TBD by C-series implementation. |
| `inventory_icon` | Inventory slot icon | **YES** (for all assets) | L0-L1 silhouette. Transparent background. Format: PNG. Small dimensions (TBD). |
| `engineering_render` | Engineering documentation | NO (optional) | Callouts, dimensions, materials, part numbers. White/light background. |
| `blueprint` | Technical documentation | NO (optional) | Section cuts, exploded diagrams, internal routing. Blueprint-style styling. |
| `exploded_view` | Assembly documentation | NO (optional) | Disassembled components with connection lines. |
| `sprite_sheet` | Animation/reference | NO (optional) | All complexity levels L0-L5 in single sheet. Transparent background. |

**Catalog presentation layer only consumes:**
- `catalog_render` (primary display)
- `inventory_icon` (inventory slot display)

**Other representations are development-time outputs** that may be served via documentation/wiki but are NOT part of the catalog presentation contract. They are stored in the Asset Registry's representation manifest with their status.

### 4. How Image/Icon Paths or Resolved Representation References Should Be Supplied

**Evidence from A4 findings:** CatalogService has no Visual Definition lookup path. Current image path derivation is broken. The catalog pipeline doesn't currently consume icon data at all.

**Evidence from B1 findings:** Development-time orchestration resolves `asset_id → artifact paths`. Pre-computed manifests bridge development-time to runtime.

**Proposed supply mechanism:**

| Layer | Mechanism | Domain |
|-------|-----------|--------|
| **Development-time orchestration** | Generates catalog manifest mapping `asset_id → catalog_render path` and `asset_id → inventory_icon path` | Development-time only |
| **Runtime bridge** | Pre-computed JSON manifest served via API or bundled with game data | Explicit boundary — NOT runtime Rails repository search |
| **CatalogService (runtime)** | Receives resolved paths from manifest; does NOT derive paths from blueprint paths | Runtime — uses pre-resolved data |

**Catalog manifest structure (development-time generated):**

```json
{
  "VEHICLE_HARVESTER_ROVER_RH400": {
    "catalog_render": "/assets/catalog/vehicles/rh400_concept.png",
    "inventory_icon": null,
    "status": "partial"
  }
}
```

**Key constraint:** Runtime Rails code does NOT perform repository search by `asset_id`. All path resolution happens in development-time tooling. The manifest is the bridge.

### 5. What Should Be Development-Time Registry Responsibility vs. Runtime Catalog Responsibility

**Evidence from VISUAL_CONTRACT.md v1.0:** "Asset-generation tooling is development-time infrastructure outside the Rails runtime."

| Responsibility | Domain | Owner |
|---------------|--------|-------|
| Asset existence and identity | Development-time | Asset Registry/orchestration |
| `asset_id → artifact path` resolution | Development-time | Asset Registry/orchestration |
| Representation manifest (which files exist/are missing) | Development-time | Asset Registry/orchestration |
| Catalog manifest generation (pre-computed paths) | Development-time | Asset Registry/orchestration |
| Blueprint loading | Runtime | CatalogService (existing) |
| Operational Data loading | Runtime | CatalogService (existing, conditional) |
| Visual Definition lookup | **Development-time enrichment** | NOT runtime Rails — development-time orchestration enriches catalog data before serving |
| Image/icon path resolution | **Runtime bridge via manifest** | Pre-computed manifest served to runtime; NOT runtime repository search |
| Catalog UI rendering | Runtime | Admin::CatalogController + frontend JS |

**Critical boundary:** Visual Definition lookup is a development-time enrichment step. The catalog presentation layer receives enriched data (including resolved image paths) from the development-time bridge. It does NOT perform Visual Definition lookups at runtime.

### 6. What Fallback Behavior Is Appropriate When a Representation Does Not Exist

**Evidence from A3 findings:** RH-400 has six planned render profiles but only three files exist. Two catalog renders exist, one experimental sprite test exists. `inventory_icon` is MISSING.

**Evidence from A5 findings:** BiomeRenderer demonstrates an existing PNG-fetch/fallback pattern for terrain assets — functional for terrain only.

**Proposed fallback behavior:**

| Representation | Fallback | Rationale |
|---------------|----------|-----------|
| `catalog_render` (MISSING) | Placeholder/empty state in catalog UI | Cannot fabricate content; user sees "asset not yet rendered" |
| `inventory_icon` (MISSING) | Placeholder icon or auto-generated silhouette from catalog render | Minimal viable fallback; does NOT promote test assets to production |
| `engineering_render` (MISSING) | Omitted from catalog view | Optional representation; absence is acceptable |
| `blueprint` (MISSING) | Omitted from catalog view | Optional representation; absence is acceptable |
| `exploded_view` (MISSING) | Omitted from catalog view | Optional representation; absence is acceptable |
| `sprite_sheet` (MISSING or EXPERIMENTAL) | Omitted from catalog view | Experimental assets are NOT production content |

**Fallback principle:** When a required representation is missing, the catalog UI shows an empty/placeholder state. It does NOT fabricate content, promote test assets, or use unrelated assets as substitutes.

### 7. Whether the Catalog Needs Explicit Representation Metadata

**Evidence from A4 findings:** Multiple representations are unmodeled in the current catalog pipeline. No representation metadata exists.

**Proposed answer: YES — but at the development-time layer, not runtime.**

| Metadata Field | Runtime? | Development-Time? | Purpose |
|---------------|----------|-------------------|---------|
| `representations` (object with status per render profile) | NO | **YES** | Asset Registry representation manifest |
| `catalog_render_path` (resolved path) | YES (via manifest) | Generated from manifest | Catalog UI displays the image |
| `inventory_icon_path` (resolved path or null) | YES (via manifest) | Generated from manifest | Catalog UI displays icon or placeholder |
| `representation_status` ("complete", "partial", "missing") | YES (via manifest) | Computed from manifest | Catalog UI shows completion indicator |

**Runtime catalog data structure:**

```json
{
  "asset_id": "VEHICLE_HARVESTER_ROVER_RH400",
  "blueprint_id": "regolith_harvester_rover",
  "asset_family": "vehicle",
  "component_class": "harvester",
  "catalog_render_path": "/assets/catalog/vehicles/rh400_concept.png",
  "inventory_icon_path": null,
  "representation_status": "partial",
  "blueprint_data": { ... },
  "operational_data": { ... }
}
```

**Key principle:** Runtime catalog data includes resolved paths (from the manifest) and a representation status indicator. The detailed representation manifest lives in development-time; runtime only needs the computed status and resolved paths.

### 8. How Existing RH-400 Files Map to the Proposed Contract

**Evidence from A3 findings:** Three existing RH-400 image files:
1. `data/images/catalog/crafts/ground/rh400_concept.png` (RGB, 1402×1122) — catalog render
2. `data/images/catalog/crafts/ground/rh400_regolith_harvesting_rover.png` (RGB, 1536×1024) — catalog render alternate
3. `data/images/catalog/crafts/ground/rh400_sprite_test.png` (RGBA, 1536×1024) — sprite test (experimental)

**Mapping to proposed contract:**

| RH-400 File | Proposed Contract Role | Status | Notes |
|------------|----------------------|--------|-------|
| `rh400_concept.png` | `catalog_render` | **VALID** | RGB, opaque background appropriate for catalog. Primary catalog render candidate. |
| `rh400_regolith_harvesting_rover.png` | `catalog_render` (alternate) | **VALID** | RGB, opaque background appropriate for catalog. Alternate catalog render candidate. |
| `rh400_sprite_test.png` | NOT a production asset | **EXPERIMENTAL** | RGBA with transparent background — suitable for surface sprite but explicitly experimental. NOT promoted to production. |

**What does NOT exist for RH-400:**
- `inventory_icon` — MISSING (no L0-L1 silhouette icon)
- `engineering_render` — MISSING
- `blueprint` — MISSING
- `exploded_view` — MISSING
- `sprite_sheet` — MISSING (the sprite_test is experimental, not a sheet)

**Deliberate non-mapping:** The RH-400 files are NOT forced into the six render profiles. Two catalog renders exist and are valid. One experimental sprite test exists but is explicitly NOT a production asset. The remaining four profiles are genuinely missing — not "covered by" any existing file.

### 9. How Embedded Visual Definition Icon Rules Can Be Referenced Without Pretending an Icon Bible Exists

**Evidence from A6 findings:** Icon Bible file (`2026-07-19-HIGH-DESIGN-GALAXYGAME_ICON_BIBLE.md`) is missing. Catalog-relevant icon rules are embedded in VISUAL_DEFINITION_TEMPLATE.md:
- 13 color families (blue, cyan, gray, silver, orange, yellow, green, purple, red, brown, black, white)
- 9 animation profiles with timing
- Tech level progression (Mk1-Mk5)
- Manufacturing styles
- Complexity levels L0-L5

**Proposed approach:**

| Icon Rule | Source | How Referenced |
|-----------|--------|---------------|
| Color families | VISUAL_DEFINITION_TEMPLATE.md embedded rules | `color_profile` field in Visual Definition; catalog UI uses for palette hints |
| Animation profiles | VISUAL_DEFINITION_TEMPLATE.md embedded rules | `animation_profile` field in Visual Definition; catalog UI uses for animated icons |
| Tech level | VISUAL_DEFINITION_TEMPLATE.md embedded rules | `technology_level` field (integer 1-5); catalog UI displays as Mk1-Mk5 |
| Manufacturing style | VISUAL_DEFINITION_TEMPLATE.md embedded rules | `manufacturing_style` field in Visual Definition; catalog UI displays as text |
| Complexity levels | VISUAL_DEFINITION_TEMPLATE.md embedded rules | `complexity_levels` field in Visual Definition; catalog UI uses for render selection |

**Key principle:** The catalog presentation contract references the embedded rules directly from VISUAL_DEFINITION_TEMPLATE.md. It does NOT reference a non-existent Icon Bible file. The embedded rules ARE the source of truth for catalog icon handling.

### 10. Contract Boundaries to Prevent Development-Time Asset Generation from Becoming Runtime Behavior

**Evidence from VISUAL_CONTRACT.md v1.0:** "Asset-generation tooling is development-time infrastructure outside the Rails runtime."

| Boundary | Enforcement |
|----------|------------|
| **Visual Definition lookup** | Development-time enrichment only; NOT runtime Rails code |
| **Path resolution** | Pre-computed manifest bridges development-time to runtime; no runtime repository search |
| **Asset generation** | Development-time tooling generates output files; runtime consumes pre-generated assets |
| **Icon Bible rules** | Embedded in VISUAL_DEFINITION_TEMPLATE.md (development-time doc); catalog UI references via resolved data, NOT by reading docs/ |
| **Representation manifest** | Development-time metadata; runtime only receives computed status and resolved paths |
| **PromptCompiler interface** | Fixed at 5 keyword args; no changes to add Visual Profile or representation discovery |

---

### Repository Evidence Summary

| Evidence Source | Finding |
|----------------|---------|
| A2 findings | Static asset storage: images in `data/images/catalog/`; no manifest or registry mapping |
| A3 findings | RH-400 has 6 planned render profiles, 3 files (2 catalog renders valid, 1 experimental); inventory_icon MISSING |
| A4 findings | CatalogService loads blueprint + operational_data; broken path derivation; no Visual Definition lookup; catalog pipeline doesn't consume icons |
| A6 findings | Icon Bible file missing but non-blocking; catalog-relevant rules embedded in VISUAL_DEFINITION_TEMPLATE.md (color families, animation profiles, tech levels, manufacturing styles, complexity levels) |
| VISUAL_CONTRACT.md v1.0 | asset_id is shared identity; Blueprint does NOT own visual fields; PromptCompiler interface fixed; development-time vs runtime boundary explicit |

### Proposed Catalog Presentation Contract

**Object Class Split:**

| Object Class | Required Data Sources | Catalog Fields |
|-------------|----------------------|---------------|
| **Components** (I-beam case) | Blueprint + Visual Definition only | `asset_id`, `blueprint_id`, `asset_family`, `component_class`, `blueprint_data`, `visual_definition_data`, `catalog_render_path`, `inventory_icon_path`, `representation_status` |
| **Units/Structures/Vehicles** (RH-400 case) | Blueprint + Operational Data + Visual Definition | All Component fields PLUS: `operational_data` (power consumption, payload, range, autonomy) |

**Critical distinction:** Components do NOT have Operational Data. The I-beam (Component) case must not produce empty/fake Unit sections.

**Catalog presentation data structure (runtime):**

```json
{
  // Identity
  "asset_id": "VEHICLE_HARVESTER_ROVER_RH400",
  "blueprint_id": "regolith_harvester_rover",
  "asset_family": "vehicle",
  "component_class": "harvester",
  
  // Canonical game data (from CatalogService)
  "blueprint_data": { /* from Blueprint JSON */ },
  "operational_data": { /* from Operational Data JSON — Units/Structures/Vehicles only */ },
  
  // Visual Definition (development-time enrichment, NOT runtime Rails lookup)
  "visual_definition": {
    "silhouette": "...",
    "color_profile": { "primary": "#C0C0C0", ... },
    "material_profiles": [...],
    "technology_level": 2,
    "manufacturing_style": "heavy_industrial",
    "animation_profile": "vehicles_status_lights"
  },
  
  // Resolved asset paths (from pre-computed manifest)
  "catalog_render_path": "/assets/catalog/vehicles/rh400_concept.png",
  "inventory_icon_path": null,
  "representation_status": "partial",
  
  // Embedded icon rules (from VISUAL_DEFINITION_TEMPLATE.md, NOT Icon Bible)
  "icon_color_families": ["silver", "gray", "orange"],
  "icon_animation_profile": "vehicles_status_lights",
  "icon_animation_timing": "0.5-1s"
}
```

### Production/Presentation Boundary (Explicit and Unresolved)

| Question | Current State | Status |
|----------|--------------|--------|
| Does catalog presentation consume production data directly? | **Unresolved** — not documented | **UNRESOLVED DECISION POINT** |
| How does production data become presentation data? | **Unresolved** — no pipeline exists | **UNRESOLVED DECISION POINT** |
| Who generates the pre-computed manifest? | Development-time orchestration (proposed) | Proposed, not implemented |
| Where is the manifest stored/served? | TBD by C-series | Implementation detail |

**This boundary is explicitly flagged as an unresolved architectural decision.** B2 defines what catalog presentation needs but does NOT decide how production data becomes presentation data. That decision belongs to a later task.

### Unresolved Questions

1. **Production/Presentation boundary** — How production data becomes presentation data is an unresolved decision point for later tasks.
2. **Visual Profile format and storage** — Locked/out-of-scope per B1 constraints; its resolution mechanism is not defined here.
3. **Render Template format and storage** — Locked/out-of-scope per B1 constraints.
4. **Migration of VEHICLE_HARVESTER_ROVER_RH400.json** — Format violation (`.json` extension but contains Markdown) is a separate task.
5. **Which of the two RH-400 catalog renders is primary?** — Both are valid; selection is an implementation detail for C-series.

### Implementation Implications for C-Series

| C-Task | B2 Dependency |
|--------|--------------|
| **C2** — Implement catalog presentation | Direct dependency: C2 implements the contract defined in B2 |
| **C3** — Implement catalog icon handling | Depends on B2's `inventory_icon_path` and embedded icon rules |
| **C4** — Implement Visual Profile resolution for catalog | Depends on B1's `visual_profile_id` association + B2's catalog data structure |

---

**B2 ARCHITECTURE COMPLETE.**

