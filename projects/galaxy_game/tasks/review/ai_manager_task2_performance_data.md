[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase6+/ai_manager_task2_performance_data.md]
[RATIONALE: UI/dashboard cosmetic, investigation task, or obsolete code cleanup — not Luna sim prerequisite]
---
name: "Wire AI Performance Page to Real Data"
priority: HIGH
phase: phase6+
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Wire AI Manager Performance Page to Real Data

**Priority**: HIGH  
**Phase**: phase6+  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

Performance view uses placeholder metrics and non-functional controls instead of data from missions and service status.

**Current**: Hardcoded metric values  
**Expected**: Controller-computed mission metrics and recent mission/service status in view

## Files to Edit

| File | Purpose |
|---|---|
| `galaxy_game/app/controllers/admin/ai_manager_controller.rb` | Update `performance` action |
| `galaxy_game/app/views/admin/ai_manager/performance.html.erb` | Replace placeholders with live data rendering |

## Acceptance Criteria

- [ ] Success rate, timeline, and mission counts are derived from Mission data
- [ ] Recent missions table renders from query results
- [ ] AI services status shown from controller helper
- [ ] Non-functional controls removed or replaced with working controls

## Commit Instructions

```bash
git add galaxy_game/app/controllers/admin/ai_manager_controller.rb galaxy_game/app/views/admin/ai_manager/performance.html.erb
git commit -m "feat: connect AI Manager performance page to real mission metrics"
```