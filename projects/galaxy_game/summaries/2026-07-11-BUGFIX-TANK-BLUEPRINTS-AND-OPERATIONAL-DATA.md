# Synthesis: Fix Tank Blueprints and Operational Data — V1.4 Template Compliance

**Date**: 2026-07-11
**Task**: 2026-07-11-HIGH-BUGFIX-TANK-BLUEPRINTS-AND-OPERATIONAL-DATA-V1.4.md
**Type**: bug-fix

## Analysis Summary

### Current State
- `lox_tank_bp.json` — component_blueprint format (wrong, not scanned by UnitLookupService)
- `methane_tank_bp.json` — component_blueprint format (wrong, not scanned by UnitLookupService)
- `methane_storage_tank_mk1_bp.json` — unit_blueprint but missing connection_schema, template_compliance = "unit_blueprint" (not v1.4)
- `lox_storage_tank_data.json` — does NOT exist
- `lox_storage_tank_mk1_data.json` — does NOT exist
- `methane_storage_tank_mk1_data.json` — does NOT exist

### Reference Templates
- unit_blueprint_v1.4.json: canonical template with storage_properties, connection_schema, metadata.template_compliance = "unit_blueprint_v1.4"
- unit_operational_data_v1.3.json: canonical operational data template with operational_status, operational_modes blocks

### Key Values (from task spec)
- LOX: volume_m3=129, empty_mass_kg=4000, capacity=10000, stored_material="O2", phase="liquid"
- Methane: already has correct values in existing bp file

## Implementation Plan

### Work Item 1: DELETE wrong-format component blueprints
- Remove `lox_tank_bp.json`
- Remove `methane_tank_bp.json`

### Work Item 2: CREATE lox_storage_tank_mk1_bp.json
- Model after methane_storage_tank_mk1_bp.json
- Populate with LOX-specific values from task spec
- Add connection_schema with fluid_port slots
- Set template_compliance = "unit_blueprint_v1.4"

### Work Item 3: UPDATE methane_storage_tank_mk1_bp.json → v1.4
- Add connection_schema block (2 fluid_port mounting slots)
- Update metadata.template_compliance to "unit_blueprint_v1.4"
- Update metadata.version to "1.4"

### Work Item 4: CREATE lox_storage_tank_mk1_data.json
- Follow unit_operational_data_v1.3 template
- Minimal operational_status and operational_modes blocks

### Work Item 5: CREATE methane_storage_tank_mk1_data.json
- Follow unit_operational_data_v1.3 template
- Minimal operational_status and operational_modes blocks

## Constraints
- JSON data only — no Ruby changes
- data/ is gitignored — no git add/commit on JSON files
- stored_material for LOX = "O2" (chemical formula convention)
