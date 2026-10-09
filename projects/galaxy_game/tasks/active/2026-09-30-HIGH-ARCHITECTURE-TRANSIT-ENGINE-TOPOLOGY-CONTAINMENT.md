---
title: "TransitEngine Topology Containment — Phase 1"
status: active
priority: HIGH
type: architecture
work_type: architecture_containment
system_domain: OTHER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
created: 2026-09-30
revised: 2026-10-06
supersedes: null
related_tasks:
  - 2026-09-29-HIGH-REFACTOR-TRANSIT-ENGINE.md
dispatch_ready: false
---

**Claude disposition**: REVISE (not approved). This revision incorporates Claude's required changes per human authorization. A fresh Qwen read-only task-text verification and Claude re-review are required after this revision.

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.** Every `[FILL IN]` marker in this file must be resolved by a read-only Fill-the-Gaps pass (local agent with terminal access) before the "All file paths are verified to exist" box can be checked.

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-09-30-HIGH-ARCHITECTURE-TRANSIT-ENGINE-TOPOLOGY-CONTAINMENT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-09-30-HIGH-ARCHITECTURE-TRANSIT-ENGINE-TOPOLOGY-CONTAINMENT.md \
         projects/galaxy_game/tasks/active/2026-09-30-HIGH-ARCHITECTURE-TRANSIT-ENGINE-TOPOLOGY-CONTAINMENT.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-30-HIGH-ARCHITECTURE-TRANSIT-ENGINE-TOPOLOGY-CONTAINMENT.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.

Dispatch authorization boundary: the human owner's dispatch approval (Status and authority, item 2) authorizes Step 0 for THIS task file only (the `git mv` to `active/` and the status change). It does not authorize committing, pushing, staging any other file, or any lifecycle action on `2026-09-29-HIGH-REFACTOR-TRANSIT-ENGINE.md`.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.

---

# TASK: TransitEngine Topology Containment — Phase 1
**Status**: BACKLOG
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-09-30
**Last Updated**: 2026-10-06

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Primary executor per workflow; has terminal access for the read-only audit and narrow verification commands.
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully (first dispatch of this task)

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section) — [FILL IN: confirm path exists]
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md` — [FILL IN: confirm path exists]
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. The synthesis report is saved as an MD file (see Dispatch Interface), not pasted in chat.

---

## Context

`Mission::TransitEngine#calculate_transfer_window` applies a fixed-`MU_SUN` heliocentric approximation to any pair of bodies whose orbital data resolves, including moon/parent-centric routes such as Earth→Luna. This task adds a narrow topology containment guard and removes the one precursor-rake call that depends on the unsupported route. It does not make the calculator physically correct for any system (see Objective).

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions — [FILL IN: confirm path]
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules — [FILL IN: confirm path]
- `docs/wiki_reorganization/transportation/` — active transportation domain hub, gap-tracking convention (documentation target)

> If a doc doesn't exist for this area, do not create one during this task. Flag the gap in your completion report instead.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Guard placement and rescue scopes
- ❌ Wrong: raise `Mission::UnsupportedTransferError` inside the existing rescue-wrapped dynamic block, where it can be swallowed into `fallback_transfer_window`.
- ✅ Right: validate before and outside every rescue scope in `calculate_transfer_window`.
- Why: a swallowed error silently reproduces the exact wrong-route behavior this task contains.

⚠️ **GOTCHA 2**: How eligibility is decided
- ❌ Wrong: check for a Sol name/identifier, a non-Sol/multi-star exclusion, or a concrete world-type allowlist.
- ✅ Right: lineage (`is_a?(CelestialBodies::Planets::Planet)`), `parent_celestial_body_id.nil?`, non-nil resolving `solar_system`, and equal `solar_system_id` as a PAIR condition.
- Why: GalaxyGame must support Eden, procedural/partial systems, and multi-star systems; the proxy is necessary containment, never a physical-correctness claim.

⚠️ **GOTCHA 3**: Test environment
- ❌ Wrong: bare `docker exec ... rspec` or `rake`, which can run in the container's default development env against the development DB.
- ✅ Right: use the wrapper in Step 6 (`unset DATABASE_URL && RAILS_ENV=test`) directly on the command.
- Why: unprefixed runs have produced untrustworthy baselines in this project.

⚠️ **GOTCHA 4**: Searching and git state
- ❌ Wrong: workspace-wide editor search for counts/callers (it returns matches from unrelated files); `git diff` being empty as proof something is committed; bare `app/...` paths in `git log` (false-empty).
- ✅ Right: shell `grep -n` against resolved paths from the repo root; `git status --short` and `git log -- galaxy_game/<path>`.
- Why: each has produced a false "clean/complete" reading in this project.

