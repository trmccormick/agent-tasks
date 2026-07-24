# Synthesis: Fitting Service Inventory Parity Fix

**Date**: 2026-07-23
**Task**: 2026-04-01-HIGH-BUGFIX-FITTING-SERVICE-INVENTORY-PARITY.md

## Analysis

### Root Cause
`FittingService.fit!` creates a `check_inventory` lambda and passes it to `install_units`, but `install_modules` and `install_rigs` are called without this parameter. This means modules and rigs bypass inventory validation entirely.

### Current Code State (verified)
- `fitting_service.rb:16`: `install_units(target, fit_data['units'], result, dry_run, check_inventory)` ✅ has check_inventory
- `fitting_service.rb:20`: `install_modules(target, fit_data['modules'], result, dry_run)` ❌ missing check_inventory
- `fitting_service.rb:24`: `install_rigs(target, fit_data['rigs'], result, dry_run)` ❌ missing check_inventory
- `install_modules` signature (line ~68): `def self.install_modules(target, modules, result, dry_run)` — no check_inventory param
- `install_rigs` signature (line ~90): `def self.install_rigs(target, rigs, result, dry_run)` — no check_inventory param

### Test Coverage Gap
Current 4 specs only verify units get inventory-checked. No test exercises modules/rigs with missing inventory items.

## Plan

1. **fitting_service.rb**: Add `check_inventory` parameter to `install_modules` and `install_rigs`, add inventory guard before `add_module`/`add_rig` calls
2. **fitting_service_spec.rb**: Add 3 new test cases for modules/rigs inventory validation
3. **Verify**: Run spec suite, expect 7 examples, 0 failures

## Risks
- Shared concern methods (`target.add_module`, `target.add_rig`) may have their own checks — verified they don't, the service is the gatekeeper
- Nil inventory is intentional (skip check) — handled by lambda returning true when inventory.nil?
- Dry_run bypasses inventory for ALL types — no changes needed to dry_run path

## Ready to Apply: YES
