---
name: "Implement Scientific Data Display Layer"
priority: MEDIUM
phase: phase8+
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Implement Scientific Data Display Layer in Monitor UI

**Priority**: MEDIUM  
**Phase**: phase8+ (Cycler Arc)  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

The monitor UI uses a canvas-based renderer with toggleable overlays, but no dedicated scientific layer exists yet. Atmospheric, geological, and orbital data are present in various controllers and models but not visualized as scientific overlays.

**Current**: Monitor UI has terrain layers only, no scientific overlay support  
**Expected**: Scientific layer toggle in monitor interface with atmospheric, geological, and orbital data visualization

## Evidence of Incompleteness

```bash
find galaxy_game/app/javascript/admin -name "*scientific*" 2>/dev/null | head -3
# Likely returns no results — scientific layer JS doesn't exist
```

## Files to Create/Modify

| File | Purpose | Key Section |
|------|---------|-------------|
| `galaxy_game/app/javascript/admin/scientific_layer.js` (new) | Scientific overlay renderer | Canvas rendering logic |
| `galaxy_game/app/views/admin/celestial_bodies/monitor.html.erb` | Monitor UI | Add scientific layer toggle |
| `galaxy_game/app/javascript/admin/monitor.js` | Monitor controller | Layer toggle integration |
| `galaxy_game/app/services/scientific_data_service.rb` (new) | Data aggregation | Atmospheric/geological/orbital data |

## Acceptance Criteria

- [ ] Scientific layer toggle available in monitor interface
- [ ] Atmospheric, geological, and orbital data display correctly as overlays
- [ ] Layer integrates properly with existing terrain layers
- [ ] Performance impact is minimal when layer is inactive
- [ ] UI and code changes documented in UI_IMPLEMENTATION.md

## Implementation Steps

1. Extend monitor.html.erb to add scientific layer toggle in UI controls
2. Update monitor.js to support rendering the scientific overlay (follow pattern for other layers)
3. Aggregate atmospheric, geological, and orbital data from backend endpoints
4. Design overlay visuals for atmospheric composition, geological features, orbital parameters
5. Implement data panels, charts/graphs, and legend system
6. Support export functionality and performance optimizations

## Blockers / Dependencies

- BLOCKED: Do not begin until 2026-04-17-HIGH-IMPLEMENT-GGMAP-SCIENTIFIC-LAYER.md is complete and approved

## Commit Instructions

```bash
git add galaxy_game/app/javascript/admin/scientific_layer.js galaxy_game/app/views/admin/celestial_bodies/monitor.html.erb galaxy_game/app/services/scientific_data_service.rb
git commit -m "feat: add scientific data display layer to monitor UI"
```
