# RESEARCH: Surface Suitability Analyzer — Geosphere Data Contract

**Date**: 2026-07-03  
**Type**: Architecture Research  
**Status**: SYNTHESIS REPORT — Awaiting human approval before implementation

---

## 0. Task Location Check
- [x] Task moved from `backlog/phase6+/` to `tasks/active/2026-07-03-HIGH-RESEARCH-SURFACE-SUITABILITY-ANALYZER.md`
- [x] Confirmed task exists in active folder

---

## 1. Scope Assessment

### Files/CODEBASE REVIEWED

| File | Purpose | Relevance |
|------|---------|-----------|
| `app/models/celestial_bodies/spheres/geosphere.rb` | Geosphere model (JSONB storage) | **Core** — data source for all terrain/resource data |
| `app/services/star_sim/system_builder_service.rb` | Ingests GeoTIFF → creates geospheres | **Core** — how terrain_map gets populated |
| `app/services/star_sim/geosphere_generator_service.rb` | Generates geosphere attributes from orbital/physical data | **Supporting** — composition, layer masses |
| `app/models/location/celestial_location.rb` | Landing site model (lat/lon coordinates) | **Core** — what AI Manager currently creates |
| `app/services/autonomous_mission_service.rb` | Current landing site selection (hardcoded) | **Target** — where the gap exists |
| `app/services/ai_manager/strategy_selector.rb` | AI decision engine | **Consumer** — future caller of suitability analyzer |
| `app/views/admin/celestial_bodies/surface.html.erb` | UI terrain data injection | **Reference** — confirms terrain_map schema |
| `app/javascript/admin/monitor.js` | Frontend terrain rendering | **Reference** — confirms resource_grid format |
| `app/services/ai_manager/station_construction_strategy.rb` | Has `assess_moon_surface_suitability()` (gravity-only) | **Comparison** — shows current superficial approach |

### Current Geosphere Data Contract (terrain_map JSONB schema)

```ruby
# geosphere.terrain_map structure (confirmed from DB logs + controller code):
{
  "elevation" => [[0.0, 10.0, 20.0, ...]],       # 2D array (NxM grid)
  "biomes" => [...],                               # 2D array of biome IDs
  "resource_grid" => [...],                        # 2D array of resource concentrations
  "width" => Integer,                              # Grid width
  "height" => Integer,                             # Grid height
  "quality_score" => Float,                        # Generation quality metric
  "generation_method" => String,                   # e.g., "perlin", "terra_sim"
  "generation_metadata" => {                       # Optional metadata
    "source" => String,
    "elevation_range" => [min, max],
    "surface_color_hint" => String
  }
}
```

### Current Geosphere Additional Fields (non-terrain_map)

```ruby
# geosphere direct columns:
crust_composition   # Hash { element_name => percentage }
mantle_composition  # Hash { element_name => percentage }
core_composition    # Hash { element_name => percentage }
total_crust_mass    # Float
total_mantle_mass   # Float
total_core_mass     # Float
base_values         # JSONB (volatile storage)
plates              # JSONB (tectonic plate positions)
stored_volatiles    # JSON
regolith_depth      # Float
regolith_particle_size # Float
weathering_rate     # Float
```

---

## 2. Implementation vs. Intent

### CRITICAL GAP IDENTIFIED

**Current State**: The AI Manager has **NO backend-native access** to geosphere terrain/resource data for site selection decisions.

**Evidence**:

1. **`autonomous_mission_service.rb#select_landing_site()`** — Hardcoded to "Marius Hills Lava Tube" or a generic default. No terrain analysis, no resource query, no suitability scoring.

2. **`station_construction_strategy.rb#assess_moon_surface_suitability()`** — Uses only `gravity` attribute from the celestial body. Does NOT access `geosphere.terrain_map` at all. This is a stub-level assessment.

3. **`CelestialLocation` model** — Stores coordinates (`"14.1°N 56.8°W"`) and references a `celestial_body`, but has NO suitability score, NO resource data, NO terrain clearance data. It's purely a positional container.

4. **`StrategySelector#execute_settlement_expansion()`** — Currently just logs "Initiating settlement expansion." No site selection logic exists.

5. **No bridge between geosphere and AI Manager** — The `geosphere` object is accessible via `CelestialBody#geosphere`, but no service or method exposes suitability scores to the AI Manager's decision pipeline.