⚠️ **GOTCHA 5**: Green tests vs. live behavior
- ❌ Wrong: treating passing specs as proof the rake timeline still works.
- ✅ Right: also run the `luna_mission:phase_timing` verification under a fixed/frozen date and compare against the captured baseline.
- Why: the rake path is what consumes `:transit_days`.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

After Step 0 and before running any other command or modifying anything, create the synthesis report below, **save it as an MD file in the summaries folder (do NOT paste it in chat)**, then STOP and wait for approval. This is Gate 1 (human approval is sufficient).

**Synthesis Report Template** (copy, fill in, save as MD):
```markdown
### STATUS SYNTHESIS REPORT

**Task**: 2026-09-30-HIGH-ARCHITECTURE-TRANSIT-ENGINE-TOPOLOGY-CONTAINMENT
**Status**: backlog → active
**Date**: YYYY-MM-DD

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `path/to/file` | [description] | [not started / pending / done] |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file in full
- ✅ Understand architecture gotchas above

### Expected Outcomes
[Exact description of what "done" looks like]

### Critical Gotchas I Will Avoid
- ❌ [wrong approach] — instead ✅ [right approach]
- ❌ [wrong approach] — instead ✅ [right approach]

---

**SYNTHESIS COMPLETE.** Waiting for Gate 1 approval before Step 2.
```

---

## Status and authority

This task remains **backlog** and is **not dispatchable** until:

1. Claude gives an explicit final technical disposition approving it for human dispatch review.
2. The human owner explicitly approves dispatch.
3. The repository workflow/status/location convention is rechecked immediately before dispatch.

Do not move, archive, supersede, deprecate, edit, stage, commit, or push the related older task:

`2026-09-29-HIGH-REFACTOR-TRANSIT-ENGINE.md`

during this task. Its later lifecycle disposition is a separate human-approved action.

## Objective

Contain known unsupported topologies in the legacy single-μ heliocentric-approximation path of `Mission::TransitEngine`, which continues to use fixed `MU_SUN`.

The existing dynamic path applies `MU_SUN` and heliocentric assumptions whenever both supplied body identifiers yield non-empty orbital JSON. It can therefore apply a solar-frame calculation to a moon/parent-centric route such as Earth→Luna.

Phase 1 must explicitly reject known unsupported topologies via a Phase 1 topology proxy (same-system, parentless planet-lineage topology). It does not establish physical correctness of the retained legacy dynamic path for Sol, Eden, other procedurally generated systems, or multi-star systems.

This task is **containment**, not a general orbital-routing implementation. GalaxyGame must support Eden, procedurally generated/partially completed systems, and multi-star systems; this task makes no claim about non-Sol or multi-star correctness.

## Evidence basis

### Verified locally

- `CelestialBodies::Planets::Planet` is an abstract base class.
- Production code already uses:

  ```ruby
  body.is_a?(CelestialBodies::Planets::Planet)
  ```

  as the structural planet-category check.

- Concrete rocky, ocean, and gaseous `Planets::*` classes descend from that abstract base.
- `CelestialBodies::CelestialBody` provides optional `parent_celestial_body_id`, optional `solar_system_id`, and `orbital_elements`.
- The current solver:
  - defaults `launch_date` to `Time.current.to_date`;
  - loads orbital JSON for both identifiers;
  - falls back to a hardcoded duration table if either orbital-data result is nil;
  - uses `MU_SUN` for all non-nil orbital data;
  - has no reference-frame or topology validation.
- A resolved Earth→Luna pair with populated orbital data enters the dynamic path.
- `luna_mission:phase_timing` invokes `schedule_departure` for Earth→Luna and consumes only `precursor_departure[:transit_days]` afterward.
- The Earth→Luna rake result is not persisted and no other schedule-return field is used by the downstream precursor timeline.
- `docs/wiki_reorganization/transportation/` is an active domain area with a hub, gap-tracking convention, and current/planned/deferred documentation pattern.

### External research / design guidance

- The classic two-impulse Hohmann model assumes a shared central body, compatible reference frame, a consistent gravitational parameter \(\mu\), and idealized circular/coplanar orbits.
- Planet environmental profiles—terrestrial, ocean, hycean, lava, carbon, super-Earth, gas giant, ice giant, and hot Jupiter—must not determine dynamic-transfer eligibility.
- Dwarf planets and minor bodies are deferred because the current project does not have validated data/contracts for their routing, not because a basic heliocentric approximation is inherently impossible.

### Reported current-model limitations

- `orbital_elements` has no explicit epoch, reference-frame tag, central-body metadata, or unit metadata.
- There is no generic per-body \(\mu\) API.
- `Star` and station models are outside the `CelestialBodies::CelestialBody` input hierarchy for the identified TransitEngine API.
- Existing direct legacy helper methods can bypass `calculate_transfer_window`.

## In scope

1. Add a domain error for a resolved but unsupported TransitEngine topology, following verified project error-class/autoload conventions:

   ```ruby
   Mission::UnsupportedTransferError
   ```

