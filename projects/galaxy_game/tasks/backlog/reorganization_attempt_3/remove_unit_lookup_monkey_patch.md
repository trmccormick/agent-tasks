---
name: "Remove UnitLookupService Monkey-Patch from Tests"
priority: HIGH
phase: 5
created: 2026-06-21
status: backlog
type: bugfix
---

# TASK: Remove UnitLookupService Monkey-Patch Test Pollution

**Priority**: HIGH  
**Phase**: 5  
**Type**: bugfix  
**Created**: 2026-06-21  

## Problem Statement

`spec/models/concerns/has_units_spec.rb` permanently monkey-patches UnitLookupService at file load time (lines 20-48), breaking ~14 tests in unit_lookup_service_spec.rb when tests run in full suite. Patch never resets and pollutes test state.

**Current**: Monkey-patch breaks subsequent specs  
**Expected**: Properly scoped stubs only (already exist in before block)

## Files to Edit

| File | Purpose |
|---|---|
| `galaxy_game/spec/models/concerns/has_units_spec.rb` | Remove monkey-patch block (lines 20-48) |

## Acceptance Criteria

- [ ] Monkey-patch block removed from has_units_spec.rb
- [ ] Existing before(:each) stubs at line 254 handle same units
- [ ] No test state pollution
- [ ] All unit_lookup_service_spec tests pass
- [ ] Has_units specs still pass

## Implementation Steps

1. Remove module Lookup / class UnitLookupService block (lines 20-48)
2. Verify before(:each) stubs at line 254 cover needed units
3. Run has_units_spec to verify still passing
4. Run full test suite to verify unit_lookup_service_spec passes
5. Confirm no other specs depend on monkey-patch

## Commit Instructions

```bash
git add galaxy_game/spec/models/concerns/has_units_spec.rb
git commit -m "fix: remove monkey-patch from has_units_spec test pollution"
```