---
name: "Integrate Scouting Recovery Logic with WormholeScoutingService"
priority: HIGH
phase: 8
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Integrate Physical Recovery Logic into WormholeScoutingService

**Priority**: HIGH  
**Phase**: 8  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

WormholeScoutingService needs to support physical delivery of Handshake Tokens between star systems using scout ships with cargo capacity.

**Current**: No recovery mission support  
**Expected**: Scouts can carry and deliver Handshake Tokens with mission success on return

## Acceptance Criteria

- [ ] Add `:seismic_survey` to ScoutShip capabilities
- [ ] Implement `carry_vault` attribute for token storage
- [ ] Mission success condition requires token delivery to Sol WTC
- [ ] AI can distinguish resource vs structural verification scouts
- [ ] Scouts successfully transport coordinates back to Sol

## Implementation Steps

1. Add seismic_survey capability to ScoutShip
2. Add carry_vault attribute for Handshake Token
3. Create recovery mission logic in WormholeScoutingService
4. Implement mission success condition (return with token)
5. Create AI selection logic for scout types
6. Add comprehensive specs for recovery missions
7. Test token transport across wormholes

## Commit Instructions

```bash
git add galaxy_game/app/models/scout_ship.rb galaxy_game/app/services/wormhole_scouting_service.rb
git commit -m "feat: add physical token recovery support to wormhole scouting"
```