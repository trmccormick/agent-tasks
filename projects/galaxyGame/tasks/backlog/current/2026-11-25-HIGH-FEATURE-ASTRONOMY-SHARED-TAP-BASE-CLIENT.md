---
status: draft
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
Task: <agent-tasks>/projects/galaxyGame/tasks/backlog/current/2026-11-25-HIGH-FEATURE-ASTRONOMY-SHARED-TAP-BASE-CLIENT.md

STEP 0 — VERIFY, STAGE IF NEEDED, THEN MOVE WITH GIT MV:
  Run from the agent-tasks repository root.

  1. Inspect the source Git state:
       git status --short -- projects/galaxyGame/tasks/backlog/current/2026-11-25-HIGH-FEATURE-ASTRONOMY-SHARED-TAP-BASE-CLIENT.md
       git ls-files --error-unmatch projects/galaxyGame/tasks/backlog/current/2026-11-25-HIGH-FEATURE-ASTRONOMY-SHARED-TAP-BASE-CLIENT.md
     If the source is not tracked, stage the source first:
       git add projects/galaxyGame/tasks/backlog/current/2026-11-25-HIGH-FEATURE-ASTRONOMY-SHARED-TAP-BASE-CLIENT.md
     Preserve all pre-existing changes; do not discard or overwrite them.

  2. Move the task:
       git mv projects/galaxyGame/tasks/backlog/current/2026-11-25-HIGH-FEATURE-ASTRONOMY-SHARED-TAP-BASE-CLIENT.md \
              projects/galaxyGame/tasks/active/2026-11-25-HIGH-FEATURE-ASTRONOMY-SHARED-TAP-BASE-CLIENT.md

  3. Verify the move:
       git status --short
       git ls-files --error-unmatch projects/galaxyGame/tasks/active/2026-11-25-HIGH-FEATURE-ASTRONOMY-SHARED-TAP-BASE-CLIENT.md
       test ! -e projects/galaxyGame/tasks/backlog/current/2026-11-25-HIGH-FEATURE-ASTRONOMY-SHARED-TAP-BASE-CLIENT.md && test -f projects/galaxyGame/tasks/active/2026-11-25-HIGH-FEATURE-ASTRONOMY-SHARED-TAP-BASE-CLIENT.md
     Confirm the source is absent, destination exists, and Git tracks the destination.

  4. Open the active task file and change YAML status from backlog to active. Verify the updated status.

  5. Record Git-state inspection, any staging command, git mv, verification outputs, and YAML status change in the synthesis report. Do not begin investigation or implementation until Step 0 and the initial synthesis report are complete.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when moving the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv.
  - New/untracked file: git add the source first, then git mv the source to the destination.
  - Never use plain mv or cp for task lifecycle moves.
  - Never leave stale copies in the source folder.
  - Verify with: find agent-tasks/projects/galaxyGame/tasks -name "2026-11-25-HIGH-FEATURE-ASTRONOMY-SHARED-TAP-BASE-CLIENT.md"
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

**Current State**: In `tasks/drafts/` committed to git  
**Next Step**: Review agent assessment → Human approval → Move to `tasks/backlog/` by human  
**Do NOT dispatch to implementation agents** until this task is in `tasks/backlog/` with `status: backlog`

See PLANNING_AGENT_SESSION_START.md § "Task Creation Workflow" for full procedure.

## 🟡 DRAFT REVIEW CHECKLIST (Human — before approving move from drafts/ to backlog/)

**APPLIES ONLY IF status: draft above. Use this checklist BEFORE moving task to tasks/backlog/.**

After review agent has returned assessment:

- [ ] Acceptance Criteria are clear and measurable (no ambiguity)
- [ ] Task scope is well-bounded (not too broad, not overlapping with active tasks)
- [ ] Dependencies listed and verified against status.md (all prerequisites exist)
- [ ] No contradictions in the task specification
- [ ] Architecture aligns with current codebase patterns (review agent verified)
- [ ] Review agent disposition is GO (not REVISE or STOP)

