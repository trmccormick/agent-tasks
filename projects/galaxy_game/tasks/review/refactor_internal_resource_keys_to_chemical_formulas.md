[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase5+/refactor_internal_resource_keys_to_chemical_formulas.md]
[RATIONALE: UI/monitoring or investigation task — not Luna sim prerequisite, cosmetic/post-MVP]
---
name: "Refactor Internal Resource Keys to Chemical Formulas"
priority: MEDIUM
phase: 5
created: 2026-06-21
status: backlog
type: refactor
---

# TASK: Refactor Resource Keys to Chemical Formulas

**Priority**: MEDIUM  
**Phase**: 5  
**Type**: refactor  
**Created**: 2026-06-21  

## Problem Statement

Internal code uses display names ("iron", "oxygen") instead of chemical formulas (Fe, O2), violating project convention. Should consistently use MaterialLookupService for resource mapping.

**Current**: Mixed display names in internal logic  
**Expected**: All internal code uses chemical formulas via MaterialLookupService

## Acceptance Criteria

- [ ] All display names replaced with chemical formulas (Fe, O2, H2O, etc.)
- [ ] MaterialLookupService used consistently
- [ ] UI display names unchanged
- [ ] New RSpec spec validates formula usage (8 examples)
- [ ] No display names in internal code paths

## Implementation Steps

1. Search for display names in internal logic (factories, services, models)
2. Replace pattern: display_name → LookupService.find_material(formula)
3. Update factories to use material_id and formula
4. Create spec/services/resource_naming_spec.rb
5. Update hardcoded arrays and case statements
6. Verify no regressions in resource-dependent code
7. Document chemical formula conventions

## Commit Instructions

```bash
git add galaxy_game/app/ galaxy_game/spec/ galaxy_game/db/
git commit -m "refactor: standardize internal resource representation to chemical formulas"
```