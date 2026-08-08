---
status: backlog
priority: LOW
type: design
system_domain: UNITS | CRAFT | HLT
mvp_alignment: NOT_MVP_CRITICAL
local_worker_safe: false
created: 2026-06-19
---

# TASK: Design Drone Bay Operational Data — Robot Deployment/Recharge as Its Own Unit

**Status**: BACKLOG
**Priority**: LOW
**Type**: design
**Created**: 2026-06-19

---

## Context

`drone_bay_heavy_v1_bp.json` already exists as a real, intentional concept
blueprint — not stale, not corrupted, just pre-implementation. It defines
a "Heavy Drone Bay Module": housing, refueling, and maintenance for
heavy-lift construction drones, fitted to the Heavy Lift Transport (HLT)
for deployment of robots. It references `operational_data_reference.file:
"drone_bay_ops.json"`, which does not exist yet.

This connects to an existing architectural question: `HeavyLander`
(`app/models/craft/transport/heavy_lander.rb`) currently implements
`charging_ports`, `dock_robot_for_charge`, and `release_robot_from_charge`
directly on the craft itself. Per design intent confirmed 2026-06-19,
robot recharge/dock/deploy was always meant to be its own dedicated unit
(a charging-station / drone-bay unit), not a capability living directly
on HeavyLander. The drone bay blueprint is the concrete shape of that
intended unit.

This is explicitly NOT MVP-critical — Luna's precursor sequence (see
`2026-06-19-HIGH-VERIFICATION-LUNA-PRECURSOR-V2-SEQUENCE-RECONCILIATION.md`)
does not depend on this. This is a design/scoping task for later, once
Luna MVP work has more headroom.

---

## Acceptance Criteria

- [ ] Decide whether `drone_bay_heavy_v1_bp.json`'s blueprint shape
  (max 4 drones, `refuel_type: slag_vapor`, maintenance tools list) is
  still correct, or needs adjustment
- [ ] Write `drone_bay_ops.json` (the missing `operational_data_reference`
  file) with real operational properties matching the existing
  `unit_operational_data` schema pattern (see `inflatable_gas_storage_data.json`
  or HLT variant files for schema reference)
- [ ] Decide whether `HeavyLander`'s existing `charging_ports` /
  `dock_robot_for_charge` / `release_robot_from_charge` should migrate to
  this new drone bay unit, be deprecated in place, or coexist
  (e.g. HeavyLander keeps a minimal/emergency charging capability, drone
  bay is the primary/intended mechanism)
- [ ] If migrating: identify all current callers of the HeavyLander
  charging methods before changing anything
- [ ] Confirm `port_type: "standard_industrial_port_v1"` compatibility
  tagging is consistent with how other unit-to-craft compatibility is
  expressed elsewhere (vs. the generic count-based port system used for
  most unit/craft connections — confirmed 2026-06-19 that most ports are
  generic typed counts, not named; confirm whether industrial ports are a
  deliberate exception or should follow the same generic model)

---

## Stop Conditions — escalate to user immediately if:
- Migrating charging logic off HeavyLander would break any currently
  working precursor-mission functionality (check against
  `lunar_precursor.json`'s CAR-300 robot deployment tasks specifically)
- Real `slag_vapor` refueling mechanic doesn't have an existing equivalent
  elsewhere in the codebase to model from

---

## Dependencies
**Blocked by**: none (concept-stage, can be scoped independently)
**Blocks**: nothing currently — not on Luna MVP critical path
**Related tasks**: none yet

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:

### What was changed
-

### Issues discovered
-

### Follow-up tasks needed
-

### Lessons learned
-
