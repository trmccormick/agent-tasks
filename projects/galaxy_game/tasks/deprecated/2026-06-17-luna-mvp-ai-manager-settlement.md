# TASK: Luna MVP — AI Manager Observable Settlement Decision Loop
**Created**: 2026-06-16
**Assigned to**: qwen3.5-35b-a3b (Ryzen 7) — heavy executor
**Reviewer**: Claude (web) — synthesis report review before approval
**Priority**: Critical — this is the Phase 5+ MVP blocker

---

## Context (read first — do not skip)

A full review of 14 existing AI Manager test/rake files was conducted to determine current real state vs aspirational/stubbed state. Findings:

**Mature and proven — do not touch:**
- `TerraSim` (atmosphere/biosphere/geosphere simulation) — confirmed mature across `terraforming_demo.rake`, `venus_simulate.rake`, `mars_simulate.rake`, `venus_mars_pipeline.rake`. This is the original core of the game and is out of scope for this task.
- `AIManager::TerraformingManager` — real phase-based decision logic (warming/maintenance phases, gas transfer, Sabatier synthesis). Out of scope for this task.
- `LaunchPaymentService`, GCC/USD account flow — proven in `ai_manager_sol_test.rake` (GCC bootstrap). Reusable.
- ISRU mass-balance math (TEU→PVE→component chain) — proven in `lunar_base_with_isru_pipeline.rake`. Reusable.

