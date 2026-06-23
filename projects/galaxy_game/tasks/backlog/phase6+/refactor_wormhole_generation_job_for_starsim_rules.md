---
name: "Refactor Wormhole Generation to Use StarSim Rules"
priority: HIGH
phase: 6
created: 2026-06-21
status: backlog
type: refactor
---

# TASK: Refactor Wormhole Generation Job for StarSim Rules Compliance

**Priority**: HIGH  
**Phase**: 6  
**Type**: refactor  
**Created**: 2026-06-21  

## Problem Statement

wormhole_generation_job.rb uses pure random() logic instead of StarSim generation rules. Doesn't enforce anchor priority, 5 AU buffer, or connectivity caps, resulting in unstable/unrealistic networks.

**Current**: Random wormhole generation without constraints  
**Expected**: Generation respects StarSim rules (anchor priority, buffer, caps)

## Acceptance Criteria

- [ ] Anchor priority enforced (Saturn-mass planets preferred)
- [ ] 5 AU buffer enforced (no natural wormholes in habitable zones)
- [ ] Connectivity cap respected (MAX_WORMHOLES_PER_SYSTEM)
- [ ] All procedural constraints from STARSIM_GENERATION_RULES.md applied
- [ ] Network stability and realism improved
- [ ] All specs pass

## Implementation Steps

1. Review STARSIM_GENERATION_RULES.md procedural constraints
2. Refactor wormhole generation to enforce anchor priority
3. Implement 5 AU habitable zone buffer check
4. Add connectivity cap enforcement
5. Remove pure random() logic
6. Update generation job to use constraint-based selection
7. Add comprehensive specs for each constraint
8. Test network stability with new rules

## Commit Instructions

```bash
git add galaxy_game/app/jobs/wormhole_generation_job.rb
git commit -m "refactor: enforce StarSim generation rules in wormhole creation"
```