# DECISION NOTE — Robot Blueprint Schema Standard: v1.3 is Canonical

**Date**: 2026-09-25
**Task**: `2026-09-10-MEDIUM-REFACTOR-CAR300-NORMALIZATION-AUDIT.md`
**Status**: DECISION RECORDED (pending Tracy's ratification of the follow-up migration scope)

---

## Decision

**`unit_blueprint_v1.3.json` is the canonical robot blueprint standard.** CAR-300
(`car_300_deployment_robot_mk1`) is the reference implementation that conforms to it.

The other 13 robots are on the **legacy** `unit_blueprint` (v1.2-era) convention and
should be migrated **up** to v1.3 — not the other way around.

## Rationale (evidence)

1. **v1.3 is the default template in codebase tooling.**
   - `galaxy_game/tools/transform_component.rb:169` — `load_template_for` returns
     `unit_blueprint_v1.3.json` for `unit` blueprints.
   - `galaxy_game/tools/transform_component.rb:397` — AI enhancement path also defaults
     to `unit_blueprint_v1.3.json`.
2. **v1.3 is documented as the current standard.**
   - `docs/developer/JSON_DATA_GUIDE.md:20` — "Use the latest `unit_blueprint` template
     (see `templates/unit_blueprint_v1.3.json`)".
   - `docs/architecture/operations/wh-expansion.md:1219,1224,1275` — new units specified
     to use `unit_blueprint_v1.3.json`.
3. **CAR-300's `operational_data/` path prefix is correct.** All 14 robot operational
   data files live under `data/json-data/operational_data/units/robots/`. CAR-300's
   `operational_data_reference.file` matches disk; the other 13 robots' references
   (missing the `operational_data/` prefix) do not. 35+ non-robot blueprints use the
   `operational_data/` prefix — CAR-300 follows the fleet-wide majority.
4. **The `.file` reference is not functionally loaded.** `catalog_service.rb` globs the
   `operational_data/` directory directly; no code reads `operational_data_reference.file`.
   The 13 robots' broken references are a latent data inconsistency, not a runtime bug.

## What this means for follow-up work (for Tracy to prioritize)

- **Do NOT downgrade CAR-300.** It is the conformant reference.
- **Task A (revised)**: Migrate the 13 legacy robots **up** to v1.3 and fix their
  `operational_data_reference.file` to include the `operational_data/` prefix.
- **Task B**: Define the `operational_properties` contract and backfill for the 13 robots
  (CAR-300 already has real values as the model).
- **Task C (new)**: Resolve the CAR-300 blueprint-vs-operational-data physical-properties
  discrepancy (blueprint: 3.5×1.8×4.2 m / 4500 kg; operational data: 2.1×1.2×2.0 m / 1200 kg).

## Related

- Synthesis report: `projects/galaxy_game/summaries/2026-09-10-REFACTOR-CAR300-NORMALIZATION-AUDIT.md`
- Prior audit: `projects/galaxy_game/summaries/2026-09-10-LUNA-BLUEPRINT-OPERATIONAL-DATA-CONTRACT-AUDIT.md`
