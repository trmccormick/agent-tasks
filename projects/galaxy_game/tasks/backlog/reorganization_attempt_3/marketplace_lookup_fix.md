---
name: "Marketplace Lookup Fix for Unknown Resources"
priority: HIGH
phase: 5
created: 2026-06-21
status: backlog
type: bugfix
---

# TASK: Fix Marketplace Lookup for Unknown Resources

**Priority**: HIGH  
**Phase**: 5  
**Type**: bugfix  
**Created**: 2026-06-21  

## Problem Statement

`Marketplace#current_market_condition` uses `find_or_create_by` which creates new Market::Condition records for unknown resources instead of returning nil. This breaks spec expectations and pollutes database.

**Current**: Creates Market::Condition for unknown resources  
**Expected**: Returns nil for unknown resources

## Files to Edit

| File | Purpose |
|---|---|
| `galaxy_game/app/models/market/marketplace.rb` | Fix lookup logic |
| `galaxy_game/spec/models/market/marketplace_spec.rb` | Verify fix |

## Acceptance Criteria

- [ ] `find_or_create_by` changed to `find_by` in current_market_condition
- [ ] Returns nil for unknown resources
- [ ] All marketplace specs pass
- [ ] No regressions in market model specs

## Implementation Steps

1. Change find_or_create_by to find_by in current_market_condition
2. Run marketplace specs to verify fix
3. Run full market model specs for regressions
4. Validate no other uses of this pattern need fixing

## Commit Instructions

```bash
git add galaxy_game/app/models/market/marketplace.rb
git commit -m "fix: marketplace lookup should return nil for unknown resources"
```