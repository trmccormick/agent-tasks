---
status: active
priority: HIGH
type: architecture
system_domain: INFRASTRUCTURE
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

You are the Implementation Agent.

Project: galaxy_game
Task file (active): /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/active/2026-07-01-HIGH-ARCHITECTURE-HUB-BUS-ROUTING.md

READ FIRST:
1) /Users/tam0013/Documents/git/agent-tasks/README.md
2) /Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md
3) The task file above

REQUIRED: Create the STATUS SYNTHESIS REPORT in the /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/summaries folder before making any
changes, then wait for human approval.

---

# TASK: Hub Bus Routing — Implement register_to_bus Engine Action

**Status**: BACKLOG
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-07-01

---

## Prerequisites — READ FIRST (Sequential Order)

1. Workflow: /Users/tam0013/Documents/git/agent-tasks/README.md
2. Project Guide: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/README.md
3. Design Doc: docs/architecture/systems/LUNA_ISRU_GAS_PROCESSING_AND_SKIMMER_OPERATIONS.md
4. Port Connection System: docs/architecture/systems/PORT_CONNECTION_SYSTEM.md
5. This Task File: Everything below

---

## Context

The Planetary Umbilical Hub is a ground-deployed network unit that acts as a
bus/switch for all Luna surface infrastructure. Units do not connect directly
to each other — they register their ports with the hub, and the hub handles
routing of power, gases, and data between all connected units.

The LegacyPortAdapter (Option C) is complete (commit b3beff34) and handles
legacy port schemas. However the register_to_bus engine action does not exist
yet in TaskExecutionEngineV2#execute_effect. The current
task_deploy_gas_separator.json uses connect_units which is architecturally
incorrect for hub-based routing.

Design reference: docs/architecture/systems/LUNA_ISRU_GAS_PROCESSING_AND_SKIMMER_OPERATIONS.md

---

## Architecture Gotchas

GOTCHA 1: Units register TO the hub, not connect to each other
- Wrong: connect_units between Gas Separator and Cryo Tank directly
- Right: Both units register to hub; hub routes gas between them
- Why: Hub is the bus — all routing goes through it

GOTCHA 2: Do not remove connect_units from execute_effect
- Wrong: Replace connect_units with register_to_bus in the engine
- Right: Add register_to_bus as a NEW case alongside connect_units
- Why: connect_units is used elsewhere and must remain functional

GOTCHA 3: Verify existing connect_units callers before migrating
- Wrong: Assume only gas separator needs migration
- Right: Grep ALL task JSON files for connect_units involving hub units
- Why: Migration scope may be larger than just gas separator

GOTCHA 4: Read the Planetary Umbilical Hub unit definition first
- Wrong: Assume hub port structure without reading the actual JSON
- Right: Find and read the hub blueprint before implementing routing
- Why: Hub port types determine how register_to_bus routes traffic

---

## Problem Statement

TaskExecutionEngineV2#execute_effect has no register_to_bus case.
task_deploy_gas_separator.json uses connect_units which bypasses the hub
routing model entirely. Units should register to the hub, not connect
directly to each other.

Current behavior: Gas separator connects directly to hub and tanks via
connect_units — architecturally incorrect.

Expected behavior: Each unit registers to hub via register_to_bus; hub
routes gases based on port types and allowed_formulas.

---

## Files Involved

### Primary Files — edit these

app/services/ai_manager/task_execution_engine_v2.rb
  Add register_to_bus case to execute_effect

data/json-data/missions/tasks_v2/task_deploy_gas_separator.json
  Migrate from connect_units to register_to_bus

spec/services/ai_manager/task_execution_engine_v2_spec.rb
  Add targeted tests for register_to_bus action

### Reference Files — read only

docs/architecture/systems/LUNA_ISRU_GAS_PROCESSING_AND_SKIMMER_OPERATIONS.md
docs/architecture/systems/PORT_CONNECTION_SYSTEM.md
app/services/lookup/legacy_port_adapter.rb

---

## Implementation Steps

### Step 1 — Pre-checks (paste all output before proceeding)

grep -r "connect_units" /Users/tam0013/Documents/git/galaxyGame/data/json-data/missions/tasks_v2/ | grep -i "hub\|umbilical"

grep -n "def execute_effect\|when 'connect_units'\|when 'register'" galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb

### Step 2 — Read Planetary Umbilical Hub unit definition
Find and read the hub blueprint and operational data JSON. Understand
what ports it exposes and how it is expected to route between units.

### Step 3 — Add register_to_bus case to execute_effect
Add new case in execute_effect alongside existing connect_units case.
Do not remove or modify the connect_units case.

### Step 4 — Implement register_to_bus_from_effect
Method should identify the unit, identify the hub, register the unit
ports with the hub, and return success/failure result.

### Step 5 — Migrate task_deploy_gas_separator.json
Replace all connect_units calls with register_to_bus format.
Each unit registers once to the hub. Hub handles routing.

### Step 6 — Add targeted tests

docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/task_execution_engine_v2_spec.rb --format documentation 2>&1 | tail -30'

### Step 7 — Run Luna rake suite

docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rake luna_mission 2>&1 | tail -40'

Expected: 12/13 baseline maintained or improved.

Save synthesis report to:
/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/summaries/YYYY-MM-DD-HUB-BUS-ROUTING-SYNTHESIS.md
Confirm filename in chat when saved.

---

## Acceptance Criteria

- register_to_bus case exists in execute_effect
- register_to_bus_from_effect implemented
- task_deploy_gas_separator.json migrated from connect_units
- connect_units remains functional for all existing callers
- Targeted specs pass: 0 failures
- Luna rake suite at 12/13 baseline or better

---

## Stop Conditions

- Planetary Umbilical Hub unit definition missing or incomplete
- register_to_bus routing logic requires architectural decision
- Luna rake suite drops below 12/13 baseline
- Any change breaks existing connect_units callers
- Any architectural decision required

---

## Dependencies

Blocked by: None
Blocks: Full Luna precursor automated build validation
Design ref: docs/architecture/systems/LUNA_ISRU_GAS_PROCESSING_AND_SKIMMER_OPERATIONS.md

---

## Commit Instructions

git add galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb
git add galaxy_game/data/json-data/missions/tasks_v2/task_deploy_gas_separator.json
git add galaxy_game/spec/services/ai_manager/task_execution_engine_v2_spec.rb
git commit -m "feat: add register_to_bus engine action; migrate gas separator to hub bus routing"
git push

---

## Completion Report

Completed by: qwen3.6:35b
Completion date: 2026-07-02
Final test result: 19 examples, 0 failures (targeted spec); 13/13 Luna rake suite

What was changed:
- task_execution_engine_v2.rb — Added register_to_bus case to execute_effect; implemented register_to_bus_from_effect with capacity-based port registration and register_bus_routing_rules for downstream hub routing
- task_deploy_gas_separator.json — Migrated from 4 connect_units calls to single register_to_bus action with multi-port format; cryo tank links handled as downstream hub routing rules
- task_execution_engine_v2_spec.rb — Added 6 targeted specs: unit/hub validation, single-port registration, multi-port registration, non-hub rejection, and routing rule deduplication

Follow-up tasks needed:
- Sabatier Reactor task (CH4 production chain)
- Remaining connect_units → register_to_bus migrations (PVE, solar array, robot ports, RTG tasks)

---

## Handoff Summary
HANDOFF SUMMARY: 3 files updated | register_to_bus engine action added alongside connect_units; gas separator migrated to hub bus routing format | Luna rake suite 13/13 baseline confirmed; follow-up migrations pending
