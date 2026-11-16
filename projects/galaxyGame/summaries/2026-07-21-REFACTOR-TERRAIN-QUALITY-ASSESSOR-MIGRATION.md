# 2026-07-21-REFACTOR-TERRAIN-QUALITY-ASSESSOR-MIGRATION

## Summary
Migrate `TerrainAnalysis::TerrainQualityAssessor` from `app/services/terrain_analysis/` to `app/services/terrain/`, update all dependents, and create full RSpec test suite.

## Scope
- **Source**: `galaxy_game/app/services/terrain_analysis/terrain_quality_assessor.rb` (module TerrainAnalysis)
- **Destination**: `galaxy_game/app/services/terrain/terrain_quality_assessor.rb` (module TerrainAnalysis — unchanged)
- **Dependents** (4 files):
  1. `galaxy_game/app/services/star_sim/automatic_terrain_generator.rb:48` — production dependency, uses `TerrainAnalysis::TerrainQualityAssessor.new` (no require needed if autoloading works, but verify)
  2. `galaxy_game/test_terrain_integration_minimal.rb:13` — requires old path
  3. `galaxy_game/test_automatic_terrain_generation.rb:7` — requires old path
  4. `AUTOMATIC_TERRAIN_GENERATION_README.md:20,193` — docs reference old path (update)

## Key Findings
- Module namespace stays `TerrainAnalysis` — no module rename needed
- Only require paths need updating in test files
- Production code (`automatic_terrain_generator.rb`) uses constant lookup `TerrainAnalysis::TerrainQualityAssessor.new` — depends on Rails autoloading or explicit require
- Service has 4 public scoring methods: `assess_terrain_quality`, `calculate_realism_score`, `calculate_playability_score`, `calculate_diversity_score`, `calculate_balance_score` (all private except first)
- Public method: `assess_terrain_quality(terrain_data, planet_properties = {})` returns hash with keys: realism, playability, diversity, balance, overall

## Implementation Plan
1. Move file via git mv in galaxyGame repo
2. Update require paths in 2 test files + 1 readme
3. Create RSpec spec at `spec/services/terrain/terrain_quality_assessor_spec.rb`
4. Run tests in Docker container
5. Verify no remaining references to old path

## Files to Create
- `spec/services/terrain/terrain_quality_assessor_spec.rb` — full test suite

## Files to Update
- `galaxy_game/test_terrain_integration_minimal.rb` — update require path
- `galaxy_game/test_automatic_terrain_generation.rb` — update require path
- `AUTOMATIC_TERRAIN_GENERATION_README.md` — update docs paths
