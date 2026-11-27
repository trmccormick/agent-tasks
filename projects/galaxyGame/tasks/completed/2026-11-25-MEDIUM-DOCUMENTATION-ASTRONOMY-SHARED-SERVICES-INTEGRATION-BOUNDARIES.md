---
status: completed
resume: false
priority: MEDIUM
type: documentation
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
Task: <agent-tasks>/projects/galaxyGame/tasks/backlog/current/2026-11-25-MEDIUM-DOCUMENTATION-ASTRONOMY-SHARED-SERVICES-INTEGRATION-BOUNDARIES.md

STEP 0 — VERIFY, STAGE IF NEEDED, THEN MOVE WITH GIT MV:
  Run from the agent-tasks repository root.

  1. Inspect the source Git state:
       git status --short -- projects/galaxyGame/tasks/backlog/current/2026-11-25-MEDIUM-DOCUMENTATION-ASTRONOMY-SHARED-SERVICES-INTEGRATION-BOUNDARIES.md
       git ls-files --error-unmatch projects/galaxyGame/tasks/backlog/current/2026-11-25-MEDIUM-DOCUMENTATION-ASTRONOMY-SHARED-SERVICES-INTEGRATION-BOUNDARIES.md
     If the source is not tracked, stage the source first:
       git add projects/galaxyGame/tasks/backlog/current/2026-11-25-MEDIUM-DOCUMENTATION-ASTRONOMY-SHARED-SERVICES-INTEGRATION-BOUNDARIES.md
     Preserve all pre-existing changes; do not discard or overwrite them.

  2. Move the task:
       git mv projects/galaxyGame/tasks/backlog/current/2026-11-25-MEDIUM-DOCUMENTATION-ASTRONOMY-SHARED-SERVICES-INTEGRATION-BOUNDARIES.md \
              projects/galaxyGame/tasks/active/2026-11-25-MEDIUM-DOCUMENTATION-ASTRONOMY-SHARED-SERVICES-INTEGRATION-BOUNDARIES.md

  3. Verify the move:
       git status --short
       git ls-files --error-unmatch projects/galaxyGame/tasks/active/2026-11-25-MEDIUM-DOCUMENTATION-ASTRONOMY-SHARED-SERVICES-INTEGRATION-BOUNDARIES.md
       test ! -e projects/galaxyGame/tasks/backlog/current/2026-11-25-MEDIUM-DOCUMENTATION-ASTRONOMY-SHARED-SERVICES-INTEGRATION-BOUNDARIES.md && test -f projects/galaxyGame/tasks/active/2026-11-25-MEDIUM-DOCUMENTATION-ASTRONOMY-SHARED-SERVICES-INTEGRATION-BOUNDARIES.md
     Confirm the source is absent, destination exists, and Git tracks the destination.

  4. Open the active task file and change YAML status from backlog to active. Verify the updated status.

  5. Record Git-state inspection, any staging command, git mv, verification outputs, and YAML status change in the synthesis report. Do not begin investigation or implementation until Step 0 and the initial synthesis report are complete.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when moving the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv.
  - New/untracked file: git add the source first, then git mv the source to the destination.
  - Never use plain mv or cp for task lifecycle moves.
  - Never leave stale copies in the source folder.
  - Verify with: find agent-tasks/projects/galaxyGame/tasks -name "2026-11-25-MEDIUM-DOCUMENTATION-ASTRONOMY-SHARED-SERVICES-INTEGRATION-BOUNDARIES.md"
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

# TASK: Astronomy Shared Services — Integration Boundaries Doc
**Status**: DRAFT  
**Priority**: MEDIUM  
**Type**: documentation  
**Created**: 2026-11-25  
**Last Updated**: 2026-11-25  
**Author**: Grok (revised after Gemini + Perplexity feedback on the shared series)

Documents how StarSim and mapping should consume shared SIMBAD and NASA Exoplanet Archive clients, what can integrate now vs what waits on OPEN decisions, and stop boundaries. Does **not** invent provenance schema or implement consumer wiring.

Prefer after Task 1 (and ideally 2–3) exist so paths/class names are accurate; if clients are absent, mark planned vs implemented.

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)  
**Why This Agent**: Documentation-only.  
**Local attempts before cloud**: N/A  
**Supervision Level**: standard  

---

## Prerequisites — READ FIRST (Sequential Order)

1. Dispatch Interface READ FIRST list.
2. `<agent-tasks>/README.md` (EXECUTOR Role)
3. This task file
4. `docs/wiki/astronomy/system_blueprints.md`
5. `galaxy_game/app/services/star_sim/system_generator_service.rb` and `galaxy_game/app/services/lookup/star_system_lookup_service.rb` (read-only)
6. Any files under `galaxy_game/app/services/astronomy/` if present

---

## Context

Owner direction requires shared services, failure-tolerant enrichment, and clear separation of observed vs generated data. OPEN: provenance schema, cache/mirror/refresh, matching thresholds. This doc is the handoff contract so later integration does not guess.

---

## Critical Information for This Task

### Credentials
None.

### Architecture Gotchas

⚠️ **GOTCHA 1**: Do not invent the provenance schema, cache design, or matching thresholds in the doc — only record that they remain OPEN.  

⚠️ **GOTCHA 2**: Documentation only — no StarSim/mapping code changes beyond optional additive wiki cross-link.  

⚠️ **GOTCHA 3**: `system_blueprints.md` owner sections take precedence over NEEDS_REVIEW or older task docs.  

⚠️ **GOTCHA 4**: StarSim generator-spec drafts — do not edit or treat as approved dispatch work.  

