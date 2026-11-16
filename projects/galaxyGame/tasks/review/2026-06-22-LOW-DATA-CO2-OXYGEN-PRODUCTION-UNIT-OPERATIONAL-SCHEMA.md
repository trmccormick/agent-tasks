[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase5+/2026-06-22-LOW-DATA-CO2-OXYGEN-PRODUCTION-UNIT-OPERATIONAL-SCHEMA.md]
[RATIONALE: UI/monitoring or investigation task — not Luna sim prerequisite, cosmetic/post-MVP]
---
status: not-started
priority: LOW
type: data
created: 2026-06-22
last-updated: 2026-06-22
phase: 5+
---

# CO₂-O₂ Production Unit — Operational Data Schema

## Context
CO₂-to-O₂ production is a critical life support system for enclosed habitats. The unit consumes CO₂ (from atmosphere or waste), processes it through chemical reduction, and produces O₂ and byproducts. Operational data file defines processing rates, efficiency, resource requirements, and failure modes.

**Reference Data**:
- [co2_oxygen_production_data.json](galaxy_game/app/data/operational_data/units/life_support/co2_oxygen_production_data.json) — exists in logs, needs formal schema

## Problem Statement
CO₂-O₂ unit needs formal specification for:
- Processing rates (CO₂ consumed → O₂ produced)
- Power requirements and efficiency curves
- Maintenance intervals and failure modes
- Temperature/pressure operating ranges
- Integration with life support simulation

## Scope
- Define operational data schema
- Populate example configurations for multiple unit sizes
- Validate against existing game equipment system
- RSpec validation tests

## Target Files
- `data/schemas/life_support/co2_oxygen_production.schema.json` (NEW)
- `galaxy_game/app/data/operational_data/units/life_support/co2_oxygen_production_data.json` (update/formalize)
- `spec/data/life_support_operational_data_spec.rb` (NEW — validation tests)

## Acceptance Criteria
- [ ] Operational data schema defined (rates, power, maintenance, failure modes)
- [ ] 2-3 unit sizes configured (small habitat, settlement, industrial)
- [ ] Validation tests confirm data matches schema
- [ ] Integration confirmed with existing equipment system
- [ ] RSpec: Full coverage for data validation

## Implementation Steps
1. Define schema with:
   - Processing rates (kg CO₂/hr → kg O₂/hr)
   - Power consumption (kW baseline + load-based)
   - Efficiency rating (useful output / energy input)
   - Temperature/pressure operating ranges
   - Failure modes and MTBF (mean time between failures)
2. Populate operational data file with unit variants
3. Create loader/validator
4. Write RSpec for all validation paths

## Dependencies
- Requires: Equipment system and material definitions
- No other task dependencies

## Notes
- Operating efficiency should vary with maintenance state
- Byproducts (carbon, water) should be modeled
- Power draw should scale non-linearly with load
