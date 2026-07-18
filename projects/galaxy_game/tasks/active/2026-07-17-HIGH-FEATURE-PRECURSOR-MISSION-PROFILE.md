---
title: "Precursor Mission Profile — Interplanetary Supply Chain"
priority: HIGH
status: active
phase: phase5
owner: Implementation Agent (Qwen)
type: feature
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
created: 2026-07-17
---

# TASK: Create Precursor Mission Profile JSON

**Status**: ACTIVE  
**Priority**: HIGH (Phase 5 — MVP prerequisite)  
**Type**: feature  
**Created**: 2026-07-17  
**Last Updated**: 2026-07-17  

---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase5+/2026-07-17-HIGH-FEATURE-PRECURSOR-MISSION-PROFILE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/phase5+/2026-07-17-HIGH-FEATURE-PRECURSOR-MISSION-PROFILE.md \
         projects/galaxy_game/tasks/active/2026-07-17-HIGH-FEATURE-PRECURSOR-MISSION-PROFILE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-17-HIGH-FEATURE-PRECURSOR-MISSION-PROFILE.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-FEATURE-PRECURSOR-MISSION-PROFILE.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*
*This section is NOT sent to agents — it's for human task management only*

- **Template Conformance**: PASS
- **Docker Wrapper Check**: N/A (JSON creation, no RSpec)
- **MVP Alignment**: VALID — Phase 5 prerequisite for Luna MVP
- **MVP Impact Note**: Profile drives all precursor mission scheduling and validation
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: JSON creation is straightforward, well-specified task
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully — first dispatch of this task type

> **Primary executor is always local Qwen via the GitHub Copilot custom agent config.**
> Cloud/paid agents are fallback only.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below
4. **Reference Files**: Listed in "Files to Read" section

> Agent MUST read in this order. Do not skip.

---

## Objective

Create `precursor_mission_profile_v1.json` — a data-driven mission profile that defines the full precursor mission supply chain as 7 sequential phases. This profile drives both the rake validation task and the Mission Planner's scheduling logic.

**Parent Task**: Full Precursor Mission Validation (4-part sequence)  
**Depends On**: None (first in sequence)  
**Feeds Into**: Task B (manifest), which references these phase IDs

---

## Architecture: 7-Phase Supply Chain

```
Phase 1: gcc_mining          → Currency generation (30 days)
Phase 2: initial_hlt_landings → Multi-cargo HLT deliveries (180 days)
Phase 3: power_grid_deployment → RTG + solar array (120 days)
Phase 4: titan_delivery       → N2 + CH4 from Titan (730 days, longest transit)
Phase 5: venus_delivery       → CO2 + N2 from Venus (400 days)
Phase 6: luna_isru_production → O2/H2/He3/I-Beams production (180 days)
Phase 7: l1_leo_supply       → Materials to orbital depot (60 days)
```

**Critical dependency chain**: Phase 4 must complete before Phase 6 can produce volatiles. Venus refueling (Phase 5) depends on Titan return OR Earth CH4 import.

---

## Implementation Requirements

### Target File
`galaxy_game/data/json-data/missions_v2/profiles/precursor_mission_profile_v1.json`

### Schema Pattern
Follow `profile_v2` template pattern. Reference existing profiles for structure:
- `data/json-data/missions_v2/profiles/luna_base_profile_v2.json` — phase structure reference
- `data/json-data/missions_v2/profiles/gcc_sat_mining_deployment/` — GCC mining reference

### Key Design Constraints

1. **Data-driven, not world-name-driven**
   - Use planet properties (atmosphere composition, distance) to determine HLT delivery contents
   - Do NOT hardcode "Titan" and "Venus" as the only valid sources
   - Pattern must work for any system with gas giants + terrestrial planets

2. **Transit timing is critical**
   - Titan transit = 730 days (longest, arrives first during precursor mission)
   - Venus transit = 400 days (follows Titan)
   - Do NOT assume all HLTs arrive simultaneously

3. **Venus refueling dependency**
   - Venus refuel timing depends on Titan return OR Earth CH4 import OR local production
   - This is the critical path constraint for the entire mission

---

## Problem Statement

