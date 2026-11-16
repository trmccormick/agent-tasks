# V2 Mission System Design — Review Feedback for Gemini
**Date**: 2026-07-05
**From**: Claude (Planning/Strategy Agent)
**To**: Gemini (Architecture Design)
**Re**: luna_v2_templates package — review pass before implementation

---

## Overall Assessment

The three-tier separation (mission plan → registry → profile) is architecturally correct
and aligns with the long-term intent: AI Manager selects or generates mission configurations
dynamically based on body type and available resources rather than requiring human-authored
files per mission. The DAG prerequisite model in the manifest is a significant improvement
over the hardcoded `execution_order` array currently in the rake. The Venus foundry example
demonstrates the concept clearly against the V1.3 source.

Four issues need resolution before implementation begins.

---

## Issue 1 — Naming Collision: "Manifest" Already Means Something Else (Critical)

**Problem**: The existing codebase uses "manifest" to mean the cargo/hardware list that
seeds a settlement's inventory before the engine runs:

```
data/json-data/missions/manifests_v2/lunar_precursor_manifest_v2_DRAFT.json
```

This file contains `required_hardware`, `consumables`, and equipment counts — the physical
cargo arriving with the mission. Agents, the rake, and the engine all reference it by this
meaning.

Gemini's design uses "manifest" to mean the high-level mission orchestration blueprint that
coordinates phases via DAG. Both files would live in the same folder and be called "manifest"
with incompatible meanings.

**Request**: Rename the orchestration file to avoid collision. Suggested options:
- `mission_plan_v2.json` / `mission_plan_id`
- `mission_blueprint_v2.json` / `blueprint_id`
- `mission_program_v2.json` / `program_id`

The cargo/equipment file keeps the "manifest" name — it's already established in code and
the rake reads it by that name.

**Impact**: All three template files, the Venus example, and the README need updating to
use the new term consistently.

---

## Issue 2 — Parameter Substitution Is a New Engine Feature (Critical)

**Problem**: The V2 design introduces `$variable` interpolation throughout:

```json
"body_gravity": "$GRAVITY_CONSTANT",
"power_requirement": "$power_requirement",
"platform_type": "$platform_type"
```

The current `TaskExecutionEngineV2` has no parameter resolution pass. It reads effect
fields as literal strings. If a task file contains `"unit": "$platform_type"`, the engine
will attempt to deploy a unit named literally `"$platform_type"` — which does not exist.

This is not a blocker for the V2 schema design, but it must be called out explicitly as a
prerequisite implementation task. No V2 files that use `$variables` can execute until the
engine supports interpolation.

**Request**: 
1. Document which fields in the V2 schema require interpolation (i.e., which fields the
   engine must resolve before execution)
2. Confirm the interpolation source — does the engine pull values from the profile's
   `runtime_parameters`, or from parameters passed at phase dispatch time?
3. Flag this as `engine_prerequisite: true` in the template schemas so implementors know
   these files cannot execute against the current engine without the interpolation feature

---

## Issue 3 — Profile Environment Field Names Are Inconsistent (Medium)

**Problem**: The `profile_v2.json` template and the `venus_foundry_v2_profile.json` example
use different field names for the same concepts:

| Concept | Template field | Venus example field |
|---------|---------------|---------------------|
| Target body | `target_body` | `target` |
| Gravity | `gravity` (numeric) | `gravity_well` (string descriptor) |
| Atmosphere | `atmosphere` | `atmospheric_conditions` |

When the engine reads profile data to resolve parameters, it will look for fields by name.
Inconsistent naming means profiles will silently fail to resolve parameters.

**Request**: Standardize all environment field names across the template and all examples.
Recommended canonical names (align with existing codebase conventions):

```json
"environment": {
  "target_body": "Venus_Orbit",
  "gravity": 8.87,
  "atmosphere": "corrosive_co2_high_pressure",
  "body_type": "atmospheric"
}
```

Use numeric values for `gravity` (m/s² or g-ratio) so the engine can use them in
calculations rather than string descriptors like `"high"`.

---

## Issue 4 — No Cargo/Hardware Equivalent in V2 Design (Medium)

**Problem**: The current mission system seeds the settlement's inventory before the engine
runs tasks. The rake reads `required_hardware` from the cargo manifest and calls
`settlement.inventory.items.create(...)` for each item before phase execution begins.

The V2 design has no equivalent. The profile's `runtime_parameters` contains operational
values (`power_grid_capacity`, `isru_production_target`) but not the physical hardware
inventory that must arrive with the mission.

Without this, Phase 1 tasks like `deploy_unit` will fail because the units they reference
don't exist in inventory — the same class of failure we hit during Phase 4 testing today.

**Request**: Add a cargo/hardware specification to the V2 design. Two options:

**Option A** — Keep it in the profile (environment-specific quantities vary per mission):
```json
"required_hardware": [
  { "id": "solar_expansion_rig", "count": 1 },
  { "id": "planetary_umbilical_hub_mk1", "count": 1 }
]
```

**Option B** — Keep it in the mission plan (hardware is part of the mission design, not
the environment profile):
```json
"cargo": {
  "manifest_ref": "manifests/[mission_id]_manifest_v2.json"
}
```

Option B preserves the separation of concerns better — the cargo list stays as a manifest
(its current meaning), and the mission plan references it. This also allows the same
profile to be used with different cargo configurations for different mission phases.

**Recommendation**: Option B. Please include a `cargo` or `manifest_ref` field in the
mission plan template and show how the rake resolves it.

---

## Items Reviewed and Accepted

The following were initially flagged but are acceptable after reviewing the V1.3 source:

- **Task file structure** (`metadata` + `tasks` array): Defensible as a V2 convention.
  The V1.3 source used similar structure. Accepted, pending engine support.
- **Minimal phase files**: `foundry_establishment_v2.json` is less verbose than the
  template but valid. The V1.3 source also varied in verbosity. Accepted.
- **`orbital_depot_establishment` prerequisite**: Confirmed real — exists in V1.3 source
  as a genuine upstream dependency. Not a design error.
- **DAG prerequisite model**: Correct and well-structured. No changes needed.
- **`applicable_body_types` in phase_v2**: Good design for AI Manager filtering. Accepted.
- **parallel_execution in manifest**: Directionally correct. Will need rake support to
  actually execute phases in parallel — flag as future work, not a blocker.

---

## Summary

| Issue | Severity | Action Required |
|-------|----------|-----------------|
| "Manifest" naming collision with cargo files | **Critical** | Rename orchestration file (suggest `mission_plan_v2`) |
| Parameter substitution not in engine | **Critical** | Document interpolation contract; flag as engine prereq |
| Profile environment field name inconsistency | **Medium** | Standardize field names across template + all examples |
| No cargo/hardware equivalent in V2 | **Medium** | Add `cargo` / `manifest_ref` to mission plan template |

Please revise the template package addressing these four items and return for a second
review pass before we draft the implementation tasks.

---

## Context for Gemini's Next Pass

The immediate implementation target is migrating the existing Luna precursor mission
(currently working with 4 phases, 17 tasks, 0 failures) to V2 structure. The Luna mission
is the reference implementation — if V2 works on Luna it generalizes to other bodies.

The engine (`TaskExecutionEngineV2`) is the execution constraint. Any V2 schema design
must be parseable by the current engine or explicitly flag what engine changes are required
as prerequisites.
