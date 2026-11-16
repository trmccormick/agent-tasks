[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase6+/remove_obsolete_poro_storage_classes.md]
[RATIONALE: UI/dashboard cosmetic, investigation task, or obsolete code cleanup — not Luna sim prerequisite]
---
name: "Remove Obsolete PORO Storage Classes"
priority: LOW
phase: 5
created: 2026-06-21
status: backlog
type: cleanup
---

# TASK: Remove Obsolete PORO Storage Classes

**Priority**: LOW  
**Phase**: 5  
**Type**: cleanup  
**Created**: 2026-06-21  

## Problem Statement

Storage::BaseStorage and subclasses are legacy PORO objects predating the Inventory + Units::BaseUnit pattern. No production code references them, only their own specs.

**Current**: Dead code taking up space  
**Expected**: Removed, replaced by active Inventory AR system

## Files to Delete

- `app/models/storage/base_storage.rb`
- `app/models/storage/gas_storage.rb`
- `app/models/storage/liquid_storage.rb`
- `app/models/storage/solid_storage.rb`
- `app/models/storage/energy_storage.rb`
- `spec/models/storage/base_storage_spec.rb`
- `spec/models/storage/solid_storage_spec.rb`
- `spec/models/storage/gas_storage_spec.rb`

## Files to Keep

- `app/models/inventory.rb` (active AR system)
- `app/models/storage/surface_storage.rb`
- `app/models/storage/material_pile.rb`
- `app/models/storage/storage_manager.rb`

## Acceptance Criteria

- [ ] All obsolete storage classes deleted
- [ ] No production code relies on deleted classes
- [ ] Inventory AR system working
- [ ] All remaining storage specs pass
- [ ] ~6-9 failures from storage specs eliminated

## Implementation Steps

1. Delete all obsolete PORO storage files
2. Run inventory specs to verify Inventory works
3. Run full test suite
4. Confirm 6-9 failures eliminated
5. Document rationale in commit

## Commit Instructions

```bash
git add galaxy_game/spec/models/
git commit -m "cleanup: remove obsolete PORO storage classes (replaced by Inventory)"
```