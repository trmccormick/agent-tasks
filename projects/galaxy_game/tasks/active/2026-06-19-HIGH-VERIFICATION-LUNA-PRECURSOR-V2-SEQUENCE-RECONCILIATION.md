---
status: active
priority: HIGH
type: verification
system_domain: AI_MANAGER | TASK_EXECUTION | LUNA_MVP
mvp_alignment: LUNA_PRECURSOR_MISSION_1
local_worker_safe: false
created: 2026-06-19
updated: 2026-06-19
---

# TASK: Luna Precursor V2 Sequence Reconciliation — Verify, Fix Stubs, Build Test Rake

**Status**: ACTIVE
**Priority**: HIGH
**Type**: verification
**Created**: 2026-06-19

---

## Context

This is Mission 1 (surface staging) readiness work — power, comms, ISRU
(TEU/PVE), and gas processing must work end-to-end and produce real
resources before Mission 2 (lava tube prep: airlocks import, I-beam
printing, floor prep with depleted regolith) can be meaningfully scoped.
Lava tube habitat construction itself is explicitly OUT of scope for this
task.

Luna's precursor build has two manifest generations:
- **v1** (`lunar_precursor.json`, `luna_base_establishment_profile_v1.json`,
  `lunar_precursor_deployment_v1.json`): confirmed real, complete task list
  including later construction steps (I-beam printer, shell printer, lava
  tube sealing/pressurization) using `construct_structure`/`set_structure_state`
  effects. This generation also used to be named with "Starship" instead of
  the current "Heavy Lift Transport (HLT)" — `starship_precursor_manifest_v1.json`
  and siblings are the historical predecessor (found in Time Machine backup),
  confirmed structurally identical apart from the craft rename and a switch
  from loose unit-name matching to canonical full-name matching.
- **v2** (`luna_base_establishment_manifest_v2.json` → three phase files →
  `tasks_v2/` library): the current intended architecture, using the shared
  140+-task reusable library. Only three phases exist so far —
  `phase_1_power_comms`, `phase_2_isru_deployment`, `phase_3_gas_processing`
  — referencing eight specific `tasks_v2/` files. This generation only uses
  `deploy_unit`, `connect_units`, and `set_unit_state` effects; it does not
  yet include construction-phase effects at all.

Per the 2026-06-16 audit, `TaskExecutionEngineV2` is the correct current
foundation (data-driven, real precondition gates, real errors via
`InfrastructureSequenceError`/`MaterialShortageError` in
`deploy_unit_from_effect`), but has 5 stub effect handlers that log and
return `true` unconditionally: `connect_units_from_effect`,
`construct_structure_from_effect`, `set_unit_state_from_effect`,
`check_unit_state_from_effect`, `manufacture_from_effect`. The v1
`TaskExecutionEngine` is deprecated but intentionally still in the
codebase — do not remove it, just don't extend it.

`starship_integration_precursor_mission.rb` is a real, mostly-sound
integration test script, but it is stale: it points at
`starship_precursor_manifest_v1.json`, which no longer exists under that
name (the data now lives in `lunar_precursor.json`, already
HLT-renamed). Its phase-execution step (step 11) is also a no-op —
comments only, no actual effect execution. This script should be replaced,
not patched.

**Relevant docs to read before starting:**
- `docs/new_agent/projects/galaxy_game/research/LUNA-MVP-SIMULATION-DESIGN.md`
- `docs/new_agent/rules/GUARDRAILS.md`
- The eight `tasks_v2/` files referenced in Acceptance Criteria below

---

## Acceptance Criteria

### Step 1 — File existence verification
- [ ] Confirm these exist under `tasks_v2/` (or locate actual path):
  `task_deploy_solar_rig.json`, `task_deploy_comms_equipment.json`,
  `task_deploy_puh_and_ppmu.json`, `task_deploy_thermal_extraction_unit.json`,
  `task_deploy_pve_unit.json`, `task_inflatable_tank_deployment.json`,
  `task_deploy_gas_separator.json`, `task_deploy_volatiles_storage.json`
