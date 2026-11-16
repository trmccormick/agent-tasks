---
title: Luna Simulation Loop — Expansion Blueprint and AI Manager Option Selection
priority: HIGH
status: draft
phase: phase5
owner: AI Manager / task-v2 runner
type: feature
tags:
  - luna
  - ai-manager
  - simulation
  - expansion
  - task-v2
  - blueprint
dependencies:
  - Luna settlement exists
  - L1 settlement exists
  - current codebase and JSON state data reviewed
relocated_from: reorganization_attempt_3/ (2026-06-22)
relocation_rationale: |
  This task is part of the Luna Precursor V2 bootstrap testing sequence.
  It generates expansion options for the AI Manager to evaluate during the simulation loop.
  This is a prerequisite for validating the full AI Manager decision-making before phase6+ lava-tube construction begins.
  Works alongside the rake task luna_mission:execute which handles the bootstrap phases (power_comms, isru_deployment, gas_processing).
---

## Goal

Create a simulation-loop task that lets the AI Manager inspect the current Luna/L1 state, generate valid expansion options, and emit a generic task file for task-v2 execution.

This task is a blueprint for expansion decisions, not the implementation of every expansion feature.

## Context

The Luna MVP requires the initial settlement to be established before broader expansion logic is allowed. Expansion planning must be driven by location state, available resources, infrastructure readiness, and current simulation outcomes.

The AI Manager should evaluate the local situation, choose among valid options, and produce a task artifact that can later be reviewed by Claude or a local agent.

### Current Architecture Baseline (as of 2026-08-08)

**tasks_v2/ missions_v2/** use flat lists with `task_ref` + static parameters — no conditional/scoring logic today. This task is meant to add decision logic on top of that baseline. The AI Manager's current capabilities are limited to:
- `Settlements::CostAnalyzer` — autonomous cost analysis (Phase 1)
- `Logistics::ManifestGenerator` — smart sourcing (player → NPC → Earth priority) (Phase 1)
- `Logistics::ShortageDetector` — resource monitoring (Phase 1)
- `ImportRequestGenerator` — shortage prediction (Phase 1)
- `StrategySelector` — pattern matching for life-support prioritization (Phase 1)

None of these provide the location-driven expansion option generation this task requires. That logic must be built as a new capability layer.

### L1 Dependency Status — BLOCKED (verified 2026-08-08)

**"L1 settlement exists" is NOT currently true.** Confirmed via codebase search:
- Zero matches for `L1.*Station` in `db/seeds.rb`, `db/migrate/`, or `data/` — no L1 settlement record exists in any seed data.
- The only real L1 content is two bare manifest files (`l1_station_depot_manifest_v1.json`, `leo_depot_construction_manifest_v1.json`) — no profile, no phases, no deployment data.
- Codebase has infrastructure stubs (cost calculations, trade service looking for `/L1.*Station/`, location operations job routing) — these are placeholder gateways that would only activate once an L1 settlement record exists.
- **This is a hard block.** The task cannot proceed until Phase 7 (Depot Building) creates a deployed L1 base with profile, plan, and phases.

### Luna Dependency Status — UNBLOCKED

**"Luna settlement exists" is confirmed true.** Settlement ID 103, Luna Base, seeded and validated per 2026-08-08 simulation work.

## Scope

In scope:
- Validate that Luna and L1 exist before expansion planning begins.
- Inspect current location/state data from the codebase and JSON fixtures.
- Generate a small set of plausible expansion options based on local conditions.
- Select the most appropriate option using AI Manager logic.
- Serialize the result into a generic task-v2 compatible task file.
- Record the decision and any useful simulation metadata for later review.

Out of scope:
- Implementing the full expansion system.
- Adding new settlement infrastructure beyond the simulation loop.
- Hard-coding future phase systems that have not been validated.
- Replacing existing task-v2 conventions or generic task file format.

## Expected behavior

The task should:
1. Confirm Luna and L1 are present as prerequisite game state.
2. Read the current location and resource state.
3. Produce multiple expansion options with brief rationale.
4. Let the AI Manager choose one option based on the situation.
5. Write the chosen option into a generic task file for downstream execution.
6. Preserve enough metadata for a reviewer to understand why the decision was made.

## Success criteria

- The simulation loop can run without assuming unfinished expansion systems exist.
- The AI Manager returns a location-driven choice, not a hard-coded one.
- The task output is compatible with task-v2/generic task file handling.
- A local agent or Claude can review the task and refine details from the codebase and JSON data.
- The task remains valid even if the chosen option later changes after simulation review.

## Questions for review

- What current code paths already provide location, settlement, or resource state?
- What JSON data structures should this task read or emit?
- What existing task-v2 conventions must be followed?
- What is the smallest useful set of expansion options for the AI Manager?
- What simulation metadata should be preserved for the next review pass?

## Deliverables

- A reviewed and refined task definition.
- A clear mapping from state inputs to expansion options.
- A generic task file produced by the simulation loop.
- Notes on any codebase or data assumptions that need follow-up tasks.