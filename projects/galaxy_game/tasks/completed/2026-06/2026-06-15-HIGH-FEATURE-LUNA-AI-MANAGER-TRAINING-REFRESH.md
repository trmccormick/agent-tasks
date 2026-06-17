---
status: completed
priority: HIGH
type: feature
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
completed_at: "2026-06-15"
---

# TASK: Luna AI Manager Training Refresh
**Status**: COMPLETED
**Priority**: HIGH
**Type**: feature
**Created**: 2026-06-15
**Last Updated**: 2026-06-15

---

## Agent Assignment

**Assigned To**: Qwen3.5-27B local (primary)
**Why This Agent**: File reading + JSON updates + rake task execution — local capability
**Local attempts before cloud**: 0 — try local first
**Supervision Level**: Watched carefully

---

## Context

The AI Manager was trained in January 2026 on mission profile JSON files. The `lunar_pattern`
in `mission_profile_patterns.json` is hollow — `equipment_requirements: {}`, `economic_model: {}`.
The training analyzer failed to load the Luna manifest due to a path resolution bug (underscore
vs hyphen mismatch) and `luna_settlement_patterns.json` was never fed into training.

Since January, the worldhouse concept was added (sealed lava tube/geological feature as early
settlement location). The AI Manager has no awareness of:
- Worldhouse construction as Luna's primary expansion path
- Luna's correct economic role (extraction outpost, not self-sufficient colony)
- Luna's mandatory infrastructure status (LDC-owned, must exist for GCC system)
- tasks_v2 as the runtime option library

This task refreshes Luna's training data using the full available corpus so the AI Manager
has meaningful behavioral substance to draw from when the Luna settlement is seeded.

**CRITICAL CONSTRAINTS:**
- DO NOT overwrite `learned_patterns.json` — January success rates must be preserved
- DO NOT delete existing patterns in `mission_profile_patterns.json` — extend only
- DO NOT run the training script until all data preparation steps are complete
- This is a middle-path approach: Luna gets properly informed bootstrap while
  architecture moves toward data-driven condition matching for all other worlds

**Relevant Architecture Docs:**
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `research/AI-MANAGER-TRAINING-PIPELINE-FINDINGS.md` — full audit findings
- `research/LUNA-MVP-SIMULATION-DESIGN.md` — Luna MVP design intent

---

## Problem Statement

**Current state of `lunar_pattern` in mission_profile_patterns.json:**
```json
"lunar_pattern": {
  "total_unit_count": 0,
  "equipment_requirements": {},
  "economic_model": {}
}
```

**Expected state after this task:**
- `equipment_requirements` populated from `luna_settlement_patterns.json`
- `economic_model` populated with extraction outpost ROI model
- `worldhouse_expansion` phase added reflecting lava tube sealing path
- `mandatory_infrastructure: true` flag added
- `economic_role: "extraction_outpost"` added
- tasks_v2 viable options mapped to Luna conditions

**Current learned_patterns.json (PRESERVE EXACTLY):**
```json
"lunar_precursor_lunar": { "success_rate": 0.92 }
"lunar-precursor_lunar": { "success_rate": 0.92 }
"venus_pattern": { "success_rate": 0.88 }
"venus_atmospheric_elevators": { "success_rate": 0.85 }
"venus_forge_operations": { "success_rate": 0.78 }
```

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Section |
|---|---|---|
| `data/json-data/ai_manager/mission_profile_patterns.json` | Pattern library | `lunar_pattern` — populate hollow fields |

### Reference Files — read but do not edit (source data)
| File | Why You Need It |
|---|---|
| `data/json-data/ai_manager/settlement-patterns/luna_settlement_patterns.json` | Real Luna condition + equipment data |
| `data/json-data/missions/luna_base_establishment/luna_base_establishment_profile_v1.json` | Mission phases + economic role |
| `data/json-data/missions/tasks_v2/` | Runtime option library — identify Luna-viable tasks |
| `data/json-data/ai_manager/learned_patterns.json` | DO NOT TOUCH — verify preserved |
| `app/models/structures/worldhouse.rb` | Worldhouse model — understand population_capacity formula |
| `app/models/celestial_bodies/features/lava_tube.rb` | LavaTube model — understand can_pressurize? |

### Migration (if needed)
- [x] No migration needed — JSON data file updates only

---

## Implementation Steps

### Step 1 — Read all source files before making any changes

Read these files in order and take notes:

1. `data/json-data/ai_manager/mission_profile_patterns.json` — full current state
2. `data/json-data/ai_manager/settlement-patterns/luna_settlement_patterns.json` — equipment + economic data
3. `data/json-data/missions/luna_base_establishment/luna_base_establishment_profile_v1.json` — phases + economic role
4. `data/json-data/ai_manager/learned_patterns.json` — confirm success rates to preserve
5. Read 5 tasks_v2 files to understand option structure:
   - `task_deploy_solar_rig.json`
   - `task_deploy_comms_equipment.json`
   - `task_deploy_car_robots.json`
   - `task_attach_solar_panels.json`
   - Any worldhouse or pressurization related tasks if they exist

