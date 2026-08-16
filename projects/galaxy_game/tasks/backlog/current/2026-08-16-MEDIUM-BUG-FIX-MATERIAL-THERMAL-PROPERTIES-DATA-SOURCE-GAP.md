---
status: backlog
priority: MEDIUM
type: bug-fix
system_domain: OTHER
mvp_alignment: SPEC_HEALTH
local_worker_safe: false
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-16-MEDIUM-BUG-FIX-MATERIAL-THERMAL-PROPERTIES-DATA-SOURCE-GAP.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-16-MEDIUM-BUG-FIX-MATERIAL-THERMAL-PROPERTIES-DATA-SOURCE-GAP.md \
         projects/galaxy_game/tasks/active/2026-08-16-MEDIUM-BUG-FIX-MATERIAL-THERMAL-PROPERTIES-DATA-SOURCE-GAP.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-16-MEDIUM-BUG-FIX-MATERIAL-THERMAL-PROPERTIES-DATA-SOURCE-GAP.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Material Thermal Properties — Data Source Gap (melting_point/boiling_point)
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: bug-fix
**Created**: 2026-08-16
**Last Updated**: 2026-08-16

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*

- **Template Conformance**: PASS
- **Docker Wrapper Check**: N/A — no RSpec commands specified yet, root cause not confirmed
- **MVP Alignment**: VALID — spec health, blocks clean test coverage of Material model
- **MVP Impact Note**: Does not block AI Manager Luna settlement directly; affects test-suite trustworthiness for the Material model and ISRU material data broadly.
- **Action Line**: NEEDS MANUAL REVIEW — root cause not yet confirmed (missing JSON data vs. lookup-chain bug), and fix touches shared code (`MaterialLookupService`)

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Standard investigation/bug-fix work with terminal access requirement
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully — first dispatch of this task, touches shared lookup service

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context

