---
name: "Refactor StationConstructionStrategy for Physics-Based Logistics"
priority: HIGH
phase: 8
created: 2026-06-21
status: backlog
type: refactor
---

# TASK: Refactor StationConstructionStrategy for Physics-Based Logistics

**Priority**: HIGH  
**Phase**: 8  
**Type**: refactor  
**Created**: 2026-06-21  

## Problem Statement

StationConstructionStrategy doesn't align with asteroid conversion physics spec: asteroid composition/density/hollowness not modeled, towing duration not calculated dynamically, and cheap rubble piles incorrectly considered viable.

**Current**: Static construction timeline and simple cost-benefit without physics  
**Expected**: Physics-based selection with dynamic timelines and hollow asteroid bracing

## Files to Edit

| File | Purpose |
|---|---|
| `galaxy_game/app/models/asteroid.rb` | Add composition/density/hollowness |
| `galaxy_game/app/services/ai_manager/station_construction_strategy.rb` | Rewrite selection logic |
| `galaxy_game/app/services/ai_manager/station_cost_benefit_analyzer.rb` | Add physics-based analysis |
| Migrations | Add asteroid physics columns |

## Acceptance Criteria

- [ ] Asteroid model has composition, density, hollowness attributes
- [ ] Qualification service rejects rubble piles and unshielded icy bodies
- [ ] Towing duration calculated from distance and mass
- [ ] Internal bracing step added for hollow asteroids
- [ ] AI correctly prioritizes solid metallic rocks over cheap rubble

## Implementation Steps

1. Add composition/density/hollowness to Asteroid schema
2. Create qualification service to filter invalid asteroids
3. Implement dynamic towing duration calculation
4. Add internal bracing logic for hollow asteroids
5. Inject physics-based strategy weighting
6. Update all related specs

## Commit Instructions

```bash
git add galaxy_game/app/models/asteroid.rb galaxy_game/app/services/ai_manager/station_construction_strategy.rb galaxy_game/db/migrate/
git commit -m "refactor: align station construction strategy with physics-based asteroid logistics"
```