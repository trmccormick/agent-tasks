# Synthesis Report: No-Magic Robot Deployment Refactor

**Date**: 2026-07-07
**Task**: 2026-03-27-HIGH-REFACTOR-ESCALATION-SERVICE-NO-MAGIC-ROBOT-DEPLOYMENT.md
**Status**: Research complete — awaiting approval

---

## Current State Analysis

### `create_automated_harvester` (lines ~220-295)
The method has THREE direct `Units::Robot.create!` calls:
1. **O2 case** (line ~225): Creates "Automated Oxygen Harvester" with `task_type: atmospheric_harvesting`
2. **H2O case** (line ~240): Creates "Automated Water Extractor" with `task_type: ice_extraction`
3. **else case** (line ~255): Creates "Automated {material} Miner" with `task_type: regolith_mining`

All three use identical pattern:
```ruby
Units::Robot.create!(
  name: "...",
  identifier: "ROBOT-#{SecureRandom.hex(4)}",
  unit_type: "robot",
  owner: settlement.owner,
  attachable: settlement,
  operational_data: operational_data
)
```

**Violation**: Creates robots from nothing — no inventory check, no manufacturing queue, no material verification.

### `create_manufacturing_unit` (lines ~297-320)
Also uses `Units::Robot.create!` directly for smelter units. Same violation pattern.

### `deploy_harvester_to_site` (lines ~322+)
Nested inside `deploy_harvester_to_site` are two **class methods** (`format_coordinates`, `random_coordinates`) that should be private class methods at the bottom of the file — they're indented incorrectly inside another method body.

---

## Reference Patterns Found

### HasUnits concern (add_unit/install_unit)
- `add_unit(unit_blueprint_id)` — creates new unit from blueprint lookup
- `install_unit(unit)` — attaches existing unit to craft/settlement
- Both call `apply_unit_effects(unit)` after attachment
- Units are stored in `base_units` association

### Settlement inventory patterns
- Settlements use `base_units` association (via HasUnits concern)
- No explicit "undeployed" flag found — units are attached via `attachable` pointer
- Need to check if there's a deployment status field on Units::Robot

### Robot model
- Inherits from `Units::BaseUnit`
- Has `location` association (CelestialLocation)
- Has `set_location` method for Craft::Harvester compatibility
- No explicit `deploy`/`activate` methods found yet — need to check full robot.rb

---

## Implementation Plan

### Decision Tree Design

```ruby
def self.create_automated_harvester(settlement, material, quantity)
  # Step 0 — Validate input
  normalized = normalize_material(material)
  return { status: :blocked, reason: 'Invalid material' } unless normalized

  # Step 1 — Check settlement inventory for existing undeployed robot
  existing_robot = find_existing_harvester(settlement, normalized)
  if existing_robot
    return deploy_existing_robot(existing_robot, normalized, quantity)
  end

  # Step 2 — Check build capability (facility + materials)
  if can_build_harvester?(settlement, normalized)
    return queue_harvester_build(settlement, normalized, quantity)
  end

  # Step 3 — All options exhausted
  { status: :blocked, reason: 'No undeployed harvester in inventory and cannot build one (missing facility or materials)' }
end
```

### Private Methods to Implement

| Method | Purpose |
|---|---|
| `find_existing_harvester(settlement, material)` | Query settlement.base_units for existing robot matching material type |
| `deploy_existing_robot(robot, material, quantity)` | Set robot's task_type/target_quantity and attach to settlement |
| `can_build_harvester?(settlement, material)` | Check robotics_assembly_line + materials availability |
| `has_robotics_facility?(settlement)` | Query base_units for robotics_assembly_line |
| `has_required_materials?(settlement, material)` | Check inventory for required materials (from blueprint) |
| `queue_harvester_build(settlement, material, quantity)` | Create ManufacturingJob with required materials deduction |

### Key Design Decisions Needed

1. **How to identify "undeployed" robots?** — Need to check if there's a deployment status field on Units::Robot, or if we use `attachable` vs `location` distinction
2. **Material lookup** — Should read from blueprint JSON (`hrv_400_resource_harvester_mk1_bp.json`) for exact material requirements
3. **ManufacturingJob creation** — Need to check existing ManufacturingJob service patterns in the codebase
4. **GCC fallback** — Task mentions 85,000 GCC purchase cost as tier 3 of sourcing hierarchy

---

## Risks

| Risk | Mitigation |
|---|---|
| Spec file may have many mocks of `Units::Robot.create!` | Will need to update all affected examples to test decision tree behavior |
| `create_manufacturing_unit` has same violation | Task scope is `create_automated_harvester` only — flag for separate task |
| Nested class methods in `deploy_harvester_to_site` | Fix indentation as bonus (not in original task scope) |
| Blueprint JSON may not exist for all materials | Return `:blocked` with specific reason if no blueprint found |

---

## Files to Edit

1. **`galaxy_game/app/services/ai_manager/escalation_service.rb`** — Refactor `create_automated_harvester` + add private methods
2. **`galaxy_game/spec/services/ai_manager/escalation_service_spec.rb`** — Rewrite specs for decision tree testing

## Files to Read (Reference Only)

1. `galaxy_game/app/models/units/robot.rb` — Check deploy/activate methods
2. `galaxy_game/app/models/settlement/base_settlement.rb` — Inventory query patterns
3. `galaxy_game/app/models/concerns/has_units.rb` — add_unit/install_unit patterns (already read)
4. `data/json-data/blueprints/units/robots/resource/hrv_400_resource_harvester_mk1_bp.json` — Material requirements
5. `galaxy_game/app/services/manufacturing_job.rb` or similar — ManufacturingJob creation pattern

---

## Ready to Apply? — waiting for approval
