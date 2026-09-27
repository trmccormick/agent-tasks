---
name: "Refactor TerraformingManager Resource Identification"
priority: MEDIUM
phase: 6
created: 2026-06-21
status: backlog
type: refactor
---

# TASK: Refactor TerraformingManager for Data-Driven Resource Identification

**Priority**: MEDIUM  
**Phase**: 6  
**Type**: refactor  
**Created**: 2026-06-21  

## Problem Statement

TerraformingManager#identify_available_resources hardcoded to specific planet names (@worlds[:venus], @worlds[:titan]) instead of using celestial body atmospheric composition data. Breaks on world name changes or system expansion.

**Current**: Hardcoded planet names in resource logic  
**Expected**: Data-driven approach using atmospheric composition

## Acceptance Criteria

- [ ] No hardcoded planet names in identify_available_resources
- [ ] Method dynamically queries atmospheric composition
- [ ] Gas percentages determine available resources
- [ ] Body types (gas_giant, terrestrial, ice_giant) mapped to profiles
- [ ] Works with any celestial body configuration
- [ ] All specs pass with new approach

## Implementation Steps

1. Analyze current hardcoded logic in identify_available_resources
2. Implement dynamic atmospheric composition querying
3. Create gas percentage thresholds for resource availability
4. Map body types to expected resource profiles
5. Refactor method to use data-driven approach
6. Update all related specs
7. Test with multiple celestial body configurations

## Commit Instructions

```bash
git add galaxy_game/app/services/ai_manager/terraforming_manager.rb
git commit -m "refactor: make TerraformingManager resource identification data-driven"
```