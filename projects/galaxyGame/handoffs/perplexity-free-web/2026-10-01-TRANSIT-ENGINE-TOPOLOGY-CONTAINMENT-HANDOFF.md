# TransitEngine Topology Containment — Session Handoff

**Created:** 2026-10-01 09:20 EDT  
**Session state:** Closed for clean-session restart  
**Implementation status:** Not dispatched; no code changes authorized

## Executive Summary

Last session pivoted the TransitEngine work away from the earlier spec-alignment premise. The new direction is **Phase 1 topology containment**: preserve dynamic solar-Hohmann planning only for a narrowly verified direct-solar-body route class, and explicitly reject unsupported route topologies rather than producing fabricated durations or silently falling back to hardcoded route tables.

A draft replacement task exists, but it is **not ready for dispatch**. The next session should begin with a bounded, read-only source-verification pass that completes the evidence needed to revise the draft into a dispatch-ready task. Do not begin implementation, move the task to `active`, or alter the old task during that pass.

## Task Inventory

| Item | Current state | Required action now |
|---|---|---|
| `2026-09-30-HIGH-ARCHITECTURE-TRANSIT-ENGINE-TOPOLOGY-CONTAINMENT.md` | Created; YAML `status: backlog`; readiness boxes unchecked; no dispatch or implementation | Review and evidence-complete before any dispatch |
| `2026-09-29-HIGH-REFACTOR-TRANSIT-ENGINE.md` | Preserved untouched; undispatched | Leave unchanged for now |
| TransitEngine application code | No changes authorized under the new task | Do not edit until task is evidence-complete and approved |

## Why the Pivot Happened

The old task assumed the dynamic TransitEngine calculation could become the canonical behavior and that specs should be aligned to its returned values. Source review invalidated that premise.

`Mission::TransitEngine#compute_transit_days_dynamic` can combine orbital radii from incompatible reference frames while using solar gravitational parameter `MU_SUN`:

- Earth orbital data is heliocentric: approximately 149,598,000 km from Sol.
- Luna orbital data is parent-centric: approximately 384,400 km from Earth.
- Combining those values in a solar-Hohmann calculation creates a fabricated Earth→Luna result of approximately 104 days.

The source comments reportedly document this distinction and explicitly warn against mixing the two conventions. The temporary containment approach therefore rejects routes involving moons and other ineligible/ambiguous topologies rather than treating their dynamic outputs as meaningful.

## Intended Phase 1 Contract

Subject to source verification, the replacement task intends to establish this bounded behavior:

- Dynamic planning is allowed only for verified direct-solar planet-to-planet routes.
- Parent/moon, sibling-moon, mixed-parent, ambiguous/procedural, and missing-orbital-data routes raise `Mission::UnsupportedTransferError`.
- The error carries `origin`, `destination`, and `classification_reason`.
- Unsupported planner calls must not silently receive a named route-table duration or generic 365-day fallback result.
- The lunar precursor validation rake task must stop treating Earth→Luna or Luna→Venus as dynamically supported routes.
- The Phase 1 classifier is a documented proxy based on verified available metadata and STI types; it is not genuine orbital-reference-frame validation.

This is not a full routing solution. Proper frame/central-body metadata, multi-leg/SOI routing, craft state, propulsion-aware trajectories, and interstellar/wormhole systems remain follow-up concerns.

## Evidence Already Available

### Draft task evidence

The new draft identifies the known public/planner surface as:

- `calculate_transfer_window(from_body, to_body, launch_date)`
- `schedule_departure(craft_id, from_body, to_body, launch_date)`
- `lunar_precursor_mission_validation.rake`

It proposes a narrow eligibility proxy using actual STI type, nil/blank parent, present `solar_system_id`, and usable orbital elements. This must remain explicitly documented as a temporary proxy, not a real frame guarantee.

### RSpec investigation evidence

The 2026-09-29 RSpec investigation reported:

- `spec/services/mission/transit_engine_spec.rb` had 32 examples and 8 failures when run alone.
- The failures were concentrated in transit-day and arrival-date behavior.
- One documented Earth→Luna assertion expected an arrival seven days after departure but received the departure date.
- No order dependence was detected for the file.

This supports prioritizing the TransitEngine contract, but it is not a substitute for direct current-source verification.

## Dispatch Blockers

The replacement task remains **not ready** for all of the following reasons.

### 1. Exact STI type allowlist is unverified

The draft contains a placeholder for dwarf-planet and legacy giant/planet types. The actual existing model classes and persisted STI strings must be inspected before the implementation task names an allowlist. Do not infer from identifiers such as `VENUS-01`, `MARS-01`, `TITAN-01`, or `PLUTO-01`.

### 2. Rake behavior is not a single testable contract

The draft allows either skipping unsupported lunar routes or reporting an expected development-validation gap. Choose one concrete behavior after source inspection, including the expected output and exit status or test/verification command.

### 3. Caller surface and fallback references are not fully verified

Before changing fallback behavior or allowing an error to propagate, search the current source for all uses of:

- `calculate_transfer_window`
- `schedule_departure`
- `fallback_transfer_window`
- `compute_transit_days`
- `compute_transit_days_dynamic`
- `luna_to_venus_transit_days`

If the caller set is broader than the current draft expects, update the design/task or escalate; do not guess caller behavior.

### 4. Error-class location/autoloading is unresolved

The draft says `app/services/mission/errors.rb` “or justified Mission-domain error location.” Inspect existing Mission exception conventions and Rails autoload behavior, then prescribe one precise destination.

### 5. Test-fixture feasibility is unknown

