---
status: completed
priority: HIGH
type: bug-fix
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/current/2026-07-04-HIGH-FIX-SURFACE-SUITABILITY-BUILDABILITY-FLAT-TERRAIN.md

READ FIRST: Task file contains all prerequisites, credentials, gotchas, and verification steps.
```

---

# TASK: Fix SurfaceSuitabilityAnalyzer — slope calc returns :too_steep for flat terrain
**Status**: COMPLETED
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-07-04
**Phase**: phase1+ (gates Phase 2)

---

## Context

`surface_suitability_analyzer_spec.rb:71` fails because the service returns `:too_steep` for flat terrain where it should return `:buildable`. This is a **service code bug**, not a spec issue. The slope calculation logic in `compute_elevation_and_slope` produces incorrect results for flat/non-water terrain.

This blocks Phase 2 work (lat/lon mapping, crater data) — must be fixed first.

---

## Problem Statement

**Error**:
```
expected: :buildable
     got: :too_steep
```

**Root cause**: The slope calculation in `compute_elevation_and_slope` (line ~194-214) produces a slope value that exceeds the `:flat` threshold even for flat terrain. Need to investigate why.

---

## Files Involved

### Primary — edit these
| File | Purpose | Key Section |
|---|---|---|
| `galaxy_game/app/services/ai_manager/surface_suitability_analyzer.rb` | Fix slope calculation logic | `compute_elevation_and_slope` line ~194, `compute_buildability_mask` line ~108 |
| `galaxy_game/spec/services/ai_manager/surface_suitability_analyzer_spec.rb:71` | Verify fix (may need spec adjustment if terrain data is wrong) | line ~71 |

### Reference — read only
| File | Why You Need It |
|---|---|
| `galaxy_game/app/services/ai_manager/surface_suitability_analyzer.rb:302-310` | `classify_terrain_clearance` thresholds |
| `galaxy_game/app/services/ai_manager/surface_suitability_analyzer.rb:315-340` | `compute_buildability_mask` logic |

---

## Architecture Gotchas

⚠️ **GOTCHA 1: This is a service code bug, not a spec issue**
- ❌ Wrong: Fix the spec to match wrong behavior
- ✅ Right: Fix the slope calculation in the service
- Why: The test expects `:buildable` for flat terrain — that's correct. The service is producing `:too_steep` incorrectly.

⚠️ **GOTCHA 2: Check the elevation grid data**
- The slope calculation depends on the input elevation grid
- If the grid has uniform values (flat terrain), finite difference should produce slope ≈ 0
- Verify the test's celestial body fixture has appropriate elevation data

---

## Implementation Steps

### Step 1 — Debug the slope calculation

Add temporary debug output to understand what's happening:

```ruby
# In the spec, before the expectation:
analyzer = described_class.new(celestial_body)
result = analyzer.score(grid_x: 1, grid_y: 1)
puts "DEBUG: slope_degrees=#{result[:slope_degrees]}, elevation=#{result[:elevation_meters]}"
puts "DEBUG: terrain_clearance=#{result[:terrain_clearance]}, buildability_mask=#{result[:buildability_mask]}"
```

Run the spec and check the debug output to understand why `:too_steep` is returned.

### Step 2 — Fix the root cause

Based on debug output, fix one of:
- **Slope calculation**: If finite difference produces non-zero slope for flat terrain, check the grid indexing or boundary handling
- **Threshold comparison**: If thresholds are wrong, adjust `CLEARANCE_THRESHOLDS` constants
- **Elevation data**: If the test fixture has incorrect elevation data, fix the fixture

### Step 3 — Verify syntax

```bash
ruby -c galaxy_game/app/services/ai_manager/surface_suitability_analyzer.rb 2>&1
```

Must return: `Syntax OK`

### Step 4 — Run targeted spec

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/surface_suitability_analyzer_spec.rb:71 --format progress 2>&1' | tail -5
```

Target: 0 failures.

### Step 5 — Run full SurfaceSuitabilityAnalyzer suite

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/surface_suitability_analyzer_spec.rb --format progress 2>&1' | tail -10
```

Verify no regressions. Note: `:152 + :157` failures are a separate issue (atmosphere schema) — they may still fail here.

### Step 6 — Commit

```bash
git add galaxy_game/app/services/ai_manager/surface_suitability_analyzer.rb galaxy_game/spec/services/ai_manager/surface_suitability_analyzer_spec.rb
git commit -m "fix: correct slope calculation in SurfaceSuitabilityAnalyzer for flat terrain"
git push
```

---

## Acceptance Criteria
- [ ] `surface_suitability_analyzer_spec.rb:71` passes (0 failures)
- [ ] No regressions in other SurfaceSuitabilityAnalyzer specs (except :152/:157 which are separate)
- [ ] Slope calculation produces correct values for flat, moderate, and steep terrain
- [ ] Service code fix committed

---

## Stop Conditions — escalate to user immediately if:
- Debug output shows the elevation grid data is wrong — stop and report what the fixture contains
- Fix causes new failures in specs you did not touch
- Root cause is unclear after debug investigation — stop and report findings

---

## Commit Instructions
```bash
git add galaxy_game/app/services/ai_manager/surface_suitability_analyzer.rb galaxy_game/spec/services/ai_manager/surface_suitability_analyzer_spec.rb
git commit -m "fix: correct slope calculation in SurfaceSuitabilityAnalyzer for flat terrain"
git push
```

---

## Completion Report
**Completed by**: [agent]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures (excluding :152/:157)

### What was changed
- `[file]` — [description]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

---

## Handoff Summary
HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
