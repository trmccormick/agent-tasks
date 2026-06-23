---
name: "Canonicalize PopulationManagement Concern"
priority: MEDIUM
phase: phase6+
created: 2026-06-21
status: backlog
type: architecture
relocated_from: reorganization_attempt_3
relocated_reason: "Population management canonicalization needed after Luna base is running"
---

# TASK: Canonicalize and Apply PopulationManagement Concern for Life Support Defaults

**Priority**: MEDIUM  
**Phase**: phase5+ (Early Game)  
**Type**: architecture  
**Created**: 2026-06-21  

## Problem Statement

`PopulationManagement` concern exists but is not included by any model and does not provide or default key life support attributes (`food_per_person`, `water_per_person`, `energy_per_person`). `BaseSettlement` sets life support defaults using hardcoded values instead of `GameConstants`.

**Current behavior**: PopulationManagement concern is orphaned; BaseSettlement uses hardcoded life support values  
**Expected behavior**: All three model types (BaseSettlement, BaseCraft, OrbitalStructure) use canonical GameConstants via PopulationManagement concern

## Evidence of Incompleteness

```bash
grep -n "include.*PopulationManagement" galaxy_game/app/models/**/*.rb 2>/dev/null | head -5
# Likely returns no results — concern is not included by any model
```

## Files to Edit

| File | Purpose | Key Section |
|------|---------|-------------|
| `galaxy_game/app/models/concerns/population_management.rb` | Population concern | Add attr_accessor + after_initialize callback |
| `galaxy_game/app/models/settlement/base_settlement.rb` | Surface settlements | Include concern, remove hardcoded defaults |
| `galaxy_game/app/models/craft/base_craft.rb` | Human-rated craft | Include concern |
| `galaxy_game/app/models/structures/orbital_structure.rb` | Crewed orbital structures | Include concern |

## Acceptance Criteria

- [ ] PopulationManagement concern provides attr_accessor for food/water/energy per person
- [ ] Defaults set using GameConstants (FOOD_PER_PERSON, WATER_PER_PERSON, ENERGY_PER_PERSON) in after_initialize callback
- [ ] All three model types include the concern
- [ ] Duplicate/hardcoded logic removed from BaseSettlement.set_life_support_defaults
- [ ] All population management logic is DRY and canonical

## Implementation Steps

1. Enhance PopulationManagement concern with attr_accessor + after_initialize defaults from GameConstants
2. Include concern in BaseSettlement, BaseCraft, OrbitalStructure
3. Remove hardcoded life support values from BaseSettlement.set_life_support_defaults
4. Verify all tests pass

## Commit Instructions

```bash
git add galaxy_game/app/models/concerns/population_management.rb galaxy_game/app/models/settlement/base_settlement.rb galaxy_game/app/models/craft/base_craft.rb galaxy_game/app/models/structures/orbital_structure.rb
git commit -m "refactor: canonicalize PopulationManagement concern for life support defaults"
```
