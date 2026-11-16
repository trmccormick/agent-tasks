[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase6+/add_solar_system_monitor_route.md]
[RATIONALE: UI/dashboard cosmetic, investigation task, or obsolete code cleanup — not Luna sim prerequisite]
---
name: "Add Solar System Monitor Route"
priority: HIGH
phase: phase6+
created: 2026-06-21
status: backlog
type: bug-fix
---

# TASK: Add Monitor Route and Action for Admin Solar Systems

**Priority**: HIGH  
**Phase**: phase6+  
**Type**: bug-fix  
**Created**: 2026-06-21  

## Problem Statement

Admin dashboard MONITOR button for solar systems links to `/admin/solar_systems/:id/monitor`, but route/action/view are missing, causing routing errors.

**Current**: Broken monitor navigation for solar systems  
**Expected**: Working monitor route, controller action, and page under admin namespace

## Files to Edit

| File | Purpose |
|---|---|
| `galaxy_game/config/routes.rb` | Add `member { get :monitor }` for admin solar systems |
| `galaxy_game/app/controllers/admin/solar_systems_controller.rb` | Add `monitor` action |
| `galaxy_game/app/views/admin/solar_systems/monitor.html.erb` | Create monitor UI |

## Acceptance Criteria

- [ ] Clicking MONITOR in admin dashboard opens solar system monitor page
- [ ] No routing errors
- [ ] Monitor view loads system overview and body status data
- [ ] UX consistent with existing admin monitor pages

## Implementation Steps

1. Add route in admin namespace for solar_system monitor member action
2. Implement `monitor` action with eager loading (`galaxy`, `stars`, `celestial_bodies`)
3. Create monitor template with summary + controls
4. Validate dashboard link and render path

## Commit Instructions

```bash
git add galaxy_game/config/routes.rb galaxy_game/app/controllers/admin/solar_systems_controller.rb galaxy_game/app/views/admin/solar_systems/monitor.html.erb
git commit -m "fix: add admin solar system monitor route, action, and view"
```