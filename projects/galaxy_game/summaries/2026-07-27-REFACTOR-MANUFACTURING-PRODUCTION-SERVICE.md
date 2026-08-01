# 2026-07-27: Manufacturing::ProductionService Refactor — Synthesis Report

## Task Overview
Refactor `Manufacturing::ProductionService` to implement material consumption and logistics tracking while preserving existing PVE metrics calculation.

## Current State
- `ProductionService` is a PARTIAL STUB
- PVE metrics calculation works (verified)
- All logistics/consumption steps are TODOs/stubs
- `manufacture_component` returns stubbed PVE data only
- `run_unit_cycle` is completely unimplemented

## Reference Patterns
### Processing Service Pattern (app/services/manufacturing/processing.rb)
- Uses `ActiveRecord::Base.transaction` for atomic material consumption
- Validates materials before consuming (raise if insufficient)
- Consumes items in order: use entire items first, then partial last
- Byproducts created as new Item records with appropriate metadata
- Error handling: rescue => e, log error, re-raise

### MaterialRequestSystem Pattern (app/services/manufacturing/material_request_system.rb)
- `check_and_request` validates availability before proceeding
- `find_missing_materials` returns hash of {material => quantity_missing}
- Uses `Inventory.check(material)` for availability queries

## Implementation Plan

### Step 1: Audit PVE Logic (Read Only)
- PVE_DATA constant defines input/output ratios per cycle
- `input_processed_kg: 5.0` → `output_inert_waste_kg: 4.85` (main product)
- Also produces: `output_gases_kg: 0.05`, `output_water_kg: 0.10`
- Cycle count = `ceil(inert_req_kg / output_inert_waste_kg)`

### Step 2: Implement Material Consumption
In `manufacture_component`:
1. Calculate total material requirements from blueprint_data and target_units
2. Validate materials available in settlement inventory (follow Processing pattern)
3. Atomic consumption within `ActiveRecord::Base.transaction`
4. Raise error if insufficient materials (stop production, no partial state)

### Step 3: Implement Logistics Tracking
1. Create Job records for each production step (regolith extraction → PVE cycles → component production)
2. Update job status as cycle progresses (pending → running → completed/failed)
3. Handle byproducts: water, gases → inventory; inert waste → surface storage
4. Return comprehensive production report

### Step 4: Implement run_unit_cycle
1. Accept unit type and input material
2. Calculate yield based on PVE_DATA ratios
3. Return yield data with all output materials

## Key Design Decisions
1. **Transaction boundary**: Entire `manufacture_component` wrapped in single transaction
2. **Validation before consumption**: Check all materials first, then consume atomically
3. **Byproduct routing**: Gases/water → settlement inventory; inert waste → surface storage pile
4. **Job tracking**: One Job per production phase with status updates
5. **Error handling**: Raise on any failure; transaction rolls back everything

## Files to Edit
- `app/services/manufacturing/production_service.rb` — Primary implementation

## Verification
Run: `docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/manufacturing/production_service_spec.rb 2>&1 | tail -20'`
Expected: X examples, 0 failures
