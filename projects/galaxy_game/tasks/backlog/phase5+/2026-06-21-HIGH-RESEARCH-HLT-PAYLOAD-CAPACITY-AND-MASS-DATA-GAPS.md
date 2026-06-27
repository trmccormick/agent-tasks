# 2026-06-21-HIGH-RESEARCH-HLT-PAYLOAD-CAPACITY-AND-MASS-DATA-GAPS.md

**Priority:** HIGH
**Type:** Research + Implementation
**Created:** 2026-06-21
**Author:** Claude (web), session handoff — dry run surfaced this gap, see Context
**Status:** Ready to dispatch
**Relocated:** From reorganization_attempt_3/ on 2026-06-22 (confirmed MVP blocker for manifest validation)

---

## Context (read before starting)

This task exists because of a real gap found while doing a "dry run" review of the
Luna precursor manifest v2 draft (`lunar_precursor_manifest_v2_DRAFT.json`, not
yet committed) against the Heavy Lift Transport (HLT) craft. Tracy raised a real
constraint — **launches shouldn't underweight or overload the craft** — and we
discovered the codebase has real, working mass-*calculation* infrastructure but
**no mass-*capacity* limit anywhere**, and no confirmation that cargo (not just
installed units) is even included in the calculation.

**Do not re-litigate what's already confirmed below.** Grep/confirm before
trusting, per standing rule — but the four investigation steps below are
specifically things we could NOT confirm from web-based file review and need
local codebase access to resolve. Don't redo the parts already settled.

---

## Already confirmed (do not redo this work)

1. **`HasMassCalculation`** (Ruby concern, exact source provided by Tracy) is
   real, implemented, and matches `heavy_lift_transport_bp.json`'s
   `physical_properties.empty_mass_kg: 163000.0` exactly. It calculates total
   craft mass = base craft mass + sum of installed units/modules/rigs
   (`base_units`, `base_modules`, `base_rigs` — i.e. things mounted to ports).
   It raises a clear error (`"Could not find mass for unit: #{unit_type}"`) if
   a blueprint lacks mass data — consistent with the project's "fail fast, no
   fake success" principle. **This is dry/structural mass only.**

2. **`LaunchPaymentService`** (exact source provided by Tracy) consumes
   `craft.calculate_mass` (preferring it directly over manual recalculation)
   to compute **launch cost** (`mass_kg × cost_per_kg + base_cost`). This is a
   pricing service, not a capacity check. **No comparison against any capacity
   limit exists in this file.**

3. **No payload mass limit field exists** on any of the 4 HLT-related files
   reviewed: `heavy_lift_transport_bp.json` (base blueprint — has
   `cargo_capacity_m3: 1000.0`, volume only), `heavy_lift_transport_data.json`,
   `heavy_lift_transport_lunar_data.json`, `heavy_lift_transport_landing_data.json`
   (3 operational-data variants, differentiated by mission profile/fuel-burn,
   not mark/generation — **do not confuse with the separate, not-yet-created
   mk1/mk2/mk3 versioning scheme** Tracy has planned).

4. **Data discrepancy found, unresolved**: `LaunchPaymentService`'s
   `extract_unit_mass` fallback sums `required_materials` (where unit is
   kilogram) when no explicit mass field exists. Applying that to
   `heavy_lift_transport_bp.json`'s own `blueprint_data.materials` list
   (stainless_steel 150000 + electronics 5000 + thermal_protection 8000 +
   titanium_alloy 20000 + carbon_fiber 10000 = **193,000 kg**) does NOT match
   the stated `empty_mass_kg: 163000.0`. Step 1 below should resolve which
   figure (if either) is authoritative.

5. **Provenance**: HLT was originally prototyped as "Starship" before renaming
   (see `starship_integration_precursor_mission.rb`'s stale filename, a known
   leftover, confirmed 2026-06-19). Tracy confirmed the original design basis
   was real-world Starship's payload capability.

6. **Baseline figure adopted by decision (2026-06-20), not derived from any
   in-universe doc**: real-world Starship's design target is consistently
   cited as **150 metric tons (150,000 kg) payload to LEO in fully-reusable
   mode** across current sources (Wikipedia, eoPortal, industry coverage, all
   checked June 2026) — reusable-mode range generally 100-150t, expendable
   ceiling 200-250t. **150,000 kg is the agreed baseline for HLT's payload
   mass limit going forward.** No prototyping-era research doc with an
   original target figure was found; this real-world figure was deliberately
   adopted instead of searching further. If you find an older in-universe doc
   with a different original target, surface it for review — don't just
   silently override the adopted baseline.

