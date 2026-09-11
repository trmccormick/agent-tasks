---
status: backlog
priority: MEDIUM
type: refactor
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
created: 2026-09-10
estimated_effort: 1-2 hours
depends_on: []
blocks: []
---

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

**Task is READY FOR DISPATCH.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/data/2026-09-10-MEDIUM-REFACTOR-CAR300-NORMALIZATION-AUDIT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/data/2026-09-10-MEDIUM-REFACTOR-CAR300-NORMALIZATION-AUDIT.md \
         projects/galaxy_game/tasks/active/2026-09-10-MEDIUM-REFACTOR-CAR300-NORMALIZATION-AUDIT.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks -name "2026-09-10-MEDIUM-REFACTOR-CAR300-NORMALIZATION-AUDIT.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-09-10-REFACTOR-CAR300-NORMALIZATION-AUDIT.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: CAR-300 Schema + Path Prefix Normalization Audit
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: refactor
**Created**: 2026-09-10
**Last Updated**: 2026-09-10

---

## Context

During the Luna Blueprint/Operational Data Contract Audit (2026-09-10), CAR-300 was initially flagged as an outlier among 14 robots. **Subsequent verification revealed that ALL 14 robots deviate from the canonical `unit_blueprint_v1.3.json` template.** This is a fleet-wide issue, not just CAR-300.

### What Was Found: All 14 Robots Deviate from Canonical v1.3 Template

The canonical `data/json-data/templates/unit_blueprint_v1.3.json` has:
- No top-level `"version"` field (only `metadata.version`)
- `operational_data_reference.operational_properties: {}` (empty placeholder)
- No `physical_properties` inside `operational_data_reference`
- Empty `category` field

**CAR-300 deviations:**
| Field | CAR-300 | Canonical v1.3 |
|-------|---------|----------------|
| Top-level `"version": "1.3"` | ✅ present (extra) | ❌ absent |
| `metadata.designation`/`mk_version` | ✅ present (extra) | ❌ absent |
| `operational_data_reference.operational_properties` | **Populated with real values** | `{}` empty |
| `category` | `"robots"` | `""` (empty) |

**Other 13 robots deviations:**
| Field | Other 13 Robots | Canonical v1.3 |
|-------|-----------------|----------------|
| Top-level `"version"` field | v1.0 or v1.2 (extra, wrong version) | ❌ absent |
| `operational_data_reference.physical_properties` | `{}` present (extra) | ❌ absent |
| `category` value | `"robot"` (singular) | `""` (empty) |
| `operational_data_reference.operational_properties` | `{}` empty | `{}` empty ✅ matches |

**Neither pattern matches the canonical template.** The v1.3 template appears aspirational — no robot actually conforms to it fully.

### Immediate Scope: CAR-300 Only

This task is narrowed to auditing CAR-300 specifically:
- Determine whether its v1.3 pilot and `operational_data/` path prefix are intentional or accidental
- Document how CAR-300 differs from both the other 13 robots AND the canonical v1.3 template
- **Do not attempt to fix all 14 robots** — that is a separate, fleet-wide issue requiring its own task and Tracy's prioritization

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1**: This is a read-only audit + minimal fix task only.
- ❌ Wrong: "I'll refactor all robots to v1.3" or "I'll rewrite the operational data loader"
- ✅ Right: Determine intent, normalize CAR-300 to match the majority convention (or vice versa if v1.3 is confirmed intentional), grep for other outliers
- Why: Scope creep on data normalization tasks burns requests and risks breaking unrelated systems

⚠️ **GOTCHA 2**: The `operational_data/` prefix may be a loader bug, not a data issue.
- ❌ Wrong: "The path prefix is wrong in the blueprint"
- ✅ Right: Check if the operational data loader strips `operational_data/` from the prefix before resolving — if so, CAR-300's file is actually correct and the loader needs to handle both conventions
- Why: If the loader already handles this, no fix is needed

⚠️ **GOTCHA 3**: v1.3 may be a planned future standard.
- ❌ Wrong: "Normalize everything to v1.2" without confirming intent
- ✅ Right: Check git history for when CAR-300 was upgraded to v1.3 and why; check if any docs reference v1.3 as the target schema
- Why: Downgrading v1.3 could break planned features

### Files Involved

