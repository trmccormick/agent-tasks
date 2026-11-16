---
title: "Fix CORE_CONCEPT_MAP.md — correct manufacturing chain owner"
date: 2026-08-05
type: DOCUMENTATION
priority: LOW
status: active
phase: phase9+
assigned_to: Qwen
---

# ⚡ Minimal Handoff

Update one line in `CORE_CONCEPT_MAP.md` to correct the manufacturing chain entry point from dead services to the actual live service.

---

## Context

The manufacturing diagnosis confirmed that `CORE_CONCEPT_MAP.md` (2026-07-16) has an incorrect "Likely owner" claim: it lists `Manufacturing::Service` + `Manufacturing::ProductionService` + `Manufacturing::AssemblyService` as the manufacturing chain owners. All three are dead/stub with zero production callers.

The real entry point is `ManufacturingService` (top-level, no namespace), which is called by `MarketStabilizationService.produce_item_for_market`.

---

## Target File

`docs/architecture/CORE_CONCEPT_MAP.md` (or wherever it lives in the docs tree — search for "Likely owner" + "Manufacturing::Service" to locate)

## What to Change

Find the line that says something like:
```
Likely owner: Manufacturing::Service + Manufacturing::ProductionService + Manufacturing::AssemblyService
```

Replace with:
```
Owner: ManufacturingService (top-level, no namespace) — called by MarketStabilizationService.produce_item_for_market
```

## Stop Conditions

- The corrected line is in place
- No other lines in the file reference the dead services as active owners
- Commit message explains the correction with evidence

## Completion Report Template

1. File path modified
2. Old text → new text (exact diff)
3. Any other stale references found and fixed

---

**Status**: Ready for dispatch — doc-only task, no code changes, low risk.
