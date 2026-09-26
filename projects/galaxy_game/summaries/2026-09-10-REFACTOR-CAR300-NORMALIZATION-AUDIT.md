# Synthesis Report — CAR-300 Schema + Path Prefix Normalization Audit

**Task**: `2026-09-10-MEDIUM-REFACTOR-CAR300-NORMALIZATION-AUDIT.md`
**Date**: 2026-09-25
**Agent**: Implementation Agent
**Status**: AUDIT COMPLETE — **STOP CONDITION TRIGGERED** (see §6)

---

## 1. Executive Summary

The task premise — that CAR-300 is an outlier that should be normalized **down** to match the other 13 robots — is **inverted by the evidence**.

- **CAR-300's v1.3 schema is INTENTIONAL.** It is the only robot that conforms to the canonical `unit_blueprint_v1.3.json` template, which is the **default template** used by the codebase's own generation tooling.
- **CAR-300's `operational_data/` path prefix is CORRECT.** It matches where the file actually lives on disk. The other 13 robots' references (without the prefix) are the ones that are **wrong** — but they are harmless because no loader reads that field.
- **No code change is required.** The `operational_data_reference.file` field is not consumed by any loader; the catalog service globs the `operational_data/` directory directly.

Per the task's own Stop Conditions, this is a case to **escalate, not auto-fix**.

---

## 2. Finding A — v1.3 Schema is Intentional (CAR-300 is the conformant one)

### Evidence

| Source | What it shows |
|--------|---------------|
| `galaxy_game/tools/transform_component.rb:169` | `load_template_for` returns **`unit_blueprint_v1.3.json`** as the default blueprint template for `unit` components |
| `galaxy_game/tools/transform_component.rb:397` | AI enhancement path also defaults to **`unit_blueprint_v1.3.json`** |
| `docs/developer/JSON_DATA_GUIDE.md:20` | "Use the latest `unit_blueprint` template (see `templates/unit_blueprint_v1.3.json`)" |
| `docs/architecture/operations/wh-expansion.md:1219,1224,1275` | New units explicitly specified to use **`unit_blueprint_v1.3.json`** |
| `docs/reference/DESIGN_INTENT_ART_BIBLE_BLUEPRINT_VISUALIZATION.md:129` | Treats `unit_blueprint_v1.3` as the established template-compliance concept |

### Conclusion

v1.3 is the **current canonical standard**, not an aspirational future schema. CAR-300 is the **only** robot that actually conforms to it. The other 13 robots are on the **older** `unit_blueprint` (v1.2-era) convention.

**CAR-300's "deviations" from the canonical template are actually the fields the canonical template expects or tolerates:**
- `metadata.template_compliance: "unit_blueprint_v1.3"` — correct self-declaration
- `metadata.designation` / `metadata.mk_version` — extra metadata, not present in the template but not forbidden
- `operational_data_reference.operational_properties` **populated** — the template has a `{}` placeholder; CAR-300 fills it with real values (power_consumption_kw, max_payload_kg, operational_range_km, battery_life_hours, autonomy_level). This is the **intended** use of the field.

> ⚠️ **Do NOT downgrade CAR-300 to v1.2.** That would move it *away* from the canonical standard and *toward* the legacy convention.

---

## 3. Finding B — `operational_data/` Prefix is Correct (CAR-300 is right, the other 13 are wrong)

### Evidence

**On-disk location of ALL 14 robot operational data files** (verified via `find`):
```
data/json-data/operational_data/units/robots/deployment/car_300_deployment_robot_mk1_data.json
data/json-data/operational_data/units/robots/construction/acr_100_space_constructor_mk1_data.json
... (all 14 live under operational_data/units/robots/)
```
There is **no** `data/json-data/units/robots/` directory at all.

**Blueprint `operational_data_reference.file` values:**
- CAR-300: `operational_data/units/robots/deployment/car_300_..._data.json` → **matches disk** ✅
- Other 13: `units/robots/.../..._data.json` → **does NOT match disk** ❌ (missing `operational_data/` prefix)

**Broader convention:** 35+ non-robot blueprints (sensors, propulsion, storage, habitats, fabricators, etc.) **all** use the `operational_data/` prefix. CAR-300 follows the fleet-wide majority convention; the other 13 robots are the minority.

### Loader behavior (GOTCHA 2 resolved)

The `operational_data_reference.file` field is **not read by any loader code.** Verified:
- `grep` for `operational_data_reference` across `**/*.rb` → only 5 hits, all reading `operational_data_reference.physical_properties.mass_kg` (a mass fallback), **none** read the `.file` field.
- `galaxy_game/app/services/catalog_service.rb:94-100` loads operational data by **globbing** `base_path.join('operational_data')` directly — it never consults the blueprint's `.file` reference.

**Conclusion:** The `.file` field is a **documentation/cross-reference hint**, not a functional path. The other 13 robots' missing prefix is a **latent data inconsistency** (their reference would 404 if ever resolved), but it causes **no runtime breakage** because nothing resolves it.

> ⚠️ **Do NOT strip the prefix from CAR-300.** That would make its reference *wrong* (pointing to a non-existent path) to match the 13 broken references.