**Once ALL boxes checked:** Approve by moving task from `tasks/drafts/` to `tasks/backlog/current/` and changing `status: draft → status: backlog`.

---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching to implementation)

**APPLIES ONLY IF status: backlog. STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section above is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**


---

# TASK: Astronomy Shared TAP Base Client
**Status**: DRAFT  
**Priority**: HIGH  
**Type**: feature  
**Created**: 2026-11-25  
**Last Updated**: 2026-11-25  
**Author**: Grok (draft for human review; investigation-based)

Part 1 of a planned shared astronomy client series (SIMBAD and NASA Exoplanet Archive clients are separate draft tasks that depend on this base). This part is independently dispatchable. This part adds only the shared HTTP/TAP base client, error taxonomy, and result shell. Provider-specific clients are separate tasks. Do not invent a persistent provenance schema or wire StarSim/mapping consumers in this task.

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)  
**Why This Agent**: Bounded new service layer with explicit file list, stubbed HTTP tests, no schema invention.  
**Local attempts before cloud**: N/A  
**Supervision Level**: watched carefully  

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Startup order**: follow the READ FIRST list in the Agent Dispatch Interface above (GUARDRAILS, project README, SESSION_GUIDANCE, then this task file). That list is the mandatory startup contract and takes precedence over any other order.
2. **Also read**, after those files: `<agent-tasks>/README.md` (EXECUTOR Role section)
3. **This Task File**: Everything below
4. **Owner architecture (required)**: In the GalaxyGame repo, read in full:  
   `docs/wiki/astronomy/system_blueprints.md` — especially sections on main-game astronomical data integrations (SIMBAD / NASA Exoplanet Archive), real-star matching, and OPEN decisions (provenance schema, cache, matching thresholds).
5. **After initial synthesis report (Stage 2 investigation)**: Confirm whether `app/services/astronomy/` already exists and whether any SIMBAD/NASA Exoplanet Archive client code is present. Do not run investigation commands before the Stage 1 synthesis report.

> Agent MUST read in this order. Do not skip. The synthesis report is saved as an MD file BEFORE starting work.

---

## Context

GalaxyGame needs reusable main-game astronomy-data services shared by StarSim and mapping. Owner-confirmed direction (2026-11-24) requires SIMBAD and NASA Exoplanet Archive integrations via targeted TAP lookups—not bulk ingestion, not per-consumer ad-hoc clients. Remote outages must not hard-fail generation; missing provider records must not be treated as proof of absence; source identity and provenance-relevant fields must be retained for a future schema decision.

This task creates only the **shared TAP HTTP base** under `app/services/astronomy/`. It does not implement SIMBAD or NASA queries, does not change StarSim or mapping, and does not invent cache or provenance persistence.

**Relevant Architecture Docs** — read before starting:
- `docs/wiki/astronomy/system_blueprints.md` (GalaxyGame) — owner-confirmed integration scope
- `config/initializers/game_data_paths.rb` — path constants pattern (do not invent new Paths for remote APIs unless required)
- `app/services/base_service.rb` — minimal `self.call` convention (optional parent; do not force inheritance if it does not fit)

---

## Critical Information for This Task

### Credentials (if needed)
None. SIMBAD and NASA Exoplanet Archive TAP endpoints are public; no API keys.

### Architecture Gotchas

⚠️ **GOTCHA 1**: Schema and matching are OPEN — do not invent them  
- ❌ Wrong: Introduce a permanent per-property provenance model, DB tables, or matching confidence thresholds in this task.  
- ✅ Right: Return structured results that **preserve** provider fields (identifiers, aliases, frames, epochs, uncertainties, measurement context when present) without normalizing them into a final schema.  
- Why: Owner-confirmed stop boundary. Schema needs a separate design decision.

⚠️ **GOTCHA 2**: Do not wire consumers yet  
- ❌ Wrong: Call the new client from `SystemGeneratorService`, `ProceduralGenerator`, mapping, or scouting.  
- ✅ Right: Implement only `app/services/astronomy/` base client + specs. Integration is a later task.  
- Why: Keeps this slice independently verifiable and avoids irreversible consumer coupling before schema decisions.

