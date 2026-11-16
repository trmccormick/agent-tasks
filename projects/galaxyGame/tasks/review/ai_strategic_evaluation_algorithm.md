---
name: "AI Strategic Evaluation Algorithm"
priority: HIGH
phase: 8
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Implement AI Strategic Evaluation Algorithm

**Priority**: HIGH  
**Phase**: 8  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

Need comprehensive algorithm to classify discovered systems (Prize Worlds, Resource Worlds, etc.) based on TEI, resources, wormhole connectivity, and strategic value.

**Current**: Discovery service exists, strategic classification incomplete  
**Expected**: Strategic evaluator returns classification, risk, economic forecast, and expansion priority

## Acceptance Criteria

- [ ] Multi-factor assessment implemented (TEI, resources, wormhole, energy, threats)
- [ ] Classification system working (Prize, Resource, Brown Dwarf, Transit, Energy)
- [ ] Risk/economic/sequencing data generated
- [ ] Integrated with discovery outputs and site selection

## Implementation Steps

1. Create StrategicEvaluator service with multi-factor scoring
2. Implement classification logic based on factor thresholds
3. Add risk assessment and economic forecasting
4. Return ranked system recommendations with expansion sequencing
5. Validate with integration specs

## Commit Instructions

```bash
git add galaxy_game/app/services/ai_manager/ galaxy_game/spec/services/ai_manager/
git commit -m "feat: implement AI strategic evaluation for system classification"
```