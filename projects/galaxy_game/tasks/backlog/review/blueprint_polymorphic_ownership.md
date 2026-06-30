---
name: "Blueprint Polymorphic Ownership"
priority: HIGH
phase: 8
created: 2026-06-21
status: backlog
type: refactor
---

# TASK: Implement Blueprint Polymorphic Ownership

**Priority**: HIGH  
**Phase**: 8  
**Type**: refactor  
**Created**: 2026-06-21  

## Problem Statement

Blueprints currently only support single owner type (Player via player_id). Need polymorphic ownership to support organizations, corporations, and settlements owning shared blueprints.

**Current**: Blueprint.player_id only  
**Expected**: Polymorphic owner (Player, Organization, Settlement)

## Files to Edit

| File | Purpose |
|---|---|
| `galaxy_game/app/models/blueprint.rb` | Add polymorphic owner |
| `galaxy_game/app/models/player.rb` | Update associations |
| `galaxy_game/app/services/manufacturing_service.rb` | Use polymorphic owner |
| Migrations | Add owner_type/owner_id columns |

## Acceptance Criteria

- [ ] Polymorphic owner_type/owner_id columns added
- [ ] Existing blueprints backfilled (Player owner type)
- [ ] Blueprint and owner models updated
- [ ] ManufacturingService uses polymorphic owner
- [ ] All specs updated
- [ ] player_id deprecated (kept for backwards compatibility)

## Implementation Steps

1. Create migration adding owner_type/owner_id to blueprints
2. Backfill existing blueprints as Player owners
3. Update Blueprint model with polymorphic owner
4. Update all owner-referencing code
5. Update all specs
6. Validate full migration before removing player_id

## Commit Instructions

```bash
git add galaxy_game/app/models/ galaxy_game/app/services/manufacturing_service.rb galaxy_game/db/migrate/
git commit -m "refactor: add polymorphic blueprint ownership (Player/Organization/Settlement)"
```