#### Primary Files — audit these first
| File | Purpose |
|------|---------|
| `data/json-data/blueprints/units/robots/deployment/car_300_deployment_robot_mk1_bp.json` | CAR-300 blueprint (v1.3, outlier) |
| `data/json-data/blueprints/units/robots/construction/acr_100_space_constructor_mk1_bp.json` | ACR-100 blueprint (v1.2, standard reference) |
| `data/json-data/blueprints/units/robots/resource/hrv_400_resource_harvester_mk1_bp.json` | HRV-400 blueprint (v1.2, standard reference) |

#### Operational Data Files
| File | Purpose |
|------|---------|
| `data/json-data/operational_data/units/robots/deployment/car_300_lunar_deployment_robot_mk1_data.json` | CAR-300 operational data (has `operational_data/` prefix) |
| `data/json-data/operational_data/units/robots/construction/acr_100_space_constructor_mk1_data.json` | ACR-100 operational data (no prefix, standard) |

#### Loader Code — check if it strips the prefix
| File | Why You Need It |
|------|-----------------|
| Search for `operational_data_reference` in codebase | Find where the path is resolved |
| Search for `unit_operational_data_v1.3` | Check if v1.3 loader exists |

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
(See Agent Dispatch Interface above.)

### Step 1: Audit — determine intent of CAR-300's v1.3 schema

**Actions:**
1. Grep for `unit_blueprint_v1.3` across the codebase to see if any loader/validator references it
2. Check git history for when CAR-300 was upgraded to v1.3:
   ```bash
   cd /Users/tam0013/Documents/git/galaxyGame && git log --oneline -10 -- data/json-data/blueprints/units/robots/deployment/car_300_deployment_robot_mk1_bp.json
   ```
3. Check if any docs reference v1.3 as a target schema
4. Grep for `operational_data/` prefix in all blueprints to find other outliers:
   ```bash
   grep -r '"file": "operational_data/' data/json-data/blueprints/
   ```

**Deliverable**: Synthesis report answering:
- Is v1.3 intentional or accidental?
- Is the `operational_data/` prefix intentional or accidental?
- Are there other units with the same pattern?

### Step 2: Normalize (CAR-300 only — minimal fix)

**If CAR-300 is accidental (most likely):**
- Downgrade CAR-300 blueprint to match the other 13 robots:
  - Remove top-level `version: "1.3"` → set to `"1.2"` or remove field
  - Remove `metadata.designation` and `metadata.mk_version` fields
  - Change `operational_data_reference.file` from `operational_data/units/robots/deployment/car_300_lunar_deployment_robot_mk1_data.json` to `units/robots/deployment/car_300_lunar_deployment_robot_mk1_data.json` (strip prefix)
- If the operational data file itself has v1.3-specific fields, strip those too

**If CAR-300 is intentional (v1.3 is future standard):**
- Document v1.3 as the target schema in DECISIONS.md
- **Do NOT upgrade other robots in this task** — file follow-up tasks for Tracy to prioritize

⚠️ **CRITICAL: Do not unilaterally "fix" all 13 non-CAR-300 robots.** They have their own deviations from the canonical template that are different from CAR-300's. Fixing them requires a separate fleet-wide task.

### Step 3: Verify

