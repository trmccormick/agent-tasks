# SYNTHESIS REPORT — BaseSettlement.create! Migration

**Date**: 2026-08-24
**Task**: 2026-05-27-HIGH-REFACTOR-AI-MANAGER-USE-SETTLEMENT-DEPLOYMENT-SERVICE.md

---

## SITE 1 — manager.rb:establish_settlement_from_plan (~line 235)

**Current code:**
```ruby
initial_settlement = Settlement::BaseSettlement.create!(
  name: "#{lavatube.name} Outpost",
  type: "Outpost",
  location: lavatube.location,
  description: plan['overall_strategy_notes'],
  current_population: plan['initial_resource_targets']['population_capacity_target'],
  power_output_mw: plan['initial_resource_targets']['power_output_mw_target'],
  resource_storage_cubic_meters: plan['initial_resource_targets']['resource_storage_cubic_meters_target'],
)
```

**Available craft variable:** NONE — this method receives `lavatube` and `plan`, no craft object exists in scope.

**Available location variable:** `lavatube.location`

**Proposed replacement:** CANNOT use `establish_from_craft` — no craft available. This is a lavatube exploration plan, not a craft deployment. Needs a different service method or a new primitive like `SettlementDeploymentService.establish_from_plan(lavatube, plan)`.

---

## SITE 2 — system_architect.rb:deploy_initial_habitats (~line 377)

**Current code:**
```ruby
habitat_settlement = Settlement::BaseSettlement.create!(
  name: "#{celestial_body.name} Subsurface Base",
  location: location,
  settlement_type: 'base',
  owner: find_bootstrap_corporation
)
```

**Available craft variable:** NONE — this method deploys habitats directly on a celestial body.

**Available location variable:** `location` (passed as parameter)

**Proposed replacement:** CANNOT use `establish_from_craft` — no craft available. This is a direct habitat deployment, not from a craft. Needs a different service method or a new primitive like `SettlementDeploymentService.establish_direct(location, owner, settlement_type:)`.

---

## SITE 3 — task_execution_engine_v2.rb:create_temporary_settlement (~line 104)

**Current code:**
```ruby
settlement = Settlement::BaseSettlement.create!(
  name: "#{body.name} Base",
  settlement_type: :base,
  operational_data: {
    "foundation_sintered" => false,
    "inflation_state" => "idle"
  }
)

location = Location::CelestialLocation.create!(
  name: "#{body.name} Base Location",
  coordinates: "00.00°N 00.00°E",
  locationable: settlement,
  celestial_body: body
)
```

**Available craft variable:** NONE — this method receives `body` (CelestialBody), no craft in scope.

**Available location variable:** Not yet created — Location::CelestialLocation is created AFTER the settlement.

**Proposed replacement:** CANNOT use `establish_from_craft` — no craft available, and location doesn't exist yet. This creates both settlement AND location from scratch. Needs a new primitive or significant refactoring.

---

## RISK ANALYSIS

### Architectural Gap (CRITICAL)
`SettlementDeploymentService.establish_from_craft` is designed for **craft-based deployment** (a craft arrives with cargo, units, and resources). All 3 call sites are **NOT craft-based**:
- manager.rb: lavatube exploration plan → settlement
- system_architect.rb: direct habitat deployment
- task_execution_engine_v2.rb: temporary settlement + location creation

The `craft` parameter is required by the current service interface. None of the 3 sites have a craft object. **This is not a simple find-and-replace — it's an architectural gap.**

### Options
1. **Extend SettlementDeploymentService** with additional class methods (e.g., `establish_from_plan`, `establish_direct`, `establish_temporary`) that handle non-craft scenarios
2. **Create a separate SettlementFactory service** for non-craft settlement creation
3. **Keep direct BaseSettlement.create!** at these sites but add cargo/unit verification as a post-step if needed

### Specs Affected
- Need to check: `spec/services/ai_manager/manager_spec.rb`
- Need to check: `spec/services/ai_manager/system_architect_spec.rb`
- Need to check: `spec/services/ai_manager/task_execution_engine_v2_spec.rb`

---

## READY TO APPLY? — NO, NEEDS ARCHITECTURAL DECISION

**STOPPING for approval.** This task cannot be completed as specified because none of the 3 call sites have a `craft` variable. The current `establish_from_craft` interface is incompatible with all 3 scenarios.

Recommendation: Extend SettlementDeploymentService with additional class methods rather than forcing craft into non-craft contexts.
