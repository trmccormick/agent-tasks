---
status: active
priority: HIGH
type: implementation
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
date_created: 2026-07-05
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/active/design/2026-07-05-HIGH-PHASE-NORMALIZATION-REGISTRY-CREATION.md

LIFECYCLE: backlog → active → completed (use git mv, never copy)
READ FIRST: Task file contains all prerequisites and implementation details.
```

---

# PHASE NORMALIZATION & REGISTRY CREATION

**Status**: ACTIVE
**Priority**: HIGH
**Type**: implementation
**Created**: 2026-07-05
**Input**: Research summary at `summaries/2026-07-05-RESEARCH-PROFILES-V2-ARCHITECTURE.md`

---

## Context

The research task (completed) established the V2 schema design. This task implements it: normalizing existing phases, creating a phase registry, and mapping all 148+ tasks.

**Research summary location**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-07-05-RESEARCH-PROFILES-V2-ARCHITECTURE.md`
(Contains all embedded JSON data — Gemini cannot access `data/` since it's gitignored)

---

## Implementation Steps

### Step 1: Create profiles_v2 Directory Structure

```
data/json-data/missions/profiles_v2/
├── README.md                    ← Schema documentation
├── phase_registry.json           ← AI Manager lookup index
├── patterns/
│   └── airless_rocky_isru.json  ← World-agnostic profile template
├── phases/
│   ├── power_comms.json          ← Normalized from phase_1
│   ├── isru_deployment.json      ← Normalized from phase_2
│   ├── gas_processing.json       ← Normalized from phase_3
│   └── robot_logistics.json      ← Normalized from phase_4
└── task_index/
    └── all_tasks.json            ← 148+ tasks mapped to phases
```

### Step 2: Normalize Phase Files

Convert each of the 4 existing phases to the V2 schema. Each normalized phase must include:

| Field | Type | Description |
|-------|------|-------------|
| `phase_id` | string | Unique identifier (e.g., "power_comms") |
| `name` | string | Human-readable name |
| `description` | string | Purpose of this phase |
| `applicable_body_types` | array | ["airless_rocky"] — world-agnostic filter |
| `dependencies` | array | Phase IDs that must complete first (e.g., ["power_comms"]) |
| `conditions` | object/null | Runtime conditions for inclusion (can be null) |
| `task_refs` | array | Direct task file paths (no intermediate task_list_file) |
| `estimated_duration_hours` | number | Total phase duration estimate |
| `priority` | number | Execution priority (lower = earlier) |
| `documentation_url` | string | Link to engine/AI Manager docs for this phase |
| `metadata` | object | version, world_agnostic flag, source_file |

**Dependency chain:**
```
power_comms          → []           (no dependencies — runs first)
isru_deployment      → ["power_comms"]  (needs power/comms before ISRU)
gas_processing       → ["isru_deployment"]  (needs ISRU units operational)
robot_logistics      → ["gas_processing"]   (runs after gas processing established)
```

### Step 3: Create phase_registry.json

The registry enables AI Manager to query phases by body type, task_affinity, and prerequisites:

```json
{
  "registry_version": "1.0",
  "last_updated": "2026-07-05",
  "phases": [
    {
      "phase_id": "power_comms",
      "phase_file": "profiles_v2/phases/power_comms.json",
      "applicable_body_types": ["airless_rocky", "thin_atmosphere", "atmospheric"],
      "required_capabilities": ["power_generation", "communications"],
      "task_affinity": ["deploy_solar_rig", "deploy_puh_and_ppmu", "deploy_comms_equipment"]
    }
  ]
}
```

### Step 4: Map All 148+ Tasks to Registry

Scan `tasks_v2/` and map each task to its phase via the normalized phase files. Create a task index that enables reverse lookup (task → applicable phases).

---

## Schema Reference

Use the minimum viable schema from the research summary (Section 6) as the baseline. Key additions for this implementation:
- `dependencies[]` — DAG-based ordering
- `applicable_body_types[]` — world-agnostic filtering
- `documentation_url` — engine/AI Manager documentation linkage
- `conditions` — runtime inclusion evaluation

---

## Stop Conditions
- If any of the 4 phase files don't exist at expected paths — list what was found and stop
- If task_v2 inventory differs significantly from research findings — document and continue with actual data

---

**Completed by**: [agent]
**Completion date**: YYYY-MM-DD
**Files created**: [count]
**Output location**: `data/json-data/missions/profiles_v2/`
