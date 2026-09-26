---
status: backlog
priority: MEDIUM
type: refactor
system_domain: DATA
mvp_alignment: OTHER
local_worker_safe: true
created: 2026-09-26
estimated_effort: 2-4 hours
depends_on: []
blocks: []
---

# TASK: Migrate 13 Legacy Robot Blueprints Up to v1.3 Schema + Fix File Prefix

## Context

The CAR-300 normalization audit (completed 2026-09-26) established that
`unit_blueprint_v1.3.json` is the **canonical** robot blueprint standard (it is the
default template in `galaxy_game/tools/transform_component.rb:169,397` and is
documented in `docs/developer/JSON_DATA_GUIDE.md:20`). CAR-300 is the only robot that
conforms. The other 13 robots are on the legacy `unit_blueprint` (v1.2-era) convention.

**Decision note**: `projects/galaxy_game/architecture/2026-09-25-ROBOT-BLUEPRINT-SCHEMA-V1.3-CANONICAL-DECISION.md`

## Scope — the 13 legacy robots

All under `data/json-data/blueprints/units/robots/` EXCEPT
`deployment/car_300_deployment_robot_mk1_bp.json`:
- construction: acr_100, acr_200, ecr_300
- exploration: smr_500
- logistics: ltr_100
- maintenance: mrr_100, mrr_200, mrr_300
- life_support: har_100, har_200, har_300
- resource: hrv_400, rpr_200

## Required Changes (per robot)

1. **Schema version**: align to v1.3 conventions — `metadata.template_compliance: "unit_blueprint_v1.3"`, `metadata.version: "1.3"`, remove/normalize the legacy top-level `version` field per the canonical template (template has no top-level `version`).
2. **`operational_data_reference.file` prefix**: currently `units/robots/...` (does not match disk). All 14 robot data files live under `data/json-data/operational_data/units/robots/`. Fix to `operational_data/units/robots/...` to match the real disk path and the fleet-wide convention (35+ non-robot blueprints already use this prefix).
3. **Remove extra `operational_data_reference.physical_properties: {}`** if present (not in canonical template) — or confirm with Tracy whether to keep.

## Out of Scope
- Backfilling `operational_properties` (separate LOW task)
- CAR-300 physical-property mismatch (separate HIGH task)
- Do NOT touch CAR-300's blueprint — it is the reference implementation.

## Verification
- `grep -r '"file": "units/robots/' data/json-data/blueprints/` → zero results after fix
- Spot-check 2-3 migrated blueprints against `data/json-data/templates/unit_blueprint_v1.3.json`
- Run robot-related specs (see completed audit task for the docker rspec command)

## Acceptance Criteria
- [ ] All 13 robots conform to v1.3 schema conventions
- [ ] All 13 `operational_data_reference.file` values resolve to real disk paths
- [ ] No regressions in robot-related specs
- [ ] Synthesis report saved to `projects/galaxy_game/summaries/`

## Related
- Completed audit: `projects/galaxy_game/tasks/completed/2026-09/2026-09-10-MEDIUM-REFACTOR-CAR300-NORMALIZATION-AUDIT.md`
- Synthesis: `projects/galaxy_game/summaries/2026-09-10-REFACTOR-CAR300-NORMALIZATION-AUDIT.md` (§2, §3, §5)
