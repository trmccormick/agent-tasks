---
status: backlog
resume: false
priority: HIGH
type: feature
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

**One allowed substitution:** before dispatch, replace `<agent-tasks>` with the absolute path of the agent-tasks clone on the machine that will run the task (run `pwd` from the repo root). Fill `[project_name]`, `[project]`, `[SUBFOLDER]` and `[FILENAME]` in the file when the task is drafted, so the block carries no other placeholders at dispatch; the only substitution made at dispatch is `<agent-tasks>`. If the project's backlog is flat (no subfolders), omit the `[SUBFOLDER]/` segment from the paths. Change nothing else.

```
You are **Implementation Agent**.

Project: Galaxy Game
Task: <agent-tasks>/projects/galaxyGame/tasks/backlog/current/2026-11-25-HIGH-FEATURE-ASTRONOMY-NASA-EXOPLANET-ARCHIVE-CLIENT.md

STEP 0 — VERIFY, STAGE IF NEEDED, THEN MOVE WITH GIT MV:
  Run from the agent-tasks repository root.

  1. Inspect the source Git state:
       git status --short -- projects/galaxyGame/tasks/backlog/current/2026-11-25-HIGH-FEATURE-ASTRONOMY-NASA-EXOPLANET-ARCHIVE-CLIENT.md
       git ls-files --error-unmatch projects/galaxyGame/tasks/backlog/current/2026-11-25-HIGH-FEATURE-ASTRONOMY-NASA-EXOPLANET-ARCHIVE-CLIENT.md
     If the source is not tracked, stage the source first:
       git add projects/galaxyGame/tasks/backlog/current/2026-11-25-HIGH-FEATURE-ASTRONOMY-NASA-EXOPLANET-ARCHIVE-CLIENT.md
     Preserve all pre-existing changes; do not discard or overwrite them.

  2. Move the task:
       git mv projects/galaxyGame/tasks/backlog/current/2026-11-25-HIGH-FEATURE-ASTRONOMY-NASA-EXOPLANET-ARCHIVE-CLIENT.md \
              projects/galaxyGame/tasks/active/2026-11-25-HIGH-FEATURE-ASTRONOMY-NASA-EXOPLANET-ARCHIVE-CLIENT.md

  3. Verify the move:
       git status --short
       git ls-files --error-unmatch projects/galaxyGame/tasks/active/2026-11-25-HIGH-FEATURE-ASTRONOMY-NASA-EXOPLANET-ARCHIVE-CLIENT.md
       test ! -e projects/galaxyGame/tasks/backlog/current/2026-11-25-HIGH-FEATURE-ASTRONOMY-NASA-EXOPLANET-ARCHIVE-CLIENT.md && test -f projects/galaxyGame/tasks/active/2026-11-25-HIGH-FEATURE-ASTRONOMY-NASA-EXOPLANET-ARCHIVE-CLIENT.md
     Confirm the source is absent, destination exists, and Git tracks the destination.

  4. Open the active task file and change YAML status from backlog to active. Verify the updated status.

  5. Record Git-state inspection, any staging command, git mv, verification outputs, and YAML status change in the synthesis report. Do not begin investigation or implementation until Step 0 and the initial synthesis report are complete.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when moving the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv.
  - New/untracked file: git add the source first, then git mv the source to the destination.
  - Never use plain mv or cp for task lifecycle moves.
  - Never leave stale copies in the source folder.
  - Verify with: find agent-tasks/projects/galaxyGame/tasks -name "2026-11-25-HIGH-FEATURE-ASTRONOMY-NASA-EXOPLANET-ARCHIVE-CLIENT.md"
    Exactly ONE result should exist. Record this output in the synthesis/evidence report.

READ FIRST (after Step 0), in this order:
  1. <agent-tasks>/rules/GUARDRAILS.md
  2. <agent-tasks>/projects/galaxyGame/README.md
  3. <agent-tasks>/projects/galaxyGame/SESSION_GUIDANCE.md (if the project has one)
  4. The task file. It holds what is specific to this task; the files above hold standing rules.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting investigation.
  Summaries path: <agent-tasks>/projects/galaxyGame/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Update the report after Step 0/before investigation, after investigation/before implementation, and after implementation/verification.
  In chat, provide only the report path and concise status; do not paste the report or raw command output.

Closeout: follow SESSION_CLOSEOUT.md only.
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

## 🟡 DRAFT STATUS NOTE (for tasks in tasks/drafts/)

**This section applies ONLY if status: draft above.**

This task is in draft status and awaiting review approval. It has NOT yet been approved for backlog.

**Current State**: In `tasks/drafts/`  
**Next Step**: Independent review (template + content) → git-aware verification of repo claims → Human approval → Move to `tasks/backlog/` by human  
**Do NOT dispatch to implementation agents** until this task is in `tasks/backlog/` with `status: backlog`

## 🟡 DRAFT REVIEW CHECKLIST (Human — before approving move from drafts/ to backlog/)

**APPLIES ONLY IF status: draft above.**

After independent review and git-aware path verification:

- [ ] Acceptance Criteria are clear and measurable (no ambiguity)
- [ ] Task scope is well-bounded (not too broad, not overlapping with active tasks)
- [ ] Dependencies listed and verified against status.md (all prerequisites exist)
- [ ] No contradictions in the task specification
- [ ] Architecture aligns with current codebase patterns (git-aware agent or human verified)
- [ ] Review agent disposition is GO (not REVISE or STOP)

**Once ALL boxes checked:** Approve by moving task from `tasks/drafts/` to `tasks/backlog/current/` and changing `status: draft → status: backlog`.

---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching to implementation)

**APPLIES ONLY IF status: backlog.**

- [x] Agent Dispatch Interface section above is complete and accurate (no placeholders)
- [x] All Step 0-N instructions are clear and actionable (not vague)
- [x] Synthesis report template is provided
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist (git-aware check)
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear
- [x] Dependency Task 1 base client is present in the GalaxyGame codebase (for provider client tasks)

**Task is READY. All checkboxes completed.**


---

# TASK: Astronomy NASA Exoplanet Archive Client (Targeted Lookups)
**Status**: BACKLOG  
**Priority**: HIGH  
**Type**: feature  
**Created**: 2026-11-25  
**Last Updated**: 2026-11-25  
**Author**: Grok (revised after Gemini + Perplexity feedback on the shared series; investigation-based)

Provider client that depends on the shared TAP base client. Independently reviewable; do not dispatch until the base client exists in the GalaxyGame codebase. Does not invent provenance schema, cache, or consumer wiring. Unrelated to `Import::NasaDemImporter` (terrain DEM).

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)  
**Why This Agent**: Bounded provider client on existing base; stubbed tests.  
**Local attempts before cloud**: N/A  
**Supervision Level**: watched carefully  

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Startup order**: follow the READ FIRST list in the Agent Dispatch Interface above.
2. **Also read**: `<agent-tasks>/README.md` (EXECUTOR Role section)
3. **This Task File**
4. **Owner architecture**: `docs/wiki/astronomy/system_blueprints.md` — NASA Exoplanet Archive role, OPEN decisions.
5. **Hard dependency**: Shared TAP base client from Task `2026-11-25-HIGH-FEATURE-ASTRONOMY-SHARED-TAP-BASE-CLIENT` must exist under `galaxy_game/app/services/astronomy/`. If missing after Stage 2, STOP — do not reimplement the base client.

---

## Context

NASA Exoplanet Archive supplies confirmed exoplanets and host parameters via TAP. Confirmed tables are not exhaustive; missing planet row ≠ proof no planets exist. Prefer modern TAP tables (e.g. `ps` with `default_flag=1`). Use base-client error taxonomy.

**Relevant Architecture Docs**:
- `docs/wiki/astronomy/system_blueprints.md`
- Base client under `galaxy_game/app/services/astronomy/`

---

## Critical Information for This Task

### Credentials
None.

### Architecture Gotchas

⚠️ **GOTCHA 1**: Empty lookup ≠ transport failure and ≠ “no planets”  
- HTTP 2xx with zero matching rows is **successful transport** with an empty/no-match result — not `error_kind: unavailable`.  
- `unavailable` / `timeout` / `unsuccessful` remain **transport-level** outcomes from the base client only.  
- ❌ Wrong: Map empty planet rows to transport `unavailable`, or treat empty as proof the host has no planets.  
- ✅ Right: Preserve success status; empty = unknown/no-match; never claim definitive absence of planets.  

⚠️ **GOTCHA 2**: Preserve source fields; no provenance schema or schema versioning in this task  

⚠️ **GOTCHA 3**: Do not couple to `Import::NasaDemImporter`  

⚠️ **GOTCHA 4**: Stub transport or base client; no live network  

⚠️ **GOTCHA 5**: StarSim generator-spec drafts — do not touch  

⚠️ **GOTCHA 6**: Prefer targeted `ps` (or current recommended table) with hostname + default_flag filters — not full-table download  

⚠️ **GOTCHA 7**: Do not invent cache/rate-limit policy (OPEN)  

---

## 🔴 REQUIRED: Status Synthesis Report (Before Investigation or Implementation)

After Step 0, create the synthesis report under `projects/galaxyGame/summaries/`. Filename pattern: `YYYY-MM-DD-FEATURE-ASTRONOMY-NASA-EXOPLANET-ARCHIVE-CLIENT.md`.

```markdown
## STATUS SYNTHESIS REPORT

