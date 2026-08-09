---
title: "Tighten special_case_name? Mixed* prefix to exact match 'Mixed Volatiles'"
status: completed
type: BUGFIX
priority: LOW
phase: phase5-luna-calibration
created: 2026-08-09
completed: 2026-08-09
author: M4 (executed), Claude (coordination review)
---

# Tighten special_case_name? Mixed* prefix to exact match 'Mixed Volatiles'

## Problem

During the Phase 5 TEU/PVE ISRU production task (`2026-08-09-HIGH-FEATURE-ADD-TEU-PVE-ISRU-PRODUCTION-LOGIC-TO-LUNA-OPERATIONS-SIMULATION.md`), an ISRU production fix added `name.start_with?("Mixed")` to `Item#special_case_name?` as a validation bypass for "Mixed Volatiles" inventory items.

The bare `"Mixed"` prefix is broader than needed — it would match any future `"Mixed X"` item name, creating an unintended validation bypass. Only `"Mixed Volatiles"` is the actual case.

## Architecture Gotchas

- `Item#special_case_name?` is a shared model method used across all Item creation paths
- The original change was a process violation: committed without required synthesis/approval step
- Fix is safe (narrowing, not widening) but still requires documentation per standing rules

## Minimal Handoff

**Target file**: `galaxy_game/app/models/item.rb` line ~310

**Change**: Replace `name.start_with?("Mixed")` with `name == "Mixed Volatiles"`

**Acceptance**: 
- luna:simulate_operations[50,ID] still produces correct ISRU chain
- luna_operations_simulation_service_spec.rb ISRU specs still pass (23 examples, 2 pre-existing failures)
- Full suite: 4646/172 pre-existing (unchanged)

## Resolution

**Commit**: `bda0f96d` in galaxyGame

```diff
- return true if name.start_with?("Mixed") # Skip for mixed material blends
+ return true if name == "Mixed Volatiles" # Skip for mixed material blends (exact match only)
```

**Why it mattered**: The broader prefix could silently bypass validation for any future `"Mixed X"` item, potentially allowing unregistered materials through the ISRU pipeline. Exact match prevents this while preserving the existing `"Mixed Volatiles"` case.

## Validation Results

| Test | Pass | Fail | Notes |
|------|------|------|-------|
| Full RSpec suite | 4474 | 172 | All pre-existing, unchanged |
| luna_operations_simulation_service_spec.rb (ISRU) | 21 | 2 | Pre-existing failures unrelated |
| luna:simulate_operations[50,1730] | — | — | O2 +1.575kg/day, Mixed Volatiles +0.05/day, He3 +0.0/day |

## Standing Lesson

Every fix — even small ones — goes through: draft → M4 stages as a proper TASK_TEMPLATE.md file → Tracy dispatches. Don't skip staging for "quick" fixes. If a fix's real root cause touches shared/global code (base model, concern, factory), produce Synthesis Report with explicit RISK statement before committing.
