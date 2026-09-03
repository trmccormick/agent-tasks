---
status: backlog
priority: HIGH
type: feature
system_domain: OTHER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
created: 2026-08-19
last_updated: 2026-09-03
# 2026-09-03: status corrected active → backlog (was in backlog/current/ with stale "active" header)
phase_status: "Phases 1-4 COMPLETE (commits c9d44ca4, 1f8df564, 683327b5). Phase 5 (TransitEngine integration) PENDING."
# DISPATCH ORDERING — do not dispatch a task whose depends_on is not yet completed.
# Phase 5 extends Mission::TransitEngine, which is created by the Transit Timing Engine task.
depends_on:
  - 2026-08-18-HIGH-FEATURE-LAUNCH-WINDOW-TRANSIT-TIMING-ENGINE   # Phase 5 extends this engine
blocks: []
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [x] All Step 0-N instructions are clear and actionable (not vague)
- [x] Synthesis report template is provided (copy/paste ready, not as example)
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is READY FOR DISPATCH (Phase 5 only — Phases 1-4 already complete).**

> ⚠️ **STATE NOTE (flag for human):** This task is in `backlog/current/` but marked `status: active`
> with Phases 1-4 already committed. Only Phase 5 remains. Consider whether the completed phases
> should be split out to `completed/2026-08/` and only Phase 5 kept active, OR keep as one task
> and dispatch Phase 5. Confirm intended handling before dispatch.

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-19-HIGH-FEATURE-ORBITAL-MECHANICS-DATA-LAYER.md

IMPORTANT — PARTIALLY COMPLETE TASK:
  Phases 1-4 are ALREADY DONE and committed (c9d44ca4, 1f8df564, 683327b5).
  Do NOT redo Phases 1-4. Your ONLY job is Phase 5 (TransitEngine integration).
  Read the "Completed Phases" section to understand what already exists before starting.

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-19-HIGH-FEATURE-ORBITAL-MECHANICS-DATA-LAYER.md \
         projects/galaxy_game/tasks/active/2026-08-19-HIGH-FEATURE-ORBITAL-MECHANICS-DATA-LAYER.md
  (status is already active — verify, do not change)
  Paste the output of the command in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Never leave stale copies in the source folder
  - Verify with: find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks -name "2026-08-19-HIGH-FEATURE-ORBITAL-MECHANICS-DATA-LAYER.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-08-19-FEATURE-ORBITAL-MECHANICS-DATA-LAYER-PHASE5.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Orbital Mechanics Data Layer
**Status**: ACTIVE (Phases 1-4 complete, Phase 5 pending)
**Priority**: HIGH
**Type**: feature
**Created**: 2026-08-19
**Last Updated**: 2026-09-02

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*

- **Template Conformance**: PASS (updated 2026-09-02 to conform to TASK_TEMPLATE.md)
- **Docker Wrapper Check**: PASS — RSpec commands use correct docker exec format
- **MVP Alignment**: VALID — orbital elements are the data foundation for launch window calculation and transit timing (feeds the TransitEngine task)
- **MVP Impact Note**: Without this data layer, the TransitEngine cannot compute real phase angles/launch windows — it falls back to hardcoded transit-day constants
- **Action Line**: READY FOR LOCAL DISPATCH (Phase 5 only) — but confirm handling of already-completed Phases 1-4 first

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Integration work building on already-committed Phases 1-4, well within local Qwen's terminal/tool-use access
**Local attempts before cloud**: N/A
**Supervision Level**: standard

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below (especially the "Completed Phases" section)
4. **TransitEngine task**: `2026-08-18-HIGH-FEATURE-LAUNCH-WINDOW-TRANSIT-TIMING-ENGINE.md` — Phase 5 integrates with this engine

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context

### The Problem
Orbital elements (semi_major_axis, eccentricity, inclination, mean_anomaly) are needed for launch window calculation and transit timing. Currently:

1. **sol-complete.json** only has `orbital_elements` for Jupiter — Earth/Venus/Mars/Luna/Titan missing
2. **SystemBuilderService** explicitly strips `:orbital_elements` (line 255 of system_builder_service.rb) — discards known data
3. **Survey mechanic** is a stub (`perform_survey` at line 351 of task_execution_engine.rb) — doesn't generate values for unknowns

### The Architecture (Three-Layer Design)

