---
name: "AI System Discovery Implementation"
priority: HIGH
phase: 8
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Implement AI System Discovery with Real Database Queries

**Priority**: HIGH  
**Phase**: 8  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

StateAnalyzer uses mock scouting logic instead of querying real star systems database and analyzing wormhole connectivity.

**Current**: Mock scouting with fake system IDs  
**Expected**: Real database queries with distance/resource/habitability/wormhole analysis

## Acceptance Criteria

- [ ] Queries star_systems table for unexplored/reachable systems
- [ ] Distance calculations and wormhole range analysis working
- [ ] Resource potential and habitability scoring implemented
- [ ] Threat assessment and comparative ranking working

## Implementation Steps

1. Replace mock logic in StateAnalyzer with real database queries
2. Implement spatial distance calculations for wormhole range
3. Add resource/habitability/threat factor scoring
4. Return ranked candidate systems with strategic metadata
5. Integrate with strategic evaluator

## Commit Instructions

```bash
git add galaxy_game/app/services/ai_manager/ galaxy_game/spec/services/ai_manager/
git commit -m "feat: implement real AI system discovery with database queries and evaluation"
```