### What EXISTS That Can Be Reused

1. **terrain_map has all needed data**: elevation (for slope calculation), resource_grid (for concentration), biomes (for buildability).
2. **surface.html.erb already extracts terrain_data** — The controller (`celestial_bodies_controller.rb#show`) passes `@celestial_body.geosphere&.terrain_map` to the view. This proves the data path works.
3. **CelestialLocation has coordinate validation** — The format `"00.00°N 00.00°E"` can be parsed to grid indices if we know the body's grid dimensions (stored in `terrain_map['width']` and `terrain_map['height']`).

### What DOES NOT EXIST

1. **No suitability scoring service** — No method computes resource density vs. terrain clearance scores.
2. **No coordinate-to-grid mapping** — No way to convert lat/lon coordinates to grid indices (x, y) for querying the terrain_map.
3. **No crater/buildability mask data** — The terrain_map does not currently store crater information or buildability masks. This would need to be added.
4. **No slope calculation utility** — Elevation data exists but no method derives slope from it.

---

## 3. Proposed Action

### Recommended Architecture: `SurfaceSuitabilityAnalyzer` Service

```ruby
# app/services/ai_manager/surface_suitability_analyzer.rb

module AIManager
  class SurfaceSuitabilityAnalyzer
    # Primary API: Score a coordinate on a celestial body
    def self.score(celestial_body, latitude:, longitude:)
      # Returns: {
      #   suitability_score: 0.0..1.0,
      #   resource_density: { material => concentration },
      #   terrain_clearance: :flat | :moderate | :rough | :extreme,
      #   buildability_mask: :buildable | :cratered | :too_steep | :flooded,
      #   slope_degrees: Float,
      #   elevation_meters: Float,
      #   grid_x: Integer,
      #   grid_y: Integer
      # }
    end

    # Secondary API: Find best site within a region
    def self.find_best_site(celestial_body, lat_range:, lon_range:, limit: 10)
      # Returns array of scored sites sorted by suitability_score descending
    end

    # Tertiary API: Score all grid cells (for UI visualization)
    def self.score_entire_surface(celestial_body)
      # Returns 2D array of score hashes matching terrain_map dimensions
    end
  end
end
```

### Data Contract Requirements

The analyzer needs the following from `geosphere.terrain_map`:

| Metric | Source Field | Derivation |
|--------|-------------|------------|
| Resource Concentration | `resource_grid` | Direct lookup at grid cell |
| Terrain Slope | `elevation` | Finite difference between adjacent cells |
| Buildability Mask | **MISSING** | Needs new field: `crater_mask` or `buildability_grid` |
| Elevation | `elevation` | Direct lookup |
| Biome | `biomes` | Direct lookup (for flood/water detection) |

### Integration Point Recommendation

**Dedicated service called by StrategySelector**, NOT a method on CelestialBody.

Rationale:
- CelestialBody is a domain model for astronomical data, not AI decision logic
- The analyzer needs to combine geosphere data with other factors (orbital mechanics, proximity to trade routes) that CelestialBody doesn't know about
- StrategySelector already calls multiple services — adding `SurfaceSuitabilityAnalyzer` follows the existing pattern

---

## 4. DEEP AUDIT FINDINGS

### Finding 1: terrain_map is JSONB — No Schema Migration Needed for Existing Data

The `terrain_map` column on `geospheres` table is already JSONB. New fields (`crater_mask`, `buildability_grid`) can be added without migration — they'll just be new keys in the hash. However, **backfilling** existing geospheres with crater/buildability data would require a one-time rake task.

### Finding 2: No Lat/Lon to Grid Index Mapping Exists

The terrain_map stores elevation/biomes/resource as 2D arrays indexed by [y][x]. There is NO code that maps geographic coordinates (lat/lon) to grid indices. This mapping requires:
- Knowing the celestial body's grid origin (top-left corner lat/lon)
- Grid cell size in degrees = `360 / width` (for longitude) and `(max_lat - min_lat) / height` (for latitude)
- The SystemBuilderService does NOT currently store this mapping metadata

**Recommendation**: Add `grid_origin_lat`, `grid_origin_lon`, `grid_cell_size_deg` to the geosphere model or terrain_map metadata.

### Finding 3: Current CelestialLocation Coordinates Are Strings, Not Structured Data

