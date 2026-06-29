---
name: "Migrate CO2 Oxygen Production Data Schema"
priority: LOW
phase: phase6+
created: 2026-06-21
status: backlog
type: data
relocated_from: reorganization_attempt_3
relocated_reason: "Life support needed for lava-tube base verification loop in phase6+"
---

# TASK: Migrate CO2 Oxygen Production Unit Data to New Schema and Fix Stoichiometry

**Priority**: LOW  
**Phase**: phase5+ (Early Game)  
**Type**: data migration  
**Created**: 2026-06-21  

## Problem Statement

`co2_oxygen_production_data.json` is the operational data file for the CO2 Oxygen Production Unit, a life support unit that splits atmospheric CO2 into breathable oxygen. The file was never migrated from the old `resource_management.consumables/generated` schema to the current `input_resources` / `output_resources` / `processing_capabilities` schema used by all ISRU units (GCU, TEU, Gas Separator). Additionally its chemistry is physically impossible — inputs and outputs do not mass-balance.

**Current**: Old schema with resource_management.consumables/generated structure; chemically impossible stoichiometry  
**Expected**: New schema matching GCU/TEU/Gas Separator patterns; chemically valid CO2 → O2 + C conversion

## Evidence of Incompleteness

```bash
grep -n "resource_management\|consumables" data/json-data/operational_data/units/life_support/co2_oxygen_production_data.json 2>/dev/null | head -5
# Likely shows old schema structure
```

## Files to Edit

| File | Purpose | Key Section |
|------|---------|-------------|
| `data/json-data/operational_data/units/life_support/co2_oxygen_production_data.json` | CO2 production data | Migrate to new schema |

## Acceptance Criteria

- [ ] Data migrated from old resource_management.consumables/generated schema to input_resources/output_resources/processing_capabilities schema
- [ ] Chemistry is physically valid (CO2 → O2 + C mass balance)
- [ ] Schema matches canonical example in gas_conversion_unit_data.json
- [ ] UnitLookupService can load the data without errors
- [ ] ISRU evaluator can read output_resources and processing_capabilities correctly

## Implementation Steps

1. Read canonical example: `data/json-data/operational_data/units/production/refineries/gas_conversion_unit_data.json`
2. Migrate co2_oxygen_production_data.json to new schema format
3. Fix stoichiometry: CO2 → O2 + C (mass balance must be correct)
4. Verify UnitLookupService loads the data correctly
5. Test with ISRU evaluator if extended to cover life support units

## Commit Instructions

```bash
git add data/json-data/operational_data/units/life_support/co2_oxygen_production_data.json
git commit -m "fix: migrate CO2 oxygen production data to new schema and fix stoichiometry"
```
