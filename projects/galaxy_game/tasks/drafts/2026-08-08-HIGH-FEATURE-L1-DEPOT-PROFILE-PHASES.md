---
status: backlog
priority: HIGH
type: feature
system_domain: MISSION_PLANNING
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT (Phase 7 — depot building gates all downstream expansion)
local_worker_safe: true
---

# TASK: Create L1 Depot Mission Profile and Phase Structure

---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase07-depot-building/2026-08-08-HIGH-FEATURE-L1-DEPOT-PROFILE-PHASES.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/drafts/2026-08-08-HIGH-FEATURE-L1-DEPOT-PROFILE-PHASES.md \
         projects/galaxy_game/tasks/active/2026-08-08-HIGH-FEATURE-L1-DEPOT-PROFILE-PHASES.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: draft → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-08-HIGH-FEATURE-L1-DEPOT-PROFILE-PHASES.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

## Context

Per the post-Luna mission profile inventory (`summaries/2026-08-08-RESEARCH-INVENTORY-POST-LUNA-MISSION-PROFILES.md`), L1 Depot is the **most critical gap** in the macro build order. The confirmed chain is:

> Earth → Luna → **L1** → Mars → Phobos/Deimos → Asteroid Belt → Venus Station → Cycler Network

L1 currently has only two bare manifest files (`l1_station_depot_manifest_v1.json`, `leo_depot_construction_manifest_v1.json`) — no mission profile, no mission plan, no phases. This is a structural deficiency: every other build stage (Mars, Venus, Psyche) has the full profile → plan → phases → tasks_v2 pattern.

This task creates the missing L1 Depot profile and phase structure using the Luna base establishment as the structural reference.

**Relevant Architecture Docs** — read before starting:
- `docs/architecture/planning/COMPLETE_PHASE_STRUCTURE.md` — Phase 7 = "Depot Building (LEO/L1 Infrastructure)"
- `summaries/2026-08-08-RESEARCH-INVENTORY-POST-LUNA-MISSION-PROFILES.md` — inventory confirming L1 gap
- `data/json-data/missions_v2/profiles/luna_base_profile_v2.json` — structural reference (profile_v2 template)
- `data/json-data/missions_v2/mission_plans/luna_precursor_mission_plan_v2.json` — structural reference (mission_plan_v2 template)

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: L1 is NOT a surface settlement**
- ❌ Wrong: Model L1 Depot after Luna base profile (airless_rocky ISRU pattern)
- ✅ Right: L1 is an orbital depot — no regolith, no atmosphere, no surface operations. It's a logistics node that STORES and TRANSFERS fuel/cargo between Earth, Luna, and downstream destinations.
- Why: The existing manifests describe craft/inventory but don't capture the strategic role of L1 as the transport cost reduction gateway.

⚠️ **GOTCHA 2: Use profile_v2 / mission_plan_v2 templates — NOT legacy format**
- ❌ Wrong: Create a new-style JSON that doesn't match existing v2 templates
- ✅ Right: Follow `luna_base_profile_v2.json` (profile) and `luna_precursor_mission_plan_v2.json` (plan) structure exactly — same fields, same template names, adapted for orbital depot context
- Why: The AI Manager's TaskExecutionEngineV2 expects consistent profile/plan schemas across all missions.

⚠️ **GOTCHA 3: L1 feeds downstream stages**
- ❌ Wrong: Design L1 in isolation
- ✅ Right: Explicitly model how L1 enables Mars (fuel supply chain), Cycler Network (refueling nodes), and Venus Station (orbital depot for atmospheric skimmers)
- Why: The macro build order is a dependency chain. L1's profile must articulate its role as the gateway.

### What Already Exists (DO NOT RECREATE)

| File | Content | Use For |
|---|---|---|
| `l1_station_depot_manifest_v1.json` | Craft/inventory manifest for L1 core module | Reference for structural modules, fuel capacity, crew requirements |
| `leo_depot_construction_manifest_v1.json` | LEO depot using L1 pattern (LOX-focused) | Reference for Luna LOX integration, gravity well economics |

### What Does NOT Exist (CREATE THESE)

| File | Purpose | Template Reference |
|---|---|---|
| `l1_station_depot_profile_v2.json` | Mission profile — strategic framing, success conditions, environment | `luna_base_profile_v2.json` |
| `l1_depot_mission_plan_v2.json` | Mission plan — phased build order with DAG dependencies | `luna_precursor_mission_plan_v2.json` |
| Phase files (4-6 phases) | Individual phase definitions | `phases/power_comms_v2.json` pattern |

