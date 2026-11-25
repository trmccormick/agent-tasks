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
Task: <agent-tasks>/projects/galaxyGame/tasks/backlog/current/2026-11-25-HIGH-FEATURE-ASTRONOMY-SIMBAD-CLIENT.md

STEP 0 — VERIFY, STAGE IF NEEDED, THEN MOVE WITH GIT MV:
  Run from the agent-tasks repository root.

  1. Inspect the source Git state:
       git status --short -- projects/galaxyGame/tasks/backlog/current/2026-11-25-HIGH-FEATURE-ASTRONOMY-SIMBAD-CLIENT.md
       git ls-files --error-unmatch projects/galaxyGame/tasks/backlog/current/2026-11-25-HIGH-FEATURE-ASTRONOMY-SIMBAD-CLIENT.md
     If the source is not tracked, stage the source first:
       git add projects/galaxyGame/tasks/backlog/current/2026-11-25-HIGH-FEATURE-ASTRONOMY-SIMBAD-CLIENT.md
     Preserve all pre-existing changes; do not discard or overwrite them.

  2. Move the task:
       git mv projects/galaxyGame/tasks/backlog/current/2026-11-25-HIGH-FEATURE-ASTRONOMY-SIMBAD-CLIENT.md \
              projects/galaxyGame/tasks/active/2026-11-25-HIGH-FEATURE-ASTRONOMY-SIMBAD-CLIENT.md

  3. Verify the move:
       git status --short
       git ls-files --error-unmatch projects/galaxyGame/tasks/active/2026-11-25-HIGH-FEATURE-ASTRONOMY-SIMBAD-CLIENT.md
       test ! -e projects/galaxyGame/tasks/backlog/current/2026-11-25-HIGH-FEATURE-ASTRONOMY-SIMBAD-CLIENT.md && test -f projects/galaxyGame/tasks/active/2026-11-25-HIGH-FEATURE-ASTRONOMY-SIMBAD-CLIENT.md
     Confirm the source is absent, destination exists, and Git tracks the destination.

  4. Open the active task file and change YAML status from backlog to active. Verify the updated status.

  5. Record Git-state inspection, any staging command, git mv, verification outputs, and YAML status change in the synthesis report. Do not begin investigation or implementation until Step 0 and the initial synthesis report are complete.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when moving the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv.
  - New/untracked file: git add the source first, then git mv the source to the destination.
  - Never use plain mv or cp for task lifecycle moves.
  - Never leave stale copies in the source folder.
  - Verify with: find agent-tasks/projects/galaxyGame/tasks -name "2026-11-25-HIGH-FEATURE-ASTRONOMY-SIMBAD-CLIENT.md"
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

- [ ] Agent Dispatch Interface section above is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist (git-aware check)
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear
- [ ] Dependency Task 1 base client is present in the GalaxyGame codebase (for provider client tasks)

**Task is NOT READY until all checkboxes are completed.**


---

# TASK: Astronomy SIMBAD Client (Targeted Lookups)
**Status**: DRAFT  
**Priority**: HIGH  
**Type**: feature  
**Created**: 2026-11-25  
**Last Updated**: 2026-11-25  
**Author**: Grok (revised after Gemini + Perplexity feedback on the shared series; investigation-based)

Provider client that depends on the shared TAP base client (`2026-11-25-HIGH-FEATURE-ASTRONOMY-SHARED-TAP-BASE-CLIENT`). Independently reviewable; do not dispatch until the base client exists in the GalaxyGame codebase. Does not invent provenance schema, cache, or consumer wiring.

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
4. **Owner architecture**: `galaxy_game/docs/wiki/astronomy/system_blueprints.md` — SIMBAD role, OPEN decisions (schema, cache, matching).
5. **Hard dependency**: Shared TAP base client from Task `2026-11-25-HIGH-FEATURE-ASTRONOMY-SHARED-TAP-BASE-CLIENT` must exist under `galaxy_game/app/services/astronomy/`. If missing after Stage 2 investigation, STOP and report — do not reimplement the base client in this task.

---

## Context

SIMBAD is the owner-confirmed source for object identity and cross-identification: catalog identifiers, aliases, classifications, coordinates/astrometry, and available measurements. It is not a complete census; absence is not proof of non-existence. Lookups must be targeted via TAP. This client is a shared main-game service.

