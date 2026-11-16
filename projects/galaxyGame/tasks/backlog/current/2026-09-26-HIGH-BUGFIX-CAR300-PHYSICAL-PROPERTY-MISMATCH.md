---
status: backlog
priority: HIGH
type: bugfix
system_domain: DATA
mvp_alignment: OTHER
local_worker_safe: true
created: 2026-09-26
estimated_effort: 1-2 hours
depends_on: []
blocks: []
---

# TASK: CAR-300 Blueprint vs Operational-Data Physical-Property Mismatch

## Context

The CAR-300 normalization audit (2026-09-10, completed 2026-09-26) confirmed that
**CAR-300 is the reference implementation** for the canonical `unit_blueprint_v1.3`
robot schema. During that audit, a data-quality bug was discovered: CAR-300's
blueprint and its operational data file **disagree on physical properties**.

| Field | Blueprint (`_bp.json`) | Operational data (`_data.json`) |
|-------|------------------------|--------------------------------|
| length_m | 3.5 | 2.1 |
| width_m | 1.8 | 1.2 |
| height_m | 4.2 | 2.0 |
| empty_mass_kg | 4500.0 | 1200 |
| volume_m3 | 26.5 | 5.04 |

Files:
- `data/json-data/blueprints/units/robots/deployment/car_300_deployment_robot_mk1_bp.json`
- `data/json-data/operational_data/units/robots/deployment/car_300_deployment_robot_mk1_data.json`

## ⚠️ Priority Rationale

CAR-300 is the **reference implementation** of the v1.3 robot schema. If it was used
as a template/example when authoring other units or when AI-enhancing data
(`transform_component.rb` uses it-era conventions), this inconsistency may have been
**propagated** to other units. Before fixing CAR-300, grep for the same conflicting
values (e.g., `4500.0` mass with `2.1` length, or the two size sets) across
`data/json-data/blueprints/` and `data/json-data/operational_data/`.

Also note: `galaxy_game/app/models/concerns/has_mass_calculation.rb:66-67` falls back
to `operational_data_reference.physical_properties.mass_kg` — CAR-300's blueprint
`physical_properties` block has no `mass_kg` key, so verify which source the runtime
actually uses for CAR-300's mass and confirm the "correct" value matches it.

## Implementation Steps

1. Determine the source of truth for CAR-300's physical properties (design intent:
   "Heavy-duty modular humanoid robot", 4500 kg / 3.5×1.8×4.2 m in the blueprint
   reads as the intended spec; the operational data file looks like a stale/placeholder
   value set). Confirm with Tracy if ambiguous.
2. Grep for propagated copies of the mismatched values across blueprints + operational data.
3. Align the two files to the confirmed correct values.
4. Run robot-related specs (see completed audit task for the docker rspec command).

## Acceptance Criteria
- [ ] Source of truth determined and documented in the completion report
- [ ] Blueprint and operational data agree on all physical properties
- [ ] Propagation check performed; any other affected units listed (fix only if trivial, else file follow-up)
- [ ] No regressions in robot-related specs
- [ ] Synthesis report saved to `projects/galaxy_game/summaries/`

## Related
- Completed audit: `projects/galaxy_game/tasks/completed/2026-09/2026-09-10-MEDIUM-REFACTOR-CAR300-NORMALIZATION-AUDIT.md`
- Synthesis: `projects/galaxy_game/summaries/2026-09-10-REFACTOR-CAR300-NORMALIZATION-AUDIT.md` (§4)
- Decision note: `projects/galaxy_game/architecture/2026-09-25-ROBOT-BLUEPRINT-SCHEMA-V1.3-CANONICAL-DECISION.md`
