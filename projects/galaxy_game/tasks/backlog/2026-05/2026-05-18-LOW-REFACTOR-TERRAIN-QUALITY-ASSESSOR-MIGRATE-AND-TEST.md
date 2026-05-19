# TASK: Migrate and Test Terrain Quality Assessor
**Status**: BACKLOG  
**Priority**: LOW  
**Type**: refactor  
**Created**: 2026-05-18

---

## Problem Statement
The `TerrainQualityAssessor` service is currently located in `app/services/terrain_analysis/`, but should be moved to `app/services/terrain/` for better organizational alignment with the terrain generation system. The file lacks comprehensive test coverage.

## Goals
- Migrate `TerrainQualityAssessor` to correct directory
- Establish full RSpec test suite before any logic changes
- Ensure all existing functionality is preserved during migration

## Acceptance Criteria
- [ ] File moved from `app/services/terrain_analysis/terrain_quality_assessor.rb` to `app/services/terrain/terrain_quality_assessor.rb`
- [ ] All require paths updated in dependent files
- [ ] Full RSpec test suite created covering all public methods
- [ ] All tests pass
- [ ] No scoring logic changes (tests only, no refactoring)
- [ ] Bare planet scoring mode NOT included (future enhancement)

## Implementation Notes
1. Move file to new location
2. Update any require statements pointing to old location
3. Create RSpec suite with test files matching service structure
4. Test edge cases: nil values, boundary conditions, different biome types
5. Do not modify scoring algorithms - preserve existing behavior exactly

## Out of Scope
- Bare planet scoring mode (separate future task)
- Any modifications to scoring logic or thresholds
- Performance optimizations

## Diagnostic/Debugging
- Verify all tests pass after migration
- Check that no other files reference old path
- Confirm service behaves identically before and after move

## Related Files/Paths
- app/services/terrain_analysis/terrain_quality_assessor.rb (source)
- app/services/terrain/terrain_quality_assessor.rb (destination)
- spec/services/terrain/terrain_quality_assessor_spec.rb (new test file)

## References
- Supersedes: 2026-02-11-HIGH-FEATURE-TERRAIN-QUALITY-SERVICE.md
- Supersedes: 2026-02-11-HIGH-FEATURE-TERRAIN-QUALITY.md
- Supersedes: 2026-02-11-HIGH-FEATURE-TERRAIN-FIX-TERRAIN-QUALITY.md

---
