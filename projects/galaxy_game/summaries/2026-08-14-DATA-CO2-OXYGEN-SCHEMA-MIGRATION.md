# Synthesis: CO2 Oxygen Production Unit Schema Migration

**Date**: 2026-08-14  
**Task**: 2026-04-03-LOW-DATA-CO2-OXYGEN-PRODUCTION-UNIT-SCHEMA-AND-STOICHIOMETRY.md  
**Status**: In Progress  

---

## Analysis Summary

### Current State
The CO2 Oxygen Production Unit data file (`co2_oxygen_production_data.json`) has a **hybrid schema** — it has some new-format fields (`template`, `telemetry`, `metadata`, `base_cost_eap`, `usd_import_fee`) but still uses the old `resource_management.consumables/generated` format for resource definitions.

### Key Findings

1. **Schema gap**: The file has `template: "unit_operational_data"` and `metadata.template_compliance: "unit_operational_data"` but lacks the canonical fields that UnitLookupService expects:
   - `input_resources` array (missing — uses old `consumables` instead)
   - `output_resources` array (missing — uses old `generated` instead)
   - `processing_capabilities` object (missing entirely)
   - `operational_properties` object (missing — power is in `operational_modes` only)

2. **Stoichiometry error**: Current data shows 45 kg input → 65 kg output (mass not conserved). The chemistry mixes CO₂ electrolysis with Sabatier without clarification.

3. **Correct chemistry**: Pure CO₂ solid-oxide electrolysis:
   - Reaction: CO₂ → C + O₂
   - Mass balance: 44 kg CO₂ → 12 kg Carbon + 32 kg O₂
   - Energy: ~65 kWh per cycle (solid-oxide electrolysis at high temperature)

4. **GCU reference**: The canonical GCU format has these fields in order:
   - `template`, `id`, `name`, `description`
   - `category`, `subcategory`
   - `processing_capabilities` (with `atmospheric_processing` and `geosphere_processing`)
   - `input_resources` array
   - `output_resources` array
   - `operational_properties` (power, heat, failure_rate, maintenance_interval)
   - `resource_management` (legacy — kept for backward compat)
   - `operational_modes`, `connections`, `diagnostics`, `error_states`, `telemetry`, `metadata`, `base_cost_eap`, `usd_import_fee`

### Implementation Plan

1. Add `description` documenting CO₂ electrolysis chemistry
2. Replace `consumables` with `input_resources`: `{id: "CO2", amount: 44.0, unit: "kilogram"}`
3. Replace `generated` with `output_resources`: `{id: "C", amount: 12.0, unit: "kilogram"}`, `{id: "O2", amount: 32.0, unit: "kilogram"}`
4. Add `processing_capabilities.atmospheric_processing` (enabled, types: ["co2_electrolysis"])
5. Add `operational_properties` with power ~65 kWh/hr, heat generation, failure rate, maintenance interval
6. Keep existing `resource_management` block for backward compatibility (GCU retains it)
7. Update `error_states` to include realistic states
8. Update `telemetry` data points to match new schema
9. Verify UnitLookupService loads correctly

### Risks & Considerations

- **Backward compat**: GCU retains its `resource_management` block — keep the old format for backward compatibility
- **Life support identity**: Must NOT merge with GCU (different category, different purpose)
- **Energy scaling**: The task says ~65 kWh per cycle. Need to determine if this is per-hour rate or per-cycle total. For `operational_properties.power_consumption_kw`, use hourly rate (~65 kW if 1 hr cycle, or adjust based on production rate)
- **Existing operational_modes**: Keep existing modes but update power_draw values to reflect new energy requirements

---

## Files to Modify

| File | Action |
|------|--------|
| `data/json-data/operational_data/units/life_support/co2_oxygen_production_data.json` | Migrate schema, fix stoichiometry |

## Reference Files (read-only)

| File | Purpose |
|------|---------|
| `data/json-data/operational_data/units/production/refineries/gas_conversion_unit_data.json` | Canonical GCU format template |
| `galaxy_game/app/services/logistics/isru_capability_manager.rb` | Verify CO2 unit loads correctly |
| `galaxy_game/spec/services/logistics/isru_capability_manager_spec.rb` | Verify no regressions |

---

*Synthesis complete. Ready for implementation.*
