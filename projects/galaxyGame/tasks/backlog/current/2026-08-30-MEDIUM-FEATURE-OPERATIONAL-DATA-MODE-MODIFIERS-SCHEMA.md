---
status: backlog
priority: MEDIUM
type: feature
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate — **EXCEPT** the `[project]`/`[SUBFOLDER]` path segments, matching the same gap as the last task Qwen filled in; fill in before dispatch
- [x] All Step 0-N instructions are clear and actionable
- [x] Synthesis report template provided
- [ ] All file paths are verified to exist — **NOT DONE**, Claude has no filesystem access; Step 1 of this task is exactly that verification
- [x] Architecture Gotchas are specific
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent**.

Project: [FILL IN — project folder name under agent-tasks/projects/]
Task: /Users/tam0013/Documents/git/agent-tasks/projects/[project]/tasks/backlog/[SUBFOLDER]/2026-08-30-MEDIUM-FEATURE-OPERATIONAL-DATA-MODE-MODIFIERS-SCHEMA.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  Confirm whether this file is tracked in git first (`git ls-files <path>`) —
  if tracked, use `git mv`; if untracked, use `mv` then `git add` the final
  path (per the lesson from the previous task's dispatch). Do not assume
  either state without checking.
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis
  until this is done.

LIFECYCLE: backlog → active → completed
  Verify with: find agent-tasks/projects/[project]/tasks -name "2026-08-30-MEDIUM-FEATURE-OPERATIONAL-DATA-MODE-MODIFIERS-SCHEMA.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/[project]/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat.
```

**IMPORTANT: Do not modify or abbreviate the text above.**

---

# TASK: Add `mode_modifiers` to unit_operational_data schema (v1.3 → v1.4)
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: feature
**Created**: 2026-08-30

---

## Local Worker Triage Report (Optional — for backlog review only)
- **Template Conformance**: [FILL IN — Qwen to verify against current TASK_TEMPLATE.md]
- **Docker Wrapper Check**: [FILL IN — confirm RSpec run convention for this repo]
- **MVP Alignment**: Not itself MVP-gating, but unblocks the L1 shield reactor's `low_power_sustain` mode, which is part of the terraforming/magnetosphere design thread
- **Action Line**: [FILL IN by Qwen after Step 1 verification]

---

## Agent Assignment (Human-filled, not seen by agents)
**Assigned To**: Qwen (shared-code change — needs Synthesis Report review before commit per standing guardrail)
**Supervision Level**: watched carefully — this touches a schema consumed by every unit with operational_data

---

## Prerequisites — READ FIRST (Sequential Order)
1. `/path/to/agent-tasks/README.md` (EXECUTOR Role section)
2. `/path/to/agent-tasks/projects/[project]/README.md`
3. This task file

---

## Context
While building a compact fusion reactor blueprint (`compact_fusion_reactor_l1`, see related task/summary if filed separately), a real need surfaced: the reactor needs a `low_power_sustain` operational mode where fuel consumption and power output both drop significantly (to keep a downstream magnetic-shield array's cryocoolers powered during a fuel-supply disruption) without dropping to zero (`standby`).

The current `unit_operational_data_v1.3` schema has no mechanism for this: `operational_modes` is just an array of mode-name strings plus a `current_mode` scalar, while `input_resources`/`output_resources` are flat arrays with a single rate each — there's no way to express "this rate changes depending on which mode is active." Gemini (doing the reactor's design pass) correctly flagged this as a schema gap rather than inventing an ad hoc pattern, and proposed two options.

**Decision made (2026-08-30): Approach B (multipliers), not Approach A (per-mode object restructuring).**
Reasoning: Approach A (restructuring `operational_modes` into an array of objects, each with its own full input/output/power block) would likely require migrating every existing unit's operational_data to the new shape — large blast radius for a need that doesn't currently exist (nothing today needs different fuel *ratios* per mode, only proportionally less of everything). Approach B is additive and backward-compatible: existing units with no `mode_modifiers` field behave exactly as they do today, and only units that need mode-dependent rates opt in. This matches the project's general pattern of deferring speculative complexity until a real need exists (see the Material/Render Profile systems, deliberately shelved on the same principle).

## Problem Statement
Add a `mode_modifiers` object to the `unit_operational_data` schema (bump to v1.4) that supplies scalar multipliers per mode, applied to a unit's base `input_resources`/`output_resources`/`power_consumption_kw` when that mode is active. Update whatever parser/service currently reads `operational_modes`/`current_mode` to apply the multiplier when present, defaulting to 1.0 (no change) when absent — for full backward compatibility with existing units.

Example shape (illustrative, confirm actual field-naming convention against the existing schema before implementing):
```json
"operational_modes": {
  "current_mode": "steady_state",
  "available_modes": ["offline", "standby", "low_power_sustain", "steady_state", "ramp_up_charging", "overdrive"],
  "mode_modifiers": {
    "low_power_sustain": { "input_multiplier": 0.05, "output_multiplier": 0.05, "power_consumption_multiplier": 1.0 }
  }
}
```

## Architecture Gotchas
⚠️ **GOTCHA 1**: This is shared/global schema code. Per standing guardrail, STOP and produce a Synthesis Report for review BEFORE committing, regardless of confidence — this exact category of change (base/shared code) has been committed without review multiple times before (item.rb, market-fee/base_settlement.rb) and is a known repeat-violation risk.

⚠️ **GOTCHA 2**: Confirm backward compatibility empirically, not just by inspection. After implementing the parser change, run existing specs for at least 2-3 units that currently use `operational_modes` and have NO `mode_modifiers` field — confirm their behavior is byte-for-byte unchanged. Don't just assert "default 1.0 multiplier means no change" — verify it.

⚠️ **GOTCHA 3**: Don't let this quietly become Approach A. If the parser implementation naturally wants to restructure `operational_modes` into objects "since we're in here anyway," stop — that's the explicitly rejected approach. Multipliers only.

⚠️ **GOTCHA 4**: This schema is versioned (`template_compliance`). Confirm how version bumps are tracked elsewhere in the project (is there a migration/changelog convention?) before just changing the string to "1.4" — don't invent a versioning convention if one already exists.

## Files Involved
| File | Purpose |
|---|---|
| Whatever parses/reads `operational_data` JSON at runtime (likely a service under `app/services/` — [FILL IN exact file]) | Needs the multiplier-application logic |
| Schema/template documentation for `unit_operational_data_v1.3` (wherever it's canonically defined — [FILL IN]) | Needs updating to v1.4 with the new field documented |
| `compact_fusion_reactor_l1_op.json` (or wherever it ends up living) | First real consumer of `mode_modifiers`, once this schema work lands |

## Acceptance Criteria
- [ ] `mode_modifiers` field added to the operational_data schema, documented, versioned as v1.4
- [ ] Parser applies multipliers when present, defaults to 1.0 (unchanged behavior) when absent
- [ ] Existing units' behavior confirmed unchanged via spec run (Gotcha 2)
- [ ] Synthesis Report produced and reviewed before commit (Gotcha 1)
- [ ] Reactor's `low_power_sustain` mode functionally verified to reduce fuel/output as designed, using this mechanism

## Stop Conditions
- Escalate back for review once the Synthesis Report is ready — do not commit shared-code changes without that review step, per standing guardrail
- If implementing this reveals the "flat resource arrays" assumption doesn't hold elsewhere in the codebase (e.g., some other unit already handles modes differently), stop and report rather than reconciling it solo

## Dependencies
**Blocked by**: none
**Blocks**: `compact_fusion_reactor_l1`'s `low_power_sustain` mode becoming functionally real (currently just a label — see [[magnetosphere-architecture]] memory)
**Related**: the reactor blueprint/operational-data task itself, if filed separately

## Completion Report
*Filled in by the implementing agent after completion*

## Handoff Summary
HANDOFF SUMMARY: [schema updated y/n] | [backward compat verified y/n] | [reactor mode now functional y/n]
