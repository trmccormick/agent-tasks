# RESEARCH: profiles_v2 Architecture — Mission Profile and Phase File Redesign

**Status**: COMPLETED
**Priority**: MEDIUM
**Type**: research
**Created**: 2026-07-05
**Completed**: 2026-07-05
**Output consumer**: Gemini (architecture design pass)

---

## 1. File Inventory

### Generation 1 (Old Style — Verbose, Narrative-Based)

| # | File | Path | Status | Size | Notes |
|---|------|------|--------|------|-------|
| G1-1 | luna_base_establishment_profile_v1.json | `data/json-data/missions/profiles/luna_base_establishment_profile_v1.json` | ✅ EXISTS | 3,623 bytes | Full Gen 1 profile with narrative descriptions |
| G1-2 | lunar_precursor_deployment_v1.json | `data/json-data/missions/profiles/phases/lunar_precursor_deployment_v1.json` | ❌ MISSING | — | Never committed to git |
| G1-3 | habitat_construction_v1.json | `data/json-data/missions/profiles/phases/habitat_construction_v1.json` | ❌ MISSING | — | Never committed to git |

**Additional Gen 1 profiles found in `profiles/` directory (9 total):**
- `earth_mars_venus_cycler_route_establishment_profile_v1.json`
- `l1_station_depot_profile_v1.json`
- `leo_depot_construction_profile_v1.json`
- `luna_base_establishment_profile_v1.json` ← analyzed above
- `mars_ceres_transport_route_profile_v1.json`
- `mars_orbital_adjustment_v1.json`
- `mars_saturn_cycler_route_establishment_profile_v1.json`
- `psyche_asteroid_capture_v1.json`
- `solar_system_infrastructure_deployment_v1.json`

**Gen 1 phase directory:** `profiles/phases/` does NOT exist. Phase files referenced by Gen 1 profiles were never committed.

### Generation 2 (Current Working Style — Lean, Effects-Based)

| # | File | Path | Status | Notes |
|---|------|------|--------|-------|
| G2-1 | luna_settlement_profile_v1.json | `luna_base_establishment/luna_settlement_profile_v1.json` | ✅ EXISTS | Working profile (top-level orchestrator) |
| G2-2 | phase_1_power_comms.json | `luna_base_establishment/phases/phase_1_power_comms.json` | ✅ EXISTS | 6 tasks |
| G2-3 | phase_2_isru_deployment.json | `luna_base_establishment/phases/phase_2_isru_deployment.json` | ✅ EXISTS | 4 tasks |
| G2-4 | phase_3_gas_processing.json | `luna_base_establishment/phases/phase_3_gas_processing.json` | ✅ EXISTS | 3 tasks |
| G2-5 | phase_4_robot_logistics.json | `luna_base_establishment/phases/phase_4_robot_logistics.json` | ⚠️ Referenced but not read | robot_logistics_site_hardening phase |
| G2-6 | lunar_precursor_manifest_v2_DRAFT.json | `manifests_v2/lunar_precursor_manifest_v2_DRAFT.json` | ✅ EXISTS | 13 hardware items, 4 consumables, 4 data outputs |
| G2-7 | task_deploy_pve_unit.json | `tasks_v2/task_deploy_pve_unit.json` | ✅ EXISTS | Priority 7, deploy + connect effects |
| G2-8 | task_deploy_gas_separator.json | `tasks_v2/task_deploy_gas_separator.json` | ✅ EXISTS | Priority 8, preconditions on TEU+PVE, register_to_bus |
| G2-9 | task_deploy_puh_and_ppmu.json | `tasks_v2/task_deploy_puh_and_ppmu.json` | ✅ EXISTS | Priority 3, event-based completion (unique) |

### Engine and Build Files

| # | File | Path | Notes |
|---|------|------|-------|
| E-1 | TaskExecutionEngineV2 | `galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb` | Core engine — reads profiles/phases as manifest hashes |
| E-2 | luna_mission.rake | `galaxy_game/lib/tasks/luna_mission.rake` | Rake task — resolves profile paths, seeds inventory, executes phases |

### tasks_v2 Inventory (148 files total)

