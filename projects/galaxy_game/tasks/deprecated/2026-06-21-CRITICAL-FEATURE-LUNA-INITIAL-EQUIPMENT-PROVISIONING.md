---
status: obsolete
priority: CRITICAL
type: feature
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

# TASK: Luna Mission — Initial Equipment Provisioning

**Status**: OBSOLETE (2026-07-03)
**Priority**: CRITICAL 🔴
**Type**: feature
**Created**: 2026-06-21
**Obsolete Date**: 2026-07-03
**Obsolete Reason**: "resources section" was never designed — task based on assumption, not architecture

---

## Obsolescence Analysis

**Original premise**: "Luna settlement spawns with ZERO equipment because neither the mission profile nor the manifest contains a `resources` section."

**Reality check**: There was **never a design for a `resources` section** in mission profiles. Looking at all three profile structures:
- `luna_settlement_profile_v1.json` (luna_base_establishment/) — no resources section
- `luna_base_establishment_profile_v1.json` (profiles/) — uses `operational_requirements` instead
- `npc_base_deploy_profile_v1.json` — separate manifest files

**How equipment provisioning actually works**: The DRAFT manifest's `required_hardware` array is how equipment provisioning works for the precursor build. The rake seeds inventory from this into `settlement.inventory.items` (lines 75-108 of luna_mission.rake).

**The engine's `@manifest["resources"]["initial_equipment"]` at line 44 is dead code** — it was written based on an assumption that was never designed or implemented.

---

## Action Required

**No implementation needed.** This task can be archived as obsolete. The premise was based on a design assumption, not actual architecture
**How equipment provisioning actually works**: The DRAFT manifest's `required_hardware` array is how equipment provisioning works for the precursor build. The rake seeds inventory from this into `settlement.inventory.items` (lines 75-108 of luna_mission.rake).

**The engine's `@manifest["resources"]["initial_equipment"]` at line 44 is dead code** — it was written based on an assumption that was never designed or implemented.

---

## Action Required

**No implementation needed.** This task can be archived as obsolete. The premise was based on a design assumption, not actual architecture
**How equipment provisioning actually works**: The DRAFT manifest's `required_hardware` array is how equipment provisioning works for the precursor build. The rake seeds inventory from this into `settlement.inventory.items` (lines 75-108 of luna_mission.rake).

**The engine's `@manifest["resources"]["initial_equipment"]` at line 44 is dead code** — it was written based on an assumption that was never designed or implemented.

---

## Action Required

**No implementation needed.** This task can be archived as obsolete. The premise was based on a design assumption, not actual architecture
**How equipment provisioning actually works**: The DRAFT manifest's `required_hardware` array is how equipment provisioning works for the precursor build. The rake seeds inventory from this into `settlement.inventory.items` (lines 75-108 of luna_mission.rake).

**The engine's `@manifest["resources"]["initial_equipment"]` at line 44 is dead code** — it was written based on an assumption that was never designed or implemented.

---

## Action Required

**No implementation needed.** This task can be archived as obsolete. The premise was based on a design assumption, not actual architecture
**How equipment provisioning actually works**: The DRAFT manifest's `required_hardware` array is how equipment provisioning works for the precursor build. The rake seeds inventory from this into `settlement.inventory.items` (lines 75-108 of luna_mission.rake).

**The engine's `@manifest["resources"]["initial_equipment"]` at line 44 is dead code** — it was written based on an assumption that was never designed or implemented.

---

## Action Required

**No implementation needed.** This task can be archived as obsolete. The premise was based on a design assumption, not actual architecture
**How equipment provisioning actually works**: The DRAFT manifest's `required_hardware` array is how equipment provisioning works for the precursor build. The rake seeds inventory from this into `settlement.inventory.items` (lines 75-108 of luna_mission.rake).

**The engine's `@manifest["resources"]["initial_equipment"]` at line 44 is dead code** — it was written based on an assumption that was never designed or implemented.

