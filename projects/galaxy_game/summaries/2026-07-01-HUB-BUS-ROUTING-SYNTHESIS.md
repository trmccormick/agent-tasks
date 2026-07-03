# STATUS SYNTHESIS REPORT: Hub Bus Routing
**Date**: 2026-07-01
**Task**: 2026-07-01-HIGH-ARCHITECTURE-HUB-BUS-ROUTING.md
**Author**: Implementation Agent (Ollama)

---

## 1. Pre-check Results

### Pre-check 1: connect_units callers involving hub/umbilical

Found **6 task files** with `connect_units` referencing the Planetary Umbilical Hub:

| # | Task File | unit1 → unit2 | Direction |
|---|-----------|---------------|-----------|
| 1 | `task_deploy_pve_unit.json` | PVE Mk1 → PUH (volatiles_processing) | Unit→Hub |
| 2 | `task_finalize_surface_power_grid.json` | solar_array → PUH (solar_array_input) | Unit→Hub |
| 3 | `task_deploy_puh_and_ppmu.json` | PUH ↔ PPMU (main_power_in ↔ main_power_out) | Hub↔Hub |
| 4 | `task_deploy_additional_robot_ports.json` | Robot Charging Port → PUH (power_output) | Unit→Hub |
| 5 | `task_deploy_gas_separator.json` | Gas Separator → PUH + Cryo Tanks | Hub→Unit + Unit→Unit |
| 6 | `task_activate_basic_power_sources.json` | RTG → PUH (rtg_input) | Unit→Hub |

### Pre-check 2: execute_effect structure

```
Line 338: def execute_effect(effect)
Line 342: when 'connect_units'
```

**No `register_to_bus` case exists.** The method has 10 cases total (deploy_unit, connect_units, construct_structure, set_unit_state, set_settlement_state, check_unit_state, transfer_resource, manufacture, advance_deployment_stages, request_import).

---

## 2. Architecture Analysis

### Planetary Umbilical Hub Blueprint (`planetary_umbilical_hub_bp.json`)
- **Schema version**: Legacy flat ports (no `connection_schema`)
- **Port definition**: `internal_unit_ports: 4`
- **Category**: infrastructure/utility
- **Capabilities**: power_distribution_capacity_kw: 500, data_bandwidth_mbps: 1000, connection_ports: 12

### Gas Separator Blueprint (`gas_separator_unit_bp.json`)
- Uses standard unit blueprint schema
- Has input/output ports for gas processing

### Inflatable Cryo Tank Blueprint (`inflatable_cryo_tank_bp.json`)
- **Schema version**: v1.9 (has `connection_schema`)
- **utility_ports**: 5 entries (power_bus_primary, data_bus_main, cryo_grid_h2, cryo_grid_o2, cryo_grid_he3)
- **storage_bays**: 3 entries (H2, O2, He-3 each 32m³)

### LegacyPortAdapter Status
- Complete and functional (commit b3beff34)
- Handles v1.9 schemas, legacy flat ports, pre-v1.9 typed ports, zero-port units
- `resolve_port_schema(blueprint_id)` returns `{schema_version, ports_hash, connection_schema}`

---

## 3. Migration Scope Analysis

### Files that SHOULD migrate to register_to_bus (Unit→Hub routing):

These are cases where a **surface unit** connects TO the hub for resource/power routing:

1. **`task_deploy_gas_separator.json`** — PRIMARY TARGET
   - Gas Separator → PUH (volatiles_processing) — MIGRATE
   - Gas Separator → Cryo Tank (cryo_grid_h2/o2/he3) — MIGRATE (these are hub-routed, not direct)

2. **`task_deploy_pve_unit.json`** — PVE Mk1 → PUH (volatiles_processing) — MIGRATE

3. **`task_finalize_surface_power_grid.json`** — solar_array → PUH — MIGRATE

4. **`task_deploy_additional_robot_ports.json`** — Robot Charging Port → PUH — MIGRATE

5. **`task_activate_basic_power_sources.json`** — RTG → PUH — MIGRATE

### Files that should KEEP connect_units (Hub↔Hub or infrastructure):

6. **`task_deploy_puh_and_ppmu.json`** — PUH ↔ PPMU
   - This is hub-to-hub infrastructure connection, NOT unit-to-hub routing
   - Both are hub-level infrastructure units
   - **KEEP as connect_units**

### Migration Summary:
- **Migrate**: 5 task files (gas separator, PVE, solar array, robot ports, RTG)
- **Keep**: 1 task file (PUH+PPMU deployment)
- **Primary focus**: gas_separator.json (most complex migration — 3 connect_units calls)

---

## 4. register_to_bus Design

### Action Format (task JSON):
```json
{
  "action": "register_to_bus",
  "unit": "Gas Separator",
  "hub": "Planetary Umbilical Hub",
  "port_type": "volatiles_processing"
}
```

For multi-port registration (gas separator → cryo tanks via hub):
```json
{
  "action": "register_to_bus",
  "unit": "Gas Separator",
  "hub": "Planetary Umbilical Hub",
  "ports": [
    { "port_type": "input", "hub_port": "volatiles_processing" },
    { "port_type": "output_h2", "hub_port": "cryo_grid_h2", "target_unit": "Inflatable Cryogenic Tank" },
    { "port_type": "output_o2", "hub_port": "cryo_grid_o2", "target_unit": "Inflatable Cryogenic Tank" },
    { "port_type": "output_he3", "hub_port": "cryo_grid_he3", "target_unit": "Inflatable Cryogenic Tank" }
  ]
}
```

