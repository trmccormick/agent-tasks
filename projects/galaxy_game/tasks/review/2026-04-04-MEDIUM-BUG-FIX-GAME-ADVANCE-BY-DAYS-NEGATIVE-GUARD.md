[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase5+/2026-04-04-MEDIUM-BUG-FIX-GAME-ADVANCE-BY-DAYS-NEGATIVE-GUARD.md]
[RATIONALE: Old bugfix/superseded work — no longer actionable]
---
name: "Fix Game#advance_by_days Guard Clause"
priority: MEDIUM
phase: phase5+
created: 2026-06-21
status: backlog
type: bug-fix
---

# TASK: Fix Game#advance_by_days — Guard Clause Not Preventing Negative Time Advance

**Priority**: MEDIUM  
**Phase**: phase5+ (Early Game)  
**Type**: bug-fix  
**Created**: 2026-06-21  

## Problem Statement

`Game#advance_by_days` should be a no-op when called with zero or negative values. The spec confirms this intent — passing a negative value should leave `elapsed_time` unchanged. Currently the method advances time by the negative amount, resulting in `elapsed_time` going backwards.

**Current behavior**: `advance_by_days(-2)` advances time by -2, setting `elapsed_time` to -2.0  
**Expected behavior**: `advance_by_days(0)` and `advance_by_days(-2)` return early without changing `elapsed_time`

## Evidence of Bug

```bash
grep -n "advance_by_days" spec/services/game_spec.rb:77 2>/dev/null | head -5
# Expected failure: expected: 0.0, got: -2.0
```

## Files to Edit

| File | Purpose | Key Section |
|------|---------|-------------|
| `galaxy_game/app/services/game.rb` OR `galaxy_game/app/models/game.rb` | Add/fix guard clause in `advance_by_days` | Method body |

## Acceptance Criteria

- [ ] `advance_by_days(0)` returns early without changing `elapsed_time`
- [ ] `advance_by_days(-2)` returns early without changing `elapsed_time`
- [ ] Guard clause prevents negative time advancement
- [ ] Test at spec/services/game_spec.rb:77 passes

## Implementation Steps

1. Add guard clause at start of `advance_by_days`: `return self if days <= 0`
2. Verify test passes: `rspec spec/services/game_spec.rb:77`
3. Check for related negative time edge cases in other specs

## Commit Instructions

```bash
git add galaxy_game/app/services/game.rb galaxy_game/app/models/game.rb
git commit -m "fix: add guard clause to prevent negative time advance in Game#advance_by_days"
```