Transport, timeout, and error kinds (`timeout` / `unavailable` / `unsuccessful`) come from the shared base client. Do not invent a second taxonomy unless documenting a pure extension.

**Relevant Architecture Docs**:
- `galaxy_game/docs/wiki/astronomy/system_blueprints.md`
- Base client under `galaxy_game/app/services/astronomy/` from Task 1

---

## Critical Information for This Task

### Credentials
None. Public SIMBAD TAP.

### Architecture Gotchas

⚠️ **GOTCHA 1**: Empty lookup ≠ transport failure and ≠ non-existence  
- HTTP 2xx with zero matching rows is **successful transport** with an empty/no-match result — not `error_kind: unavailable`.  
- `unavailable` / `timeout` / `unsuccessful` remain **transport-level** outcomes from the base client only.  
- ❌ Wrong: Map empty rows to transport `unavailable`, or treat empty as proof the object does not exist.  
- ✅ Right: Preserve success status; represent empty as unknown/no-match; never claim definitive non-existence.  

⚠️ **GOTCHA 2**: Preserve provenance-relevant fields; no schema invention  
- Retain identifiers, aliases, coordinates, frame/epoch, uncertainties when present. Do not invent a permanent provenance/JSONB schema.

⚠️ **GOTCHA 3**: Tests — stub transport or base client; no live network  
- Stub at HTTP boundary or by stubbing the base client dependency. Do not stub the SIMBAD client under test so that nothing is exercised.

⚠️ **GOTCHA 4**: Do not implement NASA Exoplanet Archive here  

⚠️ **GOTCHA 5**: StarSim generator-spec drafts remain independent — do not touch  

⚠️ **GOTCHA 6**: Do not invent cache or rate-limit policy  
- Use base-client timeout/errors only. Cache/mirror/refresh remains OPEN.

---

## 🔴 REQUIRED: Status Synthesis Report (Before Investigation or Implementation)

After Step 0, create the synthesis report under `projects/galaxyGame/summaries/`. Filename pattern: `YYYY-MM-DD-FEATURE-ASTRONOMY-SIMBAD-CLIENT.md`.

```markdown
## STATUS SYNTHESIS REPORT

**Task**: 2026-11-25-HIGH-FEATURE-ASTRONOMY-SIMBAD-CLIENT
**Status**: [backlog → active → completed]
**Date**: YYYY-MM-DD

### Stage 1 — Initial understanding and Step 0
[Step 0 git evidence, objective, boundaries]

### Stage 2 — Investigation findings (complete before implementation)
[Confirm base client exists and API shape; SIMBAD endpoint constants; proposed methods; test plan.
After Stage 2, STOP and obtain human approval of the proposed approach before editing application files.]

### Stage 3 — Implementation and verification
[Files changed, exact rspec command/results, diff scope]

### Expected Outcomes
SIMBAD client + stubbed specs; uses base client; no consumer wiring
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

No SIMBAD client exists. Task 1 provides TAP transport; this task adds targeted SIMBAD identity/astrometry lookup with field-preserving results.

**Current behavior**: No SIMBAD integration under `app/services/astronomy/`.  
**Expected behavior**: `Astronomy::SimbadClient` (or equivalent) using the base client; targeted name/id lookup; empty ≠ non-existence; stubbed specs.

---

## Scope

### In scope
- `galaxy_game/app/services/astronomy/simbad_client.rb` using the shared base TAP client.
- Default SIMBAD TAP sync base URL as documented by the service (constants OK; full mirror/failover policy remains OPEN).
- At least one targeted lookup by object name/identifier.
- Results compose base fields (`success?`, `status`, `body`, `error_kind`, `metadata`) plus retained identity/astrometry fields when present.
- Specs under `galaxy_game/spec/services/astronomy/simbad_client_spec.rb` with stubs.

### Out of scope
- Reimplementing the base TAP client
- NASA Exoplanet Archive client
- Cache, provenance schema, matching thresholds
- StarSim/mapping consumer wiring
- Bulk catalog download; live network tests

---

## Files Involved

### Primary Files — you will create/edit these
| File | Purpose |
|---|---|
| `galaxy_game/app/services/astronomy/simbad_client.rb` | SIMBAD targeted lookups |
| `galaxy_game/spec/services/astronomy/simbad_client_spec.rb` | Stubbed specs |

Optional documented helper under `galaxy_game/app/services/astronomy/` only if needed for result shaping.

### Reference Files — read but do not edit
| File | Why |
|---|---|
| `galaxy_game/app/services/astronomy/base_tap_client.rb` | Transport + error taxonomy |
| `galaxy_game/docs/wiki/astronomy/system_blueprints.md` | Owner constraints |
| `galaxy_game/spec/services/astronomy/base_tap_client_spec.rb` | Spec conventions from Task 1 |

### Migration
- [x] No migration needed

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then Stage 1 synthesis.  
> After Stage 2 investigation, STOP and obtain human approval of the proposed approach before editing application files.

### Step 0 — Move task to active/
Follow the Agent Dispatch Interface Step 0 exactly.

### Step 1 — Stage 2 investigation
Confirm base client path and public API; propose SIMBAD methods and ADQL shape; confirm no live-network plan. Record in synthesis. **STOP for human approval before Step 2.**

### Step 2 — Implement SIMBAD client
Targeted lookup only; on HTTP 2xx with zero rows, return successful transport with empty/no-match (unknown)—do not set transport error_kind; retain identity/astrometry fields when present; delegate HTTP to base client.

### Step 3 — Specs
Stub base client or HTTP boundary. Cover: success with identity fields; HTTP 2xx empty/no-match (still success, not unavailable); at least one transport failure path via base taxonomy. Zero live SIMBAD calls.

### Step 4 — Verify
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/astronomy/simbad_client_spec.rb 2>&1 | tail -40'
```
If TTY error, drop `-it`. Expected: 0 failures. Record counts. `git status --short` limited to Files Involved (optional astronomy helper permitted).