---

## What this task needs to actually determine (local codebase access required)

### Step 1 — Resolve the empty_mass_kg vs. materials-sum discrepancy
Check whether `163000.0` or `193000` (or some other figure) is the authoritative
dry mass for the HLT, and document why the discrepancy exists (rounding?
materials list incomplete/outdated? empty_mass_kg manually overridden after
materials were set?). Fix whichever field is wrong, or document if both are
intentionally different for a reason not yet understood.

### Step 2 — Confirm whether cargo/payload mass is calculated ANYWHERE
Grep the codebase for any service, model method, or concern that sums the mass
of a manifest's `inventory` (units/rigs/supplies/consumables awaiting
deployment) as distinct from `installed_units`/`base_units` (mounted to ports).
**Do not assume either way** — confirm directly. Likely places to check:
`Logistics::ManifestGenerator`, `Logistics::TransportCostService`, any
`Mission` or `Manifest` model, anything in `app/services/missions/` or
`app/services/logistics/`.

- **If it exists**: document where, how it works, and whether it's wired into
  anything that currently runs (vs. dormant/unused like
  `resource_acquisition_logic_v1.json` was found to be on 2026-06-15).
- **If it does not exist**: this is the real gap. Scope what it would take to
  add (likely a method alongside `HasMassCalculation`, or a new concern
  `HasCargoMassCalculation`, summing manifest inventory items the same way
  `get_unit_mass` etc. already do for installed components).

### Step 3 — Confirm whether ANY capacity-limit check exists
Grep for anywhere `cargo_capacity_m3` is actually compared against something
(not just stored). Also check whether any mass-based capacity limit exists
under a different name than expected (e.g. `max_payload_kg`,
`payload_limit_kg`, etc.) before assuming none exists.

- **If a capacity check exists**: document it, confirm whether it checks mass,
  volume, or both, and whether the 150,000 kg baseline (Step 6 above) needs to
  be wired into it or whether a different existing limit should be used instead.
- **If none exists**: this needs to be designed. At minimum: add a
  `max_payload_kg` (or similarly named) field to `heavy_lift_transport_bp.json`
  set to **150000**, and a capacity-check method (likely alongside or calling
  `HasMassCalculation`) that compares total cargo mass against it and raises/
  flags when under or over. Follow the "fail fast" pattern already established
  — do not silently allow over/under-loaded launches to "succeed."

### Step 4 — Confirm mass data exists on the actual Luna precursor manifest items
Check whether `physical_properties.mass_kg` (or `empty_mass_kg`) is populated
on the blueprints for every item in `lunar_precursor_manifest_v2_DRAFT.json`'s
`required_hardware` list: `solar_expansion_rig`,
`planetary_power_management_unit_mk1`, `comms_equipment_mk1`,
`planetary_umbilical_hub_mk1`, `thermal_extraction_unit_mk1`,
`planetary_volatiles_extractor_mk1`, `inflatable_pressure_tank`,
`inflatable_cryo_tank`, `inflatable_gas_storage`, `gas_separator`,
`car_300_deployment_robot_mk1`, `robot_charging_port`.

- For each: does the blueprint have a mass field? If not, that blueprint needs
  one added before this manifest can ever be weight-checked. Report a
  checklist — which ones have it, which don't.

---

## Deliverable

A findings report covering Steps 1-4 above, plus:
- Recommended fix for the empty_mass_kg/materials-sum discrepancy (Step 1).
- Confirmation (not assumption) of cargo-mass-calculation and capacity-check
  status (Steps 2-3), with implementation scoped if either is missing.
- A checklist of which manifest items (Step 4) already have mass data and
  which need it added.

**Do not implement Steps 2-3's fixes without sign-off first if they require
new fields/methods** — report findings and a proposed approach, hold for
review, same pattern as the Luna Precursor V2 task's Step 4/5 handoff this
week. Step 1 and Step 4's blueprint mass additions (if straightforward field
additions, not architectural changes) can be done directly and reported.

---

## Stop conditions

- If `HasMassCalculation` or `LaunchPaymentService` source code on disk differs
  meaningfully from what's quoted in this task (e.g. methods renamed, moved,
  refactored since 2026-06-20), stop and report the discrepancy — do not
  assume the quoted version is still accurate.
- If a capacity-check or cargo-mass system is found that contradicts the
  150,000 kg baseline (e.g. an existing, different documented limit), stop and
  report rather than silently picking one.
- Standing rule applies: grep/confirm before trusting any of this task's
  "already confirmed" section against the live codebase — it was compiled from
  file uploads in a web session, not direct local access.
