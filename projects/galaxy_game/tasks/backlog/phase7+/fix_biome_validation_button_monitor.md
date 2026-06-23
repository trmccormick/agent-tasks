---
name: "Implement Biome Validation Button for Digital Twin"
priority: MEDIUM
phase: 7
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Implement Biome Validation for Digital Twin Sandbox

**Priority**: MEDIUM  
**Phase**: 7  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

Monitor page has non-functional "Validate Biomes" button. Should be valuable admin tool for testing biome survival and terraforming planning in digital twin sandbox.

**Current**: Button exists but runBiomeValidation() doesn't exist  
**Expected**: Functional biome validation with TerraSim integration

## Acceptance Criteria

- [ ] Button styling fixed with tool-button class
- [ ] runBiomeValidation() function implemented
- [ ] TerraSim biome stability testing working
- [ ] Validation results displayed properly
- [ ] Integration with sphere data endpoints
- [ ] Terraforming recommendations shown
- [ ] Admin can test multiple biome scenarios

## Implementation Steps

1. Fix button styling and CSS
2. Implement runBiomeValidation() in monitor.js
3. Create validation endpoint
4. Build TerraSim biome validation logic
5. Create results display
6. Add stability score calculation
7. Generate terraforming recommendations
8. Test biome validation workflows

## Commit Instructions

```bash
git add galaxy_game/app/views/admin/ galaxy_game/app/assets/javascripts/ galaxy_game/app/controllers/
git commit -m "feat: implement biome validation tool for digital twin sandbox"
```