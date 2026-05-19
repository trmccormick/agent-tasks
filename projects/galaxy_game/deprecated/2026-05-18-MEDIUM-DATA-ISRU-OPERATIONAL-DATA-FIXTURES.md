---
status: INVALID / CLOSED
priority: MEDIUM
type: data
system_domain: TERRA_SIM | CONTROLLERS | UNITS
mvp_alignment: ISRU_PRODUCTION
local_worker_safe: true
---

# TASK: ISRU Operational Data JSON Fixtures
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: data
**Created**: 2026-05-18
**Last Updated**: 2026-05-18

---

## Local Worker Triage Report
*Filled in by local model (Ollama via Continue) during backlog review*
*Local models read task files only — they cannot run commands or access the DB*

- **Template Conformance**: PASS — all sections present
- **Docker Wrapper Check**: N/A — data file creation only, no execution required
- **MVP Alignment**: VALID — realistic operational parameters critical for testing
- **MVP Impact Note**: ISRU tests cannot be validated without realistic regolith composition and TEU/PVE parameters
- **Action Line**: READY FOR LOCAL OR CLOUD AGENT (file creation only)

---

## Agent Assignment

**Assigned To**: [GPT-4.1 0x, O3-mini 0x, or Local Ollama]
**Why This Agent**: Requires domain knowledge of ISRU operations but no command execution needed
**Supervision Level**: standard

**Supervision Legend**:
- Watched carefully = 0x/0.33x cloud agents and all local models
- Standard = 0.33x agents on well-specified tasks
- Autonomous OK = 1x agents only

> Local Ollama agents: you can complete this task entirely (file creation only).
> You cannot run commands but can create/edit files via Continue.

---

## Context
ISRU Track A requires realistic operational data for testing and simulation. This includes:
- Thermal Extraction Unit (TEU) operating parameters (temperature, pressure, throughput)
- Plasma Volume Excavator (PVE) specifications (energy requirements, extraction rates)
- Lunar regolith compositions (realistic volatile content percentages)
- Tank farm system capacities and pressure limits
- Lava tube atmospheric harvesting conditions (pressure gradients, gas concentrations)

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — data contract requirements
- `config/initializers/poro_space.rb` — how operational data is loaded into PORO pools
- `app/services/isru_track_a.rb` — understand what parameters the service expects

**Reference Material (if available)**:
- NASA ISRU mission documentation for realistic parameter ranges
- Existing TerraSim operational data patterns in `config/data/*`

---

## Problem Statement
**Current behavior**: No operational data JSON fixtures exist for ISRU Track A. Tests must use hardcoded or placeholder values that don't reflect realistic lunar conditions.

**Expected behavior**: 
- JSON fixtures with realistic TEU/PVE operating parameters
- Multiple regolith composition scenarios (high/low volatile content)
- Tank farm system specifications matching lunar habitat requirements
- Lava tube atmospheric pressure and gas concentration data

**Why this matters**: Without realistic operational data, ISRU Track A tests cannot validate actual production capacity or identify performance bottlenecks before Luna deployment.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|-|
| `config/data/isru_track_a_operational_params.json` | TEU/PVE operating parameters | New file to create |
| `config/data/regolith_compositions.json` | Lunar regolith volatile content scenarios | New file to create |
| `config/data/tank_farm_specifications.json` | Tank farm system capacities and limits | New file to create |
| `config/data/lava_tube_atmospheric_data.json` | Lava tube pressure/gas concentration data | New file to create |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|-|
| `app/services/isru_track_a.rb` | Understand expected parameter structures |
| `config/data/*` | Existing operational data patterns to follow |
| `docs/new_agent/rules/DECISIONS.md` | Data contract requirements |

### Migration (if needed)
- [x] No migration needed

---

## Implementation Steps

> 0x/0.33x agents: follow these steps exactly in order.
> 1x agents: use as a guide, apply judgment.
> Local Ollama agents: you can complete this task entirely.