**Layer 1: Known Data (Sol)**
- Real astronomical values where we have them (Earth/Venus/Mars/Luna/Titan orbital_elements)
- StarSim fills procedural gaps (mean_anomaly epoch, etc.) for bodies with partial data
- "Unknown" is a valid value — doesn't mean broken, means surveyable

**Layer 2: Procedural Generation (StarSim)**
- StarSim takes known data → generates playable details procedurally
- Local Bubble systems are more incomplete than Sol — need even more procedural generation
- `OrbitalParametersGenerator` already creates semi_major_axis, eccentricity, inclination, orbital_period_days
- **Add mean_anomaly** to OrbitalParametersGenerator (random epoch for position propagation)
- Remove `:orbital_elements` from `special_keys_to_exclude` in SystemBuilderService

**Layer 3: Survey Discovery (Unknown)**
- When a player surveys a body with "Unknown" or missing orbital data, generate plausible values
- Survey results persist (System Survey History — documented in `08_ai_intelligence.md`)
- No redundant scanning of already-surveyed bodies

### Design Precedent
- `AUTOMATIC_TERRAIN_GENERATOR.md`: "For Sol worlds, the system prioritizes real NASA data"
- `wh-expansion.md`: "System Survey History remembers detailed survey results to avoid redundant scanning"
- Pattern: known data first → procedural gaps → survey fills remaining unknowns

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Do not redo Phases 1-4 — they are already committed.
- ❌ Wrong: "Let me re-add the orbital_elements to sol-complete.json and re-run the migration"
- ✅ Right: Verify the existing commits (c9d44ca4, 1f8df564, 683327b5) are present, then build Phase 5 on top
- Why: Re-running the migration or re-editing the JSON risks duplicate columns or data conflicts. The data layer already exists.

⚠️ **GOTCHA 2**: "Unknown" orbital data is a valid state, not a bug.
- ❌ Wrong: "Body has no orbital_elements → raise an error / treat as broken"
- ✅ Right: Treat missing/unknown orbital data as surveyable — the survey mechanic (Phase 4) generates plausible values on demand
- Why: Local Bubble systems are intentionally more incomplete than Sol. "Unknown" is a gameplay state, not a data defect.