---

## Action Required

**No implementation needed.** This task can be archived as obsolete. The premise was based on a design assumption, not actual architecture
**How equipment provisioning actually works**: The DRAFT manifest's `required_hardware` array is how equipment provisioning works for the precursor build. The rake seeds inventory from this into `settlement.inventory.items` (lines 75-108 of luna_mission.rake).

**The engine's `@manifest["resources"]["initial_equipment"]` at line 44 is dead code** — it was written based on an assumption that was never designed or implemented.

---

## Action Required

**No implementation needed.** This task can be archived as obsolete. The premise was based on a design assumption, not actual architecture
**How equipment provisioning actually works**: The DRAFT manifest's `required_hardware` array is how equipment provisioning works for the precursor build. The rake seeds inventory from this into `settlement.inventory.items` (lines 75-108 of luna_mission.rake).

**The engine's `@manifest["resources"]["initial_equipment"]` at line 44 is dead code** — it was written based on an assumption that was never designed or implemented.

---

## Action Required

**No implementation needed.** This task can be archived as obsolete. The premise was based on a design assumption, not actual architecture
**How equipment provisioning actually works**: The DRAFT manifest's `required_hardware` array is how equipment provisioning works for the precursor build. The rake seeds inventory from this into `settlement.inventory.items` (lines 75-108 of luna_mission.rake).

**The engine's `@manifest["resources"]["initial_equipment"]` at line 44 is dead code** — it was written based on an assumption that was never designed or implemented.

---

## Action Required

**No implementation needed.** This task can be archived as obsolete. The premise was based on a design assumption, not actual architecture
**How equipment provisioning actually works**: The DRAFT manifest's `required_hardware` array is how equipment provisioning works for the precursor build. The rake seeds inventory from this into `settlement.inventory.items` (lines 75-108 of luna_mission.rake).

**The engine's `@manifest["resources"]["initial_equipment"]` at line 44 is dead code** — it was written based on an assumption that was never designed or implemented.

---

## Action Required

**No implementation needed.** This task can be archived as obsolete. The premise was based on a design assumption, not actual architecture
**How equipment provisioning actually works**: The DRAFT manifest's `required_hardware` array is how equipment provisioning works for the precursor build. The rake seeds inventory from this into `settlement.inventory.items` (lines 75-108 of luna_mission.rake).

**The engine's `@manifest["resources"]["initial_equipment"]` at line 44 is dead code** — it was written based on an assumption that was never designed or implemented.

---

## Action Required

**No implementation needed.** This task can be archived as obsolete. The premise was based on a design assumption, not actual architecture
**How equipment provisioning actually works**: The DRAFT manifest's `required_hardware` array is how equipment provisioning works for the precursor build. The rake seeds inventory from this into `settlement.inventory.items` (lines 75-108 of luna_mission.rake).

**The engine's `@manifest["resources"]["initial_equipment"]` at line 44 is dead code** — it was written based on an assumption that was never designed or implemented.

---

## Action Required

**No implementation needed.** This task can be archived as obsolete. The premise was based on a design assumption, not actual architecture
**How equipment provisioning actually works**: The DRAFT manifest's `required_hardware` array is how equipment provisioning works for the precursor build. The rake seeds inventory from this into `settlement.inventory.items` (lines 75-108 of luna_mission.rake).

**The engine's `@manifest["resources"]["initial_equipment"]` at line 44 is dead code** — it was written based on an assumption that was never designed or implemented.

---

## Action Required

**No implementation needed.** This task can be archived as obsolete. The premise was based on a design assumption, not actual architecture
**How equipment provisioning actually works**: The DRAFT manifest's `required_hardware` array is how equipment provisioning works for the precursor build. The rake seeds inventory from this into `settlement.inventory.items` (lines 75-108 of luna_mission.rake).

**The engine's `@manifest["resources"]["initial_equipment"]` at line 44 is dead code** — it was written based on an assumption that was never designed or implemented.

