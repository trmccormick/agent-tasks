[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase6+/create_ai_manager_landing_page.md]
[RATIONALE: UI/dashboard cosmetic, investigation task, or obsolete code cleanup — not Luna sim prerequisite]
---
name: "Create AI Manager Landing Page"
priority: MEDIUM
phase: phase6+
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Create AI Manager Landing Page

**Priority**: MEDIUM  
**Phase**: phase6+  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

AI Manager subsections exist, but there is no index page for system overview, status, and quick navigation.

**Current**: Users jump directly to subsection routes  
**Expected**: `/admin/ai_manager` dashboard with status, active missions, metrics, alerts, and quick actions

## Files to Edit

| File | Purpose |
|---|---|
| `galaxy_game/app/controllers/admin/ai_manager_controller.rb` | Add `index` action data loading |
| `galaxy_game/app/views/admin/ai_manager/index.html.erb` | Create landing/dashboard page |
| `galaxy_game/config/routes.rb` | Add index route |
| `galaxy_game/app/views/admin/dashboard/index.html.erb` | Update nav target to landing page |

## Acceptance Criteria

- [ ] `/admin/ai_manager` route works
- [ ] Landing page shows AI status, active missions, metrics, alerts
- [ ] Quick links to missions/planner/decisions/patterns/performance/testing
- [ ] Dashboard navigation points to AI landing page

## Commit Instructions

```bash
git add galaxy_game/app/controllers/admin/ai_manager_controller.rb galaxy_game/app/views/admin/ai_manager/index.html.erb galaxy_game/config/routes.rb galaxy_game/app/views/admin/dashboard/index.html.erb
git commit -m "feat: add AI Manager landing page with system overview and navigation"
```