**The actual gap (what this task addresses):**
- `AIManager::TaskExecutionEngineV2` is the correct, current, data-driven foundation (no hardcoded world/material logic, real precondition gates with real errors in `deploy_unit_from_effect`). It replaces the old `TaskExecutionEngine` (v1), which is **deprecated** — v1 has hardcoded world-specific strings and several methods that fake success unconditionally ("continuing anyway for AI simulation"). **Do not extend v1. Build only on v2.**
- V2 itself has 5 stub effect handlers that currently just log and return `true` with no real logic: `connect_units_from_effect`, `construct_structure_from_effect`, `set_unit_state_from_effect`, `check_unit_state_from_effect`, `manufacture_from_effect`.
- **There is no real settlement-siting decision logic anywhere in the codebase.** `generic_base_build.rake` has real scoring methods (`calculate_resource_score`, `calculate_hazard_score`, `calculate_isru_score`) based on actual celestial body properties, but they're isolated in a rake task, not wired into the AI Manager's execution flow. Everything else that looks like "AI decision-making" (`ai_sol_system_builder.rake`'s hardcoded 7-phase sequence, `ai_manager_teaching.rake`'s "curriculum," `ai_manager_tuning.rake`'s decision tree) is either hardcoded sequencing or **literally gated by `rand()` calls with no connection to real game state**. None of that is reusable logic — treat it as historical evidence of scope drift, not a foundation to build on.

**Why this matters for phases:** the hardcoded phase sequences found in old files (Luna→L1→Venus→Mars→Belt→Titan, all in one rake task) are a root cause of the phase-numbering conflicts across documentation. This task is scoped to Luna ONLY. Do not build, reference, or extend logic for any other body.

---

## Corrected Phase Framing (for context — do not implement phases beyond Luna in this task)

The MVP progression has been clarified as:

| Phase | Focus |
|---|---|
| **5+** | Luna loop (THIS TASK) |
| 6+ | Station building, depot establishment (depends on 5+ being solid) |
| 7+ | Shipyard, orbital craft construction |
| 8+ | Phobos/Deimos hollowing, station fit-out |
| 9+ | Cycler bulk material loops |
| 10+ | Limited terraforming testing |
| 11+ | Natural wormhole, expansion beyond Sol |

Luna must work first because every later phase depends on the settlement decision loop proven here. Do not start work on station/shipyard/cycler logic in this task — flag it as future work if you notice dependencies, but stay scoped to Luna.

---

## Task Scope

### 1. Implement real logic for the 5 stub effect handlers in `TaskExecutionEngineV2`

Each currently looks like:
```ruby
def construct_structure_from_effect(effect)
  structure_type = effect['structure']
  puts "  → construct_structure not yet implemented: #{structure_type}"
  true
end
```

Replace with real implementations. Reuse patterns already proven elsewhere in the codebase:

- **`construct_structure_from_effect`** — model on `lunar_base_with_isru_pipeline.rake`'s skylight construction flow: create a real `Structures::BaseStructure` or `CelestialBodies::Features::*` record, create a `ConstructionJob` via `ConstructionJobService.create_job`, attach material requests, return false if materials can't be sourced (do not auto-succeed).
- **`manufacture_from_effect`** — model on `LunarBaseProductionService#manufacture_component` from `lunar_base_with_isru_pipeline.rake`. Must actually consume input inventory and produce output inventory via the settlement's real inventory system, not just print a message.
- **`set_unit_state_from_effect`** — must actually update `Units::BaseUnit#operational_data['state']` on the real unit record and persist it. Return false if the unit doesn't exist.
- **`check_unit_state_from_effect`** — must actually query the real unit and compare state. Return false on mismatch — **do not** copy v1's "continuing anyway" pattern that always returns true.
- **`connect_units_from_effect`** — at minimum, verify both units exist and are owned by the settlement before returning true. If a port/connection data model exists, use it; if not, flag this as a gap in your synthesis report rather than inventing one.

**Hard rule: no effect handler may return `true` without checking real state.** If real state can't be checked because a needed model/association doesn't exist, return `false` and document the gap — do not fake success.

### 2. Build a real settlement-siting decision function

Currently `TaskExecutionEngineV2#plan_tasks` only builds a task list from manifest phases or a capability fallback list (`["power", "habitat", "comms"]`) — it never decides *where* on Luna to settle or *what* to prioritize building based on actual conditions.

Build a new method (suggest: `AIManager::SettlementSitingService` or similar — your call on the right place for this, but keep it out of `TaskExecutionEngineV2` itself so it stays a single-responsibility class) that:

- Takes a target celestial body (Luna) and evaluates real candidate locations using actual data — reuse the scoring approach from `generic_base_build.rake`'s `calculate_resource_score`/`calculate_hazard_score`/`calculate_isru_score`, but wire it to real Luna location/feature data (lava tubes, regolith composition, existing `CelestialBodies::Features::LavaTube`/`Skylight` records) instead of generic body-level stats only.
- **No `rand()` anywhere in this decision logic.** Every decision must trace to a real data point (resource availability, existing structures, distance, hazard data).
- Outputs a decision with visible reasoning — not just a score number, but a short text explanation of *why* (e.g. "Selected Marius Hills Lava Tube: pre-surveyed, skylight present, regolith composition favorable for ISRU per [data source]").

### 3. Wire the decision into execution + produce observable output

This is the part Tracy explicitly wants to see: a single rake task (e.g. `rake ai:luna:settle`) that:

- Calls the new siting decision logic first and **prints/logs the decision and reasoning before doing anything else**
- Then drives `TaskExecutionEngineV2` to execute the resulting task plan step by step
- Logs every effect execution with clear pass/fail (reuse the `✓`/`✗` style already used elsewhere, it's readable)
- **Stops on first failure** rather than continuing past it — do not replicate v1's "continue anyway" pattern anywhere in this new task
- At the end, prints a clear summary: what was decided, what was built, what failed, current settlement/inventory state

This should be runnable as a rake task so Tracy can watch it execute and interrupt it (Ctrl+C) if something looks wrong, per his explicit request.

### 4. Synthesis report (required before Claude review)

When done, produce a short report covering:
- Which of the 5 stub handlers got real implementations, and which (if any) you couldn't complete and why
- What the settlement-siting decision function actually checks and how it scores candidates
- Any gaps found in underlying models (e.g. missing port/connection data model) that blocked full implementation
- Full output of one real run of `rake ai:luna:settle` against current Luna data

---

## Explicit Out-of-Scope (do not touch)

- TerraSim, TerraformingManager, any Venus/Mars/Titan logic
- Any phase 6+ logic (stations, shipyards, cyclers)
- The old `TaskExecutionEngine` (v1) — do not extend it, do not delete it yet either (other rake tasks may still reference it; flag for later cleanup instead)
- `ai_manager_teaching.rake`, `ai_manager_tuning.rake`, `ai_sol_system_builder.rake` — do not fix or extend these; they represent the scope-drift problem, not a foundation. Flag for archival/deletion in your report but don't spend time on them.

---

## Escalation Criteria

- If a fresh local session hits a tool execution failure, start a new session with this task file — do not retry in the same session.
- If you find a genuine architectural blocker (e.g. a core model is missing entirely, not just a stub), stop and report back to Claude/Tracy rather than improvising a workaround that touches out-of-scope systems.
- Do not recreate any spec or service file from scratch — edit in place only.

---

## Deprecation Note

This task has been superseded by a set of more focused phase-specific task files.

Recommended replacements:
- `2026-06-17-HIGH-FEATURE-LUNA-Bootstrap-Manufacturing-Loop.md`
- `2026-06-17-HIGH-FEATURE-SHARED-Hybrid-Structural-Component-Catalog.md`
- `2026-06-17-MEDIUM-FEATURE-L1-LEO-Depot-Staging-Cost-Reduction.md`

Reason for deprecation:
- The original task was too broad for a single execution pass.
- The later task split keeps Luna bootstrap, shared hybrid component definitions, and depot staging logically separated.
- This prevents scope drift and makes review easier for local agents and Claude.

Important context preserved from the original task:
- `TaskExecutionEngineV2` remains the correct active foundation.
- The old `TaskExecutionEngine` v1 remains deprecated and should not be extended.
- Luna remains the Phase 5+ blocker.
- No phase 6+ station/depot logic belongs in the Luna bootstrap task.
- The real bottleneck is manufacturing capacity, not spin-gravity capability.

This file is retained only as historical context and should not be used as the active source of truth for implementation.