The current `luna_mission:execute` rake has no data-driven mission profile for the full precursor mission supply chain. There is no JSON profile that defines the 7-phase interplanetary logistics pipeline (GCC mining → Titan/Venus HLT delivery → Luna ISRU production → L1/LEO depot construction). This gap means:
- Mission Planner cannot schedule precursor missions
- No validation rake exists for end-to-end supply chain testing
- No data-driven source of truth for phase dependencies and durations

**Current behavior**: No precursor mission profile exists.
**Expected behavior**: A `profile_v2` JSON file defines all 7 phases with correct dependencies, durations, and outputs.

---

## Profile Structure

```json
{
  "template": "profile_v2",
  "version": "1.0",
  "profile_id": "precursor_mission_validation",
  "name": "Full Precursor Mission Validation",
  "description": "End-to-end validation of interplanetary supply chain: GCC mining → Titan/Venus HLT delivery → Luna ISRU production → L1/LEO depot construction.",
  "world_requirements": {
    "body_type": "multi_body_system",
    "requires_gas_giants": true,
    "requires_terrestrial_planets": true,
    "notes": "Pattern applies to any system with gas giants and terrestrial planets. Sol is the reference implementation."
  },
  "phases": [
    {
      "phase_id": "gcc_mining",
      "name": "GCC Satellite Mining",
      "description": "Deploy GCC mining satellite on Earth to generate currency for procurement.",
      "prerequisites": [],
      "duration_days": 30,
      "outputs": ["currency_generation", "economic_layer"]
    },
    {
      "phase_id": "initial_hlt_landings",
      "name": "Initial HLT Landings — Multi-Cargo Manifests",
      "description": "Multiple HLT landings with different cargo manifests: habitat modules, TEU/PVE equipment, tank farm components.",
      "prerequisites": ["gcc_mining"],
      "duration_days": 180,
      "outputs": ["habitat_modules_delivered", "isru_equipment_delivered", "tank_farm_components_delivered"],
      "landing_manifests": [
        {
          "manifest_id": "hlt_landing_1_habitat",
          "cargo": ["habitation_modules", "life_support_systems", "pressurization_equipment"]
        },
        {
          "manifest_id": "hlt_landing_2_isru",
          "cargo": ["thermal_extraction_unit", "planetary_volatiles_extractor", "gas_separator"]
        },
        {
          "manifest_id": "hlt_landing_3_tank_farm",
          "cargo": ["inflatable_tanks", "tank_farm_infrastructure", "sealing_materials"]
        }
      ]
    },
    {
      "phase_id": "power_grid_deployment",
      "name": "Power Grid Deployment — RTG + 3D Printed Solar Array",
      "description": "Deploy full power grid: RTG for nighttime power, 3D printed solar array beams from regolith feedstock.",
      "prerequisites": ["initial_hlt_landings"],
      "duration_days": 120,
      "outputs": ["rtg_power_online", "solar_array_built", "power_grid_connected"]
    },
    {
      "phase_id": "titan_delivery",
      "name": "Titan HLT Atmosphere Harvesting",
      "description": "Deploy Titan HLT to harvest N2 + CH4 from atmosphere and deliver to Luna.",
      "prerequisites": ["gcc_mining"],
      "duration_days": 730,
      "outputs": ["n2_delivery", "ch4_delivery"]
    },
    {
      "phase_id": "venus_delivery",
      "name": "Venus HLT Atmosphere Harvesting",
      "description": "Deploy Venus HLT to harvest CO2 + N2 from atmosphere and deliver to Luna.",
      "prerequisites": ["gcc_mining"],
      "duration_days": 400,
      "outputs": ["co2_delivery", "n2_delivery"]
    },
    {
      "phase_id": "luna_isru_production",
      "name": "Luna ISRU Production Validation",
      "description": "Validate TEU/PVE produce volatiles (O2, H2, He3) and manufacturing pipeline works.",
      "prerequisites": ["titan_delivery"],
      "duration_days": 180,
      "outputs": ["o2_production", "h2_production", "he3_production", "i_beam_production"]
    },
    {
      "phase_id": "l1_leo_supply",
      "name": "Luna → L1/LEO Depot Supply Chain",
      "description": "Validate materials flow from Luna to orbital depot construction.",
      "prerequisites": ["luna_isru_production"],
      "duration_days": 60,
      "outputs": ["depot_materials_delivered", "l1_depot_ready"]
    }
  ],
  "success_conditions": {
    "complete_phases": ["gcc_mining", "initial_hlt_landings", "power_grid_deployment", "titan_delivery", "venus_delivery", "luna_isru_production", "l1_leo_supply"],
    "currency_generated": true,
    "n2_delivered_to_luna": true,
    "ch4_delivered_to_luna": true,
    "co2_delivered_to_luna": true,
    "rtg_power_online": true,
    "solar_array_built": true,
    "power_grid_connected": true,
    "isru_producing": true,
    "depot_materials_available": true
  },
  "metadata": {
    "version": "1.0",
    "pattern_class": "interplanetary_supply_chain",
    "auto_generatable": true
  }
}
```

