# 2026-07-09-BUGFIX-HRV400-BLUEPRINT-AND-ESCALATION-SERVICE

## Summary
Fix `EscalationService#create_automated_harvester` to use correct HRV-400 blueprint ID and load operational_data from JSON via UnitLookupService. Also create missing HRV-400 blueprint and operational data files in active data directory.

## Problem
- `create_automated_harvester` creates robots with `unit_type: "robot"` (ghost type, no blueprint exists)
- Operational data is hardcoded instead of loaded from JSON blueprints
- HRV-400 blueprint only exists in `old-code/`, not in active data directory

## Files to Create
1. `data/json-data/blueprints/units/robots/resource/hrv_400_resource_harvester_mk1_bp.json` — HRV-400 blueprint
2. `data/json-data/operational_data/units/robots/resource/hrv_400_resource_harvester_mk1_data.json` — operational defaults

## Files to Edit
1. `galaxy_game/app/services/ai_manager/escalation_service.rb` — refactor create_automated_harvester
2. `galaxy_game/spec/services/ai_manager/escalation_service_spec.rb` — update expectations

## Implementation Plan
1. Create HRV-400 blueprint (adapt from old-code, match active structure)
2. Create operational data file with defaults
3. Refactor EscalationService to use UnitLookupService
4. Update spec to mock UnitLookupService and expect correct unit_type
5. Run specs to verify

## MVP Alignment
Earth-delivered equipment for Luna Phase 5 MVP — direct creation is correct. No manufacturing hierarchy needed yet.
