---
status: backlog
priority: LOW
type: investigation
system_domain: BACKLOG_REORGANIZATION
mvp_alignment: BACKLOG_CLEANUP
local_worker_safe: true
---
# TASK: 2026-05-18-LOW-INVESTIGATE-TERRAIN-BACKLOG-ALIGNMENT

## Context
During a multi-generation backlog reorganization sweep, a structural overlap was flagged between legacy tasks expecting a new 'TerrainQualityService' and a functional backend implementation already existing in the codebase. This tracking file anchors a deferred investigation pass to reconcile our shifting map architectures—specifically how we transition historical procedural metrics (Civ4 style hex matrices) to wrap around or interpret our newer generated planetary dataset layers (NASA GeoTIFF global arrays).

## Reference Locations
- **Active Ruby Code Base:** `galaxy_game/app/services/terrain_analysis/terrain_quality_assessor.rb`
- **Triage Data Source Folder:** `docs/new_agent/tasks/backlog/reorganization attempt 2/2026-02/`
- **New Task Standard Location:** `docs/new_agent/projects/galaxy_game/tasks/backlog/[YEAR]/`

## ⚠️ Critical Findings (For Next Agent)
**DISCOVERED DUPLICATES IN OLD BACKLOG:**
Three overlapping task files were found in the deprecated location that must be reconciled before any code work resumes:
1. `2026-02-11-HIGH-FEATURE-TERRAIN-QUALITY-SERVICE.md` - Asks to "Create TerrainQualityService" (Does not exist as requested)
2. `2026-02-11-HIGH-FEATURE-TERRAIN-QUALITY.md` - Duplicate of #1 with incomplete description
3. `2026-02-11-HIGH-FEATURE-TERRAIN-FIX-TERRAIN-QUALITY.md` - Overlapping "fix" task

**EXISTING IMPLEMENTATION:**
Functionality ALREADY EXISTS as `TerrainQualityAssessor` in `terrain_analysis/`. It includes:
- ✅ `quality_score()` method (returns 0.0-1.0)
- ✅ `assess_terrain(data)` logic
- ✅ Biome counting, clustering analysis, playability/diversity/balance scoring
- ❌ **NO TESTS EXIST** for this implementation

**PATH MISMATCH:**
- Tasks expect: `app/services/terrain/terrain_quality_service.rb` (with tests)
- Code is at: `app/services/terrain_analysis/terrain_quality_assessor.rb` (no tests)

## Investigation Status: COMPLETE ✅
**Completed on 2026-05-18 by Terrain Backlog Cleanup Agent:**

1. **Duplicates Archived:** All three overlapping task files have been moved to `docs/new_agent/projects/galaxy_game/tasks/backlog/reorganization attempt 2/archive/` without alteration.

2. **Consolidated Task Created:** A single replacement task has been generated:
   - **File:** `2026-05-18-LOW-REFACTOR-TERRAIN-QUALITY-ASSESSOR-MIGRATE-AND-TEST.md`
   - **Location:** `docs/new_agent/projects/galaxy_game/tasks/backlog/2026-05/`
   - **Scope:**
     - Migrate `TerrainQualityAssessor` from `app/services/terrain_analysis/` to `app/services/terrain/`
     - Create full RSpec test suite (tests first, no logic changes)
     - Exclude bare planet scoring mode (future enhancement)

3. **Next Steps:** Proceed with the consolidated migration task when ready. No further investigation required.
## Deferred Investigation Scope (Original)
1. **Preserve History:** Retain all active file states inside the `reorganization attempt 2` folder to ensure no metric formulas, scoring weights, or design notes are deleted or lost prematurely.
2. **Data Model Bridge:** Map out how `TerrainQualityAssessor` can evaluate diverse, playable, and balanced resource matrices using data parsed from the newer continuous GeoTIFF engine layers.
3. **Consolidation Pass:** When ready to resume active terrain iteration, draft a single consolidated Refactor & Migrate ticket to move the validated assessor into the standardized `app/services/terrain/` structure alongside a dedicated RSpec suite.
4. **Duplicate Cleanup:** Delete or archive the three overlapping task files from the old backlog once the migration plan is confirmed.

## Acceptance Criteria (Original)
- [x] This tracking document is successfully created inside the active `projects/galaxy_game/backlog/2026-05/` directory.
- [x] No files in the old backlog or archive subdirectories are deleted or altered during creation.
- [x] Next agent must review the "Critical Findings" section before creating any migration tasks.

**Note:** Investigation complete. Proceed to execute `2026-05-18-LOW-REFACTOR-TERRAIN-QUALITY-ASSESSOR-MIGRATE-AND-TEST.md`.