Run any specs that load operational data for robots:
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/models/units/base_unit_spec.rb spec/services/mission/transit_engine_spec.rb 2>&1 | tail -20'
```

### Step 4: Synthesis Report (before committing)

Save to summaries folder. Do not commit until approved.

---

## Wider Problem: Fleet-Wide Deviations from Canonical v1.3 Template

**All 14 robot blueprints deviate from `data/json-data/templates/unit_blueprint_v1.3.json`.** The canonical template appears aspirational — no robot actually conforms to it fully.

### Summary of Fleet-Wide Deviations

| Issue | Scope | Details |
|-------|-------|---------|
| Extra top-level `"version"` field | All 14 robots | Canonical v1.3 has no top-level version; only `metadata.version` is expected |
| `operational_data_reference.physical_properties: {}` (empty) | Other 13 robots | Not present in canonical template at all — extra field added to these blueprints |
| `operational_data_reference.operational_properties` populated | CAR-300 only | Canonical v1.3 has `{}` placeholder; CAR-300 is the only robot with real values |
| `metadata.designation` / `metadata.mk_version` fields | CAR-300 only | Not in canonical template — CAR-300 pilot for future schema evolution |
| `category` naming inconsistency | All 14 robots | CAR-300 uses `"robots"` (plural); other 13 use `"robot"` (singular); canonical has `""` (empty) |
| Empty `operational_properties: {}` on 13 robots | Other 13 robots | Deployable robots with zero operational data — genuine gap, not just structural |

### Recommended Follow-Up Tasks for Tracy

**Task A: Robot Blueprint Schema Normalization**
> "Align all 14 robot blueprints with canonical v1.3 (or revise the template to match reality)."
- Audit which fields in the canonical v1.3 template are still relevant vs. obsolete
- Decide: update all robots to match the template, OR update the template to match what robots actually need
- Fix extra `version` fields, `physical_properties` inside `operational_data_reference`, `category` naming
- This is a fleet-wide refactor affecting 14 blueprint files + potentially loader code

**Task B: Operational-Properties Population for Deployable Robots**
> "Define what `operational_properties` should contain and backfill for all 14 robots."
- Define the contract: which fields are required vs. optional in `operational_data_reference.operational_properties`
- CAR-300 already has real values (power_consumption_kw, max_payload_kg, operational_range_km, battery_life_hours, autonomy_level)
- Other 13 robots have `{}` — deployable robots with no operational data means the loader can't do anything meaningful with them
- This is a genuine data gap, not just a structural issue

---

## Acceptance Criteria
- [ ] Determined whether CAR-300's v1.3 schema is intentional or accidental
- [ ] Determined whether the `operational_data/` path prefix is intentional or accidental
- [ ] Documented how CAR-300 differs from both the other 13 robots AND the canonical v1.3 template
- [ ] Confirmed all 14 robots deviate from canonical v1.3 (Wider Problem section above)
- [ ] If accidental: CAR-300 normalized to match the other 13 robots with minimal changes
- [ ] If intentional: v1.3 documented in DECISIONS.md + follow-up tasks filed for Tracy
- [ ] No regressions in robot-related specs
- [ ] Synthesis report saved to summaries folder

---

## Stop Conditions — escalate to user immediately if:
- The loader code strips `operational_data/` from the prefix (CAR-300 is actually correct, no fix needed)
- v1.3 schema is confirmed intentional but no docs exist for it (gap in documentation)
- Fix requires changes beyond CAR-300's blueprint + operational data files
- **⚠️ CRITICAL: Do NOT unilaterally "fix" all 13 non-CAR-300 robots.** They have different deviations from the canonical template. Fleet-wide normalization is a separate task requiring Tracy's prioritization.
- **⚠️ CRITICAL: Do NOT backfill `operational_properties` on other 13 robots in this task.** That is a genuine data gap but requires its own scope and contract definition.

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add [specific files only]
git commit -m "refactor: normalize CAR-300 blueprint to match 13-robot standard (v1.2, strip operational_data/ prefix)"
# OR if v1.3 is intentional:
git commit -m "docs: record v1.3 schema as target standard for robot blueprints"
```

**Task file move on completion:**
```bash
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy_game/tasks/active/2026-09-10-MEDIUM-REFACTOR-CAR300-NORMALIZATION-AUDIT.md \
       projects/galaxy_game/tasks/completed/2026-09/2026-09-10-MEDIUM-REFACTOR-CAR300-NORMALIZATION-AUDIT.md
git commit -m "chore: move CAR-300 normalization audit to completed/"
```

---

## Documentation
- [ ] If v1.3 is intentional: update DECISIONS.md with schema version decision
- [x] No doc changes if accidental (data fix only)

---

## Dependencies
**Blocked by**: none
**Blocks**: None directly; may unblock Luna settlement sim if loader has path resolution issues
**Related tasks**: RH-400 duplicate blueprint consolidation (2026-09-09); Luna Blueprint/Operational Data Contract Audit (2026-09-10)

**Recommended follow-up tasks for Tracy:**
- **Task A: Robot Blueprint Schema Normalization** — align all 14 robot blueprints with canonical v1.3 (or revise the template to match reality)
- **Task B: Operational-Properties Population** — define what `operational_properties` should contain and backfill for all 14 robots

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Final result**:

### What was changed

### Issues discovered

### Follow-up tasks needed

### Lessons learned

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY:
