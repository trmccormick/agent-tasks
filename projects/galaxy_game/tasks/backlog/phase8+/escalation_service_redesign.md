---
name: "Redesign EscalationService for ISRU-First"
priority: HIGH
phase: 8
created: 2026-06-21
status: backlog
type: refactor
---

# TASK: Redesign EscalationService with ISRU-First Escalation Order

**Priority**: HIGH  
**Phase**: 8  
**Type**: refactor  
**Created**: 2026-06-21  

## Problem Statement

Current EscalationService doesn't follow ISRU-first design: it incorrectly classifies oxygen/water as special missions and uses wrong settlement queries. Escalation order must be: expand ISRU → deploy robot fleet → query local trade → schedule cycler import.

**Current**: Broken escalation order with incorrect resource classification  
**Expected**: ISRU-first escalation with correct priority sequence

## Files to Edit

| File | Purpose |
|---|---|
| `galaxy_game/app/services/ai_manager/escalation_service.rb` | Redesign escalation order |
| `galaxy_game/spec/services/ai_manager/escalation_service_spec.rb` | Update specs |

## Acceptance Criteria

- [ ] Escalation order matches ISRU-first sequence
- [ ] `critical_resource?` logic removed (all resources harvested locally first)
- [ ] `find_nearby_settlements` stubbed until Cycler network implemented
- [ ] Special mission triggers for oxygen/water removed
- [ ] All specs pass with new logic

## Implementation Steps

1. Redesign escalation order per ISRU-first principles
2. Remove incorrect `critical_resource?` classification
3. Stub `find_nearby_settlements` with note on Cycler dependency
4. Update all affected escalation specs
5. Verify no regressions in mission/resource logic

## Commit Instructions

```bash
git add galaxy_game/app/services/ai_manager/escalation_service.rb galaxy_game/spec/services/ai_manager/escalation_service_spec.rb
git commit -m "refactor: redesign EscalationService to follow ISRU-first escalation order"
```