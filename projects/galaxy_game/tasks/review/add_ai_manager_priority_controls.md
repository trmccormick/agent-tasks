[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase6+/add_ai_manager_priority_controls.md]
[RATIONALE: UI/dashboard cosmetic, investigation task, or obsolete code cleanup — not Luna sim prerequisite]
---
name: "Add AI Manager Priority Controls"
priority: MEDIUM
phase: phase6+
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Add AI Manager Priority Controls to Admin Simulation

**Priority**: MEDIUM  
**Phase**: phase6+  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

Admin simulation lacks controls to tune AI manager priority weights during testing. Priority values are hardcoded and not exposed for simulation-time adjustments.

**Current**: Hardcoded AI priorities in service logic  
**Expected**: Priorities in GameConstants with admin controls for testing and tuning

## Files to Edit

| File | Purpose |
|---|---|
| `galaxy_game/config/initializers/game_constants.rb` | Add `AI_PRIORITIES` constants |
| `galaxy_game/app/services/ai_manager/ai_priority_system.rb` | Read from constants, not hardcoded literals |
| `galaxy_game/app/views/admin/simulation/index.html.erb` | Add AI manager controls section |

## Acceptance Criteria

- [ ] Priority values are centralized in GameConstants
- [ ] AI priority system references constants
- [ ] Admin simulation includes controls for critical/operational categories
- [ ] Testing workflow supports changing priorities and observing effects

## Implementation Steps

1. Move priority defaults to `GameConstants::AI_PRIORITIES`
2. Refactor priority system to use centralized values
3. Add admin simulation controls (inputs/sliders)
4. Wire control values into testing path (persisted or session-scoped as designed)

## Commit Instructions

```bash
git add galaxy_game/config/initializers/game_constants.rb galaxy_game/app/services/ai_manager/ai_priority_system.rb galaxy_game/app/views/admin/simulation/index.html.erb
git commit -m "feat: add AI manager priority controls for admin simulation tuning"
```