### Step 5 — Synthesis Stage 3; Review Gate
Do not commit.

---

## Acceptance Criteria

- [ ] SIMBAD client exists under `galaxy_game/app/services/astronomy/` and uses the Task 1 base client for transport.
- [ ] Targeted name/identifier lookup (not bulk).
- [ ] HTTP 2xx with zero rows ⇒ successful transport + empty/no-match (unknown)—not transport `unavailable`, and not definitive non-existence.
- [ ] When present, retain primary id, aliases, and coordinate/astrometry fields (including frame/epoch/uncertainty if returned)—no invented schema.
- [ ] Specs stub transport or base client; cover success, empty, and failure; zero live network.
- [ ] Specs pass under the exact docker rspec command above (or without `-it` if required).
- [ ] `git status --short` evidence shows only Files Involved (+ optional astronomy helper); no star_sim/mapping/lookup edits.
- [ ] No cache, provenance schema, or DB migration.
- [ ] Synthesis report saved; no agent commit/push.

---

## Stop Conditions — escalate to user immediately if:

- Base client missing and cannot proceed without reimplementing it
- Specs outside this task fail because of the change
- Same failure after two fix attempts
- Gemfile change required
- Migration or architectural decision (schema, cache, matching, consumer wiring) required
- Files outside Files Involved need changes (optional documented result helper under astronomy/ is permitted)
- Live network required for specs to pass
- StarSim or generator-spec task files appear to need edits

---

## Commit Instructions

> PRE-COMMIT: Reviewer GO + explicit human authorization for commit; separate message for push. Host-only git. Never `git add .`.

```bash
git add galaxy_game/app/services/astronomy/simbad_client.rb         galaxy_game/spec/services/astronomy/simbad_client_spec.rb
git commit -m "feature: astronomy SIMBAD client — targeted identity lookup, stubbed specs"
```

Task completion move only when human directs (agent-tasks active → completed).

---

## Documentation

Brief class comments only. No new provenance/cache architecture docs.

---

## Dependencies

- **Blocked by**: `2026-11-25-HIGH-FEATURE-ASTRONOMY-SHARED-TAP-BASE-CLIENT` present in codebase.
- **Blocks**: Optional later consumer integration (not this task).
- **Independent of**: NASA client draft (after base exists, either order is fine); StarSim generator-spec drafts.

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

Record steps, paths, rspec results, reviewer/human auth, confirmation of no live network / no schema / no StarSim wiring.

---

## Handoff Summary

- **Done**: SIMBAD targeted client + stubbed specs on shared base.
- **Not done**: NASA client, consumer wiring, schema, cache.
- **Next**: Human may review/dispatch NASA client draft after base exists; integration boundaries doc remains separate.
