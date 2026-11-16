---
status: not-started
priority: HIGH
type: feature
created: 2026-06-22
last-updated: 2026-06-22
phase: 6+
---

# Worldhouse Maintenance Service

## Context
Worldhouse segments experience environmental stress and material fatigue. The maintenance system simulates periodic maintenance events, manages repairs, and tracks operational readiness. This service orchestrates maintenance event generation, repair execution, and state updates.

**Reference Models**:
- [worldhouse.rb](galaxy_game/app/models/structures/worldhouse.rb) — main worldhouse model
- [worldhouse_segment.rb](galaxy_game/app/models/structures/worldhouse_segment.rb) — segment with status tracking

## Problem Statement
Worldhouse segments need continuous maintenance to remain operational. Without a maintenance event system, segments would persist indefinitely without degradation. This task implements event simulation and repair mechanics.

## Scope
- Periodic maintenance event generation (scheduled or random)
- Event severity calculation based on segment age, materials, environmental conditions
- Repair action execution (consumes resources, takes time)
- Maintenance state transitions (`operational` → `needs_maintenance` → `under_repair` → `operational`)
- Integration with construction and failure analysis systems
- Full RSpec coverage

## Target Files
- `app/services/worldhouse_maintenance.rb` (NEW)
- `app/models/structures/worldhouse.rb` (modify to track maintenance history)
- `app/models/structures/worldhouse_segment.rb` (add maintenance fields: `last_maintained_at`, `maintenance_due_at`)
- `spec/services/worldhouse_maintenance_spec.rb` (NEW)
- `data/schemas/worldhouse_state.schema.json` (extend with maintenance event schema)

## Acceptance Criteria
- [ ] Maintenance event generation (scheduled + random)
- [ ] Severity calculation logic (age, materials, environmental factors)
- [ ] Repair action execution with resource consumption
- [ ] State transitions fully covered
- [ ] Integration with failure analysis (failure events trigger immediate maintenance)
- [ ] RSpec: >90% coverage for all maintenance paths

## Implementation Steps
1. Define maintenance event types as enum
2. Implement `WorldhouseMaintenanceService` with:
   - `generate_event(segment)` → creates maintenance event with severity
   - `calculate_repair_duration(event, available_resources)` → returns days
   - `execute_repair(event, resources)` → updates segment state
   - `recalculate_maintenance_schedule(worldhouse)` → updates all segments' `maintenance_due_at`
3. Add maintenance tracking fields to model
4. Hook into construction system (newly enclosed segments get first maintenance date)
5. Write comprehensive RSpec covering all event types and repair scenarios

## Dependencies
- Requires BEFORE: `2026-06-22-HIGH-FEATURE-WORLDHOUSE-FAILURE-ANALYSIS-SERVICE.md` (must analyze failures before creating maintenance events)
- Requires: Material/resource lookup for repair consumption
- Requires: Time simulation system for scheduling

## Notes
- Maintenance events should be queryable for UI dashboard
- Repair state should be persisted (partial repairs possible)
- Maintenance due dates should propagate to settlement level (affects overall settlement health)
- Consider environmental conditions (dust storms, extreme temperatures) affecting maintenance interval
