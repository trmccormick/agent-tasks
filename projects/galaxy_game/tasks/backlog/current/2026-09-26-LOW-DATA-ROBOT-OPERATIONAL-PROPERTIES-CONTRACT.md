---
status: backlog
priority: LOW
type: data
system_domain: DATA
mvp_alignment: OTHER
local_worker_safe: true
created: 2026-09-26
estimated_effort: 2-4 hours
depends_on: []
blocks: []
---

# TASK: Define operational_properties Contract + Backfill All 14 Robots

## Context

The canonical `unit_blueprint_v1.3.json` template defines
`operational_data_reference.operational_properties` as a `{}` placeholder. CAR-300 is
the only robot with real values:

```json
"operational_properties": {
  "power_consumption_kw": 40,
  "max_payload_kg": 500,
  "operational_range_km": 50,
  "battery_life_hours": 72,
  "autonomy_level": "high"
}
```

The other 13 robots have `{}` — deployable robots with zero operational data means
consumers can't do anything meaningful with them. This is a genuine data gap, not just
a structural one.

## Implementation Steps

1. **Define the contract**: which fields are required vs. optional in
   `operational_data_reference.operational_properties` for deployable robots.
   Use CAR-300's five fields as the starting model; decide per-robot applicability
   (e.g., `max_payload_kg` may not apply to a greenhouse monitor).
2. Document the contract (candidate location: `docs/developer/JSON_DATA_GUIDE.md` or
   a dedicated robot-data contract doc — confirm with Tracy).
3. Backfill all 13 legacy robots with values consistent with their existing
   operational data files (`data/json-data/operational_data/units/robots/...`) and
   blueprint `production_data`/`physical_properties`.
4. Verify CAR-300's values still conform to the new contract.

## Out of Scope
- Schema migration of the 13 robots (separate MEDIUM task)
- CAR-300 physical-property mismatch (separate HIGH task)

## Acceptance Criteria
- [ ] Contract defined and documented (required vs. optional fields)
- [ ] All 14 robots have populated `operational_properties` conforming to the contract
- [ ] Values cross-checked against each robot's operational data file
- [ ] No regressions in robot-related specs
- [ ] Synthesis report saved to `projects/galaxy_game/summaries/`

## Related
- Completed audit: `projects/galaxy_game/tasks/completed/2026-09/2026-09-10-MEDIUM-REFACTOR-CAR300-NORMALIZATION-AUDIT.md`
- Synthesis: `projects/galaxy_game/summaries/2026-09-10-REFACTOR-CAR300-NORMALIZATION-AUDIT.md` (§5)
- Decision note: `projects/galaxy_game/architecture/2026-09-25-ROBOT-BLUEPRINT-SCHEMA-V1.3-CANONICAL-DECISION.md`