---

## 4. Finding C — Genuine Data Inconsistency Inside CAR-300 (new, not in task)

CAR-300's **blueprint** and its **operational data file** disagree on physical properties:

| Field | Blueprint (`_bp.json`) | Operational data (`_data.json`) |
|-------|------------------------|--------------------------------|
| length_m | 3.5 | 2.1 |
| width_m | 1.8 | 1.2 |
| height_m | 4.2 | 2.0 |
| empty_mass_kg | 4500.0 | 1200 |
| volume_m3 | 26.5 | 5.04 |

This is a real data-quality gap (which is the source of truth for mass/size?). It is **out of scope** for this audit task but should be flagged to Tracy. Note `has_mass_calculation.rb:66-67` reads `operational_data_reference.physical_properties.mass_kg` — CAR-300's blueprint `physical_properties` block uses `length/width/height` (no `mass_kg`), so the mass fallback would miss.

---

## 5. Fleet-Wide Confirmation (task acceptance criterion)

Confirmed: **all 14 robots deviate from the canonical v1.3 template**, but in **two different directions**:
- **CAR-300**: conforms to v1.3 (the standard) — its "deviations" are the intended v1.3 fields.
- **Other 13**: on legacy `unit_blueprint` (v1.2-era), with empty `operational_properties: {}` and a broken `operational_data_reference.file` (missing prefix).

The canonical template is **not** aspirational — it is the active default. The 13 legacy robots are the ones lagging.

---

## 6. Stop Condition Triggered — Escalation Required

The task's Stop Conditions state:

> - "The loader code strips `operational_data/` from the prefix (CAR-300 is actually correct, no fix needed)"
> - "v1.3 schema is confirmed intentional but no docs exist for it (gap in documentation)"

**Both are effectively met:**
1. CAR-300 is **actually correct** on both the schema version AND the path prefix. No fix is needed — in fact, the "fix" the task describes (downgrade to v1.2, strip prefix) would **introduce** errors.
2. v1.3 is confirmed intentional (it is the default template), and it **is** documented (`JSON_DATA_GUIDE.md`, `wh-expansion.md`) — so the documentation gap is smaller than feared, but the **decision** that "v1.3 is the target standard and the 13 legacy robots should be migrated up" is not yet recorded as an explicit decision.

**Recommended action (pending Tracy's confirmation):**
- **Do NOT modify CAR-300.** It is the reference implementation.
- **Record the decision** that v1.3 is the canonical robot blueprint standard (done — see decision note).
- **Reframe the follow-up tasks** (for Tracy):
  - **Task A (revised)**: Migrate the **13 legacy robots UP to v1.3** (not down), and **fix their `operational_data_reference.file` to include the `operational_data/` prefix** so the cross-reference resolves.
  - **Task B (unchanged)**: Define the `operational_properties` contract and backfill for the 13 robots (CAR-300 already has real values as the model).
  - **Task C (new)**: Resolve the CAR-300 blueprint-vs-operational-data physical-properties discrepancy (§4).

---

## 7. Files Examined

| File | Finding |
|------|---------|
| `data/json-data/templates/unit_blueprint_v1.3.json` | Canonical template — no top-level `version`, `operational_properties: {}` placeholder, no `physical_properties` inside `operational_data_reference` |
| `data/json-data/blueprints/units/robots/deployment/car_300_deployment_robot_mk1_bp.json` | v1.3, populated `operational_properties`, correct `operational_data/` prefix |
| `data/json-data/blueprints/units/robots/construction/acr_100_space_constructor_mk1_bp.json` | Legacy `unit_blueprint`, empty `operational_properties`, broken prefix |
| `data/json-data/operational_data/units/robots/deployment/car_300_..._data.json` | Physical props disagree with blueprint (§4) |
| `galaxy_game/tools/transform_component.rb` | v1.3 is the default template (lines 169, 397) |
| `galaxy_game/app/services/catalog_service.rb` | Loads operational data by globbing `operational_data/` dir; ignores `.file` reference |
| `galaxy_game/app/models/concerns/has_mass_calculation.rb` | Reads `operational_data_reference.physical_properties.mass_kg` (mass fallback) |
| `docs/developer/JSON_DATA_GUIDE.md`, `docs/architecture/operations/wh-expansion.md` | v1.3 documented as the current standard |

---

## 8. Acceptance Criteria Status

- [x] Determined whether CAR-300's v1.3 schema is intentional or accidental → **Intentional** (default template)
- [x] Determined whether the `operational_data/` path prefix is intentional or accidental → **Correct/intentional** (matches disk + fleet convention)
- [x] Documented how CAR-300 differs from both the other 13 robots AND the canonical v1.3 template → §2, §3, §5
- [x] Confirmed all 14 robots deviate from canonical v1.3 → §5 (in two different directions)
- [ ] If accidental: normalize CAR-300 → **N/A — not accidental; do NOT normalize**
- [x] If intentional: document v1.3 in decision log + file follow-up tasks → decision note created; follow-ups reframed in §6
- [x] No regressions in robot-related specs → **N/A — no code changed**
- [x] Synthesis report saved to summaries folder → this file