---

## Problem Statement

L1 Depot has no profile or phases. The AI Manager cannot autonomously sequence L1 construction because there is no strategic framing (profile) and no phased build order (plan + phases). Every other post-Luna stage has this structure. Without it, Phase 7 cannot proceed to autonomous task generation.

---

## Files to Create

### Primary Output Files

| File | Purpose | Template Reference |
|---|---|---|
| `data/json-data/missions_v2/profiles/l1_station_depot_profile_v2.json` | Mission profile — strategic framing, environment, success conditions | `luna_base_profile_v2.json` |
| `data/json-data/missions_v2/mission_plans/l1_depot_mission_plan_v2.json` | Mission plan — phased build order with DAG | `luna_precursor_mission_plan_v2.json` |
| `data/json-data/missions_v2/phases/l1_[phase_id]_v2.json` (4-6 files) | Individual phase definitions | `phases/power_comms_v2.json` pattern |

### Reference Files — read but do not edit

| File | Why You Need It |
|---|---|
| `l1_station_depot_manifest_v1.json` | Existing craft/inventory data to reference (fuel capacity, modules, crew) |
| `leo_depot_construction_manifest_v1.json` | Existing LEO depot data for Luna LOX integration context |
| `data/json-data/missions/profiles/earth_mars_venus_cycler_route_establishment_profile_v1.json` | Cycler coordination context (L1 hosts cycler operations) |
| `data/json-data/missions/phases/cycler_coordination_setup_v1.json` | Existing cycler phase tied to L1 depot |

---

## Implementation Steps

### Step 1 — Create L1 Depot Profile (`l1_station_depot_profile_v2.json`)

Follow `luna_base_profile_v2.json` structure exactly. Adapt fields for orbital depot context:

**Key adaptations from Luna profile:**
- `profile_id`: `"l1_station_depot_profile_v2"`
- `name`: "L1 Station Depot Establishment"
- `description`: Frame as the critical transport cost reduction gateway — enables all downstream stages
- `world_requirements`: `{ body_type: "orbital_lagrange_point", location: "Earth-Moon L1", gravity: "microgravity", atmosphere_required: false }`
- `environment.target_body`: `"EARTH-MOON_L1"` (not a celestial body — a Lagrange point)
- `parameters.isru_mode`: N/A for orbital depot — replace with `depot_mode: "fuel_storage_and_transfer"`
- `success_conditions.complete_phases`: Reference the phases you create in Step 3
- `runtime_parameters.power_requirement`: Use solar array data from manifest (15 panels)
- `manifest_ref`: Point to existing `l1_station_depot_manifest_v1.json`
- `mission_plan_ref`: Point to your new plan file

**[FILL IN]** — Specific fuel storage capacity numbers, crew requirements, and economic projections. Use the existing manifest as a guide:
- Fuel storage: 50,000 tons (from manifest)
- Cargo throughput: 10,000 tons/month (from manifest)
- Crew: initial 15, full complement 50 (from manifest)

### Step 2 — Create L1 Depot Mission Plan (`l1_depot_mission_plan_v2.json`)

Follow `luna_precursor_mission_plan_v2.json` structure exactly. Define 4-6 phases that build the depot sequentially:

**Proposed phase DAG:**
```
Phase 1 (priority 1): core_structure — Deploy L1 station core module, power generation, comms
Phase 2 (priority 2): fuel_infrastructure — Install fuel depot tanks, cryogenic systems, refueling arms
Phase 3 (priority 3): cargo_logistics — Install cargo container systems, docking adapters, navigation beacons
Phase 4 (priority 4): cycler_coordination — Install cycler docking modules, coordination systems
Phase 5 (priority 5): operational_test — First fuel transfer test, crew deployment, economic validation
```

**[FILL IN]** — Estimated duration hours for each phase. Use Luna's durations as a rough guide:
- power_comms: 48h → core_structure: [estimate]
- isru_deployment: 72h → fuel_infrastructure: [estimate]
- gas_processing: 48h → cargo_logistics: [estimate]
- robot_logistics: 96h → cycler_coordination + operational_test: [estimate]

### Step 3 — Create Phase Files (4-6 files)