Also check if worldhouse tasks exist:
```bash
find ~/Documents/git/galaxyGame/data/json-data/missions/tasks_v2 -name "*worldhouse*" -o -name "*lava*" -o -name "*pressur*" 2>/dev/null
```

### Step 2 — Map Luna conditions to tasks_v2 options

Based on Luna's physical conditions, identify which tasks_v2 options are viable:

**Luna conditions:**
- Gravity: 0.16g
- Atmosphere: none (vacuum)
- Surface: regolith, water ice at poles, lava tubes present
- Power: nuclear primary, solar secondary (2-week day/night cycle)
- ISRU: O₂ 25 kg/hr, H₂O 15 kg/hr, 200 kW power generation
- Geological features: lava tubes suitable for worldhouse conversion

**For each tasks_v2 file read, note:**
- Is this task viable on Luna given conditions above? (Y/N)
- What parameters would change for Luna vs other worlds?
- Does it reference any world-specific assumptions that need noting?

### Step 3 — Populate lunar_pattern in mission_profile_patterns.json

Update the hollow `lunar_pattern` with condition data from source files.
Frame as conditions under which option clusters succeed, NOT Luna-locked rules.

Add these fields to `lunar_pattern`:

```json
"lunar_pattern": {
  "description": "Conditions observed during lunar precursor operations — extraction outpost model",
  "economic_role": "extraction_outpost",
  "mandatory_infrastructure": true,
  "organization_affiliation": "LDC",
  "import_dependent": true,
  "environmental_conditions": {
    "gravity_g": 0.16,
    "atmosphere": "vacuum",
    "day_night_cycle_hours": 708,
    "radiation_environment": "high_surface_shielding_required",
    "thermal_range_celsius": [-173, 127],
    "geological_features": ["lava_tubes", "craters", "regolith"]
  },
  "equipment_requirements": {
    "power": ["nuclear_power_plant", "compact_solar_panel"],
    "life_support": ["oxygen_generator", "water_extractor"],
    "isru": ["regolith_processor", "helium3_mining_rig"],
    "habitation": ["modular_habitat_unit"],
    "mobility": ["surface_rover", "CAR-300"],
    "communications": ["comms_equipment"],
    "construction": ["solar_expansion_rig"]
  },
  "isru_capabilities": {
    "oxygen_production_rate_kg_hr": 25,
    "water_production_rate_kg_hr": 15,
    "power_generation_kw": 200,
    "regolith_processing": true,
    "helium3_extraction": true
  },
  "economic_model": {
    "revenue_streams": ["lox_sales", "structural_materials_export", "helium3_sales", "research_data"],
    "primary_customers": ["l1_station_construction", "cycler_resupply"],
    "import_ratio": 0.4,
    "critical_imports": ["food", "water", "oxygen", "specialized_equipment"],
    "break_even_timeline_days": 360,
    "gcc_flow_model": "subsidy_to_positive",
    "ldc_backstop": true
  },
  "expansion_model": {
    "primary_expansion_path": "worldhouse",
    "expansion_trigger": "positive_gcc_flow",
    "worldhouse_method": "lava_tube_sealing",
    "population_model": {
      "surface_outpost_max": 12,
      "crew_rotation_days": 180,
      "population_source": "earth_orbital_station_transfers",
      "permanent_surface_population": false,
      "gravity_constraint": "0.16g_long_term_health_risk"
    },
    "worldhouse_population_capacity": "calculated_from_enclosed_volume"
  },
  "tasks_v2_viable_options": [],
  "total_unit_count": 8,
  "phase_structure": {
    "total_phases": 4,
    "phases": [
      {"id": "precursor_deployment", "name": "Precursor Deployment", "duration_days": 30},
      {"id": "isru_establishment", "name": "ISRU Infrastructure Setup", "duration_days": 60},
      {"id": "habitat_construction", "name": "Habitat Construction", "duration_days": 90},
      {"id": "gcc_banking", "name": "GCC Banking Establishment", "duration_days": 60}
    ]
  }
}
```

For `tasks_v2_viable_options` — populate with the task_ids you identified as viable
in Step 2. Example format:
```json
"tasks_v2_viable_options": [
  {"task_id": "deploy_solar_rig", "viability": "high", "note": "Solar secondary to nuclear"},
  {"task_id": "deploy_comms_equipment", "viability": "high", "note": "Essential for uplink"},
  {"task_id": "deploy_car_robots", "viability": "high", "note": "Surface ops in vacuum"}
]
```

### Step 4 — Fix duplicate key in learned_patterns.json

The file has two entries for the same pattern with different key formats:
- `"lunar_precursor_lunar"` (underscore)
- `"lunar-precursor_lunar"` (hyphen-underscore mix)

Both have identical data. Remove the hyphenated duplicate — keep only
`"lunar_precursor_lunar"`. Verify success_rate 0.92 is preserved on the kept entry.