2. Add a Phase 1 eligibility boundary to `Mission::TransitEngine.calculate_transfer_window`.

3. Restrict the existing **dynamic** calculation (retained legacy dynamic path) to resolved body records that satisfy all of the following **per-endpoint** conditions:

   - `is_a?(CelestialBodies::Planets::Planet)`;
   - `parent_celestial_body_id.nil?`;
   - non-nil `solar_system_id`;
   - the `solar_system` association resolves.

   When both endpoints resolve, their `solar_system_id` values must be equal. This is a **pair condition**, not an individual endpoint property.

4. Raise `Mission::UnsupportedTransferError` for any **resolved** `CelestialBodies::CelestialBody` endpoint that fails the Phase 1 topology proxy.

5. Ensure resolved unsupported topology raises before the existing:
   - dynamic calculation;
   - nested dynamic fallback;
   - outer `fallback_transfer_window`;
   - hardcoded `compute_transit_days` route table.

6. Preserve the existing fallback contract for an identifier that cannot be resolved to a `CelestialBodies::CelestialBody` record, subject to the resolution/validation order below.

7. Replace the dynamic Earth→Luna precursor-rake call with a labeled, static 7-game-day scenario that provides the downstream-compatible `:transit_days` value.

8. Add narrow tests and narrow documentation updates described below.

9. The implementer performs the mandatory read-only pre-implementation caller audit (see Verification) of all callers of `calculate_transfer_window` and `schedule_departure` across rakes, services, jobs, controllers, and specs.

10. Require GAPS.md entries documenting residual gaps (see Documentation requirement section).

## Required behavior

### Resolution, validation, and calculation order

`calculate_transfer_window` must behave in this order:

1. Resolve both supplied identifiers independently, using the same existing resolver semantics currently used by TransitEngine. Do not introduce divergent name/case/identifier resolution rules.

2. Validate every endpoint that resolves against the Phase 1 topology proxy.
   - A body is eligible only when it:
     - is in the `CelestialBodies::Planets::Planet` lineage;
     - has no parent celestial body (`parent_celestial_body_id.nil?`);
     - has non-nil `solar_system_id`;
     - the `solar_system` association resolves.
   - Pair condition (not a per-body property): when both endpoints resolve, their `solar_system_id` values must be equal. A mismatch raises like any other proxy failure.

3. If **any resolved endpoint** fails the Phase 1 topology proxy, raise `Mission::UnsupportedTransferError`, even if the other endpoint is unresolvable. The error must be raised before orbital data is interpreted, before dynamic calculation, and before any outer or nested fallback calculation.

4. Only if every resolved endpoint passes the proxy **and** one or both endpoints are unresolvable: preserve existing legacy fallback behavior. Do not raise `Mission::UnsupportedTransferError` for unresolvable identifiers alone.

5. Only if both endpoints resolve and both pass the proxy: continue through the existing orbital-data retrieval path, preserve existing behavior for missing/empty orbital data on otherwise eligible pairs, and preserve existing dynamic calculation behavior for eligible pairs except as required by the guard.

### Examples / acceptance criteria for resolution order

- Parentless same-system planet-lineage pair → retained legacy dynamic path; no physics-validity claim.
- Parentless planet system A + parentless planet system B (different `solar_system_id`) → raise `Mission::UnsupportedTransferError`.
- Planet → moon → raise.
- Moon → planet → raise.
- Moon → unknown, or unknown → moon (unknown in either position) → raise (resolved moon fails proxy).
- Eligible planet → unknown, or unknown → eligible planet (unknown in either position) → preserve legacy fallback.
- Unknown → unknown → preserve legacy fallback.

### Phase 1 interpretation

This is a **temporary topology proxy**, not true reference-frame validation.

> These are necessary topology containment conditions, not sufficient validation of a common governing primary, compatible orbital reference frame, primary-specific μ, epoch, unit contract, or physical transfer feasibility. Passing the proxy retains legacy fixed-`MU_SUN` behavior only.

It must not claim to validate heliocentric frame, orbital epoch, units, central-body \(\mu\), SOI transitions, or general transfer feasibility.

It must not use a manually maintained concrete world-subtype allowlist. In particular, eligibility must not depend on whether a planet is terrestrial, ocean, gaseous, hycean, lava, carbon, a super-Earth, a gas giant, an ice giant, or a hot Jupiter.

It must not add a Sol identity/name/identifier check.
It must not add a runtime non-Sol/multi-star exclusion.
It must not claim that any non-Sol, generated, or multi-star route is physically correct after this task.

### Runtime rejection scope

For the current `CelestialBody` input contract, reject resolved endpoints that are:

- outside the verified `CelestialBodies::Planets::Planet` lineage;
- moons/satellites or other parented celestial bodies;
- dwarf planets, minor bodies, comets, asteroids, and legacy non-lineage celestial-body types;
- planet-lineage records with a non-nil parent;
- planet-lineage records with no usable solar-system context (`solar_system_id` nil or `solar_system` association not resolving);
- resolved endpoint pairs whose `solar_system_id` values differ.