**Task**: 2026-11-25-HIGH-FEATURE-ASTRONOMY-NASA-EXOPLANET-ARCHIVE-CLIENT
**Status**: [backlog → active → completed]
**Date**: YYYY-MM-DD

### Stage 1 — Initial understanding and Step 0
[Step 0 git evidence, objective, boundaries: uses base client; no schema/cache; empty ≠ transport failure and ≠ no planets]

### Stage 2 — Investigation findings (complete before implementation)
[Confirm base client exists and API shape; proposed ADQL/table; test plan with stub seam.
After Stage 2, STOP and obtain human approval of the proposed approach before editing application files.]

### Stage 3 — Implementation and verification
[Files changed, exact rspec command/results, confirmation empty is success+no-match not unavailable, diff scope]

### Expected Outcomes
NASA EA client + stubbed specs; uses base client; no consumer wiring; no DEM coupling
```

In chat, provide only the report path and concise status.

---


## 🔴 REVIEW GATE — STOP BEFORE COMMITTING (MANDATORY)

**You MAY NOT commit, push, or mark any task step as "complete" until BOTH of the following conditions are met:**

1. **Reviewer has issued a GO disposition** — a reviewer has assessed your work and disposed it as one of: `GO`, `REVISE`, or `STOP`. See REVIEW_AGENT_GUIDE.md for reviewer authority definitions.
2. **Human authorization** — Requires an explicit human message that authorizes commit (for example, 'commit it') and an explicit human message that authorizes push (for example, 'push it'). Authorization to commit does not imply authorization to push, and authorization to push does not imply authorization to commit.

Confirming a command in an agent's own approval prompt counts as authorization only after a reviewer GO is on record and the human explicitly authorizes that particular operation; before a GO, a click does not authorize commit or push.

### What This Means In Practice

| You CAN do | You CANNOT do |
|---|---|
| Implement changes per task instructions | Commit/push/mark-complete without both conditions above |
| Run tests and verify your changes locally | Assume reviewer GO is sufficient to proceed to commit |
| Create synthesis and evidence artifacts | Proceed to the next task step if review disposition is REVISE or STOP |
| Report results and request review | Override a reviewer's REVISE or STOP disposition |

### Review-Gate Workflow

1. **Complete all implementation and verification steps** listed below this section
2. **Create evidence artifacts** (test output, synthesis reports)
3. **STOP** — save all evidence in the synthesis/evidence report. In chat, provide only the report path and a concise update stating: *"Implementation and verification are complete. Requesting reviewer assessment."* Do not paste the report or raw command output into chat.
4. **Wait for reviewer disposition** (GO / REVISE / STOP)
5. If **REVISE**: address the listed issues, re-test, return to step 3
6. If **STOP**: do not proceed; await human guidance
7. If **GO**: wait for an explicit human message that authorizes commit. Push needs a separate, later human message that authorizes push.
8. Once **both** conditions are satisfied: proceed to Commit Instructions below

> **This checkpoint is NOT optional.**


---

## Problem Statement

No NASA Exoplanet Archive client for confirmed planets/host parameters. Task 1 provides transport; this task adds targeted lookups with field-preserving results.

**Current behavior**: No archive client under `app/services/astronomy/`.  
**Expected behavior**: Client using base TAP transport; host-targeted confirmed-planet lookup; empty ≠ absence; stubbed specs.

---

## Scope

### In scope
- `galaxy_game/app/services/astronomy/nasa_exoplanet_archive_client.rb` using base client.
- TAP sync base URL for the archive.
- Targeted lookup by host star name (default/best parameter set when archive provides a flag).
- Results compose base result fields plus retained planet/host parameters and uncertainties when present.
- Specs with stubs under `galaxy_game/spec/services/astronomy/nasa_exoplanet_archive_client_spec.rb`.

### Out of scope
- SIMBAD client; base client reimplementation
- Cache, provenance schema, matching thresholds
- Consumer wiring; DEM importer changes
- Bulk ingestion; live network tests

---

## Files Involved

### Primary Files
| File | Purpose |
|---|---|
| `galaxy_game/app/services/astronomy/nasa_exoplanet_archive_client.rb` | Targeted archive lookups |
| `galaxy_game/spec/services/astronomy/nasa_exoplanet_archive_client_spec.rb` | Stubbed specs |

Optional documented helper under `galaxy_game/app/services/astronomy/` only if needed.

### Reference Files
| File | Why |
|---|---|
| `galaxy_game/app/services/astronomy/base_tap_client.rb` | Transport + errors |
| `docs/wiki/astronomy/system_blueprints.md` | Owner constraints |
| `galaxy_game/app/services/import/nasa_dem_importer.rb` | Confirm non-coupling (read only) |

### Migration
- [x] No migration needed

---

## Implementation Steps

> Step 0 first; Stage 1 synthesis; Stage 2 investigation; **STOP for human approval** before implementation edits.

### Step 0 — Dispatch Step 0 exactly

### Step 1 — Stage 2 investigation
Confirm base client API; propose ADQL against recommended table; no live network. **STOP for human approval.**

### Step 2 — Implement client
Targeted host query; on HTTP 2xx with zero rows, return successful transport with empty/no-match (unknown)—do not set transport error_kind; retain provider fields; no schema invention.

### Step 3 — Specs
Success with rows; HTTP 2xx empty/no-match (still success, not unavailable); transport failure via base taxonomy. Stub at HTTP or base client. Zero live calls.

### Step 4 — Verify
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/astronomy/nasa_exoplanet_archive_client_spec.rb 2>&1 | tail -40'
```
Expected: 0 failures. Diff only Files Involved (+ optional astronomy helper).