---

## Action Required

**No implementation needed.** This task can be archived as obsolete. The premise was based on a design assumption, not actual architecture
**How equipment provisioning actually works**: The DRAFT manifest's `required_hardware` array is how equipment provisioning works for the precursor build. The rake seeds inventory from this into `settlement.inventory.items` (lines 75-108 of luna_mission.rake).

**The engine's `@manifest["resources"]["initial_equipment"]` at line 44 is dead code** — it was written based on an assumption that was never designed or implemented.

---

## Action Required

**No implementation needed.** This task can be archived as obsolete. The premise was based on a design assumption, not actual architecture.

---

## Context

Luna settlement spawns with ZERO equipment because neither the mission profile nor the manifest contains a `resources` section. TaskExecutionEngineV2 expects `@manifest["resources"]["initial_equipment"]` at line 44 but finds nothing — all unit deployment tasks fail with `MaterialShortageError`.

**Architecture clarification**: The DRAFT manifest (`lunar_precursor_manifest_v2_DRAFT.json`) defines cargo to be transported from Earth (the deployment plan). The profile's `resources` section defines what the settlement spawns with BEFORE any deployment tasks run. These are two different things:
- **DRAFT manifest** = what needs to arrive at Luna (cargo)
- **Profile `resources`** = what the settlement starts with (initial state)

**Relevant Architecture Docs:**
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb` — engine resource loading (lines 39-47)

---

## Problem Statement

Luna mission profile lacks a `resources` section. Without it, TaskExecutionEngineV2 cannot populate the settlement environment with initial equipment, crew, or budget. All Phase 1-3 unit deployment tasks fail with `MaterialShortageError`.

**Current behavior**: `@manifest["resources"]` is nil → `@environment[:equipment]` is nil → every `deploy_unit_from_effect` raises `MaterialShortageError`.
**Expected behavior**: Profile contains `resources.initial_equipment` array with all 7 required units for Phases 1-3.

---

## Architecture Gotchas

⚠️ **GOTCHA 1**: Resources go in the PROFILE, not the manifest index file
- ❌ Wrong: Add resources to `luna_base_establishment_manifest_v2.json` (it's just an asset index)
- ✅ Right: Add resources section to `luna_settlement_profile_v1.json`
- Why: The profile is what TaskExecutionEngineV2 loads as `@manifest` — the rake task passes the profile path, not the manifest index

⚠️ **GOTCHA 2**: Equipment IDs must match blueprint unit IDs exactly
- ❌ Wrong: Use display names like "Solar Expansion Rig" or "Comms Equipment"
- ✅ Right: Use blueprint `id` fields from the actual JSON files
- Why: deploy_unit_from_effect looks up equipment by ID in the blueprint registry

⚠️ **GOTCHA 3**: Phase 4 was added to profile — include it in success_conditions too
- The profile already has Phase 4 (`robot_logistics_site_hardening`) wired in from the completed implementation
- Any resources section must not break Phase 4 task execution

---

## Files Involved

### Primary Files — edit these
| File | Purpose | Key Section |
|---|---|---|
| `data/json-data/missions/luna_base_establishment/luna_settlement_profile_v1.json` | Add `resources` section with initial_equipment, initial_crew, budget | After `success_conditions` block |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb` (lines 39-47) | Shows resource loading logic |
| `data/json-data/blueprints/units/production/solar_expansion_rig_bp.json` | Get exact blueprint ID for Phase 1 |
| `data/json-data/blueprints/units/infrastructure/comms_equipment_bp.json` | Get exact blueprint ID for Phase 1 |
| `data/json-data/blueprints/units/infrastructure/planetary_umbilical_hub_bp.json` | Get exact blueprint ID for Phase 1 |
| `data/json-data/blueprints/units/production/refineries/sabatier_reactor_bp.json` | Check if Sabatier should be included (Phase 5+) |

