---
status: draft
resume: false
priority: MEDIUM
type: architecture
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## Draft status

This is a review-only draft. It is not approved for backlog or implementation dispatch.

## Agent Dispatch Interface

**Status**: DRAFT — NOT READY FOR DISPATCH. Human review and approval are required first.

No models, migrations, persistence APIs, UI behavior, or simulation state changes are authorized by this draft.

# TASK: Player Plans, UI State, and Simulation State Boundaries Review

**Status**: DRAFT  
**Priority**: MEDIUM  
**Type**: architecture  
**Created**: 2026-11-05  
**Author**: ChatGPT (draft for human review)

## Context

TriffTools distinguishes named, reusable user configurations from a tool's last-used interface state. Both are scoped to the authenticated user. This is a useful product/data-model distinction, but GalaxyGame is a persistent simulation and must not treat player preferences or plans as authoritative world state.

Potential GalaxyGame examples include saved vehicle configurations, production plans, mining plans, construction layouts, and remembered map/view selections. These are examples to investigate, not approved feature requirements.

An existing GalaxyGame backlog task addresses persistence of AI Manager `SystemState` using Rails cache. That system-wide computed/operational state is a different concern and must not be conflated with player-owned saved plans or UI preferences:
- `projects/galaxy_game/tasks/backlog/phase08-shipyards/2026-06-30-MEDIUM-ARCHITECTURE-SYSTEM-STATE-PERSISTENCE.md`

The current GalaxyGame UI prototype and its wiki-recovery work may also already define some of these behaviors. Inspect those sources before proposing anything new.

External reference:
- TriffTools saved setups: https://github.com/NarcisussX/Triff.Tools-Source/blob/main/backend/routes/toolSetups.js
- TriffTools last-used tool state: https://github.com/NarcisussX/Triff.Tools-Source/blob/main/backend/routes/toolState.js

## Purpose

Determine whether GalaxyGame needs explicit, separate concepts for:
1. Player-owned reusable plans/configurations.
2. Per-player remembered interface state and preferences.
3. Actual persistent simulation state and active operations.

Recommend appropriate ownership, persistence, and lifecycle boundaries only where the current implementation or documented roadmap has a real gap.

## Review scope

1. Inspect current player/account models, relevant UI state handling, saved configurations, blueprints, construction plans, operational jobs, and persistence patterns.
2. Review the existing SystemState persistence task and related AI Manager architecture so this work does not duplicate or undermine it.
3. Identify existing player-facing configuration workflows and determine whether they represent reusable plans, active orders, or merely UI state.
4. Identify candidate data that should be player-owned and reusable, candidate data that should be remembered as interface state, and data that must remain simulation-owned.
5. Consider lifecycle questions: edit, save, copy, rename, delete, activate, apply, share, and version a saved plan. Do not assume every operation is needed.
6. Consider permissions and ownership boundaries for players, factions/corporations, NPC organizations, and administrators where supported by current design.
7. Review the UI prototype wiki-recovery work and existing tasks for overlap.
8. Recommend whether a generic persistence mechanism is appropriate or whether domain-specific models and validation are necessary.

## Required constraints

- Never store actual world/simulation state in a generic user-preference or tool-state JSON blob.
- A saved plan/configuration is not an active job, construction order, vehicle state, or production result.
- Do not propose a generic tool-key API solely because TriffTools uses one.
- Do not create models, migrations, APIs, UI controls, or persistence code.
- Do not make assumptions about multiplayer/faction sharing that are not supported by established GalaxyGame intent.
- Do not duplicate the existing AI Manager SystemState persistence task.
- Follow the code/wiki authority principle and surface conflicts rather than silently resolving them.

## Deliverable

Save a synthesis report in `projects/galaxy_game/summaries/` containing:

- Existing persistence and configuration patterns with verified paths.
- A three-way classification: player plans/configurations, remembered UI state, authoritative simulation state.
- Existing capabilities versus gaps.
- Relevant existing task/wiki references and overlap assessment.
- Recommended ownership and lifecycle rules.
- A decision on whether generic persistence, domain-specific persistence, or no new mechanism is appropriate.
- Any narrowly scoped follow-up tasks, with dependencies and suggested priority.

## Acceptance criteria

- [ ] Existing models, UI behavior, and relevant task/wiki decisions are inspected.
- [ ] Existing SystemState persistence work is explicitly distinguished and not duplicated.
- [ ] Candidate data is classified by ownership and authority.
- [ ] The report addresses lifecycle and permissions only where supported by current design.
- [ ] Any proposed persistence abstraction is justified against current domain needs.
- [ ] No code, schema, UI, or runtime behavior is changed.

## Source note

TriffTools is a reference for distinguishing saved configurations from last-used interface state. Its generic tool-oriented storage model is not a prescribed GalaxyGame architecture.
