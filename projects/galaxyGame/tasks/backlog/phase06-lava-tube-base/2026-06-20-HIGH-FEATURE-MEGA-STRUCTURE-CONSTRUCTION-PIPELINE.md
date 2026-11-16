---
status: backlog
priority: HIGH
type: feature
system_domain: AI_MANAGER | STRUCTURES | MANUFACTURING
mvp_alignment: LUNA_PRECURSOR_MISSION_2
local_worker_safe: false
created: 2026-06-20
---

# TASK: Mega-Structure Construction Pipeline — Wire Worldhouse System to TaskExecutionEngineV2

**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-06-20

---

## Context

Sealing the Luna lava tube is a worldhouse project (enclosing a geological volume). The mega-structure system models/concerns exist but are not wired to TaskExecutionEngineV2, and no task_v2 files define the construction pipeline. This task creates the construction pipeline tasks_v2 files and wires them to the engine.

**Existing infrastructure:**
- `Worldhouse` model — tracks segments, coverage %, construction_complete?
- `Coverable` concern — status workflow: uncovered → materials_requested → under_construction → primary_cover → full_cover
- `Enclosable` concern — base enclosure functionality
- `SegmentCoveringService` — covers segments
- `MegaProject` model — mega-project tracking

**What's missing:**
1. task_v2 template file for standardizing new tasks
2. Construction pipeline task_v2 files (6 tasks)
3. Simulation layer task_v2 files (3 tasks, Phase 6+)
4. Consolidation of two atmosphere models into unified service
5. Wiring to TaskExecutionEngineV2 effects

**Phase alignment:**
- **Phase 6**: Construction pipeline (structural enclosure only — sealing is optional when resources allow)
- **Phase 6+**: Simulation layer (failure analysis, maintenance, pressurization lifecycle)

---

## Acceptance Criteria

### Step 1 — Create task_v2 template
- [ ] Create `/data/json-data/templates/task_v2_template.json` with standard structure
- [ ] Template includes metadata, tasks array, effects schema, example usage in profile

### Step 2 — Generate construction pipeline task_v2 files (Phase 6)
Create these 6 files in `/data/json-data/missions/tasks_v2/`:
- [ ] `task_worldhouse_segment_fabrication.json` — 3D print structural segments from regolith
- [ ] `task_worldhouse_segment_transport.json` — Move segments to deployment site
- [ ] `task_worldhouse_segment_installation.json` — Anchor segments to geological feature
- [ ] `task_worldhouse_bracing_installation.json` — Install structural bracing between segments
- [ ] `task_worldhouse_panel_deployment.json` — Deploy covering panels (transparent cover, radiation shielding)
- [ ] `task_worldhouse_sealing_and_pressurization.json` — Seal joints, pressurize (optional when resources allow)

Each task must:
- Use parameter-driven design (no hardcoded body names or locations)
- Include effects array with deploy_unit/connect_units/set_unit_state/check_unit_state
- Define success_criteria as measurable outcomes
- Follow the template structure

### Step 3 — Generate simulation layer task_v2 files (Phase 6+)
Create these 3 files in `/data/json-data/missions/tasks_v2/`:
- [ ] `task_enclosed_habitat_atmosphere_simulation.json` — Unified atmosphere tracking
- [ ] `task_enclosed_habitat_failure_analysis.json` — TTR modeling, failure cascade detection
- [ ] `task_enclosed_habitat_maintenance_scheduling.json` — Degradation tracking, repair scheduling

### Step 4 — Consolidate atmosphere models
- [ ] Review existing `enclosed_habitat_atmosphere.rb` and `enclosed_habitat/atmosphere.rb`
- [ ] Consolidate into unified `EnclosedHabitat::AtmosphereService`
- [ ] Remove duplicate model, keep one source of truth

### Step 5 — Wire to TaskExecutionEngineV2
- [ ] Verify existing stub handlers (`connect_units_from_effect`, `set_unit_state_from_effect`) are implemented (from Luna Precursor V2 task)
- [ ] Confirm construction pipeline tasks execute via TaskExecutionEngineV2 effects
- [ ] Document the lava tube sealing sequence in mission profile format

---

## Stop Conditions — escalate to user immediately if:
- Step 4 reveals the two atmosphere models have incompatible schemas that can't be consolidated without data loss
- Implementing wiring reveals a need for `construct_structure_from_effect` or `manufacture_from_effect` — stop and report, do not implement those stubs without explicit go-ahead
- Any task_v2 file needs mission-specific data (body names, locations) — stop and report, this violates the parameter-driven design principle

---

## Dependencies
**Blocked by**: Luna Precursor V2 Sequence Reconciliation (stub handler implementation)
**Blocks**: Mission 2 scoping (lava tube sealing), Phase 6+ simulation layer work
**Related tasks**: 2026-06-19-HIGH-VERIFICATION-LUNA-PRECURSOR-V2-SEQUENCE-RECONCILIATION.md

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Final test result**: X examples, Y failures

### What was changed
- `[file]` — [description]

### Issues discovered
[Any problems found during implementation that weren't anticipated]

### Follow-up tasks needed
[List only — do not create the files]

### Lessons learned
[What worked, what didn't]
