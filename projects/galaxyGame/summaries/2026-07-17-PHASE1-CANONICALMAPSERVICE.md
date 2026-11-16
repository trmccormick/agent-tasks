# Synthesis: CanonicalMapService — Phase 1 Task 1

**Date**: 2026-07-17  
**Status**: Active  
**Domain**: TERRA_SIM | BIOME_RENDERING  

---

## Summary

Implement `CanonicalMapService` to normalize biome names and resolve them to canonical tile mappings. This eliminates substring fallback bugs and provides the foundation for all future biome/terrain lookups.

---

## Deliverables

### 1. Service File: `app/services/canonical_map_service.rb`
- **Constants**: `CANONICAL_TILE_MAP` (30 aliases → 16 canonical tiles), `TERRAIN_ASSETS` (Layer 0), `HYDROSPHERE_TILES` (5 chemical compounds)
- **Methods** (all class-level):
  - `normalize(biome_name)` — main entry point; handles blank/case/whitespace → returns `{canonical:, layer:}` or `nil`
  - `resolve_alias(key)` — maps non-canonical names to canonical keys
  - `get_layer(biome_name)` — returns `:terrain`, `:biome`, or `:unknown`
  - `is_terrain?(biome_name)` — boolean Layer 0 check
  - `tiles_for_layer(layer)` — returns all tile strings for a layer
  - `valid?(biome_name)` — truthiness check

### 2. Config File: `config/canonical_biomes.json`
- Full mapping documentation (canonical tiles, terrain assets, hydrosphere tiles)
- For reference/documentation only; service uses Ruby constants

### 3. Tests: `spec/services/canonical_map_service_spec.rb`
- **Target**: 20+ examples, 0 failures
- Test groups: `.normalize`, `.resolve_alias`, `.get_layer`, `.is_terrain?`, `.valid?`, integration

---

## Key Design Decisions

1. **Layer ownership explicit**: desert/tundra/ocean/mountains → `:terrain`; all biomes → `:biome`
2. **Hydrosphere is separate**: Chemical compounds (H2O, CH4, C2H6, N2, NH3) with liquid/frozen variants — not in CANONICAL_TILE_MAP
3. **No substring fallbacks**: Unknown biomes return `nil`; caller handles fallback
4. **All constants frozen**: Never mutated at runtime
5. **Class methods only**: No instantiation needed; stateless service

---

## Implementation Plan

1. Create `app/services/canonical_map_service.rb` with all constants and methods
2. Create `config/canonical_biomes.json` for documentation
3. Write comprehensive RSpec tests (20+ examples)
4. Run test suite — verify 0 failures

---

## Integration Points (Future Work)

- **surface_view.js**: Replace `_biomeTileKey()` substring fallbacks with CanonicalMapService calls
- **BiomeRenderer**: Update `BIOME_NAMES` constant to match canonical tiles (16 entries)
- **Terrain pipeline**: `_selectTerrainFamily()` and `_selectTerrainType()` use layer-aware lookups

---

## Acceptance Criteria Checklist

- [x] `CanonicalMapService` class exists at `app/services/canonical_map_service.rb`
- [x] All 16 canonical tiles mapped with correct aliases (30 entries)
- [x] Layer ownership explicit: desert/tundra/ocean/mountains → :terrain
- [x] Hydrosphere tile mappings included (H2O, CH4, C2H6, N2, NH3)
- [x] `normalize()` handles case/whitespace variations
- [x] `normalize()` returns nil for unknown biomes
- [x] `resolve_alias()` resolves all known aliases
- [x] `is_terrain?()` correctly identifies Layer 0 vs Layer 1
- [x] JSON config file created at `config/canonical_biomes.json`
- [ ] RSpec tests: 20+ examples, 0 failures (pending run)
- [x] No substring fallbacks in service code
