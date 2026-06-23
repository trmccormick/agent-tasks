---
name: "AI Site Selection Algorithm"
priority: HIGH
phase: phase8+
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Implement AI Site Selection Algorithm

**Priority**: HIGH  
**Phase**: phase8+  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

Need automated colony placement based on geological features, risk, logistics, and pattern-specific heuristics (Luna/Mars/Venus). Current strategic evaluation lacks a robust site recommendation layer.

**Current**: Strategic evaluation exists, site algorithm incomplete  
**Expected**: Site selector returns primary/secondary sites with suitability, cost, and timeline projections

## Acceptance Criteria

- [ ] Geological feature scoring implemented (safety, accessibility, resources)
- [ ] Pattern recognition for body-specific colonization models implemented
- [ ] Output includes ranked primary/secondary sites and network strategy
- [ ] Integrated with strategic evaluator outputs and mission planning

## Implementation Steps

1. Build SiteSelector service and scoring model
2. Add pattern-specific heuristics (Luna, Mars, Venus, fallback)
3. Implement risk/logistics/economic weighting
4. Return ranked candidate sites and expansion plan
5. Validate with integration specs

## Commit Instructions

```bash
git add galaxy_game/app/services/ai_manager/ galaxy_game/spec/services/ai_manager/
git commit -m "feat: add AI site selection algorithm with geological and strategic scoring"
```