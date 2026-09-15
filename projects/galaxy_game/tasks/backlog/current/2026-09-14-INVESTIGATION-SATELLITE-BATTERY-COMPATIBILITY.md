# Satellite Battery Compatibility Investigation

**Status**: DRAFT — Not dispatch-ready  
**Created**: 2026-09-14  
**Last Updated**: 2026-09-14  
**Priority**: MEDIUM (data integrity issue)  
**Type**: DATA/ARCHITECTURE TASK CANDIDATE  

---

## Finding

There is a mismatch between the GCC mining satellite's recommended fit and the generic satellite blueprint's compatible_units whitelist:

| File | Field | Value |
|------|-------|-------|
| `crypto_mining_satellite_data.json` | `recommended_fit.units` | includes `satellite_battery` count: 1 |
| `generic_satellite_bp.json` | `compatible_units` whitelist | does NOT include `satellite_battery` |

---

## Research Question

Is `generic_satellite_bp.json` intentionally the universal generic-satellite compatibility contract, a temporary fallback, or a placeholder base?

### Evidence Gathered

- `generic_satellite_bp.json` has `"template": "base_craft"` and `"id": "generic_satellite"` — appears to be a universal generic-satellite platform blueprint
- The `compatible_units` whitelist represents the "generic" subset of mountable units (basic/advanced/quantum_computer, solar_panels, RTG, basic/advanced_sensor, basic_ion_thruster, fuel_tank_s)
- The GCC mining satellite has its own operational data file (`crypto_mining_satellite_data.json`) with a `recommended_fit` that includes `satellite_battery`
- This suggests specialized crafts should have their own blueprint files

### Battery Importance

The battery is essential for GCC mining continuity during eclipse periods:
- Satellite power consumption: 250 kW
- Battery capacity: 500.0 kWh
- Max discharge rate: 150.0 kW
- Without battery, satellite cannot mine during grid outage/eclipse

---

## Resolution Options

### Option A (Recommended): Dedicated GCC Blueprint
Create a dedicated GCC mining satellite blueprint file that includes `satellite_battery` in its `compatible_units` whitelist, derived from `generic_satellite_bp.json` as a base.

**Pros**: Clean separation of generic vs. specialized; battery is essential equipment for GCC mining
**Cons**: Requires creating new blueprint file

### Option B (Quick Fix): Add to Generic Whitelist
Add `satellite_battery` to `generic_satellite_bp.json`'s `compatible_units`.

**Pros**: Quick fix, minimal effort
**Cons**: Pollutes generic platform with specialized unit; not architecturally clean

### Option C (Data-Driven): Auto-Populate from recommended_fit
Have the construction system read `recommended_fit` from the craft's operational_data and auto-populate compatible_units.

**Pros**: Eliminates whitelist maintenance burden
**Cons**: Bypasses intentional design constraints; recommended_fit should not override compatibility validation

---

## Recommendation

Option A as immediate fix, Option C as long-term design consideration. Do NOT make `recommended_fit` override compatibility validation (that would bypass intentional design constraints).

---

## Status Labels

| Aspect | Status |
|--------|--------|
| Classification | Historical documentation / data-file comparison |
| Urgency | MEDIUM — battery is essential for GCC mining but mismatch may not be enforced yet |
| Verification needed | Confirm whether `compatible_units` whitelist is actually enforced in construction/deployment code |

---

**Status**: Requires Gemini review (economic design intent for satellite equipment requirements). Not dispatch-ready.