Do not add special Star or station handling/tests. They are not established `CelestialBody` endpoints for this task. If source evidence demonstrates that these types can reach this TransitEngine API, stop and report; do not add handling in this task.

## Error and compatibility contract

- Use `Mission::UnsupportedTransferError` for a **resolved but unsupported topology**.
- The error must make clear that the requested dynamic transfer topology is unsupported by the Phase 1 legacy single-μ calculator. Include offending identifier(s) (for a different-system pair, both identifiers) and a stable fragment describing the limitation, for example:
  `unsupported topology for legacy transfer calculation`

At least one unsupported-topology test must assert all of the following:
- `Mission::UnsupportedTransferError` is raised;
- the stable message fragment `unsupported topology for legacy transfer calculation` appears in the error message;
- the offending resolved identifier appears in the error message (both identifiers for the different-system pair);
- the error propagates from `calculate_transfer_window` with no returned result.

Do not require a full exact error message string.
- Do not convert unresolvable identifiers into `Mission::UnsupportedTransferError`.
- Preserve legacy fallback behavior only when every endpoint that resolves passes the Phase 1 topology proxy and one or both identifiers remain unresolvable, and for missing/empty orbital data on otherwise eligible pairs.
- The guard must be outside every rescue scope in `calculate_transfer_window`.
- `Mission::UnsupportedTransferError` must follow verified local error-class/autoload/inheritance convention. Before implementation, the implementer must verify (Verification item 4) that the selected inheritance is not caught by an existing broad rescue.
- Do not rescue `Mission::UnsupportedTransferError` in `schedule_departure`.
- Do not remove or alter the existing hardcoded route-table methods in this task.
- Do not invoke a fallback route duration for a resolved unsupported topology.

## Rake baseline and static scenario

Before implementation, capture the existing narrow `luna_mission:phase_timing` output under a fixed/frozen date where project conventions permit:
- Record the current Earth→Luna transit value and relevant downstream timeline output.

In `luna_mission:phase_timing`:

1. Remove the Earth→Luna dynamic invocation of:

   ```ruby
   Mission::TransitEngine.schedule_departure(
     "precursor_hlt_1",
     "EARTH-01",
     "LUNA-01",
     ...
   )
   ```

2. Replace it with an explicitly named/labeled static scenario duration of **7 game days**.
   - If a named 7-day mission profile/scenario constant already exists, reuse it rather than duplicate literal `7`.
   - Otherwise use a clearly named/labeled static scenario constant/variable representing 7 game days.

3. Preserve the existing downstream-compatible shape used by the rake:

   ```ruby
   precursor_departure[:transit_days]
   ```

   Current evidence establishes that no other field of the prior `schedule_departure` result is consumed in this rake path.

4. The implementation preserves the existing downstream arithmetic after setting the precursor arrival anchor to the explicit 7-game-day scenario duration:
   - precursor arrival is anchored at 7 game days;
   - existing 30-day landing-pad work remains unchanged;
   - existing 15-day tank-farm work remains unchanged;
   - existing offload/timeline logic remains unchanged.

5. Preserve the Earth→Venus dynamic `schedule_departure` call.

6. Preserve existing later `luna_to_venus_transit_days` and `can_offload_n2?` behavior.

7. The static Earth→Luna scenario:
   - must not call `calculate_transfer_window`;
   - must not use rescue as a substitute for removal;
   - must not depend on `Time.current`, `Date.current`, `Date.today`, or host-clock-derived duration.

## Simulation-time boundary

GalaxyGame may capture a real-world timestamp as an initial Sol simulation epoch. After initialization, ongoing game “now” must be driven by simulation-time advancement and game speed.

This task does **not** redesign the game clock or the existing TransitEngine default-date behavior.

Requirements for this task:

- Do not add any new ambient host-time lookup to changed production code.
- Use explicit fixed launch dates/epochs in new or modified tests.
- The static Earth→Luna scenario must use an explicit duration and must not depend on ambient host time.
- Record the existing `calculate_transfer_window` default of `Time.current.to_date` as a separate future simulation-time-authority concern. Do not refactor it here unless a named stop condition is reached and human/Claude review authorizes a split.

## Legacy-helper boundary

The Phase 1 guard belongs only in `calculate_transfer_window`.

Keep public direct legacy helpers unchanged, including:

- `fallback_transfer_window`;
- `compute_transit_days`;
- `compute_transit_days_dynamic`;
- named `earth_to_*` and `luna_to_*` helper methods;
- existing direct-helper test examples.

These direct helpers remain a documented Phase 1 containment gap. This task must neither represent them as fully topology-safe nor refactor them into a broader routing API.

## Files Involved

