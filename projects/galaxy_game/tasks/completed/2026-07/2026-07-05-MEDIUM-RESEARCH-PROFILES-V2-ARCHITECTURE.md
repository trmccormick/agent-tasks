---
status: backlog
priority: MEDIUM
type: research
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
date_created: 2026-07-05
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Research Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/design/2026-07-05-MEDIUM-RESEARCH-PROFILES-V2-ARCHITECTURE.md

LIFECYCLE: backlog → active → completed (use git mv, never copy)
READ FIRST: Task file contains all prerequisites and research scope.

CRITICAL: Save completed synthesis report as MD file to summaries folder BEFORE any other work.
Output will be handed to Gemini for architecture design — completeness matters more than speed.
```

---

# RESEARCH: profiles_v2 Architecture — Mission Profile and Phase File Redesign

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: research
**Created**: 2026-07-05
**Output consumer**: Gemini (architecture design pass)

---

## MANDATORY READS

1. `/Users/tam0013/Documents/git/agent-tasks/README.md`
2. `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md`
3. All files listed in the Research Scope below — read before writing anything

---

## REQUIRED: STATUS SYNTHESIS REPORT

*Create this report as a summaries MD file before any other work, then wait for human approval.*

### 0. Task Location Check
- [ ] Confirm task exists in `agent-tasks/projects/galaxy_game/tasks/active/`
- [ ] If not, move it there before proceeding

### 1. Scope Assessment
List every file read and what it contains.

### 2. Implementation vs. Intent
Describe the gap between current profile/phase structure and the v2 goal.

### 3. Proposed Research Output
Confirm the summary will contain everything Gemini needs for a design pass — file inventory, pattern analysis, gap list, and design constraints.

---

## Context

The current mission system has two generations of profile and phase files that need to be understood before designing `profiles_v2/`:

**Generation 1 (old style)** — verbose, human-authored, narrative-based:
- Located in `data/json-data/missions/luna_base_establishment/` and `data/json-data/missions/profiles/`
- Tasks defined inline with string durations, personnel counts, resource names
- Tied to specific `mission_context` fields (not world-agnostic)
- Not executable by TaskExecutionEngineV2

**Generation 2 (current working style)** — lean, effects-based, engine-executable:
- Profiles: `luna_settlement_profile_v1.json` (in `luna_base_establishment/`)
- Phases: `phases/phase_1_power_comms.json` through `phase_3_gas_processing.json`
- Tasks: `tasks_v2/task_*.json` files
- Executed by `AIManager::TaskExecutionEngineV2`

**Long-term intent**:
- `profiles_v2/` folder (parallel to `tasks_v2/`) containing world-agnostic mission templates
- AI Manager selects or generates profiles dynamically based on body type, available resources, settlement goals
- Human should not need to hand-author a profile for every mission
- Profiles and phases should be composable — mix and match based on conditions

---

## Research Scope

### 1. Current Working Files (Generation 2 — read all)

| File | Path |
|------|------|
| Working profile | `data/json-data/missions/luna_base_establishment/luna_settlement_profile_v1.json` |
| Phase 1 | `data/json-data/missions/luna_base_establishment/phases/phase_1_power_comms.json` |
| Phase 2 | `data/json-data/missions/luna_base_establishment/phases/phase_2_isru_deployment.json` |
| Phase 3 | `data/json-data/missions/luna_base_establishment/phases/phase_3_gas_processing.json` |
| Phase 4 (may not exist yet) | `data/json-data/missions/luna_base_establishment/phases/phase_4_robot_logistics.json` |
| DRAFT manifest | `data/json-data/missions/manifests_v2/lunar_precursor_manifest_v2_DRAFT.json` |
| Example task file | `data/json-data/missions/tasks_v2/task_deploy_pve_unit.json` |
| Example task file | `data/json-data/missions/tasks_v2/task_deploy_gas_separator.json` |
| Example task file | `data/json-data/missions/tasks_v2/task_deploy_puh_and_ppmu.json` |

### 2. Old Style Files (Generation 1 — read for design reference)

| File | Path |
|------|------|
| Old profile | `data/json-data/missions/profiles/luna_base_establishment_profile_v1.json` |
| Old phase (precursor) | `data/json-data/missions/profiles/phases/lunar_precursor_deployment_v1.json` |
| Old phase (habitat) | `data/json-data/missions/profiles/phases/habitat_construction_v1.json` |
| LEO depot profile | `data/json-data/missions/profiles/leo_depot_construction_profile_v1.json` (if exists) |
| LEO depot phase | `data/json-data/missions/profiles/phases/leo_positioning_establishment_v1.json` (if exists) |
| List all files in profiles/ | `ls data/json-data/missions/profiles/` |

### 3. Engine and Rake (read for constraints)

| File | Path | Why |
|------|------|-----|
| TaskExecutionEngineV2 | `galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb` | Understand what the engine actually reads from profiles and phases |
| Rake task | `galaxy_game/lib/tasks/luna_mission.rake` | Understand how profiles are loaded and passed to engine |
| AI Manager | `galaxy_game/app/services/ai_manager/` — list and read key files | Understand how AI Manager will eventually consume profiles |

### 4. Existing tasks_v2 inventory

```bash
ls data/json-data/missions/tasks_v2/
```

List all task files and note any patterns in naming, structure, and effects used.

---

## Research Questions for Gemini

The summary must answer all of these — Gemini will use them for the design:

1. **What fields does the engine actually read from a profile?** Which fields are used vs ignored?
2. **What fields does the engine actually read from a phase file?** What is the minimum viable phase file?
3. **What makes the current profile world-agnostic vs Luna-specific?** Which fields would need to vary per body type?
4. **What is the complete list of V2 effects the engine supports?** (`deploy_unit`, `connect_units`, etc. — find all of them)
5. **How does the rake currently resolve profile paths?** Legacy-first or v2-first?
6. **What is the relationship between manifest, profile, and phases?** Which data lives where and why?
7. **What would a world-agnostic phase look like?** What would need to be parameterized?
8. **What body types exist in the game that would need different profile variants?** (airless rocky, atmospheric, gas giant, etc.)
9. **How does the AI Manager currently interact with profiles?** Does it read them directly or through the engine?
10. **What is the minimum change to make profiles_v2/ work with the existing engine?** What can be reused?

---

## Expected Output

Save to: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-07-05-RESEARCH-PROFILES-V2-ARCHITECTURE.md`

The summary must contain:

1. **File inventory** — every file read, what it contains, generation (1 or 2)
2. **Engine field analysis** — exactly what the engine reads from profiles and phases
3. **Current pattern** — annotated example of a working V2 profile + phase + task showing how they connect
4. **Gap analysis** — what's missing from current structure to support world-agnostic composable profiles
5. **Design constraints** — what the AI Manager needs from profiles to make dynamic selection/generation work
6. **Recommendation** — suggested `profiles_v2/` folder structure and minimum viable schema for Gemini to design from

---

## Stop Conditions — escalate to user immediately if:
- Profile files don't exist at expected paths — list what was found and where
- Engine reads profiles in an unexpected way — document and stop
- Any architectural decision is required before research can continue

---

## Completion Report
**Completed by**: [agent]
**Completion date**: YYYY-MM-DD
**Files read**: [count]
**Summary saved to**: summaries/2026-07-05-RESEARCH-PROFILES-V2-ARCHITECTURE.md

---

## Handoff Summary
HANDOFF SUMMARY: [files read] | [key findings] | [summary location for Gemini handoff]