---

## Implementation Steps

### Step 1 — Identify all required equipment IDs
Read the blueprint files for each unit needed in Phases 1-4. List exact `id` values:
- Phase 1: Solar Expansion Rig, Comms Equipment, Planetary Umbilical Hub
- Phase 2: Thermal Extraction Unit (TEU), Planetary Volatiles Extractor Mk1, Inflatable Pressure Tank
- Phase 3: Gas Separator, additional Inflatable Pressure Tank
- Phase 4: Robot Charging Port (x2)

### Step 2 — Add resources section to luna_settlement_profile_v1.json
```json
"resources": {
  "initial_crew": 5,
  "initial_equipment": [
    { "id": "<blueprint_id>", "quantity": <count> },
    ...
  ],
  "budget": 50000
}
```

### Step 3 — Validate JSON syntax
```bash
python3 -c "import json; json.load(open('data/json-data/missions/luna_base_establishment/luna_settlement_profile_v1.json'))" && echo "VALID"
```

### Step 4 — Verify with rake (after RSpec tests complete)
```bash
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rake luna_mission:execute 2>&1
```
Expected: No `MaterialShortageError` for inventory checks. Tasks may still fail on other criteria but should attempt execution.

---

## Acceptance Criteria
- [ ] `luna_settlement_profile_v1.json` has `resources` section with `initial_crew`, `initial_equipment`, and `budget`
- [ ] Equipment list includes all 7+ required units for Phases 1-4 (unique IDs + quantities)
- [ ] JSON syntax validated
- [ ] `rake luna_mission:execute` shows 0 `MaterialShortageError` for inventory checks
- [ ] Phase 1-3 tasks attempt to execute (may fail on other criteria, but not inventory)

---

## Stop Conditions — escalate to user immediately if:
- Equipment IDs cannot be determined from blueprint files
- Adding resources breaks existing Phase 4 task execution
- Any architectural decision required about which units belong in initial provisioning vs. phase-specific deployment

---

## Dependencies
**Blocked by**: None
**Blocks**: Luna Phase 1 execution 🔴 CRITICAL — all unit deployments fail without this
**Related**: Phase 4 Robot Logistics (completed), Sabatier Reactor (completed)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `[file]` — [description of change]

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

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are the Implementation Agent.

Project: galaxy_game

Task file (backlog): /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/phase5+/2026-06-21-CRITICAL-FEATURE-LUNA-INITIAL-EQUIPMENT-PROVISIONING.md

READ FIRST:
1) /Users/tam0013/Documents/git/agent-tasks/README.md
2) /Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md
3) The task file above, then move it to active/ and update status to active.

REQUIRED: Create the STATUS SYNTHESIS REPORT in the task file before making any changes, then wait for human approval.
```

---

## Context

Luna settlement spawns with ZERO equipment because neither the mission profile nor the manifest contains a `resources` section. TaskExecutionEngineV2 expects `@manifest["resources"]["initial_equipment"]` at line 44 but finds nothing — all unit deployment tasks fail with `MaterialShortageError`.

**Relevant Architecture Docs:**
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb` — engine resource loading (lines 39-47)

---

## Problem Statement

Luna mission profile and manifest lack a `resources` section. Without it, TaskExecutionEngineV2 cannot populate the settlement environment with initial equipment, crew, or budget. All Phase 1-3 unit deployment tasks fail with `MaterialShortageError`.

**Current behavior**: `@manifest["resources"]` is nil → `@environment[:equipment]` is nil → every `deploy_unit_from_effect` raises `MaterialShortageError`.
**Expected behavior**: Profile or manifest contains `resources.initial_equipment` array with all 7 required units for Phases 1-3.

---

## Architecture Gotchas

