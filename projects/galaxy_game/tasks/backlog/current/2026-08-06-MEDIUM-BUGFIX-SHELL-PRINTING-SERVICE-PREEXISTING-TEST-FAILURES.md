# Task File Template
# Copy this file, rename it to describe the task, fill in all sections.
# Delete these instruction comments before saving.
# Place in docs/new_agent/projects/{project name}/tasks/backlog/{YYYY-MM}/ or active/ as appropriate.
#
# FILENAME CONVENTION — mandatory for all task files:
#   YYYY-MM-DD-PRIORITY-TYPE-DESCRIPTIVE-NAME.md
#
#   YYYY-MM-DD  = date the task was created (not assigned, not completed)
#   PRIORITY    = CRITICAL | HIGH | MEDIUM | LOW
#   TYPE        = bug-fix | feature | refactor | architecture | data | documentation
#   NAME        = kebab-case, descriptive, no spaces
#
#   Examples:
#     2026-03-29-HIGH-REFACTOR-WORMHOLE-EXPANSION-SERVICE.md
#     2026-03-27-MEDIUM-FEATURE-FINANCIAL-TRANSACTION-MODEL.md
#     2026-03-30-LOW-DOCUMENTATION-BACKLOG-AUDIT-AND-RENAME.md
#
# DEPTH GUIDE — Claude/Perplexity = architecture and strategy only, never implementation-level guesses.
# Exact file paths, line numbers, and confirmed source tables should be filled by Qwen.

---
status: backlog
priority: MEDIUM
type: bug-fix
system_domain: MANUFACTURING
mvp_alignment: PHASE 9+ (surface settlement infrastructure)
local_worker_safe: true
phase_placement: current (non-blocking — fixes pre-existing test bugs exposed by 2026-08-03 task)
blocked_by: []
---

## TASK: Shell Printing Service — Fix Two Pre-existing Test Failures
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: bug-fix
**Created**: 2026-08-06
**Last Updated**: 2026-08-06

---

## Context

The 2026-08-03 task (`HIGH-BUGFIX-SHELL-PRINTING-THICKNESS-USes-CONSTRUCTION-TIME-SHIELDING`) un-skipped two tests that were hidden behind `xit`. Both tests now fail due to pre-existing bugs in the service code, not in the new thickness logic.

## Problem Statement

### Bug 1: `job.inflatable_tank` doesn't exist (line 133)
- **Test**: `shell_printing_service_spec.rb` line 133 calls `expect(job.target_unit).to eq(inflatable_tank)`
- **Issue**: The test was written expecting a `inflatable_tank` accessor on `ConstructionJob`, but the model only has `target_unit` (via `belongs_to :target_unit, foreign_key: 'inflatable_id'`) and `inflatable_tank_id` (accessor on `target_values`).
- **Fix**: Either add an `inflatable_tank` alias method to `ConstructionJob` that delegates to `target_unit`, or update the test to use `job.target_unit`.

### Bug 2: Materials composition not stored in `materials_consumed` (line 155)
- **Test**: `shell_printing_service_spec.rb` line 155 expects `job.materials_consumed['inert_waste']['composition']` to contain `{ 'SiO2' => 43.0, 'Al2O3' => 24.0 }`
- **Issue**: The composition data flow is broken — `ensure_materials_available` strips the `:missing` flag but the composition from the inventory item isn't being persisted into `target_values['materials_consumed']`. The test expects composition to be stored, but it's returning `{}`.
- **Fix**: Trace the data flow through `calculate_shell_materials` → `ensure_materials_available` → `consume_materials` → `create_shell_printing_job` and ensure composition is preserved in the stored `materials_consumed` hash.

## Files Involved

| File | Purpose |
|---|---|
| `galaxy_game/app/services/manufacturing/shell_printing_service.rb` | Fix data flow for composition storage (Bug 2) |
| `galaxy_game/app/models/construction_job.rb` | Add `inflatable_tank` alias if needed (Bug 1) |
| `galaxy_game/spec/services/manufacturing/shell_printing_service_spec.rb` | Un-skip the two xit tests once bugs are fixed |

## Implementation Steps

1. **Bug 1**: Add `def inflatable_tank; target_unit; end` to `ConstructionJob` as an alias for `target_unit`, OR update the spec to use `job.target_unit` (preferred — no model change needed).
2. **Bug 2**: Trace composition data flow in `ShellPrintingService`:
   - `calculate_shell_materials` builds materials hash with `composition` key
   - `ensure_materials_available` strips `:missing` flag but should preserve `composition`
   - `create_shell_printing_job` stores `materials_consumed` in `target_values`
   - Verify composition survives the full pipeline and is accessible via `job.materials_consumed['inert_waste']['composition']`
3. **Un-skip tests**: Change both `xit` back to `it` once bugs are fixed.
4. **Run spec**: Confirm 0 failures.

## Acceptance Criteria
- [ ] `job.target_unit` (or `inflatable_tank` alias) returns the correct inflatable tank
- [ ] `job.materials_consumed['inert_waste']['composition']` contains the source item's composition
- [ ] Both previously-skipped tests pass as regular `it` examples
- [ ] All 14 examples in shell_printing_service_spec.rb pass with 0 failures

## Stop Conditions
- Stop if composition data flow requires changes to shared inventory models beyond shell printing.
- Stop if the test expectations don't match the intended design — verify with task author.
