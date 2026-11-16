---
status: deprecated
priority: MEDIUM
type: architecture
system_domain: MATERIALS
mvp_alignment: phase8+
local_worker_safe: true
created: 2026-06-21
last_updated: 2026-07-30
deprecated_date: 2026-07-30
---

# TASK: Material Storage Classification — Architecture Design (DEPRECATED)

**Priority**: MEDIUM  
**Phase**: phase8+ (Cycler Arc)  
**Type**: architecture/design  
**Created**: 2026-06-21  
**Deprecated**: 2026-07-30  

## Deprecation Reason

This task is **superseded by the existing surface_storage system**. The referenced service (`DockingTransactionService`) no longer exists (merged into `UniversalDockingService`), and the proposed classification fields (`requires_enclosure`, `outdoor_eligible`) are not in the current material schema.

## Current State (2026-07-30)

### Outdoor eligibility is already handled
- **`surface_storage`** system exists and works — settlements can store outdoor-eligible materials on the planet surface without enclosure (unlimited capacity)
- Confirmed working: 6+ examples, 0 failures in recent testing
- `inventory.surface_storage` provides the mechanism for outdoor storage

### Material schema has evolved
- Current material JSON fields: `storage.stability`, `state_at_room_temp`, `handling.hazard_class`, `transport_category`
- No `requires_enclosure` or `outdoor_eligible` flag exists in the schema
- The classification logic would need to derive from existing fields if re-implemented

### Docking service renamed/merged
- **Old**: `DockingTransactionService` (referenced in this task) — **does not exist**
- **Current**: `UniversalDockingService` (in `ai_manager/` namespace) — handles universal docking between any entities

## What Replaced This Task

The surface_storage system already provides the outdoor storage capability this task was trying to design. No additional classification system is needed at this time.

If enclosure requirements become a formal feature later, they should be added to the material schema (`data/schemas/material_v1.6.schema.json`) and derived from `storage.stability` + `handling.hazard_class` rather than a separate flag.