---

## Implementation Steps

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/current/2026-07-17-HIGH-FEATURE-PRECURSOR-MISSION-PROFILE.md \
       projects/galaxy_game/tasks/active/2026-07-17-HIGH-FEATURE-PRECURSOR-MISSION-PROFILE.md
```

Then update YAML status: `status: backlog → status: active`

### Step 1 — Read reference profiles for schema pattern

Read these files to understand the profile_v2 schema:
- `galaxy_game/data/json-data/missions_v2/profiles/luna_base_profile_v2.json` — phase structure reference
- `galaxy_game/data/json-data/missions_v2/profiles/gcc_sat_mining_deployment/` — GCC mining reference

### Step 2 — Create precursor_mission_profile_v1.json

Create the file at `galaxy_game/data/json-data/missions_v2/profiles/precursor_mission_profile_v1.json` with:
- All 7 phases in correct dependency order
- Transit timing: Titan 730d, Venus 400d
- No world-name hardcoding (uses planet properties pattern)
- Valid JSON (parseable by Ruby's JSON.parse)

### Step 3 — Validate JSON

```bash
docker exec web bash -c 'ruby -r json -e "JSON.parse(File.read(\"app/data/json-data/missions_v2/profiles/precursor_mission_profile_v1.json\"))"'
```

Expected: No errors (JSON is valid)

---

## Success Criteria

- [ ] File created at correct path
- [ ] All 7 phases present with correct phase_ids
- [ ] Phase dependency chain is valid (no circular deps)
- [ ] Transit timing reflects real constraints (Titan 730d, Venus 400d)
- [ ] No world-name hardcoding (uses planet properties pattern)
- [ ] Follows profile_v2 schema pattern (verified against existing profiles)
- [ ] JSON is valid (parseable by Ruby's JSON.parse)

---

## Stop Conditions — escalate to user immediately if:
- Profile_v2 schema has changed since last reference file was read
- Any phase dependency creates a circular chain
- World-name hardcoding is required by existing patterns (flag as gap)

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add galaxy_game/data/json-data/missions_v2/profiles/precursor_mission_profile_v1.json
git commit -m "feat: create precursor mission profile v1 with 7-phase interplanetary supply chain"
git push
```

---

## Documentation
- [ ] No doc changes needed
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: none
**Blocks**: Task B (manifest), which references these phase_ids
**Related tasks**: Full Precursor Mission Validation (4-part sequence)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was changed
- `galaxy_game/data/json-data/missions_v2/profiles/precursor_mission_profile_v1.json` — created with 7 phases

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]

---

## Files to Read Before Starting

| File | Why |
|------|-----|
| `galaxy_game/data/json-data/missions_v2/profiles/luna_base_profile_v2.json` | profile_v2 schema reference |
| `galaxy_game/data/json-data/missions_v2/profiles/gcc_sat_mining_deployment/` | GCC mining phase reference |
| `galaxy_game/lib/tasks/luna_mission.rake` | Current rake pattern (for context) |

---

## Stop Conditions

- Profile JSON is complete and valid
- All 7 phases are present with correct dependencies
- No world-name hardcoding in the profile structure
- **Do NOT** create manifest or rake files — those are separate tasks