⚠️ **GOTCHA 1**: Resources go in the PROFILE, not the manifest index file
- ❌ Wrong: Add resources to `luna_base_establishment_manifest_v2.json` (it's just an asset index)
- ✅ Right: Add resources section to `luna_settlement_profile_v1.json`
- Why: The profile is what TaskExecutionEngineV2 loads as `@manifest` — the rake task passes the profile path, not the manifest index

⚠️ **GOTCHA 2**: Equipment IDs must match blueprint unit IDs exactly
- ❌ Wrong: Use display names like "Solar Expansion Rig" or "Comms Equipment"
- ✅ Right: Use blueprint `id` fields from the actual JSON files
- Why: deploy_unit_from_effect looks up equipment by ID in the blueprint registry

⚠️ **GOTCHA 3**: Phase 4 was added to profile — include it in success_conditions too
- The profile already has Phase 4 (`robot_logistics_site_hardening`) wired in from the completed implementation
- Any resources section must not break Phase 4 task execution

---

## Files Involved

### Primary Files — edit these
| File | Purpose | Key Section |
|---|---|---|
| `data/json-data/missions/luna_base_establishment/luna_settlement_profile_v1.json` | Add `resources` section with initial_equipment, initial_crew, budget | After `success_conditions` block |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb` (lines 39-47) | Shows resource loading logic |
| `data/json-data/blueprints/units/production/solar_expansion_rig_bp.json` | Get exact blueprint ID for Phase 1 |
| `data/json-data/blueprints/units/infrastructure/comms_equipment_bp.json` | Get exact blueprint ID for Phase 1 |
| `data/json-data/blueprints/units/infrastructure/planetary_umbilical_hub_bp.json` | Get exact blueprint ID for Phase 1 |
| `data/json-data/blueprints/units/production/refineries/sabatier_reactor_bp.json` | Check if Sabatier should be included (Phase 5+) |

---

## Implementation Steps

### Step 1 — Identify all required equipment IDs
Read the blueprint files for each unit needed in Phases 1-4. List exact `id` values:
- Phase 1: Solar Expansion Rig, Comms Equipment, Planetary Umbilical Hub
- Phase 2: Thermal Extraction Unit (TEU), Planetary Volatiles Extractor Mk1, Inflatable Pressure Tank
- Phase 3: Gas Separator, additional Inflatable Pressure Tank
- Phase 4: Robot Charging Port (x2)

### Step 2 — Add resources section to luna_settlement_profile_v1.json
```json
"resources": {
  "initial_crew": 5,
  "initial_equipment": [
    { "id": "<blueprint_id>", "quantity": <count> },
    ...
  ],
  "budget": 50000
}
```

### Step 3 — Validate JSON syntax
```bash
python3 -c "import json; json.load(open('data/json-data/missions/luna_base_establishment/luna_settlement_profile_v1.json'))" && echo "VALID"
```

### Step 4 — Verify with rake (after RSpec tests complete)
```bash
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rake luna_mission:execute 2>&1
```
Expected: No `MaterialShortageError` for inventory checks. Tasks may still fail on other criteria but should attempt execution.

---

## Acceptance Criteria
- [ ] `luna_settlement_profile_v1.json` has `resources` section with `initial_crew`, `initial_equipment`, and `budget`
- [ ] Equipment list includes all 7+ required units for Phases 1-4 (unique IDs + quantities)
- [ ] JSON syntax validated
- [ ] `rake luna_mission:execute` shows 0 `MaterialShortageError` for inventory checks
- [ ] Phase 1-3 tasks attempt to execute (may fail on other criteria, but not inventory)

---

## Stop Conditions — escalate to user immediately if:
- Equipment IDs cannot be determined from blueprint files
- Adding resources breaks existing Phase 4 task execution
- Any architectural decision required about which units belong in initial provisioning vs. phase-specific deployment

---

## Dependencies
**Blocked by**: None
**Blocks**: Luna Phase 1 execution 🔴 CRITICAL — all unit deployments fail without this
**Related**: Phase 4 Robot Logistics (completed), Sabatier Reactor (completed)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
