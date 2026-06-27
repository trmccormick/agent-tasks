# TASK_TEMPLATE: ManufacturingPipelineE2E — thermal_extraction Interface Fix

**Codebase Status (2026-06-22)**: Task description references lines 320, 560, 561, 609 for thermal_extraction calls, but grep finds only 1 match at line 38 (data structure, not method call). Manufacturing_pipeline_e2e_spec.rb shows 3 examples, 0 failures, 3 pending (tests skipped with xit at lines 281, 552, 601 — different line numbers than task describes). Task description appears stale; either bug was already fixed or line numbers have drifted. Requires detailed review to confirm actual status before work.

**Phase**: phase5+
**Priority**: MEDIUM
**Type**: BUGFIX
**Status**: BACKLOG
**Created**: 2026-04-04
**Last Updated**: 2026-06-21
**Estimate**: 2 hours (find 4 call sites, replace with correct interface)

---

## 🎯 Objective

Fix integration spec calling invented `thermal_extraction` method. Should use real `process(unit, material, amount)` interface instead.

---

## 📋 Problem

- **Error**: NoMethodError `thermal_extraction` method doesn't exist
- **Root Cause**: Spec invented a method instead of using real `process` interface
- **Call Sites**: 4 locations in `manufacturing_pipeline_e2e_spec.rb` (lines 320, 560, 561, 609)
- **Impact**: E2E spec fails, but not blocking Luna mission test

---

## ✅ Acceptance Criteria

- [ ] All 4 call sites replaced with `process(unit, input_material, input_amount)` pattern
- [ ] Spec runs without NoMethodError
- [ ] All assertions still valid after interface replacement

---

## 📂 Files

- [galaxy_game/spec/integration/manufacturing_pipeline_e2e_spec.rb](galaxy_game/spec/integration/manufacturing_pipeline_e2e_spec.rb)
  - Lines 320, 560, 561, 609: Replace method calls
  
- Reference: [galaxy_game/app/services/manufacturing/material_processing_service.rb](galaxy_game/app/services/manufacturing/material_processing_service.rb)
  - Real interface: `def process(unit, input_material, input_amount)`

---

## 📝 Completion Template

**Status**: [NOT STARTED]
