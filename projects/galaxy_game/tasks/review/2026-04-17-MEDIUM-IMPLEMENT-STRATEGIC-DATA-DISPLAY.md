---
name: "Implement Strategic Data Display Layer"
priority: MEDIUM
phase: phase8+
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Implement Strategic Data Display Layer in Monitor UI

**Priority**: MEDIUM  
**Phase**: phase8+ (Cycler Arc)  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

Strategic planning requires visualization of economic, development, and military overlays beyond basic terrain data. The monitor UI uses a canvas-based renderer with toggleable overlays but lacks strategic layer support.

**Current**: Monitor UI has terrain/scientific layers only, no strategic overlay  
**Expected**: Strategic layer toggle with economic zones, trade routes, development hubs, and military/defense data visualization

## Evidence of Incompleteness

```bash
find galaxy_game/app/javascript/admin -name "*strategic*" 2>/dev/null | head -3
# Likely returns no results — strategic layer JS doesn't exist
```

## Files to Create/Modify

| File | Purpose | Key Section |
|------|---------|-------------|
| `galaxy_game/app/javascript/admin/strategic_layer.js` (new) | Strategic overlay renderer | Canvas rendering logic |
| `galaxy_game/app/views/admin/celestial_bodies/monitor.html.erb` | Monitor UI | Add strategic layer toggle |
| `galaxy_game/app/javascript/admin/monitor.js` | Monitor controller | Layer toggle integration |
| `galaxy_game/app/services/strategic_data_service.rb` (new) | Data aggregation | Economic/military data |

## Acceptance Criteria

- [ ] Strategic layer toggle available in monitor interface
- [ ] Economic, development, and military overlays display correctly
- [ ] Multiple overlays can be combined and toggled
- [ ] Layer supports planetary, system, and galactic scales
- [ ] Performance remains acceptable with complex overlays
- [ ] UI and code changes documented

## Implementation Steps

1. Extend monitor.html.erb to add strategic layer toggle in UI controls
2. Update monitor.js to support rendering the strategic overlay (follow pattern for other layers)
3. Aggregate strategic/economic/military data from backend endpoints
4. Design overlay visuals for economic zones, trade routes, development hubs, military/defense data
5. Support multi-scale overlays (planetary, system, galactic)
6. Ensure performance remains acceptable with complex overlays

## Blockers / Dependencies

- BLOCKED: Do not begin until 2026-04-17-HIGH-IMPLEMENT-GGMAP-STRATEGIC-LAYER.md is complete and approved

## Commit Instructions

```bash
git add galaxy_game/app/javascript/admin/strategic_layer.js galaxy_game/app/views/admin/celestial_bodies/monitor.html.erb galaxy_game/app/services/strategic_data_service.rb
git commit -m "feat: add strategic data display layer to monitor UI"
```
