---
name: "Fix AI Manager Escalation Service Dependencies"
priority: HIGH
phase: 5
created: 2026-06-21
status: backlog
type: bugfix
---

# TASK: Fix AI Manager Escalation Service Dependencies

**Priority**: HIGH  
**Phase**: 5  
**Type**: bugfix  
**Created**: 2026-06-21  

## Problem Statement

Escalation system has missing dependencies preventing emergency mission creation and atmosphere simulation. Missing EmergencyMissionService and temperature clamping in atmosphere model.

**Current**: Missing services and validation methods  
**Expected**: All dependencies present and working

## Files to Edit

| File | Issue |
|---|---|
| `app/services/ai_manager/escalation_service.rb` | Calls non-existent EmergencyMissionService |
| `app/services/terra_sim/atmosphere_simulation_service.rb` | Missing temperature clamping |
| `app/models/concerns/atmosphere_concern.rb` | Missing temperature setter methods |

## Acceptance Criteria

- [ ] EmergencyMissionService created and functional
- [ ] Temperature clamping implemented (150-400K)
- [ ] Greenhouse effect capped at 2x base
- [ ] All escalation tests pass
- [ ] Atmosphere simulations working

## Implementation Steps

1. Create EmergencyMissionService
2. Add temperature setter methods to atmosphere concern
3. Implement temperature clamping logic
4. Add greenhouse effect capping
5. Update all related specs
6. Test full escalation workflow
7. Verify atmosphere simulation

## Commit Instructions

```bash
git add galaxy_game/app/services/ galaxy_game/app/models/concerns/
git commit -m "fix: add missing escalation service dependencies and atmosphere validation"
```