```ruby
# validates :coordinates, format: { with: /\A\d+\.\d+°[NS]\s+\d+\.\d+°[EW]\z/ }
```

This is parseable but inconvenient. Consider adding `latitude` and `longitude` as separate float columns for easier querying.

### Finding 4: Station Construction Strategy Has a Stub Suitability Method

`station_construction_strategy.rb#assess_moon_surface_suitability()` uses only gravity:
```ruby
def assess_moon_surface_suitability(moon)
  gravity = moon.gravity
  suitability = 1.0
  suitability *= 0.7 if gravity > 0.5
  suitability *= 1.2 if gravity < 0.1
  suitability *= 0.8 if atmosphere != 'none'
  suitability
end
```

This is a **good starting point** for the new analyzer — it should be refactored to use the full terrain_map data rather than just gravity/atmosphere.

### Finding 5: No Crater Data in Current Architecture

The task mentions "Buildability Mask (from crater JSON data)" but no crater data exists in the geosphere model or terrain_map. This would need to be generated during terrain creation (e.g., via a crater generation service that adds a `crater_mask` grid to terrain_map).

### Finding 6: Surface View and Monitor JS Already Use resource_grid

The frontend (`monitor.js`, `surface_view.js`) reads `terrainData.resource_grid` as a 2D array. The analyzer should use the **same data source** to ensure consistency between what the UI shows and what the AI Manager queries.

---

## 5. RISK ASSESSMENT

| Risk | Severity | Mitigation |
|------|----------|------------|
| terrain_map missing crater/buildability data | HIGH | Add `buildability_grid` as new terrain_map key; generate during terrain creation |
| No lat/lon → grid mapping | MEDIUM | Store grid metadata in terrain_map or geosphere model |
| Performance on large grids | MEDIUM | Cache computed scores; only score regions of interest |
| Backfilling existing geospheres | LOW | One-time rake task; new bodies get full data automatically |
| Breaking existing AI Manager contracts | HIGH | New service, no changes to existing models/services |

---

## 6. IMPLEMENTATION PLAN (If Approved)

### Phase 1: Core Analyzer Service (New File)
- Create `app/services/ai_manager/surface_suitability_analyzer.rb`
- Implement `score(celestial_body, latitude:, longitude:)` with:
  - Lat/lon → grid index conversion
  - Resource density lookup from `resource_grid`
  - Slope calculation from `elevation` (finite difference)
  - Biome-based flood detection
  - Gravity/atmosphere scoring (from existing stub)
- Return structured result hash

### Phase 2: Grid Metadata Extension
- Add `grid_origin_lat`, `grid_origin_lon`, `grid_cell_size_deg` to geosphere model
- Update `SystemBuilderService` or terrain generation to populate these fields
- Add `buildability_grid` key to terrain_map (generated during terrain creation)

### Phase 3: Integration with StrategySelector
- Add `SurfaceSuitabilityAnalyzer` call in `execute_settlement_expansion()`
- Replace hardcoded "Marius Hills" with autonomous site selection
- Update `CelestialLocation` creation to use scored sites

### Phase 4: Best-Site Finder (Optional)
- Implement `find_best_site()` for region-wide scanning
- Add caching layer for repeated queries

---

## 7. VERIFICATION STEPS (From Task Requirements)

- [x] **Audit `system_builder_service.rb`** — Confirmed: ingests GeoTIFF → creates geosphere with terrain_map JSONB containing elevation, biomes, resource_grid, width, height
- [x] **Verify `surface.html.erb` surface-data JSON** — Confirmed: controller passes `@celestial_body.geosphere&.terrain_map`, view injects as JSON, JS reads `terrainData.resource_grid` and `terrainData.elevation`
- [ ] **Document proposed API for StrategySelector** — See Section 3 above (pending approval)

---

## 8. RECOMMENDATION

**Proceed with Phase 1 only** (core analyzer service). Defer Phase 2 (grid metadata) until crater/buildability data generation is also scoped, as they are tightly coupled. The core analyzer can work with existing terrain_map fields (elevation, resource_grid, biomes) for a useful MVP that scores sites on:
- Resource density
- Terrain slope
- Flood/water presence
- Gravity/atmosphere (from existing stub)

This provides immediate value without requiring schema changes or backfill tasks.

---

**Report generated**: 2026-07-03  
**Awaiting**: Human approval to proceed with implementation planning
