---
name: "Fix SpaceStation Storage Capacity Calculation"
priority: HIGH
phase: 5
created: 2026-06-21
status: backlog
type: bugfix
---

# TASK: Fix SpaceStation Storage Capacity Override Issue

**Priority**: HIGH  
**Phase**: 5  
**Type**: bugfix  
**Created**: 2026-06-21  

## Problem Statement

SpaceStation#calculate_storage_capacity incorrectly overrides BaseSettlement#total_storage_capacity, summing units to 50006.0 instead of expected 50000.0. Child method bypasses correct parent implementation.

**Current**: SpaceStation returns 50006.0  
**Expected**: All code uses BaseSettlement#total_storage_capacity returning 50000.0

## Files to Edit

| File | Purpose |
|---|---|
| `galaxy_game/app/models/settlement/space_station.rb` | Remove override (lines 73-82) |

## Acceptance Criteria

- [ ] SpaceStation#calculate_storage_capacity removed
- [ ] All code uses BaseSettlement#total_storage_capacity
- [ ] Spec returns 50000.0 as expected
- [ ] No regressions in settlement specs

## Implementation Steps

1. Remove SpaceStation#calculate_storage_capacity (lines 73-82)
2. Verify BaseSettlement#total_storage_capacity is correct
3. Run space station specs to confirm 50000.0
4. Check for any other code calling the removed method
5. Run full settlement specs for regressions

## Commit Instructions

```bash
git add galaxy_game/app/models/settlement/space_station.rb
git commit -m "fix: remove SpaceStation storage capacity override, use BaseSettlement"
```