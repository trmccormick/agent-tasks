---
name: "AI Resource Allocation Engine"
priority: HIGH
phase: phase8+
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Implement AI Resource Allocation Engine

**Priority**: HIGH  
**Phase**: phase8+  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

New colonies need automated bootstrap logistics, ISRU prioritization, and startup economics. Current planning outputs are not converted into executable resource allocation plans.

**Current**: Site/strategy data exists but no allocation engine  
**Expected**: Resource allocation service that produces initial package, ISRU priorities, and economic startup plan

## Acceptance Criteria

- [ ] Engine outputs initial allocation package (energy/water/food/construction)
- [ ] ISRU priorities ranked by strategic value and timeline
- [ ] Break-even and ROI startup economics generated
- [ ] Dynamic reallocation supports settlement growth and risk buffers

## Implementation Steps

1. Create resource allocation service and input contracts
2. Implement bootstrap logistics + transport planning
3. Add ISRU ranking and local-vs-import economic model
4. Integrate with strategic/site selection outputs and test end-to-end

## Commit Instructions

```bash
git add galaxy_game/app/services/ai_manager/ galaxy_game/spec/services/ai_manager/
git commit -m "feat: implement AI resource allocation engine for bootstrap settlements"
```