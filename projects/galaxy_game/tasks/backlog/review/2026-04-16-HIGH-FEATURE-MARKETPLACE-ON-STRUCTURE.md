---
name: "Marketplace Association on BaseStructure"
priority: HIGH
phase: phase8
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Add Marketplace Association to BaseStructure

**Priority**: HIGH  
**Phase**: phase8 (Cycler Arc)  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

`Market::Marketplace` currently belongs_to `Settlement::BaseSettlement` only. Orbital structures (depots, stations) are not settlements — they are `Structures::BaseStructure` instances owned by a player, corporation, or AI Manager. Without a marketplace on the structure, craft docking at an orbital structure have no local order book to transact against.

**Current**: Marketplace only belongs_to settlement  
**Expected**: Marketplace can belong to either a settlement OR a structure; Structure gets `has_one :marketplace`

## Evidence of Incompleteness

```bash
grep -n "belongs_to" galaxy_game/app/models/market/marketplace.rb | head -3
# Output: line 6: belongs_to :settlement, class_name: 'Settlement::BaseSettlement'
# No structure association exists
```

## Files to Edit

| File | Purpose | Key Section |
|------|---------|-------------|
| `galaxy_game/app/models/market/marketplace.rb` | Market order book | Line 6 (belongs_to) |
| `galaxy_game/app/models/structures/base_structure.rb` | Structure base class | associations block |

## Acceptance Criteria

- [ ] `Market::Marketplace` supports both settlement and structure as owners (polymorphic or optional association)
- [ ] `BaseStructure` has `has_one :marketplace` association
- [ ] Marketplace can be created on orbital structures without errors
- [ ] Existing marketplace functionality on settlements continues to work
- [ ] Tests verify marketplace creation on both settlement and structure

## Implementation Steps

1. Update `Market::Marketplace` to support polymorphic ownership (settlement or structure)
2. Add `has_one :marketplace` to `BaseStructure`
3. Create migration if needed for polymorphic association columns
4. Update tests to verify marketplace on structures works correctly

## Commit Instructions

```bash
git add galaxy_game/app/models/market/marketplace.rb galaxy_game/app/models/structures/base_structure.rb
git commit -m "feat: add marketplace association to BaseStructure for orbital depots"
```
