---
name: "Implement Planetary View Phase 1"
priority: MEDIUM
phase: phase5+
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Implement Planetary View Phase 1 — Monitor UI Upgrade

**Priority**: MEDIUM  
**Phase**: phase5+ (Early Game)  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

Monitor UI needs upgrade from basic overview to planetary view with 4K canvas resolution. Phase 1 focuses on renaming monitor → planetary, updating canvas dimensions, and validating rendering.

**Current**: Monitor UI uses basic overview rendering  
**Expected**: Planetary view with 4096x2048 canvas resolution and validated rendering

## Evidence of Incompleteness

```bash
grep -n "monitor\|planetary" galaxy_game/app/views/admin/celestial_bodies/*.erb 2>/dev/null | head -5
# Check if monitor.html.erb still uses old naming/conventions
```

## Files to Edit/Create

| File | Purpose | Key Section |
|------|---------|-------------|
| `galaxy_game/app/views/admin/celestial_bodies/planetary_view.html.erb` (rename from monitor.html.erb) | Planetary view UI | Canvas dimensions 4096x2048 |
| `galaxy_game/app/javascript/admin/planetary_view.js` (new) | Planetary view controller | Canvas rendering logic |
| `galaxy_game/spec/features/planetary_view_spec.rb` (new) | Feature tests | Canvas validation |

## Acceptance Criteria

- [ ] Monitor renamed to planetary (html.erb + controller + JS)
- [ ] Canvas resolution set to 4096x2048
- [ ] RSpec validation passes for planetary view rendering
- [ ] Documentation updated with PLANETARY_VIEW_INTENT.md
- [ ] Cuba/Florida pixel validation confirmed

## Implementation Steps

1. Rename monitor → planetary (html.erb + controller + JS)
2. Update canvas dimensions to 4096x2048
3. Implement RSpec validation for planetary view rendering
4. Update documentation with PLANETARY_VIEW_INTENT.md
5. Validate Cuba/Florida pixel accuracy

## Commit Instructions

```bash
git add galaxy_game/app/views/admin/celestial_bodies/planetary_view.html.erb galaxy_game/app/javascript/admin/planetary_view.js
git commit -m "feat: implement Planetary View Phase 1 with 4K canvas resolution"
```
