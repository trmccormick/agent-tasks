---
name: "Dynamic Wormhole Pathfinding Service"
priority: HIGH
phase: 8
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Implement Dynamic Wormhole Pathfinding Service

**Priority**: HIGH  
**Phase**: 8  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

Need BFS-based pathfinding for dynamic wormhole networks that updates in real-time when wormholes are created/removed (artificial, natural, or special easter egg connections).

**Current**: No pathfinding service; static network assumptions  
**Expected**: Live shortest-path BFS that handles dynamic network changes

## Acceptance Criteria

- [ ] WormholeNavigator service implements BFS pathfinding
- [ ] Uses live WormholeConnection queries (not cached)
- [ ] Handles artificial, natural, and special connections
- [ ] Reconstructs full path between any two systems
- [ ] Integrated with scouting and AI manager services

## Implementation Steps

1. Create WormholeNavigator service
2. Implement BFS shortest path algorithm
3. Add live edge querying (no caching)
4. Implement path reconstruction logic
5. Integrate with WormholeScoutingService
6. Add specs covering dynamic network changes

## Commit Instructions

```bash
git add galaxy_game/app/services/wormhole_navigator.rb galaxy_game/spec/services/wormhole_navigator_spec.rb
git commit -m "feat: add dynamic wormhole pathfinding service with BFS algorithm"
```