---

## 🔴 REQUIRED: Status Synthesis Report (Before Investigation or Implementation)

After Step 0, create the synthesis report under `projects/galaxyGame/summaries/`. Filename pattern: `YYYY-MM-DD-DOCUMENTATION-ASTRONOMY-INTEGRATION-BOUNDARIES.md`.

```markdown
## STATUS SYNTHESIS REPORT

**Task**: 2026-11-25-MEDIUM-DOCUMENTATION-ASTRONOMY-SHARED-SERVICES-INTEGRATION-BOUNDARIES
**Status**: [backlog → active → completed]
**Date**: YYYY-MM-DD

### Stage 1 — Initial understanding and Step 0
[Step 0 git evidence, objective, documentation-only boundaries]

### Stage 2 — Investigation findings (complete before writing the doc)
[Inventory of galaxy_game/app/services/astronomy/ if present; proposed doc path; outline of six required sections.
After Stage 2, STOP and obtain human approval of the proposed approach before creating/editing the doc file.]

### Stage 3 — Implementation and verification
[Doc path written, section headings present, no application Ruby changes, optional blueprints link if any]

### Expected Outcomes
One boundaries markdown file; OPEN items not invented; no consumer code
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

Without an explicit boundaries doc, later agents may wire clients early, invent schema, or treat provider gaps as hard facts.

**Current behavior**: Architecture intent lives in system_blueprints; no dedicated integration-boundaries page for the shared clients.  
**Expected behavior**: One focused markdown doc under GalaxyGame docs with the six content items below.

---

## Scope

### In scope
- Create **one** of:
  - `docs/wiki/astronomy/shared_services_integration_boundaries.md` (preferred), or
  - `galaxy_game/docs/architecture/astronomy/...` only if that tree is already the project norm — choose one; do not duplicate.
- Content:
  1. Service inventory (base TAP, SIMBAD, NASA EA) and intended consumers (StarSim, mapping/Galaxy View)
  2. Safe now (optional enrichment, full failure tolerance) vs must wait (schema, matching, persistent cache policy)
  3. Failure semantics: timeout/unavailable → generation continues; empty → unknown, not absence
  4. Observed vs generated separation
  5. Non-goals: bulk ingestion, per-consumer clients, coupling to StarSim SPEC drafts
  6. Pointers to OPEN items in system_blueprints
- Optional additive “see also” link from system_blueprints only — no rewrite of owner-confirmed sections

### Out of scope
- Implementing consumer code
- Designing/implementing provenance schema or cache
- Editing StarSim SPEC draft tasks
- Application Ruby changes

---

## Files Involved

### Primary Files
| File | Purpose |
|---|---|
| `docs/wiki/astronomy/shared_services_integration_boundaries.md` (or chosen architecture path) | New boundaries doc |

### Reference Files
| File | Why |
|---|---|
| `docs/wiki/astronomy/system_blueprints.md` | Authority + optional one-line cross-link |
| `galaxy_game/app/services/astronomy/*` | Implemented vs planned naming |
| `galaxy_game/app/services/star_sim/system_generator_service.rb` | Consumer entry context |

### Migration
- [x] No migration needed

---

## Implementation Steps

> Step 0; Stage 1 synthesis; Stage 2 investigation (what clients exist); **STOP for human approval** of doc path and outline before writing the file.

### Step 0 — Dispatch Step 0

### Step 1 — Stage 2
Inventory astronomy services; propose exact doc path and outline. **STOP for human approval.**

### Step 2 — Write the doc (six sections)

### Step 3 — Optional additive cross-link only

### Step 4 — Verify by inspection (no RSpec required). Record path and section headings in synthesis.

### Step 5 — Review Gate; no commit without dual authorization

---

## Acceptance Criteria

- [ ] One new markdown file under GalaxyGame `docs/` at the chosen path; path in synthesis.
- [ ] All six Scope content items present.
- [ ] Doc does **not** propose final provenance schema, cache design, or matching thresholds.
- [ ] Doc does not instruct same-change-set consumer wiring as client tasks.
- [ ] No application Ruby changes; no StarSim SPEC task edits.
- [ ] Optional blueprints link is additive only.
- [ ] Synthesis saved; no agent commit/push.

---

## Stop Conditions — escalate to user immediately if:

- Pressure to invent schema/cache/matching in the doc
- Application code changes seem required
- Need to rewrite large owner-confirmed blueprint sections
- Unclear which docs path is canonical and human has not decided

---

## Commit Instructions

> Reviewer GO + explicit human commit auth; separate push auth. Host-only git. Add only the doc file(s) touched.

```bash
git add docs/wiki/astronomy/shared_services_integration_boundaries.md
git commit -m "docs: astronomy shared services integration boundaries"
```

---

## Documentation

This task *is* documentation. No additional meta-doc required.

---

## Dependencies

- **Preferred after**: Task 1 (and ideally 2–3) in codebase for accurate names.
- **Can proceed** with planned vs implemented labels if clients missing.
- **Independent of**: StarSim generator-spec drafts.

---

## Progress / Resume

```
resume: false
last_completed_step: none
next_step: Step 0 when dispatched
notes: Revised after series review feedback.
```

---

## Completion Report

Path chosen, sections written, whether clients were implemented or planned, auth evidence.

---

## Handoff Summary

- **Done**: Integration boundaries doc for shared astronomy services.
- **Not done**: Schema decision, cache policy, consumer implementation, matching thresholds.
- **Next**: Human-driven design tasks for OPEN items when prioritized.
