---
name: "Implement AWS Handshake Token and Injection Logic"
priority: HIGH
phase: 8
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Implement AWS Handshake Token for Wormhole Creation

**Priority**: HIGH  
**Phase**: 8  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

Artificial wormhole creation should require physical HandshakeToken with target coordinates instead of blind random generation. Prevents arbitrary AWS placement and enables strategic wormhole positioning.

**Current**: Random AWS generation without token requirement  
**Expected**: AWS requires token-carried coordinates for endpoint

## Acceptance Criteria

- [ ] HandshakeToken item class created (Items::PhysicalDataVault)
- [ ] Wormhole creation validates token presence
- [ ] Coordinate extraction from token metadata working
- [ ] Token consumed/archived after successful AWS creation
- [ ] Scouts can carry and deliver tokens
- [ ] AWS exits at exact target coordinates
- [ ] Cannot create AWS without valid token

## Implementation Steps

1. Create Items::HandshakeToken class
2. Add before_create validation to Wormhole
3. Implement coordinate extraction from token
4. Create token consumption logic
5. Update AWS generation to use token coordinates
6. Integrate with scout mission system
7. Add comprehensive specs for token validation
8. Test end-to-end AWS creation workflow

## Commit Instructions

```bash
git add galaxy_game/app/models/ galaxy_game/app/services/
git commit -m "feat: add HandshakeToken requirement for AWS wormhole creation"
```