⚠️ **GOTCHA 3**: Tests must not depend on live network  
- ❌ Wrong: Specs that hit real SIMBAD or NASA endpoints.  
- ✅ Right: Stub HTTP (e.g. stub `Net::HTTP` / client method) with fixture response bodies; assert timeout and error paths without live calls.  
- Why: GUARDRAILS and owner requirement: tests do not depend on live external API availability.

⚠️ **GOTCHA 4**: Targeted lookups only; no bulk ingestion  
- ❌ Wrong: Download entire catalogs or open-ended table dumps.  
- ✅ Right: Base client supports parameterized query strings suitable for object-by-name / host-by-name style TAP queries used by later clients.  
- Why: Owner-confirmed architecture.


⚠️ **GOTCHA 6**: Conservative request behavior ≠ inventing cache policy  
- Document a reasonable default timeout in the client. Do **not** implement a persistent cache, refresh cadence, or rate-limit registry in this task—those remain OPEN per `system_blueprints.md`. A simple in-process timeout and structured error return is sufficient.

⚠️ **GOTCHA 5**: Do not touch StarSim generator-spec draft tasks  
- The four `2026-11-22-HIGH-BUG-FIX-STARSIM-SPEC-*` drafts and related generator-spec work remain independent. Do not move, edit, or implement them.

---

## 🔴 REQUIRED: Status Synthesis Report (Before Investigation or Implementation)

After completing the dispatch Step 0 move and YAML status update, but before investigation commands or application-file changes, create the initial **synthesis report** as a Markdown file. Step 0 Git commands are the only commands permitted before this initial report. Update the same report after investigation and before implementation, then again after implementation and verification.

