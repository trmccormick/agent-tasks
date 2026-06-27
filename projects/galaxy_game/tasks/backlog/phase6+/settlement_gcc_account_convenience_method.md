---
name: "Add gcc_account Convenience Method"
priority: LOW
phase: 5
created: 2026-06-21
status: backlog
type: refactor
---

# TASK: Add GCC Account Convenience Method to BaseSettlement

**Priority**: LOW  
**Phase**: 5  
**Type**: refactor  
**Created**: 2026-06-21  

## Problem Statement

settlement.account is ambiguous and fragile with multiple currency accounts. Specs break when using settlement.account instead of proper find_or_create_for_entity_and_currency lookup.

**Current**: Fragile settlement.account usage throughout  
**Expected**: Consistent gcc_account convenience method

## Files to Edit

| File | Purpose |
|---|---|
| `galaxy_game/app/models/settlement/base_settlement.rb` | Add convenience method |

## Acceptance Criteria

- [ ] gcc_account convenience method added to BaseSettlement
- [ ] Method returns GCC Financial::Account for settlement
- [ ] Uses proper find_or_create_for_entity_and_currency
- [ ] Specs updated to use gcc_account
- [ ] Keep has_one :account for backwards compatibility

## Implementation Steps

1. Add gcc_account method to BaseSettlement
2. Update specs to use gcc_account instead of account
3. Verify all settlement tests still pass
4. Consider similar method for Player model
5. Document convenience method usage pattern

## Commit Instructions

```bash
git add galaxy_game/app/models/settlement/base_settlement.rb
git commit -m "refactor: add gcc_account convenience method to BaseSettlement"
```