All paths below are [FILL IN] by a read-only terminal pass. Do not guess; confirm each with `find`/`grep` from the repo root and record the result.

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| [FILL IN] `Mission::TransitEngine` source file | Phase 1 guard | `calculate_transfer_window` |
| [FILL IN] `Mission::UnsupportedTransferError` (new) | Domain error | follows verified error-class/autoload convention |
| [FILL IN] `luna_mission:phase_timing` rake file | Static 7-game-day Earth→Luna scenario | Earth→Luna `schedule_departure` call |
| [FILL IN] TransitEngine spec file(s) | Tests 1–15, 17 | — |
| [FILL IN] rake spec or narrow-command convention | Test 16 | — |
| `docs/wiki_reorganization/transportation/` (+ its `GAPS.md`) | Limited documentation update | per Documentation requirement |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| [FILL IN] celestial-body model/factory/spec support files | Fixture feasibility for Tests 2–11 |
| [FILL IN] `CelestialBodies::Planets::Planet` and `CelestialBodies::CelestialBody` | Lineage and attribute verification |
| `2026-09-29-HIGH-REFACTOR-TRANSIT-ENGINE.md` | Protected related task; read-only, never modified |

### Migration
No migration needed (see Explicit non-goals: no schema change). If one appears necessary, stop and escalate.

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0, then Step 1 (synthesis report, Gate 1). Do not proceed to Step 2 until Gate 1 is approved.

All agents: follow these steps exactly in order.
- Do not skip steps or reorder them.
- Do not proceed to the next step if the current step has not produced a clean result.
- Any listed escalation trigger (Stop conditions, Verification) stops the work before the next step.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

Exactly as written in the Agent Dispatch Interface. Paste the `git mv` and `find` outputs in chat. Expected: exactly one result, at the `active/` path. Do not use `cp` or plain `mv`.

### Step 1 — Synthesis report (Gate 1)

Save the Status Synthesis Report (template above) to the summaries folder. Stop and wait for approval.

### Step 2 — Pre-implementation verification (read-only) and six-group report (Gate 2)

Perform the six checks in the Verification section. No code, spec, rake, or documentation changes. Save the six-group report to the summaries folder (filename pattern from the Dispatch Interface, suffix `-PREIMPL`), then STOP. Gate 2 approval comes from Claude review. If any escalation trigger is found, stop and report it in that same file.

### Step 3 — Guard and error class

Implement per Required behavior and Error and compatibility contract. Touch nothing listed in Explicit non-goals or the Legacy-helper boundary.

### Step 4 — Rake static scenario

Implement per Rake baseline and static scenario. Earth→Venus stays dynamic.

### Step 5 — Tests

Add the 17 cases listed in Tests, following existing project test style and factories.

### Step 6 — Verify

