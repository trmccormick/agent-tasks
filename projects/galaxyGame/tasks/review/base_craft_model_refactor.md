---
name: "BaseCraft Model Refactor & Concern Audit"
priority: HIGH
phase: 8
created: 2026-06-21
status: backlog
type: refactor
---

# TASK: Audit and Refactor BaseCraft Model Architecture

**Priority**: HIGH  
**Phase**: 8  
**Type**: refactor  
**Created**: 2026-06-21  

## Problem Statement

BaseCraft model contains excessive business logic, service calls, and concern overlap, violating architectural boundaries and making testing/maintenance difficult.

**Current**: Business logic embedded in model with overlapping concerns  
**Expected**: Lean model with logic separated into concerns/services

## Acceptance Criteria

- [ ] Complete audit of all BaseCraft methods
- [ ] Concern/service overlaps identified and documented
- [ ] Business logic moved to appropriate services
- [ ] Atmosphere/location logic refactored
- [ ] Validation/deployment logic separated
- [ ] All tests pass with new boundaries
- [ ] Refactor plan documented

## Implementation Steps

1. Audit all BaseCraft methods for business logic
2. Identify concern overlaps (HasUnits, HasModules, EnergyManagement, etc.)
3. Map business logic to target services/concerns
4. Refactor atmosphere and location logic
5. Separate validation and deployment concerns
6. Update model to focus on persistence/associations
7. Update all tests for new boundaries

## Commit Instructions

```bash
git add galaxy_game/app/models/base_craft.rb galaxy_game/app/concerns/ galaxy_game/app/services/
git commit -m "refactor: lean BaseCraft model with business logic separated to services"
```