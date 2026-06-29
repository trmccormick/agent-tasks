---
name: "Fix EscalationService Water Escalation ISRU Chain"
priority: HIGH
phase: 6
created: 2026-06-21
status: backlog
type: bugfix
---

# TASK: Fix Water Escalation to Use Correct ISRU Chain

**Priority**: HIGH  
**Phase**: 6  
**Type**: bugfix  
**Created**: 2026-06-21  

## Problem Statement

Water escalation incorrectly creates generic robots with ice_extraction task. Luna water comes from regolith processing via TEU + PVE, not ice harvesting. Architecture is wrong.

**Current**: Generic robots with ice_extraction task  
**Expected**: Uses actual TEU + PVE ISRU units

## Files to Edit

| File | Action |
|---|---|
| `app/services/ai_manager/escalation_service.rb` | Check for TEU/PVE, trigger deployment |
| `spec/services/ai_manager/escalation_service_spec.rb` | Update or skip wrong tests |

## Acceptance Criteria

- [ ] Water escalation uses TEU + PVE units
- [ ] Checks for existing units first
- [ ] Triggers ISRU deployment if missing
- [ ] Deploys additional PVE if needed
- [ ] Water production as regolith byproduct
- [ ] Tests updated or skipped
- [ ] Architecture validated

## Implementation Steps

1. Update escalation logic to check for TEU/PVE
2. Remove ice_extraction robot creation
3. Trigger precursor ISRU deployment
4. Implement additional PVE deployment
5. Verify water as regolith byproduct
6. Update water escalation spec
7. Test full water escalation workflow

## Commit Instructions

```bash
git add galaxy_game/app/services/ai_manager/escalation_service.rb
git commit -m "fix: water escalation to use correct ISRU TEU/PVE architecture"
```