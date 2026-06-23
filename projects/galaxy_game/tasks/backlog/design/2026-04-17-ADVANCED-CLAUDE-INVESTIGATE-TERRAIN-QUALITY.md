---
name: "Investigate Terrain Quality Issues"
priority: MEDIUM
phase: design
created: 2013-06-21
status: backlog
type: investigation
relocated_from: reorganization_attempt_3
relocated_reason: "UI/rendering task moved to design folder (geotiff loads correctly but quality is low)"
---

# TASK: Investigate and Resolve Terrain Quality Issues (Earth & Exoplanet)

**Priority**: MEDIUM  
**Phase**: phase5+ (Early Game)  
**Type**: investigation/audit  
**Created**: 2026-06-21  

## Problem Statement

Known terrain quality issues exist for both Earth (NASA GeoTIFF) and exoplanet procedural generation. Exoplanet terrain appears visually odd (too uniform/random), Earth terrain may have data or rendering fidelity problems. No recent work has addressed these issues; RSpec/test failures have been prioritized.

**Current**: Terrain quality issues documented but not investigated  
**Expected**: Root cause analysis with evidence, distinguishing code bugs from parameter/model tuning issues

## Evidence of Issues

```bash
grep -n "learned_from_nasa_data\|sine_wave_fallback" galaxy_game/app/services/star_sim/automatic_terrain_generator.rb 2>/dev/null | head -5
# Check metadata for pattern application status
```

## Files to Review/Create

| File | Purpose | Key Section |
|------|---------|-------------|
| `galaxy_game/app/services/star_sim/automatic_terrain_generator.rb` | Terrain generation | Pattern loading logic |
| `data/json-data/ai_learning/learned_patterns.json` | Learned patterns | NASA pattern files |
| `docs/testing/terrain_quality_audit.md` (new) | Audit report | Findings and recommendations |

## Acceptance Criteria

- [ ] Earth NASA terrain loads and displays accurately
- [ ] Exoplanet terrain generation issues are identified and documented
- [ ] Root causes are evidenced with metadata/code/screenshots
- [ ] Clear distinction between code bugs and parameter/model issues
- [ ] Actionable recommendations for improvement are provided
- [ ] Performance benchmarks are established
- [ ] All findings documented in terrain_quality_audit.md

## Implementation Steps

1. **Earth Terrain Audit**
   - Verify NASA GeoTIFF elevation data loads and renders correctly
   - Visually inspect Earth terrain for continental accuracy and hydrosphere integration
   - Benchmark loading speed and memory usage
2. **Exoplanet Terrain Investigation**
   - Check metadata for 'learned_from_nasa_data' vs 'sine_wave_fallback'
   - Confirm NASA pattern files are loaded and applied
   - Verify Civ4 Earth reference is accessible and used for pattern learning
   - Analyze elevation scaling, smoothing, and planet-type matching
3. **Visual Quality Assessment**
   - Identify specific visual issues (uniformity, randomness, feature mismatch)
   - Compare exoplanet vs Earth terrain quality
   - Ensure planet-type matching and gas giant handling are correct
4. **Root Cause Documentation**
   - Catalog all terrain quality problems with evidence
   - Trace code/data flow from seed to render
   - Distinguish between code bugs and parameter/model tuning issues
   - Provide detailed, actionable recommendations for fixes

## Commit Instructions

```bash
git add docs/testing/terrain_quality_audit.md
git commit -m "docs: document terrain quality audit findings and recommendations"
```
