# HIGH-FEATURE-ORBITAL-MECHANICS-DATA-LAYER

## Metadata
```yaml
task_id: 2026-08-19-HIGH-FEATURE-ORBITAL-MECHANICS-DATA-LAYER
name: Orbital Mechanics Data Layer
priority: high
status: active
date_created: 2026-08-19
date_completed: 2026-08-19
author: strategist (Tracy)
description: Fix orbital mechanics data pipeline — known data preserved, StarSim fills gaps, survey generates plausible values for unknowns
intended_usage: Implement the three-layer orbital mechanics data system
```

## Status
**Phase 1-4 COMPLETE.** Phase 5 (TransitEngine integration) is pending.

### Completed Phases
| Phase | Description | Commit |
|-------|-------------|--------|
| Phase 1 | JSON data entry (Venus, Earth, Mars in sol-complete.json + sol.json) | — |
| Phase 2 | Database schema + SystemBuilderService fix | `c9d44ca4` |
| Phase 3 | mean_anomaly generation in OrbitalParametersGenerator | `1f8df564` |
| Phase 4 | Survey discovery mechanic in TaskExecutionEngine | `683327b5` |

## Context

### The Problem
Orbital elements (semi_major_axis, eccentricity, inclination, mean_anomaly) are needed for launch window calculation and transit timing. Currently:

1. **sol-complete.json** only has `orbital_elements` for Jupiter — Earth/Venus/Mars/Luna/Titan missing
2. **SystemBuilderService** explicitly strips `:orbital_elements` (line 255 of system_builder_service.rb) — discards known data
3. **Survey mechanic** is a stub (`perform_survey` at line 351 of task_execution_engine.rb) — doesn't generate values for unknowns

### The Architecture (Three-Layer Design)

**Layer 1: Known Data (Sol)**
- Real astronomical values where we have them (Earth/Venus/Mars/Luna/Titan orbital_elements)
- StarSim fills procedural gaps (mean_anomaly epoch, etc.) for bodies with partial data
- "Unknown" is a valid value — doesn't mean broken, means surveyable

**Layer 2: Procedural Generation (StarSim)**
- StarSim takes known data → generates playable details procedurally
- Local Bubble systems are more incomplete than Sol — need even more procedural generation
- `OrbitalParametersGenerator` already creates semi_major_axis, eccentricity, inclination, orbital_period_days
- **Add mean_anomaly** to OrbitalParametersGenerator (random epoch for position propagation)
- Remove `:orbital_elements` from `special_keys_to_exclude` in SystemBuilderService

**Layer 3: Survey Discovery (Unknown)**
- When a player surveys a body with "Unknown" or missing orbital data, generate plausible values
- Survey results persist (System Survey History — documented in `08_ai_intelligence.md`)
- No redundant scanning of already-surveyed bodies

### Design Precedent
- `AUTOMATIC_TERRAIN_GENERATOR.md`: "For Sol worlds, the system prioritizes real NASA data"
- `wh-expansion.md`: "System Survey History remembers detailed survey results to avoid redundant scanning"
- Pattern: known data first → procedural gaps → survey fills remaining unknowns

## Implementation Steps

### Step 1: Add Known Orbital Elements to sol-complete.json ✅ COMPLETE
Added `orbital_elements` for Earth, Venus, Mars in both sol-complete.json and sol.json using real astronomical values.

**Earth:**
```json
"orbital_elements": {
  "semi_major_axis": 149597870700.0,
  "eccentricity": 0.0167,
  "inclination": 0.0,
  "mean_anomaly": 357.52
}
```

**Venus:**
```json
"orbital_elements": {
  "semi_major_axis": 108208000000.0,
  "eccentricity": 0.0068,
  "inclination": 3.39,
  "mean_anomaly": 50.12
}
```

**Mars:**
```json
"orbital_elements": {
  "semi_major_axis": 227943800000.0,
  "eccentricity": 0.0934,
  "inclination": 1.85,
  "mean_anomaly": 19.38
}
```

**Luna (relative to Earth):**
```json
"orbital_elements": {
  "semi_major_axis": 384400000.0,
  "eccentricity": 0.0549,
  "inclination": 5.14,
  "mean_anomaly": 115.34
}
```

**Titan (relative to Saturn):**
```json
"orbital_elements": {
  "semi_major_axis": 1221870000.0,
  "eccentricity": 0.0288,
  "inclination": 0.33,
  "mean_anomaly": 0.0
}
```

**Note:** Luna and Titan were already present in sol-complete.json from prior work. Jupiter was already present.

### Step 2: Stop SystemBuilderService from Discarding Known Data ✅ COMPLETE
Removed `:orbital_elements` from `special_keys_to_exclude` in `system_builder_service.rb:255`.

**Before:**
```ruby
:geological_features, :magnetosphere, :magnetic_field_strength, :rotation_period, :orbital_elements, # Additional attributes
```

**After:**
```ruby
:geological_features, :magnetosphere, :magnetic_field_strength, :rotation_period, # Additional attributes
```

Also added:
- Migration `20260819120107_add_orbital_elements_to_celestial_bodies.rb` — adds JSONB column
- `store_accessor :orbital_elements, :semi_major_axis, :eccentricity, :inclination, :mean_anomaly` in CelestialBody model

### Step 3: Add mean_anomaly to OrbitalParametersGenerator ✅ COMPLETE
Added `mean_anomaly` generation for bodies without known orbital_elements.

**Added to `OrbitalParametersGenerator#generate`:**
```ruby
mean_anomaly: generate_mean_anomaly
```

**Added private method:**
```ruby
def generate_mean_anomaly
  rand(0.0..360.0).round(2) # Random epoch in degrees
end
```

### Step 4: Implement Survey Discovery for Unknown Bodies ✅ COMPLETE
Implemented `perform_survey(task)` in TaskExecutionEngine.

**Survey flow:**
1. Check if body has `orbital_elements` (from known data or StarSim)
2. If missing/unknown → use OrbitalParametersGenerator to create plausible values
3. Persist to body record (update DB with discovered orbital_elements)
4. Record in System Survey History (operational_data on mission or new survey_record association)
5. Return survey results to player

### Step 5: TransitEngine Reads Discovered Data
Once bodies have orbital_elements (from known data, StarSim, or survey), TransitEngine computes real phase angles and launch windows.

**TransitEngine reads:**
- `mean_anomaly` + `orbital_period` → propagate positions forward from epoch
- `semi_major_axis` + `eccentricity` → compute delta-v for transfer orbits
- `inclination` → plane change costs

## Stop Conditions
- [x] sol-complete.json has orbital_elements for Earth/Venus/Mars/Luna/Titan
- [x] SystemBuilderService no longer strips orbital_elements
- [x] OrbitalParametersGenerator generates mean_anomaly for procedural bodies
- [x] Survey mechanic populates unknown orbital data and persists results
- [ ] TransitEngine can read discovered orbital data

## Return Format
Report completion of each step with:
- Files modified
- Key changes made
- Any decisions or assumptions noted
