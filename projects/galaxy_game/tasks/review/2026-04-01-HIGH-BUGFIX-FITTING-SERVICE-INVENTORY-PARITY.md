# TASK_TEMPLATE: FittingService — Inventory Checking Parity

**Phase**: phase8
**Priority**: HIGH
**Type**: BUGFIX
**Status**: BACKLOG
**Created**: 2026-04-01
**Last Updated**: 2026-06-20
**Estimate**: 4 hours (diagnosis + fix + test rewrite)

---

## 🎯 Objective

Fix inventory validation inconsistency in `FittingService`. Currently `install_units` validates inventory before installation, but `install_modules` and `install_rigs` bypass inventory checks entirely. This causes test failures and allows fitting components that aren't in inventory.

---

## 📋 Problem Statement

| Component | Current Behavior | Expected Behavior |
|-----------|------------------|-------------------|
| **Units** | Checks `inventory.has_item?` before install | ✅ Works correctly |
| **Modules** | Calls `target.add_module` with no inventory check | Should verify inventory first |
| **Rigs** | Calls `target.add_rig` with no inventory check | Should verify inventory first |
| **Dry Run** | Bypasses inventory for all types | ✅ Correct (intended) |
| **Nil Inventory** | Should succeed (no check needed) | Fails when modules/rigs can't be added |

### Test Failures

| Test | Expected | Actual | Root Cause |
|------|----------|--------|------------|
| "fits all components from inventory" | success: true | success: false | Modules/rigs missing inventory check |
| "fits components without inventory if inventory is nil" | success: true | success: false | Modules/rigs can't be added when inventory nil |

---

## 🔍 Technical Analysis

### File Structure

[galaxy_game/app/services/fitting_service.rb](galaxy_game/app/services/fitting_service.rb):
```ruby
# Lines 1-30: Main fit! method
#   ✅ Passes check_inventory to install_units
#   ❌ Does NOT pass to install_modules or install_rigs

# Lines 35-62: install_units — HAS inventory checking
#   Line 37-38: Creates check_inventory lambda: `inventory.nil? || inventory.items.where(name: name).exists?`
#   Line 44-46: Uses check_inventory to validate before install
#   ✅ Correct

# Lines 73-95: install_modules — NO inventory checking
#   Line 83: Calls target.add_module(module_id) directly
#   ❌ Missing inventory validation

# Lines 98-120: install_rigs — NO inventory checking
#   Line 108: Calls target.add_rig(rig_id) directly
#   ❌ Missing inventory validation
```

### Root Cause

The `check_inventory` lambda (lines 6-8) is only passed to `install_units`. The other two methods need the same pattern:
- If `inventory` is nil → skip check (allows fitting without inventory)
- If `inventory` exists → verify item exists before calling concern method

---

## ✅ Acceptance Criteria

- [ ] `install_modules` receives `check_inventory` parameter
- [ ] `install_modules` validates each module in inventory before calling `target.add_module`
- [ ] `install_rigs` receives `check_inventory` parameter
- [ ] `install_rigs` validates each rig in inventory before calling `target.add_rig`
- [ ] Both methods handle `inventory: nil` case (allow fitting without check)
- [ ] Test: "fits all components from inventory" passes
- [ ] Test: "fits components without inventory if inventory is nil" passes
- [ ] Test: "returns errors if inventory is missing items" still passes
- [ ] No regressions in related craft fitting specs

---

## 🚧 Implementation Checklist

### Phase 1: Understanding
- [ ] Review current `install_units` inventory checking logic (lines 44-46)
- [ ] Understand `check_inventory` lambda purpose and interface
- [ ] Check what `target.add_module` and `target.add_rig` return (class or error string)
- [ ] Verify test setup creates modules/rigs in inventory

### Phase 2: Implementation
- [ ] Pass `check_inventory` parameter to `install_modules` (line 16)
- [ ] Pass `check_inventory` parameter to `install_rigs` (line 19)
- [ ] Add inventory validation in `install_modules` (before line 83)
  - Pattern: `check_inventory.call(module_id)` → if false, add error and skip
- [ ] Add inventory validation in `install_rigs` (before line 108)
  - Pattern: `check_inventory.call(rig_id)` → if false, add error and skip
- [ ] Ensure error messages match `install_units` pattern: `"Missing inventory item: #{id}"`

### Phase 3: Testing
- [ ] Run: `rspec spec/services/fitting_service_spec.rb --format progress`
- [ ] Verify all 4 tests pass
- [ ] Check for regressions: `rspec spec/services/fitting* --format progress`

### Phase 4: Commit
- [ ] Commit: `fix: FittingService — inventory validation parity for modules and rigs`

---

## 📂 Files to Edit

- [galaxy_game/app/services/fitting_service.rb](galaxy_game/app/services/fitting_service.rb)
  - Lines 16-20: Pass `check_inventory` to install_modules and install_rigs
  - Lines 73-95: Add inventory check before `target.add_module(module_id)` (line 83)
  - Lines 98-120: Add inventory check before `target.add_rig(rig_id)` (line 108)
  
- [galaxy_game/spec/services/fitting_service_spec.rb](galaxy_game/spec/services/fitting_service_spec.rb)
  - No changes needed (tests should pass as-is once fix is applied)

---

## 🔗 Dependencies

- **Blocked By**: None
- **Blocks**: Craft fitting pipeline (Phase 8+)
- **Related**: `2026-04-01-HIGH-BUG-FIX-STORAGE-CAPACITY-FILTER.md` (inventory-related)

---

## 📝 Completion Template

*Agent completes this section upon implementation*

**Status**: [NOT STARTED / IN PROGRESS / COMPLETED / BLOCKED]
**Completion Date**: [YYYY-MM-DD]
**Git Commit**: [commit hash or "not yet pushed"]
**Test Results**: [4 passed, 0 failed] or [X passed, Y failed, reason]
**Notes**: [Any blockers, assumption verification]
