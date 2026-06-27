---
title: Luna Bootstrap and Manufacturing Loop
priority: HIGH
status: draft
phase: phase5
relocated_from: reorganization_attempt_3/ (2026-06-22)
relocation_rationale: |
  Luna MVP bootstrap and manufacturing loop is essential for validating the settlement production chain.
  This is part of the phase5 Luna Precursor V2 testing sequence.
  Confirmed Luna MVP blocker with hard gate on prior task completion.
owner: AI Manager / task-v2 runner
type: feature
tags:
  - luna
  - ai-manager
  - settlement
  - manufacturing
  - isru
  - regolith
  - shielding
  - task-v2
dependencies:
  - TaskExecutionEngineV2 is the active execution path
  - "TASK-luna-mvp-ai-manager-settlement.md (2026-06-16) is COMPLETE and reviewed —
     specifically: construct_structure_from_effect and manufacture_from_effect have
     real implementations, not stub log-and-return-true behavior"
  - Luna settlement loop task exists and is in progress
  - No phase 6+ station/depot logic is required for this task
canonical: true
---

## Goal

Prove the AI Manager can bootstrap Luna as a working settlement site and scale its local production loop.

This task is about settlement creation, resource processing, and manufacturing throughput. It is not a station build task.

## ⚠️ Hard Gate — Read Before Starting

Do not start this task until `TASK-luna-mvp-ai-manager-settlement.md` is complete and Claude-reviewed.

That task fixes 5 stub effect handlers in `TaskExecutionEngineV2` that currently log a message and unconditionally return `true` with no real logic — most relevant to this task: `construct_structure_from_effect` and `manufacture_from_effect`. If this Bootstrap task is run before those are real, any "successful" structural printing or manufacturing output you observe is fake — the handler is reporting success without actually creating records, consuming inventory, or producing anything. This would silently reproduce the exact problem the June 16 audit found in `ai_manager_tuning.rake` (decisions gated by fake success rather than real state).

Before starting, confirm in the prior task's synthesis report that both handlers have real implementations. If they don't yet, this task is blocked — escalate to Claude/Tracy rather than working around the stub.

## Context

Luna is the first manufacturing base for the wider Sol system. It must be able to process regolith, capture volatiles, store shielding mass, and output 3D-printed structural components that later support stations, cyclers, and tugs.

The goal is to validate the local production loop before any L1 or LEO depot work begins.

## Scope

In scope:
- Validate settlement siting and initial base bootstrapping on Luna (reuse the siting decision logic built in the prerequisite task — do not build a second siting function).
- Validate regolith processing, volatile extraction, and depleted-regolith stockpiling.
- Validate 3D printing of core structural parts from processed local materials, using the now-real `manufacture_from_effect` implementation.
- Validate that Luna can produce reusable structural outputs for later hybrid infrastructure.
- Validate that the AI Manager can observe state, choose actions, and progress the base without hard-coded future expansion logic.

Out of scope:
- L1 depots.
- LEO depots.
- Station assembly.
- Cycler assembly.
- Tug assembly.
- Later expansion planning beyond the Luna bootstrap loop.
- Re-implementing or duplicating the effect handlers, siting logic, or observable rake task built in the prerequisite task — extend them, don't rebuild them.

## Expected outputs

The task should demonstrate:
- A working Luna settlement loop.
- A visible manufacturing chain from raw regolith to processed regolith to depleted regolith.
- At least one printed structural output suitable for later reuse in station or transit infrastructure.
- A stockpile of depleted regolith that can later be used as shielding mass.
- Clear logging that shows what the AI Manager selected and why.

## Manufacturing targets

The task should treat Luna as a source of shared structural mass and shielding material.

Early outputs should include the kinds of components that later systems reuse — see the canonical shared component list in `2026-06-17-HIGH-FEATURE-SHARED-Hybrid-Structural-Component-Catalog.md` and `GALAXY-GAME-IMAGE-ASSETS.md` (Cycler Component Catalog section) rather than re-deriving this list:
- Structural truss segments.
- Structural ring frames.
- Hull panels.
- Utility spine sections.
- Docking frameworks.
- Cargo lattice modules.
- Radiation shielding tank segments.

## Success criteria

- Luna can be established as a working settlement site.
- The local production loop can process regolith into useful outputs.
- Depleted regolith is treated as a valuable byproduct, not waste.
- The AI Manager can make state-driven choices without random or hard-coded placeholder logic.
- The task completes with enough observable output for a local agent or Claude to review the result.
- No effect handler reports success without a corresponding real state change (inventory consumed, record created, etc.) — this is a hard requirement carried over from the prerequisite task, not optional here.

## Review questions

- What current data models already support regolith processing and local manufacturing?
- Which printed parts are already represented in the codebase or JSON data?
- What production bottlenecks appear first under a Luna bootstrap load?
- What additional follow-up tasks are needed after the first successful run?
