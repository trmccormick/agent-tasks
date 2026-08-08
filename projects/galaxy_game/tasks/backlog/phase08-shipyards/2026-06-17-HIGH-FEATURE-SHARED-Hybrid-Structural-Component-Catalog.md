---
title: Shared Hybrid Structural Component Catalog
priority: HIGH
status: draft
phase: phase5+
owner: AI Manager / task-v2 runner
type: analysis
tags:
  - luna
  - stations
  - cyclers
  - tugs
  - hybrid
  - components
  - manufacturing
  - task-v2
canonical: true
---

## Goal

Define the shared structural component family that Luna, stations, cyclers, and tugs can all reuse.

This task focuses on reusable industrial parts, not full vehicle or station assembly.

## ⚠️ Note Before Starting — Source Material Already Exists

`GALAXY-GAME-IMAGE-ASSETS.md` (committed 2026-06-16) already documents this exact component family in full, under "Cycler Component Catalog (20 components, split by manufacturing origin)." It includes the same 8 Lunar structural / 8 Earth precision / 4 hybrid breakdown, a priority generation order, and locked visual design decisions (sourced from a detailed ChatGPT design discussion, not invented for that document).

This task should **start from that file**, not re-derive the list from scratch. The value-add of this task is:
1. Confirming which of those 20 components already exist as real code/JSON definitions (vs. only existing as image-generation prompts)
2. Mapping each component to its actual model/data representation, if one exists
3. Flagging components that need new model/data definitions before they can be used in task-v2 task files

Treat the existing list as the canonical naming/scope reference. Do not rename or restructure the component families without a clear reason — downstream tasks (e.g. Luna Bootstrap Manufacturing Loop) already reference these exact names.

## Context

The game uses a hybrid manufacturing model:
- Luna provides bulk regolith-derived structure and shielding mass.
- Earth provides precision modules and high-value hardware.
- Stations, cyclers, and tugs share a common design language and may reuse many of the same structural parts.

The purpose of this task is to make that overlap explicit so future tasks can consume a stable component catalog.

## Scope

In scope:
- Cross-reference the shared component list (see note above) against current codebase and JSON data.
- Identify which parts are Lunar-built, Earth-built, or hybrid — this classification already exists in the source document, confirm it still holds against actual code.
- Note which parts can be reused across Luna, stations, cyclers, and tugs.
- Capture the minimum prompt or data fields needed for asset generation and task-v2 use.
- Identify gaps — components with an image-generation prompt but no corresponding model/data definition.

Out of scope:
- Full vehicle assembly.
- Full station assembly.
- UI integration for assets.
- Balancing manufacturing costs.
- Re-deriving the component list or visual style guide — both already exist in `GALAXY-GAME-IMAGE-ASSETS.md`.

## Shared component families

Canonical list — see `GALAXY-GAME-IMAGE-ASSETS.md` Cycler Component Catalog for full origin/description breakdown (Lunar-built, Earth-built, Hybrid):

- Structural truss segment.
- Structural ring frame.
- Hull panel section.
- Utility spine section.
- Docking framework.
- Cargo lattice module.
- Radiation shield tank segment.
- Habitat drum shell.
- Agricultural drum shell.
- Industrial manufacturing drum shell.
- Observatory module shell.

## Reuse rules

- Lunar-built parts should favor regolith composite and structural bulk.
- Earth-built parts should favor precision systems, avionics, life support, and docking hardware.
- Hybrid parts should combine Lunar shells with Earth interiors or control modules.

## Success criteria

- The component catalog is cross-referenced against actual code/JSON — not just restated from the source document.
- Each part has a manufacturing origin, reuse intent, and a clear note on whether it exists as real data yet.
- The catalog can be used for both settlement and orbital infrastructure planning.
- The output is detailed enough for a local agent to refine against the codebase and asset guide.

## Review questions

- Which components already exist in code, JSON, or image asset documentation? (Most already exist as image-generation prompts per `GALAXY-GAME-IMAGE-ASSETS.md` — the open question is code/data, not naming.)
- Which parts should be shared between station and cycler builds?
- Which parts are only valid for Luna surface construction?
- What missing component definitions should be split into follow-up tasks?