### register_to_bus_from_effect Logic:
1. Verify both `unit` and `hub` exist at settlement
2. Verify hub is actually a Planetary Umbilical Hub (or similar bus-type unit)
3. For each port registration:
   a. Use LegacyPortAdapter to resolve port schema for the unit
   b. Check available ports on the unit for the requested type
   c. Check available routing capacity on the hub
   d. Register the port with the hub's routing table (stored in hub's operational_data)
   e. If `target_unit` specified, also register that target's receiving port
4. Return success/failure result

### Hub Routing Table Structure (in hub's operational_data):
```json
{
  "bus_routing_table": {
    "registered_units": [
      {
        "unit_name": "Gas Separator",
        "ports": [
          { "port_type": "input", "hub_port": "volatiles_processing", "direction": "in" },
          { "port_type": "output_h2", "hub_port": "cryo_grid_h2", "direction": "out", "target_unit": "Inflatable Cryogenic Tank" }
        ]
      }
    ],
    "routing_rules": [
      { "from_hub_port": "volatiles_processing", "to_hub_port": "cryo_grid_h2", "formula": "H2" }
    ]
  }
}
```

---

## 5. Risk Assessment

### Low Risk:
- Adding new case alongside existing connect_units (GOTCHA 2) — no existing code modified
- register_to_bus_from_effect is a new method — no impact on existing callers
- Gas separator migration is isolated to one task file

### Medium Risk:
- Hub blueprint uses legacy flat ports (internal_unit_ports: 4) — LegacyPortAdapter handles this
- Need to verify port count doesn't exceed hub's capacity during registration
- Multi-port registration format needs careful JSON design

### High Risk (Stop Conditions):
- If hub blueprint is missing required routing data → STOP
- If register_to_bus routing logic requires architectural decision → STOP
- If Luna rake suite drops below 12/13 baseline → STOP
- If any change breaks existing connect_units callers → STOP

---

## 6. Implementation Plan

### Step 1: Add register_to_bus case to execute_effect
- Location: Line ~343 in task_execution_engine_v2.rb (after connect_units case)
- New case alongside existing connect_units — DO NOT remove or modify connect_units

### Step 2: Implement register_to_bus_from_effect method
- Verify unit and hub exist
- Resolve port schemas via LegacyPortAdapter
- Register ports with hub's routing table
- Handle single-port and multi-port registration formats

### Step 3: Migrate task_deploy_gas_separator.json
- Replace 4 connect_units calls with register_to_bus format
- Gas Separator registers to hub; hub routes to cryo tanks

### Step 4: Migrate remaining Unit→Hub task files (optional — defer if scope too large)
- PVE, solar array, robot ports, RTG tasks
- Each needs one register_to_bus call replacing connect_units

### Step 5: Add targeted specs
- Test register_to_bus case in execute_effect
- Test port registration with hub routing table
- Test migration of gas separator task

### Step 6: Run targeted tests
- `rspec spec/services/ai_manager/task_execution_engine_v2_spec.rb`
- Verify 0 failures, baseline maintained

---

## 7. Files to Modify

### Primary (must modify):
1. `galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb` — Add register_to_bus case + method
2. `data/json-data/missions/tasks_v2/task_deploy_gas_separator.json` — Migrate to register_to_bus
3. `spec/services/ai_manager/task_execution_engine_v2_spec.rb` — Add targeted tests

### Secondary (should modify):
4. `data/json-data/missions/tasks_v2/task_deploy_pve_unit.json` — Migrate to register_to_bus
5. `data/json-data/missions/tasks_v2/task_finalize_surface_power_grid.json` — Migrate to register_to_bus
6. `data/json-data/missions/tasks_v2/task_deploy_additional_robot_ports.json` — Migrate to register_to_bus
7. `data/json-data/missions/tasks_v2/task_activate_basic_power_sources.json` — Migrate to register_to_bus

### Read-only (reference):
- `docs/architecture/systems/LUNA_ISRU_GAS_PROCESSING_AND_SKIMMER_OPERATIONS.md`
- `docs/architecture/systems/PORT_CONNECTION_SYSTEM.md`
- `galaxy_game/app/services/lookup/legacy_port_adapter.rb`
- `data/json-data/blueprints/units/infrastructure/planetary_umbilical_hub_bp.json`
- `data/json-data/blueprints/units/storage/inflatable_cryo_tank_bp.json`

---

## 8. Recommendations

1. **Start with gas separator migration** — it's the most complex and demonstrates the pattern
2. **Keep connect_units for PUH↔PPMU** — this is infrastructure-to-infrastructure, not unit routing
3. **Use LegacyPortAdapter** for all port schema resolution — don't duplicate logic
4. **Hub routing table** stored in hub's operational_data under `bus_routing_table` key
5. **Multi-port registration** via `ports` array in single register_to_bus action
6. **Test baseline first** — know the current Luna rake suite count before making changes

---

## 9. Synthesis Complete

**Status**: Ready for human approval to proceed with implementation.
**Next step**: Human reviews and approves synthesis report, then agent proceeds with Steps 1-7.