**Synthesis Report Template** (save as MD file; do not paste the report or raw command output into chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: 2026-11-25-HIGH-FEATURE-ASTRONOMY-SHARED-TAP-BASE-CLIENT
**Status**: [backlog → active → completed]
**Date**: YYYY-MM-DD

### Stage 1 — Initial understanding and Step 0
[Record exact Step 0 Git commands/results, source/destination verification, YAML status, task objective, boundaries, unknowns, and planned evidence.]

### Stage 2 — Investigation findings (complete before implementation)
[Record verified paths, absence/presence of astronomy clients, BaseService patterns, HTTP usage in repo, proposed file list, test plan. After Stage 2, STOP and obtain human approval of the proposed approach before editing application files.]

### Stage 3 — Implementation and verification
[After implementation, append actual changes, exact test commands/results, acceptance disposition, remaining issues.]

### What I'm About to Do
[2-3 sentences]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `docs/wiki/astronomy/system_blueprints.md` | Owner architecture | |
| `app/services/base_service.rb` | Existing service convention | |
| `Gemfile` | net-http availability | |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read GUARDRAILS, project README, SESSION_GUIDANCE, this task file
- ✅ Read system_blueprints astronomy sections

### Expected Outcomes
[Base client + error types + stubbed specs; no consumer wiring]

### Critical Gotchas I Will Avoid
- ❌ Inventing provenance schema — instead ✅ preserve fields only
- ❌ Live network in tests — instead ✅ stubs/fixtures
- ❌ Wiring StarSim/mapping — instead ✅ clients only
```

In chat, provide only the report path and a concise status update.

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

> **This checkpoint is NOT optional.** It applies to all implementation tasks regardless of size or perceived risk. The reviewer does not commit directly; only the human authorizes commits.

---

## Problem Statement

There is no shared astronomy TAP client layer. StarSim and mapping obtain system data only from local JSON/seeds. Owner direction requires reusable SIMBAD and NASA Exoplanet Archive integrations with resilient, targeted TAP access. A shared base client is the first independent building block.

**Current behavior**: No `app/services/astronomy/` client layer; no shared TAP transport for SIMBAD or NASA Exoplanet Archive.

**Expected behavior**: A reusable base TAP client under `app/services/astronomy/` with structured success/failure results, configurable base URL and timeout, parameterized GET, and stubbed RSpec coverage—without consumer wiring, cache, or provenance schema.

---

## Scope

### In scope
- Create `galaxy_game/app/services/astronomy/` namespace (paths relative to GalaxyGame repo root; inside the `web` container the app root is already the working directory, so RSpec paths are `spec/services/astronomy/...`).
- Implement a base TAP client (e.g. `Astronomy::BaseTapClient`) using `net-http` if available (or the project’s existing HTTP approach)—configurable base URL, timeout, GET with query params, response body return.
- Explicit error/result taxonomy (deterministic mapping below).
- Lightweight result with minimum fields: `success?` (or equivalent), `status` (HTTP status or nil), `body` (raw response body or nil), `error_kind` (nil on success), `metadata` (Hash, default empty)—without defining a permanent provenance schema. ParseError is **deferred** to provider clients; this task does not require ParseError handling.
- Focused RSpec under `spec/services/astronomy/` with HTTP stubbed at the transport boundary (`Net::HTTP` or project stub gem)—not by stubbing the class under test so that nothing is exercised.
- Keep provider-specific ADQL/query construction out of this task.

### Error classification (deterministic)

| Condition | `error_kind` |
|---|---|
| Open/read timeout | `timeout` |
| Connection refused, DNS, TLS, socket errors | `unavailable` |
| HTTP 5xx | `unavailable` |
| Other non-2xx including 3xx and 4xx (including 429) | `unsuccessful` (preserve HTTP status on the result) |
| HTTP 2xx | success (`error_kind` nil); retain body |

- Do **not** invent a rate-limit policy for 429; classify as `unsuccessful` and keep status.
- Whether redirects are followed: use `Net::HTTP` default behavior for this task; document the choice in the synthesis report. Do not build custom redirect policy.
- On non-success HTTP responses, retain status and body when available (TAP error detail is often in the body).
- Transport failures return structured results; uncaught transport exceptions must not escape to callers. Invalid programmer arguments (e.g. nil base URL if you choose to validate) may still raise—document that in synthesis.

### Out of scope
- SIMBAD or NASA Exoplanet Archive query methods.
- Cache, mirror policy, refresh cadence, DB models.
- Provenance/normalization schema.
- Any changes to StarSim, `Lookup::StarSystemLookupService`, mapping, wormhole scouting, or generators.
- Live network tests.
- Commits without human authorization.
- Adding a new Gemfile dependency unless a Stop Condition is hit and the human approves.

---

## Files Involved

### Primary Files — you will create/edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/app/services/astronomy/base_tap_client.rb` | Shared TAP HTTP client | query/execute entry point |
| `galaxy_game/spec/services/astronomy/base_tap_client_spec.rb` | Stubbed unit specs | success + each failure kind |

Optional: a small result value object under `galaxy_game/app/services/astronomy/` if a bare Hash is insufficient—document the choice.

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/docs/wiki/astronomy/system_blueprints.md` | Owner-confirmed integration scope and OPEN decisions |
| `galaxy_game/app/services/base_service.rb` | Existing minimal service convention (do not force inheritance) |
| `galaxy_game/config/initializers/game_data_paths.rb` | Path constant patterns (do not invent remote Paths unless required) |
| `galaxy_game/Gemfile` | Confirm `net-http` / stubbing availability |
| An existing small `spec/services/**/*_spec.rb` | Local RSpec/service spec conventions |

### Migration
- [x] No migration needed
- [ ] Migration needed: (not expected for this task)

**If migration needed: follow GUARDRAILS Rule 2 before proceeding — and treat as a Stop Condition.**

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and save your STATUS SYNTHESIS REPORT (Stage 1).
> Do not proceed to investigation or implementation until Step 0 and Stage 1 are done.
> Obtain human approval of the proposed approach after Stage 2 investigation and before implementation edits.

- Do not skip steps or reorder them.
- All RSpec commands use the Docker wrapper. Container working directory is already `/home/galaxy_game` — do not `cd`. Never fabricate results.
- If Docker reports the input device is not a TTY, rerun without `-it` and record that in the synthesis report.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

Follow Step 0 of the Agent Dispatch Interface exactly. Record the outputs in the synthesis report.

### Step 1 — Stage 2 investigation (after Stage 1 synthesis report)

Confirm baseline and patterns; record in synthesis:

- Whether `app/services/astronomy/` exists; any existing SIMBAD/NASA client code.
- How `BaseService` is used; whether `net-http` is in the Gemfile; whether WebMock/stub patterns exist in specs.
- Proposed class name and file list (must match Files Involved primary list unless you document a justified rename).

After Stage 2, STOP and obtain human approval of the proposed approach before editing application files. Record the proposal in Stage 2 of the synthesis report; do not implement until the human approves.

### Step 2 — Implement base client

Add `galaxy_game/app/services/astronomy/base_tap_client.rb` (or documented equivalent name):

- Configurable base URL and timeout (document default timeout as a constant or comment).
- Parameterized GET with query params.
- Apply the error classification table in Scope.
- Return structured results with minimum fields listed in Scope.
- No uncaught transport exceptions escaping to callers.

### Step 3 — Specs

Add `galaxy_game/spec/services/astronomy/base_tap_client_spec.rb`:

- Stub at the HTTP boundary (`Net::HTTP` or project stub library)—do not stub the class under test in a way that proves nothing.
- Required examples: (1) success 2xx with body, (2) timeout → `timeout`, (3) connection-style failure → `unavailable`, (4) 5xx → `unavailable`, (5) 4xx (e.g. 400 or 429) → `unsuccessful` with status preserved.
- Optional but preferred: query params encoded/sent; empty-body 2xx success.
- Zero live network calls.

### Step 4 — Verify

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/astronomy/base_tap_client_spec.rb 2>&1 | tail -40'
```

If TTY error, drop `-it` and note it.

**Expected result**: all examples pass, 0 failures (exact example count depends on how many examples you added; record the count in the synthesis report).

Also record `git status --short` / diff limited to Files Involved primary paths—no StarSim, mapping, or lookup service files.

### Step 5 — Synthesis Report (before any commit)

Update Stage 3 of the synthesis report with paths changed, test command, example counts, exit status, and confirmation that no commit was made. Then stop at the Review Gate.

---

## Acceptance Criteria

- [ ] Directory `galaxy_game/app/services/astronomy/` exists.
- [ ] Base TAP client class exists under that namespace with configurable base URL and timeout (default documented as constant or comment).
- [ ] Client performs parameterized GET and returns a structured result with at least: success indicator, `status`, `body`, `error_kind`, `metadata` (default empty)—not a bare transport exception for timeout/connection/HTTP failures.
- [ ] Error classification matches the Scope table (timeout / unavailable / unsuccessful), including 429 as `unsuccessful` with status preserved.
- [ ] Specs stub HTTP at the transport boundary and cover: success, timeout, unavailable (connection or 5xx), unsuccessful (4xx). Zero live network calls.
- [ ] Specs pass under:
  ```bash
  docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/astronomy/base_tap_client_spec.rb 2>&1 | tail -40'
  ```
  (or the same without `-it` if required; recorded in synthesis).
- [ ] `git diff` / status evidence in the synthesis report shows only Files Involved primary paths (plus optional result helper under `astronomy/`); no `star_sim/`, mapping, or lookup service edits; no generator-spec task edits.
- [ ] No DB migration, no cache store, no persistent provenance/JSONB schema introduced.
- [ ] Synthesis report saved under `projects/galaxyGame/summaries/` with Step 0 evidence and exact test commands/results.
- [ ] No commit or push performed by the agent.

---

## Stop Conditions — escalate to user immediately if:

- Specs you did not touch start failing because of this change
- Same failure persists after two fix attempts
- Root cause requires editing a shared base class used widely outside astronomy (beyond optional use of `BaseService` patterns)
- A database migration is needed
- Gemfile change is required to add a stub library or HTTP client and was not anticipated
- Any architectural decision is required (provenance schema, cache policy, matching thresholds, consumer wiring)
- Fix requires changing files outside Files Involved (the optional documented result helper under astronomy/ is permitted)
- StarSim, mapping, or generator-spec task files appear to need edits
- Live network access seems required for the specs to pass

---

## Commit Instructions

> ⚠️ **PRE-COMMIT CHECK**: Before running ANY of the commands below, confirm:
> - [ ] Reviewer has issued GO disposition (not just pending)
> - [ ] Human has explicitly authorized the command I am about to run (commit or push) in a message that names it
> - [ ] Both conditions documented in your completion report
>
> If either condition is unmet, DO NOT proceed. Wait for the missing authorization.

Run git commands on **host only** — never inside the Docker container. Commit only the GalaxyGame application/spec files for this task (and agent-tasks task-file lifecycle moves when human directs). Never `git add .`.

```bash
# Example — only after dual authorization; adjust paths to actual files touched:
git add galaxy_game/app/services/astronomy/base_tap_client.rb \
        galaxy_game/spec/services/astronomy/base_tap_client_spec.rb
git commit -m "feature: astronomy shared TAP base client — structured results, stubbed specs"
```

Push only after a separate human message authorizing push.

**Task file move on completion** (only when human directs completion):

```bash
# From agent-tasks root; tracked file:
git mv projects/galaxyGame/tasks/active/2026-11-25-HIGH-FEATURE-ASTRONOMY-SHARED-TAP-BASE-CLIENT.md \
       projects/galaxyGame/tasks/completed/2026-11-25-HIGH-FEATURE-ASTRONOMY-SHARED-TAP-BASE-CLIENT.md
```

Update YAML status to `completed` when the human directs that move.

---

## Documentation

- No new wiki page is required for this task.
- Code-level comments: document default timeout and error classification briefly on the client class.
- Do not invent architecture docs for provenance/cache in this task.
- Flag in the completion report if `system_blueprints.md` should later cross-link the implemented client (human/doc task).

---

## Dependencies

- **Blocked by**: None for this base client.
- **Blocks**: Draft tasks `2026-11-25-HIGH-FEATURE-ASTRONOMY-SIMBAD-CLIENT.md` and `2026-11-25-HIGH-FEATURE-ASTRONOMY-NASA-EXOPLANET-ARCHIVE-CLIENT.md` (still in drafts/; not approved for dispatch with this task).
- **Related but independent**: StarSim generator-spec drafts (do not touch); future provenance schema design; real-star matching design; optional later documentation/integration-boundaries task.

---

## Progress / Resume

```
resume: false
last_completed_step: none
next_step: Step 0 when dispatched
notes: New draft; no prior implementation progress.
```

If this task is later marked `resume: true`, the agent must continue from `next_step` and not restart from scratch.

---

## Completion Report

When work is finished (after verification, before or after commit per human authorization), record:

- Steps completed
- Files created/changed (exact paths)
- Spec command(s) and results (example counts, failures, exit status)
- Reviewer disposition and human authorization messages (quoted/dated)
- Remaining risks or follow-ups (provider client drafts, schema decision)
- Confirmation: no live network in tests; no schema/cache; no StarSim wiring

Save under the synthesis report and/or as directed by SESSION_CLOSEOUT.md.

---

## Handoff Summary

- **Done when complete**: Shared TAP base client + stubbed specs under `app/services/astronomy/`.
- **Not done (by design)**: SIMBAD client, NASA Exoplanet Archive client, consumer integration, provenance schema, cache.
- **Next human choice**: Approve sibling client drafts for review/backlog when ready; do not dispatch them until this base exists in the codebase if they depend on it.
- **Owner OPEN items unchanged**: provenance schema, cache/mirror/refresh, real-star matching thresholds.