### Step 5 — Synthesis Stage 3; Review Gate; no commit

---

## Acceptance Criteria

- [ ] Client exists under `galaxy_game/app/services/astronomy/` and uses Task 1 base client.
- [ ] Targeted host-based confirmed-planet lookup (not bulk).
- [ ] HTTP 2xx with zero matching rows ⇒ successful transport + empty/no-match (unknown); not transport `unavailable`; not proof the host has no planets.
- [ ] Retain planet name, hostname, key parameters/uncertainties when present — no permanent provenance schema.
- [ ] Specs stub transport/base client; success, empty, failure; zero live network.
- [ ] Exact docker rspec command passes (or without `-it`).
- [ ] git status evidence limited to Files Involved (+ optional helper); no DEM/StarSim/mapping edits.
- [ ] No cache, schema versioning, or DB migration.
- [ ] Synthesis saved; no agent commit/push.

---

## Stop Conditions — escalate to user immediately if:

- Base client missing
- Untouched specs fail; two failed fix attempts
- Gemfile change or migration needed
- Architectural decision (schema, cache, matching, consumer wiring)
- Files outside Files Involved (optional astronomy helper permitted)
- Live network required; StarSim/generator-spec edits appear required
- Pressure to couple to NasaDemImporter

---

## Commit Instructions

> Reviewer GO + explicit human commit auth; separate push auth. Host-only git.

```bash
git add galaxy_game/app/services/astronomy/nasa_exoplanet_archive_client.rb         galaxy_game/spec/services/astronomy/nasa_exoplanet_archive_client_spec.rb
git commit -m "feature: astronomy NASA Exoplanet Archive client — targeted host lookup, stubbed specs"
```

---

## Documentation

Class comments only; no schema/cache design docs.

---

## Dependencies

- **Blocked by**: Task 1 base client in codebase.
- **Independent of**: SIMBAD client draft (after base exists); StarSim generator-spec drafts.

---

## Progress / Resume

```
resume: false
last_completed_step: none
next_step: Step 0 when dispatched
notes: Revised after series review feedback; depends on Task 1 in code.
```

---

## Completion Report

Steps, paths, rspec results, auth evidence, no live network / no schema / no DEM coupling.

---

## Handoff Summary

- **Done**: NASA EA targeted client + stubbed specs on shared base.
- **Not done**: Consumer wiring, schema, cache, SIMBAD if not yet done.
- **Next**: Integration boundaries documentation task when human prioritizes it.