This is safe — the duplicate was created by a path resolution bug in January training.
The underscore version is the canonical key.

### Step 5 — Run MissionProfileAnalyzer to re-extract patterns

Find and run the mission profile analyzer rake task:
```bash
find ~/Documents/git/galaxyGame -name "*.rake" | xargs grep -l "mission_profile\|analyze_all" 2>/dev/null
```

Run the analyzer (read-only extraction — does NOT overwrite learned_patterns.json):
```bash
docker exec -it web bash -c 'unset DATABASE_URL && bundle exec rake ai_manager:analyze_mission_profiles 2>&1 | tail -20'
```

If the rake task name is different, find the correct one and run it.
If the analyzer fails, document the error and stop — do not proceed to Step 6.

### Step 6 — Verify learned_patterns.json preserved

After running the analyzer:
```bash
cat ~/Documents/git/galaxyGame/data/json-data/ai_manager/learned_patterns.json
```

Confirm ALL of these are still present with original success rates:
- `lunar_precursor_lunar: 0.92`
- `venus_pattern: 0.88`
- `venus_atmospheric_elevators: 0.85`
- `venus_forge_operations: 0.78`

If ANY are missing or changed — STOP IMMEDIATELY. Do not commit. Escalate to human.

### Step 7 — Run AI Manager suite

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/ 2>&1 | tail -10'
```

Expected: 746 examples, ≤3 failures (flaky only), 4 pending

### Step 8 — Synthesis Report

```
SYNTHESIS REPORT
Files changed:
- mission_profile_patterns.json — lunar_pattern populated
- learned_patterns.json — duplicate key removed

LUNAR PATTERN POPULATED WITH:
- environmental_conditions: [list]
- equipment_requirements: [list]
- economic_model: [summary]
- expansion_model: worldhouse via lava tube sealing
- tasks_v2_viable_options: [count] options mapped

LEARNED PATTERNS PRESERVED:
lunar_precursor_lunar: [rate]
venus_pattern: [rate]
venus_atmospheric_elevators: [rate]
venus_forge_operations: [rate]

TASKS_V2 VIABLE OPTIONS IDENTIFIED: [count]
WORLDHOUSE TASKS IN TASKS_V2: [found/not found]

ANALYZER RUN RESULT: [success/failure + output]

TEST RESULT: X examples, Y failures

GAPS IDENTIFIED:
[any missing tasks_v2 files, missing data, or architectural gaps discovered]

READY TO COMMIT? — waiting for approval
```

---

## Acceptance Criteria
- [ ] `lunar_pattern.equipment_requirements` populated (not empty)
- [ ] `lunar_pattern.economic_model` populated with extraction outpost model
- [ ] `lunar_pattern.expansion_model` includes worldhouse path
- [ ] `lunar_pattern.mandatory_infrastructure: true` set
- [ ] `lunar_pattern.tasks_v2_viable_options` populated with viable task list
- [ ] Duplicate `lunar-precursor_lunar` key removed from learned_patterns.json
- [ ] `lunar_precursor_lunar: 0.92` success rate preserved
- [ ] All other January success rates preserved
- [ ] MissionProfileAnalyzer ran successfully (or failure documented)
- [ ] ai_manager suite: 746 examples, ≤3 failures (flaky only), 4 pending

---

## Stop Conditions — escalate immediately if:
- `learned_patterns.json` shows ANY changes to success rates after analyzer run
- MissionProfileAnalyzer overwrites the file instead of extending it
- Any ai_manager spec that was passing now fails
- tasks_v2 has NO viable options for Luna conditions — document and escalate
- Worldhouse tasks completely absent from tasks_v2 — note gap, continue, document

---

## Commit Instructions
Host only — never inside Docker:
```bash
git add data/json-data/ai_manager/mission_profile_patterns.json
git add data/json-data/ai_manager/learned_patterns.json
git commit -m "feat: Luna AI Manager training refresh — populate lunar_pattern with condition data, worldhouse expansion path, tasks_v2 options, remove duplicate key"
git push
```

---

## Dependencies
**Blocked by**: `2026-06-15-HIGH-BUG-FIX-AI-MANAGER-DECISION-TREE-DEAD-STEPS.md` — fix decision tree first
**Blocks**: Task 3 (StateAnalyzer extension), Task 4 (Luna settlement seeding)
**Related tasks**: `2026-06-12-MEDIUM-RESEARCH-AI-MANAGER-TRAINING-PIPELINE-AUDIT.md`

---

## Completion Report
*Filled in by implementing agent*

**Completed by**:
**Completion date**:
**Final test result**:

### What was changed

### Issues discovered

### Follow-up tasks needed

### Lessons learned

---

## Handoff Summary
HANDOFF SUMMARY: mission_profile_patterns.json + learned_patterns.json | lunar_pattern populated, duplicate removed | Dispatch Task 3 (StateAnalyzer extension) next