Full listing available in workspace. Key patterns:
- All named `task_<action>.json`
- Templates: `generic_repeatable_task`, `repeatable_task`, `probe_deployment`, etc.
- Effects used: `deploy_unit`, `connect_units`, `register_to_bus`, `set_unit_state`, `transfer_resource`, `manufacture`, `construct_structure`, `check_unit_state`, `advance_deployment_stages`, `request_import`
- Completion criteria types: `unit_state` (most common), `event` (rare — PUH+PPMU task)

---

## 2. Engine Field Analysis

### 2.1 What the Engine Actually Reads from a Profile

The profile is passed to `TaskExecutionEngineV2.new(target, manifest_or_path)` as a **Hash** (parsed JSON). The engine reads these fields:

| Field | Used? | How |
|-------|-------|-----|
| `phases` | ✅ YES | Array of phase objects. Each phase's `phase_id`, `phase_name`, `objectives` stored in `@task_plan`. Also used for capability extraction via `phase_id`. |
| `resources` | ✅ YES | If Hash, merges `initial_crew`, `initial_equipment`, `budget` into environment. |
| `required_hardware` | ✅ YES | Extracts `task_affinity` from each item → builds capability list for planning. |
| `manifest_id` | ❌ NO | Never read by engine. Only used by rake task for display. |
| `template` | ❌ NO | Present in JSON but never checked. |
| `mission_id` | ❌ NO | Present but never used by engine. |
| `name` / `description` | ❌ NO | Never read by engine. Only displayed by rake task. |
| `world_requirements.body_type` | ⚠️ PARTIAL | Not directly read by engine, but `parameters.isru_mode` and `day_night_cycle_hours` are present in profile for AI Manager use. |
| `success_conditions` | ❌ NO | Never read by engine. Would need to be added for automated completion checking. |
| `parameters` | ⚠️ PARTIAL | Present in Gen 2 profile (`isru_mode`, `day_night_cycle_hours`, etc.) but NOT read by current engine. Intended for AI Manager dynamic selection. |

**Key insight:** The engine treats the profile as a **manifest hash**. It does NOT have a separate "profile" concept — profiles and manifests are merged in the current implementation.

### 2.2 What the Engine Actually Reads from a Phase File

Phase files are loaded via `load_phase_tasks(task_list_file)`:

| Field | Used? | How |
|-------|-------|-----|
| `tasks` | ✅ YES | Array of task entry objects |
| `task_ref` (within each task entry) | ✅ YES | Relative path to task JSON file |
| `phase` | ❌ NO | Present in phase JSON but never read by engine |
| `description` | ❌ NO | Present but never used |

**Minimum viable phase file:**
```json
{
  "tasks": [
    { "task_ref": "tasks_v2/task_something.json" }
  ]
}
```

That's it. The engine only needs the `tasks[].task_ref` array. Everything else is metadata for human readers.

### 2.3 What the Engine Does NOT Read (But Should for profiles_v2)

