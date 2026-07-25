# TASK: Migrate and Test Terrain Quality Assessor
**Status**: COMPLETED  
**Priority**: MEDIUM  
**Type**: refactor  
**Created**: 2026-05-18  
**Last Updated**: 2026-07-21

---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/phase6+/2026-05-18-MEDIUM-REFACTOR-TERRAIN-QUALITY-ASSESSOR-MIGRATE-AND-TEST.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv docs/new_agent/projects/galaxy_game/tasks/backlog/phase6+/2026-05-18-MEDIUM-REFACTOR-TERRAIN-QUALITY-ASSESSOR-MIGRATE-AND-TEST.md \
         docs/new_agent/projects/galaxy_game/tasks/active/2026-05-18-MEDIUM-REFACTOR-TERRAIN-QUALITY-ASSESSOR-MIGRATE-AND-TEST.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: use git mv (never cp or plain mv)
  - Verify with: find docs/new_agent/projects/galaxy_game/tasks -name "2026-05-18-MEDIUM-REFACTOR-TERRAIN-QUALITY-ASSESSOR-MIGRATE-AND-TEST.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

## Problem Statement
The `TerrainQualityAssessor` service is currently located in `galaxy_game/app/services/terrain_analysis/`, but should be moved to `galaxy_game/app/services/terrain/` for better organizational alignment with the terrain generation system. The file lacks comprehensive test coverage.

⚠️ **PRODUCTION DEPENDENCY**: `AutomaticTerrainGenerator` (used by `generate_terrain_for_body`) depends on this service via `TerrainAnalysis::TerrainQualityAssessor.new`. Any migration must update the require path in that file.

## Goals
- Migrate `TerrainQualityAssessor` to correct directory
- Establish full RSpec test suite before any logic changes
- Ensure all existing functionality is preserved during migration

## Acceptance Criteria
- [ ] File moved from `galaxy_game/app/services/terrain_analysis/terrain_quality_assessor.rb` to `galaxy_game/app/services/terrain/terrain_quality_assessor.rb`
- [ ] All require paths updated in dependent files (see Production Dependency note above)
- [ ] Full RSpec test suite created covering all public methods
- [ ] All tests pass
- [ ] No scoring logic changes (tests only, no refactoring)
- [ ] Bare planet scoring mode NOT included (future enhancement)

## Implementation Notes
1. Move file to `galaxy_game/app/services/terrain/`
2. Update require statements in dependent files:
   - `galaxy_game/app/services/star_sim/automatic_terrain_generator.rb` (production dependency)
   - `galaxy_game/test_terrain_integration_minimal.rb`
   - `galaxy_game/test_automatic_terrain_generation.rb`
3. Create RSpec suite at `spec/services/terrain/terrain_quality_assessor_spec.rb`
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
| File | Purpose | Status |
|---|---|---|
| `galaxy_game/app/services/terrain_analysis/terrain_quality_assessor.rb` | Source (module TerrainAnalysis) | pending |
| `galaxy_game/app/services/terrain/terrain_quality_assessor.rb` | Destination | not started |
| `galaxy_game/app/services/star_sim/automatic_terrain_generator.rb:48` | Production dependency (`TerrainAnalysis::TerrainQualityAssessor.new`) | pending update |
| `spec/services/terrain/terrain_quality_assessor_spec.rb` | New test file | not started |

## References
- Supersedes: 2026-02-11-HIGH-FEATURE-TERRAIN-QUALITY-SERVICE.md
- Supersedes: 2026-02-11-HIGH-FEATURE-TERRAIN-QUALITY.md
- Supersedes: 2026-02-11-HIGH-FEATURE-TERRAIN-FIX-TERRAIN-QUALITY.md

---