Confirm that spec-local fixtures can instantiate the necessary exact allowed and disallowed body types and provide the required orbital/parent/solar-system data without depending on ambient seed or JSON data.

### 6. The draft contains conflicting workflow instructions

Its dispatch interface says to save synthesis to the summaries folder and never paste it into chat. Other sections say to create/post or paste it in chat. The revised task must contain one canonical rule only. The intended rule is:

> Save the synthesis report as a Markdown file in the summaries directory; report its path/status in chat, but do not paste the report body into chat.

### 7. The draft claims readiness inaccurately

The Local Worker Triage block currently says `READY FOR LOCAL DISPATCH`, while the readiness checklist is entirely unchecked and the listed evidence gaps remain. Change that triage status to not-ready or remove it until the task is actually ready.

## Next Session: Authorized Scope

Start with a **read-only pre-dispatch verification pass**. Its purpose is to gather source evidence and revise the task draft only.

### Allowed

- Read the agent-task workflow and project guide.
- Read relevant GalaxyGame source, models, factories, specs, rake tasks, and existing error conventions.
- Search the repository for the specified call sites and fallback references.
- Inspect task-lifecycle examples only as needed for later workflow knowledge.
- Create a synthesis/report file in the project summaries directory according to the canonical workflow.
- Revise the new backlog task after evidence is established, removing placeholders and contradictions.

### Not allowed

- Do not move the replacement task from `backlog` to `active`.
- Do not change its YAML status to `active`.
- Do not modify application code, specs, rake code, database/schema, factories, or data.
- Do not stage, commit, push, or create a pull request.
- Do not change, move, deprecate, or supersede the September 29 task.
- Do not dispatch an implementation agent.

## Required Read-Only Findings

The pre-dispatch report must answer each item with concrete file/path evidence.

1. **STI classification:** Exact planet/direct-solar candidate class names and `sti_name` values; satellite/moon and other excluded types; any legacy/dwarf/giant variants.
2. **Data shape:** Exact behavior and return shape of `orbital_data`; key type/access pattern; what counts as missing/invalid orbital data; identifier normalization/lookup behavior.
3. **Transit flow:** Exact `calculate_transfer_window`, `schedule_departure`, dynamic calculation, fallback behavior, and returned transfer-window structure.
4. **Full caller surface:** Every direct reference to the six specified calculation/fallback/route-table names and the behavior each caller expects.
5. **Errors:** Existing Mission error classes, file locations, namespaces, and autoload-compatible convention.
6. **Lunar rake:** Present behavior, exact unsupported-route usage, selected replacement behavior, and a concrete verification method.
7. **Fixture constraints:** Factories/model validations and a viable strategy for entirely spec-local valid and rejected route fixtures.
8. **Task revision:** Exact changes needed to eliminate all placeholders, either/or choices, and workflow contradictions.

## Required Revision Standard

Before the implementation task can be marked ready:

- All readiness checklist claims must be true, not aspirational.
- All implementation code examples must contain verified type names or be replaced with non-placeholder, evidence-backed instructions.
- The rake behavior must be one chosen behavior, not alternatives.
- Error placement must be fixed and source-supported.
- The caller surface must be documented or a stop/escalation condition must be explicitly tied to verified unknown callers.
- Synthesis-report handling must be internally consistent.
- The task must not claim it is ready before human review checks are completed.

## Later Old-Task Disposition

Do not take this action now.

After the replacement task is evidence-complete, approved, and handled according to the actual project workflow, perform a separate approved lifecycle action for `2026-09-29-HIGH-REFACTOR-TRANSIT-ENGINE.md`:

- Inspect the repository’s actual status values, folder conventions, and existing superseded/deprecated examples.
- Use the canonical disposition rather than assuming a folder or YAML status.
- Add a factual note that the prior task’s spec-alignment premise was invalidated by the mixed-reference-frame dynamic calculation defect.
- Link/reference the replacement containment task.
- State that no implementation occurred under the old task.
- Keep that lifecycle edit isolated from the implementation task unless the workflow explicitly requires otherwise.

## Proposed Fresh-Session Opening Prompt

```text
We are resuming GalaxyGame TransitEngine planning after a pivot. Do not implement, dispatch, move any task, change YAML status, edit application/spec/rake code, stage, commit, or push.

First read the project workflow, project guide, the session handoff, and the backlog task:
- [handoff file path]
- projects/galaxy_game/tasks/backlog/current/2026-09-30-HIGH-ARCHITECTURE-TRANSIT-ENGINE-TOPOLOGY-CONTAINMENT.md

Perform a read-only pre-dispatch verification pass. Create the required synthesis/report Markdown file in the project summaries directory; provide only its path and completion status in chat, not its full contents.

Verify exact STI type strings, orbital_data and transit-flow behavior, all caller/fallback references, Mission error-class conventions, lunar rake behavior, and fixture feasibility. Then revise the backlog task only, using verified evidence to remove every placeholder, either/or decision, inaccurate readiness claim, and conflicting workflow instruction.

Report: (1) verified findings with file paths, (2) exact task changes made, (3) unresolved blockers/stop conditions, and (4) whether the revised task is ready for human review. Do not mark it ready yourself unless every human readiness checklist item is genuinely verifiable.
```

## Source Artifacts for the Next Session

- `2026-09-30-HIGH-ARCHITECTURE-TRANSIT-ENGINE-TOPOLOGY-CONTAINMENT.md` — current replacement task draft.
- `rspec-investigation-2026-09-29.md` — read-only investigation supporting context.

## Final Status Line

**HANDOFF STATUS: clean-session restart approved | replacement containment task remains backlog and not dispatchable | next action is read-only evidence completion and task revision only | old task remains untouched pending later separate lifecycle action**
