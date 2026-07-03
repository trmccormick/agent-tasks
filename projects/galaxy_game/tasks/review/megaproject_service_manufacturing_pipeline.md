---
name: "Implement Megaproject Service for Manufacturing"
priority: HIGH
phase: 8
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Implement Megaproject Service for Megastructure Manufacturing

**Priority**: HIGH  
**Phase**: 8  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

Megastructure blueprints (e.g., Lunar Space Elevator) use different cost schema and construction pipeline than unit blueprints. ManufacturingService doesn't support multi-phase builds, licensing, or megastructure requirements.

**Current**: Megastructures forced into unit blueprint schema  
**Expected**: Dedicated MegaProjectService for megastructure construction

## Files to Edit

| File | Purpose |
|---|---|
| `galaxy_game/app/services/mega_project_service.rb` | New megastructure manufacturing service |
| `galaxy_game/app/models/mega_project.rb` | New megaproject model |
| `galaxy_game/spec/services/mega_project_service_spec.rb` | Comprehensive megaproject specs |

## Acceptance Criteria

- [ ] MegaProjectService handles megastructure construction
- [ ] Multi-phase build system working
- [ ] Licensing and special requirements supported
- [ ] Orbital construction yards integrated
- [ ] Clear separation from unit manufacturing
- [ ] All megastructure specs passing

## Implementation Steps

1. Design MegaProjectService architecture
2. Implement multi-phase construction tracking
3. Add licensing and special requirements logic
4. Build progress tracking and notifications
5. Integrate with orbital infrastructure
6. Create comprehensive specs for all megastructure types

## Commit Instructions

```bash
git add galaxy_game/app/services/mega_project_service.rb galaxy_game/app/models/mega_project.rb galaxy_game/spec/services/mega_project_service_spec.rb
git commit -m "feat: add dedicated MegaProjectService for megastructure manufacturing"
```