### Step 1 — Research Realistic ISRU Parameters
Gather realistic values for:
- TEU operating temperature: 800–1200°C typical range
- TEU pressure: 0.1–1.0 bar vacuum conditions
- PVE energy consumption: 5–15 kW per unit depending on regolith density
- Regolith volatile content: 0.1–5% water equivalent, higher in polar regions
- Oxygen extraction yield: 2–8 kg O₂ per ton of regolith processed
- Tank farm pressures: 3–7 bar for compressed gas storage
- Lava tube atmospheric pressure: 0.01–0.1 mbar depending on depth and venting

> Use NASA ISRU mission parameters if available, otherwise use scientifically plausible ranges.

### Step 2 — Create TEU/PVE Operational Parameters JSON
Create `config/data/isru_track_a_operational_params.json`:

```json
{
  "thermal_extraction_unit": {
    "operating_temperature_range_celsius": {
      "min": 800,
      "max": 1200,
      "optimal": 1000
    },
    "pressure_range_bar": {
      "min": 0.1,
      "max": 1.0,
      "optimal": 0.5
    },
    "throughput_rate_ton_per_hour": {
      "min": 0.5,
      "max": 2.0,
      "optimal": 1.2
    },
    "energy_consumption_kw": {
      "min": 3.5,
      "max": 6.0,
      "typical": 4.8
    },
    "extraction_efficiency_percent": {
      "min": 75,
      "max": 90,
      "target": 85
    }
  },
  "plasma_volume_excavator": {
    "operating_temperature_kelvin": {
      "min": 15000,
      "max": 25000,
      "optimal": 20000
    },
    "energy_consumption_kw": {
      "min": 8.0,
      "max": 15.0,
      "typical": 11.5
    },
    "extraction_rate_liters_per_hour": {
      "min": 50,
      "max": 150,
      "optimal": 100
    },
    "cooling_requirements_kw": {
      "min": 2.0,
      "max": 4.0,
      "typical": 3.0
    }
  }
}
```

### Step 3 — Create Regolith Composition Scenarios
Create `config/data/regolith_compositions.json` with multiple scenarios:

```json
{
  "scenarios": {
    "equatorial_regolith_low_volatiles": {
      "location": "Equatorial region",
      "water_equivalent_weight_percent": 0.1,
      "hydrogen_content_ppm": 50,
      "oxygen_extractable_percent": 2.5,
      "processing_time_hours_per_ton": 2.5
    },
    "polar_regolith_high_volatiles": {
      "location": "Polar region (shadowed crater)",
      "water_equivalent_weight_percent": 5.0,
      "hydrogen_content_ppm": 2500,
      "oxygen_extractable_percent": 8.0,
      "processing_time_hours_per_ton": 1.8
    },
    "lunar_mare_regolith_medium_volatiles": {
      "location": "Maria basalt",
      "water_equivalent_weight_percent": 0.5,
      "hydrogen_content_ppm": 200,
      "oxygen_extractable_percent": 4.0,
      "processing_time_hours_per_ton": 2.0
    }
  }
}
```

### Step 4 — Create Tank Farm System Specifications
Create `config/data/tank_farm_specifications.json`:

```json
{
  "tank_farm_system": {
    "total_capacity_liters": 5000,
    "individual_tank_capacity_liters": 500,
    "number_of_tanks": 10,
    "pressure_limits": {
      "min_operating_pressure_bar": 3.0,
      "max_safe_pressure_bar": 7.0,
      "relief_valve_setpoint_bar": 6.5
    },
    "gas_types_supported": ["oxygen", "hydrogen", "nitrogen"],
    "temperature_range_kelvin": {
      "min": 200,
      "max": 350
    },
    "transfer_rate_liters_per_minute": 100
  }
}
```

### Step 5 — Create Lava Tube Atmospheric Data
Create `config/data/lava_tube_atmospheric_data.json`:

