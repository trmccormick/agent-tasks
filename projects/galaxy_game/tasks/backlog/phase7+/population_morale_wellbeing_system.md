---
name: "Implement Population Morale & Wellbeing System"
priority: MEDIUM
phase: 7
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Implement Population Morale & Wellbeing System

**Priority**: MEDIUM  
**Phase**: 7  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

PopulationManagement handles headcount and resources but lacks morale/wellbeing. Isolation without green life, sensory variety, and routine causes psychological decline, leading to population loss and reduced settlement capacity.

**Current**: Population tracking only; no morale mechanics  
**Expected**: Morale system with wellbeing contributors and population retention

## Acceptance Criteria

- [ ] Morale/wellbeing tracking on populations
- [ ] Biophilia effects from greenhouses
- [ ] Food variety and habitat quality factors
- [ ] Workload and isolation duration modeling
- [ ] Communication effects from other settlements
- [ ] Population transfer requests when morale drops
- [ ] Productivity decline before departure

## Implementation Steps

1. Extend PopulationManagement concern with morale tracking
2. Add wellbeing contributor calculations
3. Implement greenhouse biophilia effects
4. Add food variety and habitat factors
5. Create workload and isolation decay mechanics
6. Implement population transfer request logic
7. Add productivity decline mechanics
8. Create morale monitoring UI

## Commit Instructions

```bash
git add galaxy_game/app/concerns/population_management.rb galaxy_game/spec/models/concerns/population_management_spec.rb
git commit -m "feat: add population morale and wellbeing system with retention mechanics"
```