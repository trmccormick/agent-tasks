# Synthesis Report: Precursor Mission Manifest Hardware Mapping

**Date**: 2026-07-17  
**Task**: 2026-07-17-HIGH-FEATURE-PRECURSOR-MISSION-MANIFEST.md  
**Status**: In Progress  

## Analysis

### Objective
Create `precursor_mission_manifest_v1.json` mapping all required hardware to each of the 7 precursor mission phases. This drives procurement and delivery scheduling for the full interplanetary supply chain.

### Key Design Decisions

1. **Phase IDs must match profile exactly** — The manifest's `phase_id` values must correspond to the phase IDs in `precursor_mission_profile_v1.json`. The 7 phases are:
   - `gcc_mining` — GCC mining satellite
   - `initial_hlt_landings` — 3× HLT landings (habitat, ISRU, tank farm)
   - `power_grid_deployment` — RTG + solar arrays + umbilical hub
   - `titan_delivery` — Atmosphere harvester ×2 (N2 + CH4 → Luna)
   - `venus_delivery` — Atmosphere harvester ×2 (CO2 + N2 → Luna)
   - `luna_isru_production` — TEU/PVE equipment + manufacturing pipeline
   - `l1_leo_supply` — Inflatable depot modules + docking hardware

2. **Transit timing must match profile** — Titan = 730 days, Venus = 400 days

3. **Venus refuel dependency** — Must include source options: titan_return, earth_import, luna_local_production

4. **No world-name hardcoding** — Use planet properties pattern for gas giant/terrestrial classification

5. **Follow manifest_v2 schema** — Reference existing `lunar_precursor_manifest_v2.json` structure

### Files to Read
- `galaxy_game/data/json-data/missions_v2/manifests/lunar_precursor_manifest_v2.json` — schema reference
- `galaxy_game/data/json-data/missions/tasks/gcc_sat_mining_deployment/` — GCC hardware reference
- `data/json-data/missions_v2/profiles/precursor_mission_profile_v1.json` — phase IDs to match

### Implementation Plan
1. Read reference manifest for schema pattern
2. Read profile for exact phase_ids
3. Create precursor_mission_manifest_v1.json at correct path
4. Validate JSON with Ruby
5. Commit changes

## Risks / Gotchas
- Manifest_v2 schema may have changed since last reference — verify structure
- Profile phase_ids must match exactly — any mismatch is a gap to flag
- No world-name hardcoding allowed — must use planet properties pattern