> CRITICAL EXECUTION MANDATE: All RSpec commands must use the Docker wrapper below.
> The container working directory is already /home/galaxy_game — do NOT add cd /home/galaxy_game.
> Never run bare local test commands. Never fabricate test results. Actually run the specs.
> Run only narrow commands; do not run the full suite (the human runs it separately). Never run two RSpec processes concurrently.

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [SPEC_PATH] 2>&1 | tail -20'
```

[FILL IN: exact narrow spec paths and the rake verification command, under a fixed/frozen date]

Expected result: all new/modified examples pass, 0 failures; the rake output matches the captured baseline except the Earth→Luna transit value, which is 7 game days.

### Step 7 — Documentation

Limited update per Documentation requirement, after Step 6 establishes actual behavior.

### Step 8 — Closing report (before committing anything)

Save a closing report to the summaries folder: commands run, pass/fail counts, changed files, expected baseline failures, unexpected findings, stop conditions reached, and proof no unrelated scope changed (see Verification, General verification). Do not commit until the user explicitly approves.

---

## Tests

Follow the current project’s test style, factories, helpers, and official narrow test commands. Do not redesign factories or broaden test infrastructure merely to satisfy these cases.

Add or update narrow tests proving each of the following as a separate case:

1. **Eligible retained-legacy pair (characterization)**
   - A resolved parentless same-system planet-lineage pair uses the existing dynamic path and does not invoke `fallback_transfer_window`.
   - No numeric or physical-validity assertions.

2. **Planet → moon** raises `Mission::UnsupportedTransferError`.

3. **Moon → planet** raises `Mission::UnsupportedTransferError`.

4. **Generic non-lineage body**: a resolved `CelestialBodies::CelestialBody` outside the `Planets::Planet` lineage raises.

5. **Parented planet-lineage body**: a resolved planet-lineage record with non-nil `parent_celestial_body_id` raises.

6. **Missing solar-system context**: a resolved planet-lineage record with nil `solar_system_id` or a non-resolving `solar_system` raises.

7. **Dwarf planet / minor body**: a resolved dwarf-planet or minor-body record raises, subject to the lineage verification. If no fixture exists without redesign, stop per Stop condition 3.

8. **Different-system pair**: two parentless planet-lineage bodies with different `solar_system_id` raise.

9. **Moon → unknown identifier** raises, with the unknown identifier in either position (the resolved moon fails the proxy).

10. **Eligible planet → unknown identifier** preserves the existing fallback and does not raise, with the unknown identifier in either position.

11. **Unknown → unknown** preserves the existing fallback and does not raise.

12. **Eligible pair with missing/empty orbital data** preserves the existing eligible-pair fallback.

13. **Error assertion**: at least one raising test (test cases 2–9 above) asserts all of: `Mission::UnsupportedTransferError` class; message contains `unsupported topology for legacy transfer calculation`; message contains the offending resolved identifier (both identifiers if the asserting test is case 8); the error propagates from `calculate_transfer_window` with no returned result. Do not require a full exact message.

14. **Ordering / no fallback leakage**: for resolved unsupported cases, verify the error occurs before `fallback_transfer_window`, `compute_transit_days_dynamic`, nested `fallback_transit_days`, and `compute_transit_days`. Use established project conventions; avoid private-method coupling where observable behavior proves the boundary.

15. **Legacy helper regression**: existing direct-helper behavior and expectations remain unchanged.

16. **Rake verification** (per the repository's established rake-test or narrow-command convention, under a fixed/frozen date):
    - Earth→Luna no longer invokes `schedule_departure` dynamically.
    - The precursor transit is 7 game days and downstream timeline arithmetic is intact.
    - Earth→Venus remains on the dynamic path; no date-dependent Venus numeric assertion.
    - Do not run a full suite as a requirement of this task.

17. **Time determinism**: new/modified tests use explicit fixed dates and depend on no host calendar date or clock time.

## Documentation requirement

After implementation and narrow verification establish the actual behavior, make a limited documentation update under:

`docs/wiki_reorganization/transportation/`

Follow the existing active-domain hub, cross-link, current/planned/deferred, and `GAPS.md` conventions.

Document only verified Phase 1 behavior and explicit limitations:

- The retained legacy dynamic path is reachable only for the same-system, parentless planet-lineage topology proxy; passing the proxy is not a claim of support or physical correctness.
- The precursor Earth→Luna scenario uses an explicitly labeled static 7-game-day duration.
- Moon/satellite, parented, non-planet, dwarf/minor-body, and otherwise nonconforming resolved celestial-body topologies are rejected by this dynamic path.
- The proxy is temporary containment, not a complete orbital route-planning system.
- Future work requires explicit reference-frame metadata, central-body association, per-body \(\mu\), parent/SOI transitions, and multi-leg routing.

**GAPS.md must record these residual gaps:**

1. No governing-primary or reference-frame association.
2. No per-primary \(\mu\).
3. Fixed `MU_SUN` remains applied to every pair that passes this Phase 1 topology proxy.
4. Direct legacy helpers remain unguarded, including parent-centric route-table helpers such as `luna_to_venus_transit_days`.
5. `calculate_transfer_window` retains its `Time.current.to_date` default.
6. Same-system eligibility is a topology containment check only; it does not establish multi-star, non-Sol, generated-system, or physical transfer correctness.

State that the future primary/frame-aware route architecture is human-filed follow-up work, not a task the implementer may create or dispatch.

Do not:

- create a new top-level wiki hierarchy;
- implement or canonize the unapproved proposed Simulation-domain reorganization;
- document future architecture as current behavior;
- use the documentation update to conceal or redefine unresolved code behavior.

## Explicit non-goals

- No game-clock/simulation-time authority refactor.
- No new per-body \(\mu\) storage/API.
- No orbital-frame, epoch, or unit metadata schema change.
- No planet–moon, moon–moon, mixed-parent, SOI, patched-conic, N-body, Lambert, or multi-leg routing.
- No dwarf-planet/minor-body dynamic-transfer support.
- No station-routing system.
- No broad celestial taxonomy redesign, STI migration, or world-profile refactor.
- No changes to unrelated global Zeitwerk baseline issues.
- No unrelated baseline-spec remediation.
- No task lifecycle action for the 2026-09-29 task.
- No commits or pushes unless separately and explicitly authorized by the human owner and repository workflow.

## Verification

### Pre-implementation verification (MANDATORY)

The implementer must perform these read-only checks before changing code, specs, rake files, or documentation. The implementer must include results in the required pre-implementation/synthesis report. If any listed escalation trigger is found, stop before implementation and report it.

1. **Caller audit**
   - The implementer enumerates all callers of `calculate_transfer_window` and `schedule_departure` across rakes, services, jobs, controllers, and specs.
   - The audit records identifier/body origins for production-reachable callers.
   - The audit searches for broad rescues or error transformations affecting `Mission::UnsupportedTransferError`.
   - Findings use only:
     - `currently reachable in production paths reviewed`; or
     - `currently unreachable in production paths reviewed`.
   - Stop/escalate if production-reachable paths can pass non-Sol, multi-star, procedurally generated, or partially generated/incomplete-system bodies.
   - Do not call the audit a "comprehensive" or "exhaustive" review; it is scoped to the current checkout.

2. **Lineage verification and factory/test feasibility**
   - The implementer confirms against the current checkout that:
     - `CelestialBodies::Planets::Planet` is an abstract base class;
     - concrete rocky, ocean, and gaseous `Planets::*` classes descend from it;
     - no intended-to-be-rejected dwarf planet, asteroid, comet, or minor-body class descends from `CelestialBodies::Planets::Planet`;
     - `CelestialBodies::CelestialBody` provides `parent_celestial_body_id`, `solar_system_id`, and `orbital_elements` as expected.
   - Record the verification method (e.g., `grep`, `class_parents`, `descendants`) and results in the report.
   - Confirm that current factories/spec support can create the required test fixtures without redesign: both moon/planet failure directions; a non-lineage body; the required independent parent or missing-solar-context cases where applicable.
   - If any required fixture cannot be created, note the stop condition and escalate.

3. **Seeded retained-route verification**
   - Before code/spec/rake/docs changes, verify expected retained legacy routes used by the current caller surface, including Earth/Venus where applicable, satisfy:
     - `CelestialBodies::Planets::Planet` lineage;
     - `parent_celestial_body_id.nil?`;
     - a present and resolving `solar_system` association;
     - matching `solar_system_id` values when both endpoints resolve.
   - Stop if those routes fail the proxy/same-system pair condition or their existing caller contract becomes incompatible.

4. **Resolver and error convention verification**
   - Verify the existing resolver can be reused with the same identifier and case semantics.
   - Verify the error class's expected file/path, inheritance, and autoload conventions.
   - Verify `Mission::UnsupportedTransferError` is neither swallowed nor transformed by internal rescue scopes or production caller rescues.
   - Verify that every method name referenced in the Tests section (including `fallback_transfer_window`, `compute_transit_days_dynamic`, `fallback_transit_days`, and `compute_transit_days`) exists in the current checkout. If any name differs, report the difference in the pre-implementation report and use the actual names in the tests.
   - Stop if this cannot be established without broader behavior changes.

5. **Fixed-date rake baseline capture**
   - Before any code changes, capture the existing `luna_mission:phase_timing` output under a fixed/frozen date where project conventions permit.
   - Record the current Earth→Luna transit value and relevant downstream timeline output in the pre-implementation/synthesis report.
   - Record whether an existing named 7-day mission/scenario constant exists.
   - Stop if safe capture is unavailable under documented environment and test-process rules.

6. **Overlapping task check**
   - Confirm no overlapping active/review task owns this exact code or documentation surface.

### Required report contents

The required pre-implementation/synthesis report must explicitly include results for each of these six groups:

1. Caller audit.
2. Lineage verification and factory/test feasibility.
3. Seeded retained-route verification.
4. Resolver semantics plus error class/autoload/rescue convention verification.
5. Fixed-date rake baseline, Earth→Luna output/timeline capture, and named 7-day constant check.
6. Overlapping-task check.

### General verification

Before implementation, the implementer must re-read current project workflow guidance and re-check:

- actual task lifecycle/location conventions;
- test/factory support for all required cases;
- current error-class/autoload convention;
- current rake verification convention;
- current Git state and any overlapping active/review work.

During implementation, run only approved official/narrow commands identified from the repository. Report:

- commands run;
- pass/fail counts;
- changed files;
- expected baseline failures, if any;
- unexpected findings;
- stop conditions reached;
- proof that no unrelated scope was changed.

Do not run tests concurrently with another RSpec process. Follow the repository's documented test-environment prefix/wrapper.


## Stop conditions

Stop implementation, make no speculative extension, and escalate if any of the following is encountered:

1. An intended rejected dwarf-planet, asteroid, comet, or minor-body class descends from `CelestialBodies::Planets::Planet`, or cannot be distinguished safely from that lineage.
2. Expected retained legacy caller-route bodies, including Earth/Venus where applicable, fail the topology proxy or same-system pair condition.
3. Existing factories/spec support cannot produce all required cases without broad factory, model, schema, or data redesign: moon/planet in both directions; parented planet-lineage; missing/non-resolving solar-system context; generic non-lineage body; applicable dwarf/minor body; different-system pair; and unknown-ID combinations.
4. Error inheritance/autoload conventions, internal rescue scopes, or production caller rescues swallow or transform `Mission::UnsupportedTransferError`.
5. Existing resolver semantics cannot be reused with the same identifier and case semantics without divergent resolution behavior.
6. A safe fixed-date `luna_mission:phase_timing` baseline cannot be captured under documented environment and test-process rules.
7. Callers or specs require old dynamic Earth→Luna behavior as a broader public contract, or the Earth→Luna rake call has persistence/state effects beyond the verified `:transit_days` consumer.
8. A production-reachable caller can pass non-Sol, multi-star, procedurally generated, or partially generated/incomplete-system bodies.
9. Earth→Venus or another retained legacy caller route becomes incompatible after application of the topology proxy.
10. Documentation would require an unapproved hierarchy change or claims beyond verified behavior.
11. Work expands into governing-primary/frame metadata, per-primary \(\mu\), simulation-clock redesign, multi-leg/SOI routing, legacy-helper refactor, schema migration, or unrelated baseline cleanup.
12. An overlapping active or review task is found to own the same code or documentation surface.

## Acceptance criteria

- [ ] A resolved parentless same-system planet-lineage pair uses the existing dynamic path without invoking `fallback_transfer_window`; characterization only, no numeric or physical-validity claim.
- [ ] No manually maintained concrete planet-profile allowlist and no Sol name/identity check is added.
- [ ] Planet→moon and moon→planet each raise `Mission::UnsupportedTransferError`.
- [ ] A resolved generic non-lineage `CelestialBody` raises.
- [ ] A resolved planet-lineage body with a parent raises.
- [ ] A resolved planet-lineage body with nil `solar_system_id` or a non-resolving `solar_system` raises.
- [ ] A resolved dwarf-planet or minor-body record raises (after lineage verification).
- [ ] A parentless pair with differing `solar_system_id` raises.
- [ ] Moon→unknown raises; eligible planet→unknown and unknown→unknown preserve legacy fallback and do not raise.
- [ ] An eligible pair with missing/empty orbital data preserves the existing fallback.
- [ ] At least one test asserts the error class, the fragment `unsupported topology for legacy transfer calculation`, the offending resolved identifier, and propagation with no returned result.
- [ ] Resolved unsupported topology reaches neither dynamic calculation nor any fallback/route-table path; the guard sits outside every rescue scope and the error is not swallowed or transformed by internal or production-caller rescues.
- [ ] Direct legacy helpers remain unchanged and their existing behavior is preserved.
- [ ] `luna_mission:phase_timing` does not dynamically schedule Earth→Luna; it uses a labeled static 7-game-day scenario and preserves downstream timeline arithmetic.
- [ ] Earth→Venus remains on its existing dynamic scheduling path.
- [ ] No new ambient host-time lookup is introduced; new/modified tests use fixed dates.
- [ ] The mandatory pre-implementation verification was completed and the six-group report delivered before any code, spec, rake, or docs change; any escalation trigger (including production-reachable non-Sol, multi-star, procedural, or partially generated inputs) was stopped and reported.
- [ ] Documentation under `docs/wiki_reorganization/transportation/` reflects verified Phase 1 behavior and clear deferrals only, and GAPS records all six residual gaps without framing any intended system as invalid.
- [ ] Required narrow verification commands pass, or any baseline/unrelated failure is documented with evidence.
- [ ] No unrelated code, data, test, or wiki-reorganization work is included.
- [ ] No staging, commit, push, or lifecycle action is taken beyond the Step 0 rename of this task file (authorized by dispatch approval), and no lifecycle action is taken on `2026-09-29-HIGH-REFACTOR-TRANSIT-ENGINE.md`, without explicit human approval.

## Commit Instructions

Do not stage (beyond the Step 0 rename), commit, or push anything. After Step 8, stop and report; the human owner authorizes any commit separately. When authorized, run git commands on **host only** — never inside the Docker container — and `git add` specific files only (never `git add .`). On completion, move the task file to `completed/` per the repository convention (tracked file: `git mv`).

---

## Dependencies
**Blocked by**: none known (Verification item 6 overlapping-task check must confirm)
**Blocks**: none known. Governing-primary/frame-aware routing, simulation-time authority, and legacy-helper guarding are human-filed follow-up work, not created by this task.
**Related tasks**: `2026-09-29-HIGH-REFACTOR-TRANSIT-ENGINE.md` (protected; do not modify)

---

## Readiness checklist — intentionally incomplete

The dispatch-time rechecks below are performed by whoever gives final dispatch approval, immediately before dispatch. They supplement, and do not replace, the implementer's mandatory pre-implementation Verification.

- [ ] Local evidence reconfirmed against the current checkout immediately before dispatch.
- [ ] Error-class path/inheritance and autoload convention rechecked.
- [ ] Factory/test feasibility rechecked immediately before dispatch.
- [ ] Rake verification command rechecked immediately before dispatch.
- [ ] Gemini critique reconciled.
- [ ] Claude final technical disposition received.
- [ ] Human owner approved dispatch.
- [ ] Related 2026-09-29 task lifecycle explicitly deferred and recorded.

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures
**Evidence basis:** [direct verification / review of pasted evidence / reported by agent / human assertion] — [one-line source or note when not direct verification]

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