⚠️ **GOTCHA 3**: TransitEngine (Phase 5) must READ discovered data, not recompute it from scratch.
- ❌ Wrong: "TransitEngine generates its own orbital parameters independently"
- ✅ Right: TransitEngine reads `orbital_elements` from the CelestialBody record (populated by known data, StarSim, or survey) and computes phase angles/launch windows from that
- Why: The whole point of the three-layer design is a single source of truth. If TransitEngine recomputes, the survey discovery mechanic becomes pointless.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before running any commands or modifying any files, save a synthesis report as MD to the summaries folder covering:
- Confirmation that Phases 1-4 commits are present (list the 3 commit hashes you verified)
- Your understanding of the three-layer design (known → procedural → survey)
- What Phase 5 (TransitEngine integration) specifically requires
- The files you'll touch for Phase 5 (exact paths)
- Your verification plan (how you'll confirm TransitEngine reads discovered orbital data end-to-end)

---

## Problem Statement

**Current behavior**: Phases 1-4 are complete — orbital_elements exist in sol-complete.json/sol.json, SystemBuilderService no longer strips them, OrbitalParametersGenerator produces mean_anomaly, and the survey mechanic populates unknowns. However, Phase 5 (TransitEngine reading this discovered data to compute real phase angles and launch windows) is NOT yet done.

**Expected behavior**: TransitEngine reads the `orbital_elements` (mean_anomaly, semi_major_axis, eccentricity, inclination) from CelestialBody records and computes real phase angles, delta-v for transfer orbits, and plane-change costs — replacing the hardcoded transit-day constants.

---

## Files Involved

### Primary Files — you will create/edit these (Phase 5)
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/app/services/mission/transit_engine.rb` | TransitEngine reads discovered orbital data (Phase 5) | `calculate_transfer_window`, phase-angle computation |

### Reference Files — read but do not edit (Phases 1-4, already done)
| File | Why You Need It |
|---|---|
| `galaxy_game/data/json-data/sol-complete.json` | orbital_elements for Earth/Venus/Mars/Luna/Titan (Phase 1) |
| `galaxy_game/data/json-data/sol.json` | orbital_elements (Phase 1) |
| `galaxy_game/app/services/system_builder_service.rb` | `:orbital_elements` removed from special_keys_to_exclude (Phase 2) |
| `galaxy_game/app/models/celestial_body.rb` | `store_accessor :orbital_elements, ...` (Phase 2) |
| `galaxy_game/app/services/orbital_parameters_generator.rb` | `generate_mean_anomaly` (Phase 3) |
| `galaxy_game/app/services/task_execution_engine.rb` | `perform_survey` (Phase 4) |
| `galaxy_game/db/migrate/20260819120107_add_orbital_elements_to_celestial_bodies.rb` | JSONB column migration (Phase 2) |

### Migration
- [x] No new migration needed for Phase 5 (Phase 2 already added the JSONB column)

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.
>
> ⚠️ **Phases 1-4 (Steps 1-4 below) are ALREADY COMPLETE and committed.** They are documented here
> for context and provenance. Your actionable work is **Step 5 (Phase 5) only**.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
(See Agent Dispatch Interface above — status is already `active`, just verify and move the file.)

### Step 1: Add Known Orbital Elements to sol-complete.json ✅ COMPLETE
Added `orbital_elements` for Earth, Venus, Mars in both sol-complete.json and sol.json using real astronomical values.

**Earth:**
```json
"orbital_elements": {
  "semi_major_axis": 149597870700.0,
  "eccentricity": 0.0167,
  "inclination": 0.0,
  "mean_anomaly": 357.52
}
```

**Venus:**
```json
"orbital_elements": {
  "semi_major_axis": 108208000000.0,
  "eccentricity": 0.0068,
  "inclination": 3.39,
  "mean_anomaly": 50.12
}
```

**Mars:**
```json
"orbital_elements": {
  "semi_major_axis": 227943800000.0,
  "eccentricity": 0.0934,
  "inclination": 1.85,
  "mean_anomaly": 19.38
}
```

**Luna (relative to Earth):**
```json
"orbital_elements": {
  "semi_major_axis": 384400000.0,
  "eccentricity": 0.0549,
  "inclination": 5.14,
  "mean_anomaly": 115.34
}
```

**Titan (relative to Saturn):**
```json
"orbital_elements": {
  "semi_major_axis": 1221870000.0,
  "eccentricity": 0.0288,
  "inclination": 0.33,
  "mean_anomaly": 0.0
}
```

**Note:** Luna and Titan were already present in sol-complete.json from prior work. Jupiter was already present.

### Step 2: Stop SystemBuilderService from Discarding Known Data ✅ COMPLETE
Removed `:orbital_elements` from `special_keys_to_exclude` in `system_builder_service.rb:255`.

**Before:**
```ruby
:geological_features, :magnetosphere, :magnetic_field_strength, :rotation_period, :orbital_elements, # Additional attributes
```

**After:**
```ruby
:geological_features, :magnetosphere, :magnetic_field_strength, :rotation_period, # Additional attributes
```

Also added:
- Migration `20260819120107_add_orbital_elements_to_celestial_bodies.rb` — adds JSONB column
- `store_accessor :orbital_elements, :semi_major_axis, :eccentricity, :inclination, :mean_anomaly` in CelestialBody model

### Step 3: Add mean_anomaly to OrbitalParametersGenerator ✅ COMPLETE
Added `mean_anomaly` generation for bodies without known orbital_elements.

**Added to `OrbitalParametersGenerator#generate`:**
```ruby
mean_anomaly: generate_mean_anomaly
```

**Added private method:**
```ruby
def generate_mean_anomaly
  rand(0.0..360.0).round(2) # Random epoch in degrees
end
```

### Step 4: Implement Survey Discovery for Unknown Bodies ✅ COMPLETE
Implemented `perform_survey(task)` in TaskExecutionEngine.

**Survey flow:**
1. Check if body has `orbital_elements` (from known data or StarSim)
2. If missing/unknown → use OrbitalParametersGenerator to create plausible values
3. Persist to body record (update DB with discovered orbital_elements)
4. Record in System Survey History (operational_data on mission or new survey_record association)
5. Return survey results to player

### Step 5: TransitEngine Reads Discovered Data ⬅️ ACTIONABLE (Phase 5)
Once bodies have orbital_elements (from known data, StarSim, or survey), TransitEngine computes real phase angles and launch windows.

**TransitEngine reads:**
- `mean_anomaly` + `orbital_period` → propagate positions forward from epoch
- `semi_major_axis` + `eccentricity` → compute delta-v for transfer orbits
- `inclination` → plane change costs

**Implementation guidance:**
- Extend `Mission::TransitEngine` (see `2026-08-18-HIGH-FEATURE-LAUNCH-WINDOW-TRANSIT-TIMING-ENGINE.md`) to read `CelestialBody#orbital_elements` instead of relying solely on hardcoded transit-day constants.
- Fall back to the constant baseline (e.g., 146d Earth→Venus) ONLY when a body has no `orbital_elements` (unknown/unsurveyed) — never raise.
- Add a method to propagate mean_anomaly forward by sim_day to compute current phase angle.
- Keep the simplified Hohmann approximation for MVP; real orbital mechanics is out of scope.

### Step 6: Verify

> CRITICAL EXECUTION MANDATE: All RSpec commands must use the Docker wrapper below.
> The container working directory is already /home/galaxy_game — do NOT add cd /home/galaxy_game.
> Never run bare local test commands. Never fabricate test results. Actually run the specs.

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/mission/transit_engine_spec.rb 2>&1 | tail -20'
```

Expected result: TransitEngine reads discovered orbital data; phase-angle/launch-window tests pass; unknown-body fallback works without raising.

### Step 7: Synthesis Report (before committing anything)
Save to summaries folder. Do not commit until explicitly approved.

---

## Acceptance Criteria
- [x] sol-complete.json has orbital_elements for Earth/Venus/Mars/Luna/Titan (Phase 1)
- [x] SystemBuilderService no longer strips orbital_elements (Phase 2)
- [x] OrbitalParametersGenerator generates mean_anomaly for procedural bodies (Phase 3)
- [x] Survey mechanic populates unknown orbital data and persists results (Phase 4)
- [ ] TransitEngine reads discovered orbital data (mean_anomaly, semi_major_axis, eccentricity, inclination) from CelestialBody records (Phase 5)
- [ ] TransitEngine falls back to constant baseline for unknown/unsurveyed bodies without raising (Phase 5)
- [ ] RSpec suite green for transit engine (Phase 5)
- [ ] No regressions in existing orbital/mission specs
- [ ] Full suite run completed and logged (human runs overnight — agent does not trigger)

---

## Stop Conditions — escalate to user immediately if:
- Phase 5 requires a new migration or schema change (Phase 2 should have covered it — flag if not)
- TransitEngine integration requires changes to `Game#advance_by_days` or `GameSimulationJob` (architectural decision needed)
- The `orbital_elements` store_accessor structure doesn't support the fields TransitEngine needs
- Real orbital mechanics (beyond simplified Hohmann) is required to make launch windows correct (scope expansion)
- Any architectural decision is required about how phase angles are computed/persisted

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container.

```bash
git add galaxy_game/app/services/mission/transit_engine.rb
git commit -m "feat: TransitEngine reads discovered orbital_elements — real phase angles + launch windows (Phase 5)"
```

**Task file move on completion:**
```bash
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy_game/tasks/active/2026-08-19-HIGH-FEATURE-ORBITAL-MECHANICS-DATA-LAYER.md \
       projects/galaxy_game/tasks/completed/2026-08/2026-08-19-HIGH-FEATURE-ORBITAL-MECHANICS-DATA-LAYER.md
git commit -m "chore: move orbital mechanics data layer to completed — all 5 phases done"
```

---

## Documentation
- [x] No doc changes needed (code + JSON only)
- [ ] If phase-angle computation design decisions are made: flag in DECISIONS.md for future reference

---

## Dependencies
**Blocked by**:
- Phases 1-4 (already complete — commits c9d44ca4, 1f8df564, 683327b5)
- `2026-08-18-HIGH-FEATURE-LAUNCH-WINDOW-TRANSIT-TIMING-ENGINE.md` — Phase 5 *extends* `Mission::TransitEngine`, which that task creates. **Do not dispatch this task until the Transit Timing Engine task is complete.**
**Blocks**: (none)
**Related tasks**: StarSim procedural generation; Survey discovery mechanic

> ⚠️ **DISPATCH ORDERING**: This task's Phase 5 depends on the Transit Timing Engine task. Do not dispatch a task whose `depends_on` is not yet completed.

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Final test result**:

### What was changed
- Phase 5: TransitEngine reads discovered orbital data

### Issues discovered

### Follow-up tasks needed

### Lessons learned

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: Phases 1-4 committed (c9d44ca4, 1f8df564, 683327b5) | Phase 5 TransitEngine integration [pending/done] | next: wire into launch window calculation
