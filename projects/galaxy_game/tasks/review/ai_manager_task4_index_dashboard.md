[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase6+/ai_manager_task4_index_dashboard.md]
[RATIONALE: UI/dashboard cosmetic, investigation task, or obsolete code cleanup — not Luna sim prerequisite]
---
name: "Fix AI Manager Index Dashboard Layout"
priority: MEDIUM
phase: phase6+
created: 2026-06-21
status: backlog
type: frontend
---

# TASK: Fix AI Manager Index Dashboard

**Priority**: MEDIUM  
**Phase**: phase6+  
**Type**: frontend/layout  
**Created**: 2026-06-21  

## Problem Statement

AI Manager index page already receives real controller data but uses old stylesheet/layout classes and misses alerts/testing nav integration.

**Current**: Legacy classes and incomplete UI sections  
**Expected**: Shared AI Manager layout classes, proper alerts section, and complete sidebar navigation

## Files to Edit

| File | Purpose |
|---|---|
| `galaxy_game/app/views/admin/ai_manager/index.html.erb` | Apply shared layout, alerts, and nav cleanup |

## Acceptance Criteria

- [ ] Index page uses AI manager shared layout/style classes
- [ ] System alerts render when present
- [ ] Sidebar includes Testing link and consistent nav structure
- [ ] No controller/model logic changes required

## Commit Instructions

```bash
git add galaxy_game/app/views/admin/ai_manager/index.html.erb
git commit -m "style: align AI Manager dashboard with shared layout and alerts"
```