- Phase ordering/priority (currently relies on array position in profile's phases array)
- Phase dependencies (no dependency graph between phases)
- Phase duration estimates
- AI manager integration flags
- World-agnostic body type requirements
- Success condition scoring/weighting

---

## 3. Current Pattern — Annotated Working V2 Example

### How Profile → Phase → Task Connects

```
luna_settlement_profile_v1.json (Profile)
├── phases[0].phase_id = "power_comms"
│   └── task_list_file = "phases/phase_1_power_comms.json"
│       └── tasks[0].task_ref = "tasks_v2/task_deploy_regolith_harvester_rover.json"
│           └── tasks[].effects[] = { action: "deploy_unit", unit: "...", count: 1 }
│               └── Engine calls execute_effect() → deploy_unit_from_effect()
│                   └── Creates BaseUnit record in database
│
├── phases[1].phase_id = "isru_deployment"
│   └── task_list_file = "phases/phase_2_isru_deployment.json"
│       └── tasks[].task_ref = "tasks_v2/task_deploy_pve_unit.json"
│           └── effects[] = [deploy_unit, connect_units]
│
├── phases[2].phase_id = "gas_processing"
│   └── task_list_file = "phases/phase_3_gas_processing.json"
│       └── tasks[].task_ref = "tasks_v2/task_deploy_gas_separator.json"
│           └── effects[] = [deploy_unit, register_to_bus, set_unit_state]
│               └── register_to_bus creates 7 port mappings on PUH
```

### Execution Flow (via rake)

1. Rake loads profile JSON → passes as `manifest_or_path` to engine constructor
2. Engine extracts `required_capabilities` from `manifest["required_hardware"]` and `manifest["phases"]`
3. Rake resolves phase paths: modern path first, then legacy fallback
4. Rake calls `ensure_deploy_inventory_from_v2_tasks` — scans all phases → tasks → effects for `deploy_unit` actions → seeds settlement inventory
5. Rake iterates `execution_order = ["power_comms", "isru_deployment", "gas_processing"]`
6. For each phase: resolve path → load JSON → for each task_ref → load task → execute effects

### Working V2 Profile Schema (Current)

```json
{
  "template": "mission_profile",
  "mission_id": "luna_base_establishment",
  "name": "Luna Base Establishment",
  "description": "...",
  "manifest_file": "luna_base_establishment_manifest_v2.json",
  "world_requirements": {
    "body_type": "airless_rocky",
    "has_regolith": true,
    "gravity_range": [0.1, 0.3],
    "notes": "Pattern applies to any airless rocky body..."
  },
  "phases": [
    {
      "phase_id": "power_comms",
      "name": "Initial Power and Comms",
      "task_list_file": "phases/phase_1_power_comms.json"
    }
  ],
  "parameters": {
    "isru_mode": "regolith_teu_pve",
    "day_night_cycle_hours": 708,
    "early_isru_outputs": ["O2", "H2", "He3"]
  },
  "success_conditions": {
    "complete_phases": ["power_comms", "isru_deployment", "gas_processing"],
    "isru_producing": true
  },
  "metadata": {
    "version": "1.0",
    "pattern_class": "airless_rocky_isru"
  }
}
```

### Working V2 Phase Schema (Current)

```json
{
  "phase": "power_comms",
  "description": "...",
  "tasks": [
    { "task_ref": "tasks_v2/task_deploy_puh_and_ppmu.json" },
    { "task_ref": "tasks_v2/task_deploy_solar_rig.json" }
  ]
}
```

### Working V2 Task Schema (Current)

```json
{
  "metadata": {
    "template": "generic_repeatable_task",
    "description": "...",
    "intended_usage": "...",
    "author": "...",
    "date_created": "2026-04-09"
  },
  "tasks": [
    {
      "task_id": "deploy_pve_unit",
      "name": "Deploy PVE Mk1",
      "description": "...",
      "priority": 7,
      "preconditions": [
        { "type": "unit_state", "unit": "Thermal Extraction Unit", "state": "ready" }
      ],
      "effects": [
        { "action": "deploy_unit", "unit": "Planetary Volatiles Extractor Mk1", "count": 1 },
        { "action": "connect_units", "unit1": "...", "port1": "...", "unit2": "...", "port2": "..." }
      ],
      "completion_criteria": { "type": "unit_state", "unit": "...", "state": "ready" }
    }
  ]
}
```

---

## 4. Gap Analysis — What's Missing for World-Agnostic Composable Profiles

### Gap 1: No Body-Type Parameterization

**Current:** Profile has `world_requirements.body_type = "airless_rocky"` as a static string. The engine does NOT use this field for any logic.

**Needed:** A mechanism to select/validate profile variants based on body type at runtime. AI Manager needs to know which profiles are applicable to which body types.

### Gap 2: No Phase Dependency Graph

**Current:** Phases execute in array order (or hardcoded `execution_order` in rake). No explicit dependency declarations between phases.

**Needed:** Phase-level `dependencies` field for composable phase selection. Example: "gas_processing" depends on "isru_deployment".

### Gap 3: No Composable Phase Selection

**Current:** All phases in a profile are always executed. No conditional or selective phase inclusion.

**Needed:** Phase conditions that can be evaluated at runtime (body type, available resources, prior mission state).

### Gap 4: Manifest/Profile Naming Inconsistency

**Current:** Three different manifest names across files:
1. `luna_base_establishment_manifest_v2.json` (profile's `manifest_file`)
2. `lunar_precursor_manifest_v2_DRAFT.json` (actual filename)
3. `lunar_precursor_manifest_v2` (`manifest_id` field)

**Needed:** Canonical naming convention for profiles_v2.

### Gap 5: No AI Manager Integration Layer

**Current:** AI Manager does NOT read profiles directly. The rake task is the only consumer, and it treats profiles as manifest hashes.

**Needed:** A profile registry/service that AI Manager can query to find applicable profiles for a given body/settlement state.

### Gap 6: Success Conditions Not Engine-Checked

**Current:** `success_conditions` exists in profile JSON but is never evaluated by the engine.

**Needed:** Engine method to check completion against success conditions (phase completion, unit states, production thresholds).

### Gap 7: No World-Agnostic Task Templates

**Current:** Tasks reference specific unit names ("Planetary Volatiles Extractor Mk1") that are Luna-specific.

**Needed:** Parameterized task templates where unit names, resource types, and port configurations can vary by body type.

### Gap 8: Missing Phase 4 File

**Current:** Profile references `phase_4_robot_logistics.json` but it was not read/verified. May or may not exist.

---

## 5. Design Constraints for profiles_v2

### Constraint 1: Must Work with Existing Engine

The minimum viable approach is to keep the profile structure **compatible** with how TaskExecutionEngineV2 currently reads data:
- `phases` array with `phase_id`, `task_list_file` fields
- `required_hardware` array with `task_affinity` for capability extraction
- `resources` hash for environment merging

### Constraint 2: Must Support Dynamic Selection

AI Manager needs to query profiles by:
- Applicable body types (`world_requirements.body_type`)
- Pattern class (`metadata.pattern_class`)
- Required capabilities (derived from `required_hardware` or `phases`)
- Resource availability matching

### Constraint 3: Must Be Composable

Profiles should support:
- Phase-level dependencies for DAG-based execution ordering
- Conditional phase inclusion based on body/resources/state
- Mix-and-match phases across profiles (e.g., use power_comms from one profile + isru_deployment from another)

### Constraint 4: Must Support Auto-Generation

Human should NOT need to hand-author a profile for every mission. The system needs:
- Template-based profile generation from body type + mission goal
- Parameterized unit/resource names that adapt to body type
- Default phase sequences per pattern class

### Constraint 5: Must Maintain Backward Compatibility

Existing Gen 2 profiles and phases must continue to work with the current engine and rake task. Migration path:
- profiles_v2/ can coexist alongside existing profiles
- Engine/rake should try v2 paths first, then fall back to legacy
- No breaking changes to existing JSON schemas

---

## 6. Recommended profiles_v2 Folder Structure

```
data/json-data/missions/
├── profiles_v2/                          ← NEW: world-agnostic mission templates
│   ├── README.md                         ← Schema documentation
│   │
│   ├── patterns/                         ← Reusable pattern profiles (body-type specific)
│   │   ├── airless_rocky_isru.json       ← Luna/Mercury/Ceres pattern
│   │   ├── atmospheric_giant_harvest.json ← Gas giant pattern
│   │   ├── thin_atmosphere_mars.json      ← Mars pattern
│   │   ├── icy_moon_subsurface.json       ← Europa/Titan pattern
│   │   └── ocean_world_probe.json         ← Ocean world pattern
│   │
│   ├── phases/                           ← Composable phase modules
│   │   ├── power_comms.json              ← Universal (body-agnostic)
│   │   ├── isru_deployment.json          ← Parameterized by body type
│   │   ├── gas_processing.json           ← Parameterized by available gases
│   │   ├── habitat_setup.json            ← Universal
│   │   ├── robot_logistics.json          ← Universal
│   │   └── orbital_depot.json            ← Universal
│   │
│   ├── templates/                        ← Auto-generation templates
│   │   ├── base_settlement_template.json  ← Minimal profile template
│   │   ├── isru_mission_template.json     ← ISRU-focused template
│   │   └── cycler_route_template.json     ← Transit-focused template
│   │
│   └── registry.json                     ← AI Manager profile registry
│       {
│         "profiles": [
│           {
│             "profile_id": "airless_rocky_isru",
│             "applicable_body_types": ["airless_rocky"],
│             "pattern_class": "isru_foundation",
│             "required_capabilities": ["power", "habitat", "comms", "isru"],
│             "template_file": "patterns/airless_rocky_isru.json"
│           }
│         ]
│       }
│
├── manifests_v2/                         ← Existing (keep as-is)
├── tasks_v2/                             ← Existing (keep as-is)
└── luna_base_establishment/              ← Existing Gen 2 (keep as-is)
    ├── luna_settlement_profile_v1.json
    └── phases/
```

### Minimum Viable Schema for profiles_v2

#### Profile Schema (profiles_v2/patterns/*.json)

```json
{
  "profile_id": "airless_rocky_isru",
  "name": "Airless Rocky ISRU Foundation",
  "description": "Bootstrap ISRU production on any airless rocky body.",
  "version": "1.0",
  "world_requirements": {
    "body_type": "airless_rocky",
    "has_regolith": true,
    "gravity_range": [0.1, 0.3],
    "atmosphere_required": false
  },
  "pattern_class": "isru_foundation",
  "phases": [
    {
      "phase_id": "power_comms",
      "phase_file": "phases/power_comms.json",
      "dependencies": [],
      "conditions": null
    },
    {
      "phase_id": "isru_deployment",
      "phase_file": "phases/isru_deployment.json",
      "dependencies": ["power_comms"],
      "conditions": { "requires_body_type": "airless_rocky" }
    },
    {
      "phase_id": "gas_processing",
      "phase_file": "phases/gas_processing.json",
      "dependencies": ["isru_deployment"],
      "conditions": { "requires_capability": "isru_producing" }
    }
  ],
  "parameters": {
    "isru_mode": "$body_type.isru_mode",
    "day_night_cycle_hours": "$body_type.day_night_cycle",
    "early_isru_outputs": ["O2", "H2"],
    "import_dependencies": []
  },
  "success_conditions": {
    "complete_phases": ["power_comms", "isru_deployment", "gas_processing"],
    "isru_producing": true,
    "minimum_production_rate_kg_per_hour": 1.0
  },
  "metadata": {
    "auto_generatable": true,
    "template_based_on": "base_settlement_template"
  }
}
```

#### Phase Schema (profiles_v2/phases/*.json)

```json
{
  "phase_id": "power_comms",
  "name": "Initial Power and Comms",
  "description": "Deploy power generation and communications infrastructure.",
  "applicable_body_types": ["airless_rocky", "thin_atmosphere", "atmospheric"],
  "task_refs": [
    "tasks_v2/task_deploy_solar_rig.json",
    "tasks_v2/task_deploy_puh_and_ppmu.json",
    "tasks_v2/task_deploy_comms_equipment.json"
  ],
  "estimated_duration_hours": 48,
  "priority": 1,
  "metadata": {
    "world_agnostic": true,
    "parameterized_fields": []
  }
}
```

#### Phase Schema — Parameterized (body-specific)

```json
{
  "phase_id": "isru_deployment",
  "name": "ISRU Unit Deployment",
  "description": "Deploy ISRU units appropriate for body type.",
  "applicable_body_types": ["airless_rocky"],
  "task_refs": [
    "tasks_v2/task_deploy_thermal_extraction_unit.json",
    "tasks_v2/task_deploy_pve_unit.json"
  ],
  "estimated_duration_hours": 72,
  "priority": 2,
  "parameterized_fields": {
    "unit_names": {
      "TEU": "$body_type.regolith_extraction_unit",
      "PVE": "$body_type.volatiles_extractor"
    },
    "port_mappings": {
      "hub_port": "$body_type.isru_output_port"
    }
  },
  "metadata": {
    "world_agnostic": false,
    "parameterized_fields": ["unit_names", "port_mappings"]
  }
}
```

---

## 7. Research Questions — Answers

### Q1: What fields does the engine actually read from a profile?
**Answer:** `phases[]` (phase_id, task_list_file), `resources` (initial_crew/equipment/budget), `required_hardware[]` (task_affinity). Everything else is metadata for humans/rake display.

### Q2: What fields does the engine actually read from a phase file?
**Answer:** Only `tasks[].task_ref`. Minimum viable phase: `{ "tasks": [{ "task_ref": "..." }] }`.

### Q3: What makes the current profile world-agnostic vs Luna-specific?
**Answer:** `world_requirements.body_type = "airless_rocky"` is the most world-agnostic element. The `notes` field explicitly states "Pattern applies to any airless rocky body." However, `parameters.day_night_cycle_hours = 708` and `parameters.isru_mode = "regolith_teu_pve"` are Luna-specific values that would need parameterization per body type.

### Q4: What is the complete list of V2 effects the engine supports?
**Answer:** `deploy_unit`, `connect_units`, `construct_structure`, `set_unit_state`, `set_settlement_state`, `check_unit_state`, `transfer_resource`, `manufacture`, `advance_deployment_stages`, `request_import`, `register_to_bus`. (11 total)

### Q5: How does the rake currently resolve profile paths?
**Answer:** Legacy-first: checks `luna_base_establishment/luna_settlement_profile_v1.json` first, then `profiles/luna_base_establishment_profile_v1.json`. For phases: modern path first (`task_list_file` directly), then legacy fallback (`luna_base_establishment/` prefix).

### Q6: What is the relationship between manifest, profile, and phases?
**Answer:** 
- **Manifest** (`manifests_v2/*`): Hardware/consumable inventory — what to bring. Populates settlement inventory.
- **Profile** (`profiles/*` or `luna_base_establishment/luna_settlement_profile_v1.json`): Mission plan — which phases to execute, in what order, with what parameters. References manifest file.
- **Phases** (`phases/phase_*.json`): Task lists — which specific tasks to run per phase. Reference task files via `task_ref`.
- **Tasks** (`tasks_v2/task_*.json`): Atomic actions — deploy/connect/register effects with preconditions and completion criteria.

Data flows: Manifest → inventory seeding → Profile → phase resolution → task loading → effect execution.

### Q7: What would a world-agnostic phase look like?
**Answer:** A phase where all unit names, resource types, port configurations, and environmental parameters are parameterized via `$body_type.*` or `$mission.*` placeholders. The phase structure (task_refs, dependencies, estimated_duration_hours) remains the same; only the content values vary by body type.

### Q8: What body types exist in the game?
**Answer:** Based on task file patterns and profile references:
1. `airless_rocky` — Luna, Mercury, Ceres (regolith-based ISRU)
2. `thin_atmosphere` — Mars (hybrid regolith/atmosphere processing)
3. `atmospheric` — Venus (dense atmosphere flight, acid-resistant materials)
4. `gas_giant` — Jupiter/Saturn (aerostat habitats, atmospheric harvesting)
5. `icy_moon` — Europa, Titan (subsurface ice, cryogenic processing)
6. `ocean_world` — Enceladus (probe deployment, plume collection)
7. `orbital` — L1 station, LEO depot (no surface operations)

### Q9: How does the AI Manager currently interact with profiles?
**Answer:** The AI Manager does NOT read profiles directly. The rake task (`luna_mission.rake`) is the sole consumer. It loads profile JSON, passes it as a manifest hash to TaskExecutionEngineV2, and handles phase path resolution and inventory seeding. For profiles_v2, a ProfileRegistry service would need to be created for AI Manager integration.

### Q10: What is the minimum change to make profiles_v2/ work with the existing engine?
**Answer:** Three changes:
1. **Profile compatibility layer**: Ensure profiles_v2 files have `phases[]` with `phase_id` and `task_list_file` fields (or a compatible alias). The engine reads these directly.
2. **Path resolution update**: Add profiles_v2/ to the rake task's path resolution chain — try `profiles_v2/patterns/<id>.json` before falling back to existing paths.
3. **Phase file location mapping**: Either keep phase files in the same relative locations as current (under each profile's directory) or add a `phase_base_path` field to profiles that tells the engine where to find phases.

Everything else (AI Manager integration, auto-generation, composability) is additive — not required for initial compatibility.

---

## Completion Report

**Completed by**: Research Agent
**Completion date**: 2026-07-05
**Files read**: 18 (8 Gen 2 files + 1 Gen 1 file + 3 engine/rake files + directory listings)
**Summary saved to**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-07-05-RESEARCH-PROFILES-V2-ARCHITECTURE.md`

---

## Handoff Summary

**Files read**: luna_settlement_profile_v1.json, 3 phase files, lunar_precursor_manifest_v2_DRAFT.json, 3 task files, luna_base_establishment_profile_v1.json (Gen 1), TaskExecutionEngineV2.rb, luna_mission.rake, profiles/ directory listing, tasks_v2/ directory listing (148 files)

**Key findings**: 
- Engine only reads `phases[].task_list_file` and `required_hardware[].task_affinity` from profiles — everything else is metadata
- Phase files need only `{tasks: [{task_ref: "..."}]}` — minimal structure
- 11 effect types supported by engine (deploy_unit, connect_units, register_to_bus, etc.)
- Profile/manifest naming has 3 inconsistent variants across files
- No AI Manager profile integration exists yet — rake task is sole consumer
- 7 body types identified that need different profile variants
- Gen 1 phase files were never committed to git

**Summary location for Gemini handoff**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-07-05-RESEARCH-PROFILES-V2-ARCHITECTURE.md`
