---
status: not-started
priority: HIGH
type: feature
created: 2026-06-22
last-updated: 2026-06-22
phase: 6+
---

# Digital Twin Service — Terraforming Simulation Schema

## Context
`DigitalTwinService` enables rapid testing of terraforming scenarios without running full game simulation. It accepts a world state and terraforming plan, runs the plan through a simulation engine, and returns projected outcomes (atmospheric composition, biome success, resource requirements, timeline).

This is foundational for "SimEarth-style" terraforming planning where AI or admins test multiple strategies and receive feasibility metrics before committing resources.

**Reference Code**:
- [digital_twin_service.rb](galaxy_game/app/services/digital_twin_service.rb) — exists but needs schema specification

## Problem Statement
Digital Twin service needs formal schema to:
- Define input format (world state, terraforming plan)
- Validate simulation parameters
- Define output format (success probability, timelines, resource costs)
- Enable deterministic testing and caching
- Support incremental scenario refinement

## Scope
- Define JSON schema for digital twin simulation input/output
- Implement validation layer
- Create example scenarios
- Document simulation semantics
- RSpec validation tests (no simulation engine yet)

## Target Files
- `data/schemas/digital_twin_simulation.schema.json` (NEW)
- `app/services/digital_twin_service.rb` (add schema validation)
- `spec/services/digital_twin_service_spec.rb` (NEW — schema validation tests)
- `data/examples/digital_twin_scenarios/` (NEW — reference scenarios)

## Acceptance Criteria
- [ ] Input schema defined (world state, plan, parameters)
- [ ] Output schema defined (success metrics, timelines, costs)
- [ ] Validation implemented and tested
- [ ] 3-5 example scenarios documented
- [ ] Service rejects invalid inputs with clear error messages
- [ ] RSpec: >90% coverage for schema validation paths

## Implementation Steps
1. Define input schema:
   - World state (atmospheric params, biome distribution, resources)
   - Terraforming plan (steps, timing, resources allocated)
   - Simulation parameters (confidence levels, uncertainty ranges)
2. Define output schema:
   - Success probability (0-100%)
   - Projected timelines (day ranges for each step)
   - Resource cost breakdown (by material type)
   - Risk factors (what could go wrong)
3. Implement validator in `DigitalTwinService`
4. Create reference scenarios (Mars atmosphere, Venus transformation, Luna colony)
5. Write RSpec for all validation paths

## Dependencies
- Requires: Material/resource system already defined
- Does NOT require: Actual simulation engine (schema-only task)
- Future: Will integrate with simulation engine once schema is finalized

## Notes
- This is schema/contract definition — no simulation logic yet
- Simulation engine implementation is separate task
- Schema should be versioned for future expansion
- Example scenarios should cover edge cases (impossible plans, extreme uncertainty)