Each phase file follows the `phases/power_comms_v2.json` pattern. For each phase, include:
- `phase_id`, `phase_name`, `description`
- `dependencies` (array of phase_ids)
- `estimated_duration_hours`
- `objectives` — specific build objectives for this phase
- `craft_requirements` — which manifest items are deployed in this phase
- `success_conditions` — what "complete" means for this phase

**[FILL IN]** — Specific craft requirements per phase. Map from the existing manifest:
- core_structure: l1_station_core, solar_panel_array (15), power_generation_module (3), communication_module (2)
- fuel_infrastructure: fuel_depot_tank (12), cryogenic_systems (from LEO manifest), methalox_fuel (20,000 tons)
- cargo_logistics: cargo_container_system (20), docking_adapter (8), navigation_beacon (6)
- cycler_coordination: cycler_docking_module (4), cycler coordination systems
- operational_test: crew deployment (15 initial), first fuel transfer, economic validation

### Step 4 — Verify Structure

Validate that:
- Profile uses `template: "profile_v2"` and matches luna_base_profile_v2.json field structure
- Plan uses `template: "mission_plan_v2"` and matches luna_precursor_mission_plan_v2.json field structure
- Phase files use consistent template naming
- All phase dependencies form a valid DAG (no cycles)
- `dag_execution_order` in plan matches topological sort of dependencies

---

## Acceptance Criteria
- [ ] `l1_station_depot_profile_v2.json` created with profile_v2 template, all required fields present
- [ ] `l1_depot_mission_plan_v2.json` created with mission_plan_v2 template, DAG valid
- [ ] 4-6 phase files created, each with objectives, craft_requirements, success_conditions
- [ ] Profile references the plan file and existing manifest
- [ ] Plan's dag_execution_order is a valid topological sort of phase dependencies
- [ ] No duplicate content — existing manifest files are referenced, not recreated
- [ ] L1 Depot role as transport cost reduction gateway is articulated in profile description

---

## Stop Conditions — escalate to user immediately if:
- Existing manifest data contradicts the proposed phase structure
- The DAG has unavoidable circular dependencies (shouldn't happen with linear build order)
- Profile/plan template fields differ from Luna reference in ways that break TaskExecutionEngineV2 expectations
- Any architectural decision is required beyond what's specified here

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add data/json-data/missions_v2/profiles/l1_station_depot_profile_v2.json \
       data/json-data/missions_v2/mission_plans/l1_depot_mission_plan_v2.json \
       data/json-data/missions_v2/phases/l1_*_v2.json
git commit -m "feature: l1_depot — create mission profile and phase structure for L1 depot

- Profile: l1_station_depot_profile_v2 (profile_v2 template)
- Plan: l1_depot_mission_plan_v2 with 5-phase DAG
- Phases: core_structure → fuel_infrastructure → cargo_logistics → cycler_coordination → operational_test
- Fills critical gap identified in post-Luna mission profile inventory"
git push
```

**Task file move on completion:**
```bash
mv projects/galaxy_game/tasks/active/2026-08-08-HIGH-FEATURE-L1-DEPOT-PROFILE-PHASES.md \
   projects/galaxy_game/tasks/completed/2026-08/2026-08-08-HIGH-FEATURE-L1-DEPOT-PROFILE-PHASES.md
git add projects/galaxy_game/tasks/completed/2026-08/2026-08-08-HIGH-FEATURE-L1-DEPOT-PROFILE-PHASES.md
git commit -m "chore: move 2026-08-08-HIGH-FEATURE-L1-DEPOT-PROFILE-PHASES.md to completed/"
```

---

## Documentation
- [ ] Update `summaries/2026-08-08-RESEARCH-INVENTORY-POST-LUNA-MISSION-PROFILES.md` — mark L1 Depot gap as resolved
- [ ] Update `docs/architecture/planning/COMPLETE_PHASE_STRUCTURE.md` — note Phase 7 profile/plan complete

---

## Dependencies
**Blocked by**: none — this is the first file needed for Phase 7 autonomous task generation
**Blocks**: Mars orbital presence (Phase 9), Cycler Network (Phase 10+), Venus Station (Phase 11+)
**Related tasks**: `2026-08-08-MEDIUM-RESEARCH-INVENTORY-POST-LUNA-MISSION-PROFILES` (inventory that identified this gap)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Final validation result**:

### What was changed
-

### Issues discovered
-

### Follow-up tasks needed
-

### Lessons learned
-

---

## Handoff Summary
HANDOFF SUMMARY: [files created] | L1 profile+plan+phases complete | next: Phase 7 autonomous task generation from new structure