While fixing stale test fixtures (2026-08-13-LOW-FEATURE-FIXTURE-BUNDLE-STALE-MOCKS-GAPS.md, items #2/#3/#4), the implementing agent found that `CelestialBodies::Material#melting_point` and `#boiling_point` do not read from database columns or a `properties` JSON column (no such column exists in `materials` per `db/schema.rb`). They resolve through `MaterialLookupService`, which loads material data from external JSON files under `Rails.root.join(JSON_DATA, "resources", "materials")`, split into subdirectories (building, byproducts, chemicals, gases, liquids, processed, raw). The `materials` factory was setting `melting_point`/`boiling_point` as DB-column-style factory attributes, which the model silently ignores. This was masking as a "stale fixture" problem but is actually a data-source mismatch. The factory was reverted to its original state rather than patched, since patching factory DB columns cannot fix a JSON-file-backed read path.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules, especially shared-code-change escalation rule

> If a doc doesn't exist for this area, do not create one during this task.
> Flag the gap in your completion report instead.

---

## Critical Information for This Task

### Credentials
N/A — no credentials needed for this task.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Don't re-patch the factory with DB-column attributes.
- ❌ Wrong: setting `melting_point:`/`boiling_point:` as raw factory attributes expecting the model to read them from the DB row.
- ✅ Right: confirm the real read path (`MaterialLookupService` → JSON file) first, then fix at that layer — factory should mock/stub the lookup service if that's the correct test-layer approach, not set DB columns the model ignores.
- Why: the model overrides these accessor methods to read from `properties`-equivalent JSON-sourced data, not the DB row — this was already tried and confirmed not to work in the prior session.

⚠️ **GOTCHA 2**: Don't re-run the same search twice.
- ❌ Wrong: re-searching for `base_materials_path` or similar constant/method names without confirming they exist first.
- ✅ Right: read `MaterialLookupService` in full (or at minimum trace `find_material`, referenced earlier around lines 105-130) to find the actual path-resolution logic verbatim, in one pass.
- Why: the prior session repeated an identical unsuccessful search twice without landing an answer — this task was spun off specifically to break that loop with a fresh, focused session.

### Multi-Domain / Multi-Tenant Routing
N/A — not applicable to this task.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Material Thermal Properties — Data Source Gap
**Status**: backlog → active → completed
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
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
[Exact description of what "done" looks like]

### Critical Gotchas I Will Avoid
- ❌ Setting factory DB columns the model ignores — instead ✅ fix at the real read-path layer
- ❌ Re-running identical searches — instead ✅ one full read of MaterialLookupService before acting

---

**SYNTHESIS COMPLETE.** Ready to proceed.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement

`CelestialBodies::Material#melting_point` and `#boiling_point` resolve through `MaterialLookupService`, reading from external material JSON files, not DB columns or a `properties` column (confirmed: no such column exists in `db/schema.rb`). It is not yet confirmed whether (a) the relevant JSON files (e.g. `iron.json`) are missing these fields, or (b) the fields exist in JSON but something in `MaterialLookupService`'s resolution chain or `Material`'s accessor methods fails to surface them correctly. Both possibilities must be checked before choosing a fix.

**Error output**: Not yet captured — prior session did not get a specs run against a resolved root cause. Capture actual RSpec failure output as part of this task's Step 1.

**Current behavior**: Factory-set `melting_point`/`boiling_point` values have no effect on the model's returned values (silently ignored).
**Expected behavior**: `Material#melting_point`/`#boiling_point` return correct real values for materials that have them defined, sourced consistently through whatever the real intended data path is.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/lookup/material_lookup_service.rb` | Resolves material data from JSON files | `#find_material`, ~lines 105-130 (unconfirmed, re-verify) |
| `app/models/celestial_bodies/material.rb` | Model exposing melting_point/boiling_point accessors | ~lines 95-124 (unconfirmed, re-verify) |
| `spec/factories/celestial_bodies/materials.rb` | Factory — needs to align with real data path once found | N/A |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `data/json-data/resources/materials/raw/iron.json` (path unconfirmed) | Check whether thermal properties are present in source data |
| `spec/models/celestial_bodies/material_spec.rb` | Confirms expected behavior |
| `spec/models/concerns/geosphere_concern_spec.rb` | May depend on same accessor chain |
| `spec/models/concerns/material_management_concern_spec.rb` | May depend on same accessor chain |

**[FILL IN — Qwen/implementation agent to confirm all exact paths and line numbers via terminal access before editing. Claude has no filesystem access and cannot verify these directly.]**

### Migration
- [x] No migration needed (confirmed: no `properties` column exists or is expected — data lives in JSON files by design)

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
See Minimal Handoff block above for exact commands.

### Step 1 — Locate the real data-resolution path
Read `MaterialLookupService` in full. Confirm the actual constant/method used to build the JSON file path (do not re-search for `base_materials_path` unless you first confirm it's the real name via a direct read). Identify the exact file path for `iron` and any other materials referenced by the affected specs.

### Step 2 — Determine root cause
Check whether the relevant JSON file(s) contain `melting_point`/`boiling_point` fields. If missing: this is a data gap. If present: trace why `Material`'s accessors or `MaterialLookupService`'s resolution isn't surfacing them — this is a logic bug.

### Step 3 — Apply fix at the correct layer
- If data gap: add correct real-world thermal property values to the JSON file(s) (ground in real reference data, not placeholders).
- If logic bug: fix the resolution chain in `MaterialLookupService` or the model's accessor methods.
- Either way: update `spec/factories/celestial_bodies/materials.rb` to match the real, working path (e.g. stub/mock `MaterialLookupService` in specs, rather than setting DB columns).

### Step 4 — Verify

> CRITICAL EXECUTION MANDATE: All RSpec commands must use the Docker wrapper below.
> The container working directory is already /home/galaxy_game — do NOT add cd /home/galaxy_game.
> Never run bare local test commands. Never fabricate test results. Actually run the specs.

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/models/celestial_bodies/material_spec.rb spec/models/concerns/geosphere_concern_spec.rb spec/models/concerns/material_management_concern_spec.rb spec/factories/celestial_bodies/materials.rb 2>&1 | tail -20'
```

Expected result: all examples passing, 0 failures.

### Step 5 — Synthesis Report (before committing anything)

```
SYNTHESIS REPORT
Spec: [file:line]
Error: [exact message]
Expected: [value]
Got: [value]

ROOT CAUSE
[one paragraph — data gap vs logic bug, stated explicitly]

PROPOSED FIX
[exact code/data change]

RISK
[MaterialLookupService and Material model are shared/broadly-used — confirm scope of impact beyond the specs touched here]

READY TO APPLY? — waiting for approval
```

Do not commit until the user explicitly approves. **This fix touches shared code (`MaterialLookupService`) — mandatory escalation per standing shared-code guardrail, regardless of confidence.**

---

## Acceptance Criteria
- [ ] Root cause identified and stated explicitly: missing JSON data, a resolution bug, or both
- [ ] Fix applied at the correct layer (JSON data, lookup service, or model accessor — not a factory-only patch)
- [ ] Factory updated to match the real, working data-resolution path
- [ ] Isolation run: 0 failures on all affected specs listed above
- [ ] No regressions in related specs
- [ ] Full suite run completed and logged (human runs overnight — agent does not trigger)

---

## Stop Conditions — escalate to user immediately if:
- Fix causes new failures in specs you did not touch
- Same failure persists after two attempts
- Root cause is in a shared concern, base class, or factory used across many specs (this task is already flagged as likely touching shared code — confirm scope, don't assume it's contained)
- A database migration is needed that wasn't anticipated
- Any architectural decision is required
- Fix requires changing more files than the task specifies
- Investigation requires more than ~2 clean passes without landing an answer — this task was spun off from a stuck-loop pattern once already; report findings rather than continuing to search

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add [specific files only — never git add .]
git commit -m "[type]: material thermal properties — [brief description of root cause and fix]"
git push
```

**Task file move on completion:**
```bash
git mv projects/galaxy_game/tasks/active/2026-08-16-MEDIUM-BUG-FIX-MATERIAL-THERMAL-PROPERTIES-DATA-SOURCE-GAP.md \
       projects/galaxy_game/tasks/completed/2026-08/2026-08-16-MEDIUM-BUG-FIX-MATERIAL-THERMAL-PROPERTIES-DATA-SOURCE-GAP.md
git commit -m "chore: move 2026-08-16-MEDIUM-BUG-FIX-MATERIAL-THERMAL-PROPERTIES-DATA-SOURCE-GAP.md to completed/"
```

---

## Documentation
- [ ] No doc changes needed
- [ ] Flag doc gap if `MaterialLookupService`'s data-sourcing model isn't documented anywhere — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: none
**Blocks**: none identified
**Related tasks**: 2026-08-13-LOW-FEATURE-FIXTURE-BUNDLE-STALE-MOCKS-GAPS.md (completed/2026-08/ — this task is spun off from its items #2/#3/#4)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

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