- [ ] Confirm `phase_1_power_comms.json`, `phase_2_isru_deployment.json`,
  `phase_3_gas_processing.json`, and `luna_settlement_profile_v1.json`
  exist and reference the above correctly

### Step 2 — TaskExecutionEngineV2 stub status verification
- [ ] Confirm the 5 stubs listed in Context are still stubs (re-verify,
  don't trust the 2026-06-16 audit blindly — confirm independently)
- [ ] Confirm the eight v2 task files above use ONLY `deploy_unit`,
  `connect_units`, `set_unit_state` (and `check_unit_state` for
  preconditions) — no `construct_structure` or `manufacture` calls in this
  sequence

### Step 3 — Reconcile existing-but-unwired tasks (report, don't fix without sign-off)
- [ ] **(a) Naming mismatch**: `task_deploy_gas_separator.json` connects
  Gas Separator outputs to a unit named "Cryogenic Storage Tank" — no task
  in the current three phases deploys a unit by that exact name (closest
  match is "Inflatable Cryogenic Tank" from `task_deploy_volatiles_storage.json`).
  Check `Lookup::UnitLookupService` for which name (if either) resolves.
  Report which one is correct; do not edit the task file yourself.
- [ ] **(b)** Confirmed already: "Inflatable Gas Storage" is real
  (`inflatable_gas_storage_data.json` exists with full schema). No action
  needed, just acknowledge in your report.
- [ ] **(c) Shell printing exists but is unsequenced**: Tanks are placed
  deflated, inflated to shape ONLY at low/safe pressure using initial
  TEU/PVE gas output (not full operating pressure — unsafe without
  structural support), shell is printed around the tank at this
  low-pressure shaped state, THEN pressurized to full operating spec.
  `inflatable_cryo_tank.json` already has this exact stage list
  (`transport → anchor → inflate → print_shell → pressurize`) with
  `progress_percent`/`time_remaining_hours` fields. The shell-print tasks
  already exist (`task_print_inflatable_tank_shells.json`,
  `task_regolith_shell_printer_operations.json`) but are NOT referenced by
  `phase_2_isru_deployment.json` or `phase_3_gas_processing.json`. Confirm
  whether anything currently reads/advances `deployment.stages` for any
  unit. Report the correct insertion point in phase order (logically:
  after inflate, before pressurize). Do not edit phase files yourself.
- [ ] **(d) Landing pad — resolved, not missing**: No dedicated
  "construct landing pad" task exists, but
  `task_surface_preparation_unit_operations.json`'s success criteria
  explicitly covers landing pad prep, using the same Surface Preparation
  Unit already used in v1's `prepare_lava_tube_entrance` task. This is
  existing functionality, just not yet sequenced into v2. Pad is needed
  before a second HLT lands nearby (Mission 2 resupply), not necessarily
  before the first landing. Report where in phase order this belongs
  (likely final task of Phase 3, before any Mission 2 resupply). Do not
  edit phase files yourself.
- [ ] **(e) Three overlapping tank-deployment tasks — needs a decision**:
  `task_inflatable_tank_deployment.json` (generic, world-agnostic),
  `task_deploy_volatiles_storage.json` (Luna-specific, concrete units,
  currently referenced by the phase files), and `tank_farm_setup.json`
  (parameterized/templated, explicitly reusable across lunar/asteroid/depot
  contexts, NOT currently referenced by any of the three phase files).
  Report whether `tank_farm_setup.json` is superseded, intended for a
  different context (e.g. depot/cycler operations), or genuinely redundant.
  Do not archive or delete anything yourself.
- [ ] **(f) Non-executable pattern templates — do not treat as broken**:
  `ai_decision_framework_and_manifest_optimization.json`,
  `ai_site_analysis_and_manifest_optimization.json`, and
  `regolith_processing_and_infrastructure_construction.json` use a
  different schema (`inputs`/`outputs`/`dependencies`/`pattern_reference`,
  no `effects`/`priority`/`completion_criteria`). These are likely abstract
  AI-Manager planning-pattern templates, not executable tasks. Do not
  attempt to run them through `TaskExecutionEngineV2`. Report what (if
  anything) currently consumes the `pattern_reference` schema; if nothing
  does, note as present-but-unconsumed and move on.
- [ ] **(g) Present but out of today's scope**:
  `task_begin_regolith_harvest_for_solar_array.json` exists, not referenced
  by current phases, likely belongs to a future Phase 4 (I-beam/construction
  prep). Report its existence; do not wire it in.

### Step 4 — Implement (only after Steps 1-3 reported back and reviewed)
- [ ] Implement real logic for `connect_units_from_effect`: verify both
  named units exist and are deployed, verify named ports exist in each
  unit's `operational_data`, raise an error following the existing
  `InfrastructureSequenceError`/`MaterialShortageError` pattern (do not log
  and return `true`)
- [ ] Implement real logic for `set_unit_state_from_effect`: verify the
  named unit exists, verify the target state is a valid state for that
  unit type, raise an error if invalid rather than silently succeeding
- [ ] Do NOT implement `construct_structure_from_effect` or
  `manufacture_from_effect` — not used by this sequence, out of scope
- [ ] Do NOT implement fixes for any Step 3 findings — those need
  human review first

### Step 5 — Build the test rake task (only after Step 4 passes)
- [ ] Create a new rake task replacing
  `starship_integration_precursor_mission.rb` (stale: points at a manifest
  that no longer exists under that name, and its phase-execution step is a
  no-op)
- [ ] New rake task loads `luna_settlement_profile_v1.json`, executes
  `phase_1_power_comms` → `phase_2_isru_deployment` →
  `phase_3_gas_processing` in order via `TaskExecutionEngineV2`
- [ ] Reports per-task pass/fail using real state checks, not "no error
  raised"
- [ ] Reports final unit/connection state at the end for human visual
  confirmation
- [ ] Explicitly and clearly reports that the run currently stops short of
  a fully working inflatable-tank sequence (per Step 3c) and does not
  include landing pad construction (per Step 3d) — these are known,
  separate follow-ups, not silent gaps. Do not round up to "complete."

---

## Stop Conditions — escalate to user immediately if:
- Step 1 finds any file missing or mismatched beyond what's flagged in
  Step 3 — stop and report, do not guess at corrections
- Implementing `connect_units_from_effect` or `set_unit_state_from_effect`
  reveals a real need for `construct_structure` or `manufacture` to work
  correctly — stop and report, do not implement those two without
  explicit go-ahead
- Test suite reaches or exceeds 10 failures at any point — halt immediately
- Any temptation arises to edit `task_deploy_gas_separator.json`,
  `task_deploy_volatiles_storage.json`, `tank_farm_setup.json`, or any
  phase file directly — stop, report the finding instead, this needs
  human review first
- Any temptation arises to write new `tasks_v2/` files for the
  inflate/shell/pressurize or landing-pad gaps — stop, report only

---

## Dependencies
**Blocked by**: none
**Blocks**: Mission 2 scoping (lava tube floor prep, airlock import,
I-beam printing), landing pad sequencing decision, tank-deployment-task
consolidation decision
**Related tasks**: 2026-06-16 AI Manager historical audit (stub handler
findings), deposit/spawning reconciliation (2026-06-18, separate and
unrelated gap — do not conflate)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Final test result**: X examples, Y failures

### What was changed
- `[file]` — [description]

### Step 1-3 findings summary
[Concise summary of verification results — this is the most important
section, read carefully before approving Step 4/5 work]

### Issues discovered
[Any problems found during implementation that weren't anticipated]

### Follow-up tasks needed
[List only — do not create the files]

### Lessons learned
[What worked, what didn't]