```json
{
  "lava_tube_atmosphere": {
    "depth_meters": {
      "typical_range": [10, 100],
      "optimal_for_harvesting": 50
    },
    "pressure_conditions": {
      "ambient_pressure_mbar": 0.05,
      "vent_pressure_gradient_mbar_per_meter": 0.002,
      "max_safe_pressure_mbar": 0.1
    },
    "gas_composition_percent": {
      "water_vapor": 45,
      "carbon_dioxide": 30,
      "oxygen": 15,
      "hydrogen": 8,
      "other_traces": 2
    },
    "harvesting_efficiency_percent": {
      "min": 60,
      "max": 85,
      "typical": 75
    }
  }
}
```

### Step 6 — Validate JSON Schema
Ensure all files:
- Are valid JSON (no trailing commas, proper quoting)
- Use consistent naming conventions (snake_case for keys)
- Include units in field names or comments
- Have realistic value ranges based on ISRU research

### Step 7 — Test Data Loading
Add a simple test to verify data can be loaded:
```ruby
# spec/support/isru_data_helpers.rb
def load_isru_operational_params
  JSON.parse(Rails.root.join('config/data/isru_track_a_operational_params.json').read)
end
```

---

## Acceptance Criteria
- [ ] `config/data/isru_track_a_operational_params.json` created with TEU/PVE parameters
- [ ] `config/data/regolith_compositions.json` created with 3+ realistic scenarios
- [ ] `config/data/tank_farm_specifications.json` created with system limits
- [ ] `config/data/lava_tube_atmospheric_data.json` created with pressure/gas data
- [ ] All JSON files are valid and parseable
- [ ] Values are scientifically plausible for lunar ISRU operations
- [ ] Data can be loaded by RSpec factories (if helpers created)

---

## Stop Conditions — escalate to user immediately if:
- Cannot find credible source data for realistic ISRU parameters after 1 hour of research
- Existing `app/services/isru_track_a.rb` has unexpected parameter structures that don't match proposed JSON
- More than 2 hours spent on this task without completing all 4 files

---

## Commit Instructions
Run git commands on **host**, not inside container:
```bash
git add config/data/isru_track_a_operational_params.json config/data/regolith_compositions.json config/data/tank_farm_specifications.json config/data/lava_tube_atmospheric_data.json
git commit -m "[data]: ISRU operational data JSON fixtures — realistic TEU/PVE parameters, regolith compositions, tank farm specs, lava tube atmospheric data"
git push
```

---

## Documentation
- [ ] Add comments in JSON files explaining source of parameter ranges (e.g., "NASA ISRU mission data", "based on lunar regolith analysis")
- [ ] Document any assumptions made about missing data points

---

## Dependencies
**Blocked by**: 
- None (independent task)

**Blocks**: 
- 2026-05-18-HIGH-FEATURE-ISRU-TRACK-A-RSPEC-SUITE.md (RSpec tests need realistic test data)
- 2026-05-18-MEDIUM-REFACTOR-ISRU-UNIT-MANAGER-INTEGRATION.md (may need operational parameters for refactoring)

**Related tasks**: 
- 2026-05-18-HIGH-ARCHITECTURE-ISRU-TRACK-A-SPEC-GAP-AUDIT.md (parent audit task)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was changed
- Created 4 JSON fixture files with realistic ISRU operational parameters
- Documented parameter ranges and assumptions in comments

### Parameter Sources
- [List sources used for realistic values, e.g., NASA papers, existing TerraSim patterns]

### Issues encountered
- [Any problems with data consistency or missing information]

---

"CLOSED AS INVALID on 2026-05-18. This task contains severe architectural drift. It references a fictional 'isru_track_a.rb' and demands files in 'config/data/'. Real data structures for TEU, PVE, and regolith extraction are already fully implemented as blueprints and operational data lookups within the 'json-data/extractors/' domain. No further action required."