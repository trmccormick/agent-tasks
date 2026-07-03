---
name: "Implement Time & Resource Simulation System"
priority: HIGH
phase: 8
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Implement Time & Resource Simulation for TerrainForge

**Priority**: HIGH  
**Phase**: 8  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

TerrainForge construction projects need realistic duration estimates, resource consumption tracking, progress simulation, and supply chain logistics with corporation-based access controls.

**Current**: No simulation system; construction timing and resources undefined  
**Expected**: Complete simulation covering duration, consumption, progress, and logistics

## Acceptance Criteria

- [ ] Duration calculation system with environmental factors
- [ ] Resource consumption tracking with corporation restrictions
- [ ] Progress simulation engine with real-time updates
- [ ] Supply chain loss calculation with terrain factors
- [ ] Corporation-based access controls throughout
- [ ] Completion detection and event notifications

## Implementation Steps

1. Create PROJECT_DURATIONS configuration for surface operations
2. Implement resource consumption calculation with access controls
3. Build progress simulation engine with environmental factors
4. Add supply chain loss calculation within corporation territories
5. Implement persistence and recovery mechanisms
6. Add comprehensive specs covering all phases

## Commit Instructions

```bash
git add galaxy_game/app/services/terrain_forge/ galaxy_game/app/models/construction_project.rb galaxy_game/config/
git commit -m "feat: implement time and resource simulation for TerrainForge construction"
```