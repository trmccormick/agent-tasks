---
status: backlog
resume: false
priority: HIGH
type: bug-fix
system_domain: CONTROLLERS
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
created: 2026-10-16
last_updated: 2026-10-16
---

# TASK: Contain `catalog_image` to the real catalog root (path traversal + symlink escape)

**Status**: BACKLOG (drafted by a web review session; NOT dispatched, NOT dispatch-ready until the checklist below is complete)
**Priority**: HIGH  |  **Type**: bug-fix  |  **Created**: 2026-10-16

---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps (resolve every `[FILL IN]`)
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

**One allowed substitution:** before dispatch, replace `<agent-tasks>` with the absolute path of the agent-tasks clone on the machine that will run the task (run `pwd` from the repo root). Fill `[project]`, `[SUBFOLDER]` and `[FILENAME]` as usual. Change nothing else.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: <agent-tasks>/projects/galaxy_game/tasks/backlog/asset-ui/2026-10-16-HIGH-BUG-FIX-CATALOG-IMAGE-PATH-CONTAINMENT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/asset-ui/2026-10-16-HIGH-BUG-FIX-CATALOG-IMAGE-PATH-CONTAINMENT.md \
         projects/galaxy_game/tasks/active/2026-10-16-HIGH-BUG-FIX-CATALOG-IMAGE-PATH-CONTAINMENT.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-10-16-HIGH-BUG-FIX-CATALOG-IMAGE-PATH-CONTAINMENT.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

---

## Prerequisites — READ FIRST

- Read `rules/GUARDRAILS.md` and `projects/galaxy_game/SESSION_GUIDANCE.md`.
- Code under change was added in galaxyGame commit `d7894ca3` (on origin/main). CONFIRMED: `git log -1 --format=%h -- galaxy_game/app/controllers/admin/catalog_controller.rb` → `d7894ca3`; `git log -1 --format=%h -- galaxy_game/spec/controllers/admin/catalog_controller_spec.rb` → `d7894ca3`. Both return the same commit.
- Do NOT start from the catalog-index image-resolution task or C3b. This task is a prerequisite for the first and is independent of the second.

## Context

`Admin::CatalogController#catalog_image` serves files from the catalog image directory. Its containment check is a string-prefix test on an unnormalized path. A Ruby check run by the human on 2026-10-16 (VERIFIED, path-building logic only, not an end-to-end request) gave:

| Requested `path` param | Built path | Guard passes |
|---|---|---|
| `../../etc/passwd` | `/etc/passwd` | false (blocked) |
| `images/catalog/../../../etc/passwd` | `/app/data/images/catalog/../../../etc/passwd` | **true (bypass)** |
| `images/catalog-private/x.png` | `/app/data/images/catalog-private/x.png` | **true (sibling-prefix bypass)** |
| `images/catalog/crafts/ground/rh400_concept.png` | normal | true (valid) |

`File.exist?`/`send_file` resolve `..` at the filesystem level, so the second row would be served from outside the catalog directory. The existing traversal test (`rejects path traversal attempts`, spec line 208 on origin) only sends the first row, so the suite passes while the bypass exists. A symlink inside the catalog root pointing outside it would also pass the string check (reported by the 2026-10-15 catalog-index pre-implementation report, not independently re-verified).

**Supersedes a claim on record:** that pre-implementation report (in `projects/galaxy_game/summaries/`, `2026-10-15-PREIMPL-CATALOG-INDEX-IMAGE-RESOLUTION.md`, committed in `7c07377`) says `Pathname.join` "normalizes `..` sequences, blocking simple `../../../etc/passwd` attacks". The table above shows that is true only for a leading `..`; embedded `..` segments are kept. Do not edit that report. This task supersedes that line.

## Critical Information for This Task

1. **One objective.** Serve only regular files whose resolved real path is inside the catalog root's real path. Nothing else.
2. **Do not change** the route, the URL helper `admin/catalog_image_path(path:)`, the JSON/response shape, or which image is selected for any asset. Canonical-render selection is an unresolved human decision (C3b / catalog-index lane). Do not choose or touch it.
3. **Resolve both sides.** Compare real path of the requested file with the real path of the catalog root, using a trailing separator (so `catalog-private` does not match `catalog`). The root itself may sit under symlinks.
4. **Rejects behave like the existing rejection.** Match the status the current traversal test expects. CONFIRMED: spec line 208 expects `have_http_status(:not_found)` (404). Controller returns `head :not_found`.
5. **Tests must not write into the real data directory.** The current spec hardcodes `/home/galaxy_game/app/data/images/catalog` and creates a zero-byte PNG there if missing. New tests use a temp directory / stubbed root, and a real symlink created in that temp directory.
6. **Green tests are not live behavior.** Also confirm with an actual request (see AC5). Not yet verified: whether routing or the web server normalizes `..` / `%2e%2e` before the controller, and whether `/admin` requires authentication. Record what you observe; do not expand scope to fix auth.
7. Git commands on host only. Scratch files and logs go in `temp-files/`, never the repo root.

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Save the report as an MD file in `projects/galaxy_game/summaries/` BEFORE any work. Never paste it in chat.

```markdown
## STATUS SYNTHESIS REPORT

**Task**: [name from filename]
**Status**: [backlog → active → completed]
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
- ✅ Know which domain/credentials to use

### Expected Outcomes
[Exact description of what "done" looks like]

### Critical Gotchas I Will Avoid
- ❌ [wrong approach] — instead ✅ [right approach]
- ❌ [wrong approach] — instead ✅ [right approach]

---

**SYNTHESIS COMPLETE.** Ready to proceed with [PRIORITY 1 / PRIORITY 2 / etc].
```

---

## Problem Statement

`catalog_image` (action defined at `galaxy_game/app/controllers/admin/catalog_controller.rb` line 39; the `start_with?` check at line 45, as read from origin on 2026-10-16) accepts paths that resolve outside the catalog root. Fix it so containment is decided on real, resolved paths, and prove it with tests that fail before the fix and pass after.

## Files Involved

| File | Role | Verified |
|---|---|---|
| `galaxy_game/app/controllers/admin/catalog_controller.rb` | `catalog_image` containment check | yes, origin read 2026-10-16 |
| `galaxy_game/spec/controllers/admin/catalog_controller_spec.rb` | existing traversal test at line 208; add regression tests | yes, origin read 2026-10-16 |
| No helper or shared module needed | controller is standalone; uses only Rails built-ins (`File.exist?`, `send_file`, `start_with?`) | yes, verified by reading controller source |

Changing any file beyond these is a Stop Condition.

## Implementation Steps

Outline only; the implementation agent confirms exact code and commands.

- **Step 0:** move the task file per the Dispatch Interface; paste both outputs.
- **Step 1:** write the synthesis report (above).
- **Step 2 (red first):** add tests for (a) embedded `..`, (b) sibling-prefix directory, (c) symlink inside the root pointing outside, (d) a valid catalog image still served. Run the spec; paste output showing (a)-(c) fail on current code.
- **Step 3:** change the containment check in `catalog_image` to compare real paths with a trailing separator and require a regular file.
- **Step 4:** run the controller spec in isolation (per SESSION_GUIDANCE.md RSpec entry: container working directory is `/home/galaxy_game`; use the log-redirect form and inspect with `tail -50`):
  `docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/controllers/admin/catalog_controller_spec.rb > /home/galaxy_game/log/rspec_catalog_controller_$(date +%s).log 2>&1'`
  Then: `docker exec web tail -50 /home/galaxy_game/log/rspec_catalog_controller_<timestamp>.log`. Paste output.
- **Step 5:** live check (AC5). Paste the exact requests and responses.
- **Step 6:** stage explicit paths only and report `git diff --stat`. Do not push.

## Acceptance Criteria

- [ ] **AC1:** New tests for embedded `..`, sibling-prefix and symlink escape fail on the unmodified code and pass after the fix (both outputs pasted).
- [ ] **AC2:** A valid catalog image is still served with an image content type.
- [ ] **AC3:** `catalog_controller_spec.rb` isolation run: 0 failures, output pasted. No regressions in related specs.
- [ ] **AC4:** Only the controller and its spec changed (`git diff --stat` pasted).
- [ ] **AC5:** With the app running, a request for a bypass path and a request for a valid image were actually made; the bypass is rejected and the valid image is served. Output pasted. If this cannot be done, say so; do not infer it from the specs.
- [ ] **AC6:** New tests do not create files in the real data directory; no scratch files in the repo root.
- [ ] **AC7:** No canonical-render choice was made; no route, helper or response change.

## Stop Conditions — escalate to user immediately if:

- The `/admin` namespace requires no authentication (report, do not fix).
- The fix needs a route, helper, view, service, or file beyond the two listed.
- Valid-image behavior changes in any existing spec.
- A symlink test cannot be created in the test environment.
- Same failure persists after two attempts.
- Any decision about which image is canonical comes up.
- Uncommitted changes you did not make are present in a file you need to touch.

---

## Commit Instructions

Run git commands on **host only**. The agent stages explicit paths (never `git add .` or `git commit -a`) and reports `git diff --cached --stat`. Per GUARDRAILS.md Rule 26 — "Agents may `git add` (stage) changes. Agents may NOT run `git commit` or `git push` without an explicit human approval message in the current chat session" — present the diff or commit message and STOP; wait for explicit approval before running either command.

**Task file move on completion:** `git mv projects/galaxy_game/tasks/active/2026-10-16-HIGH-BUG-FIX-CATALOG-IMAGE-PATH-CONTAINMENT.md projects/galaxy_game/tasks/completed/2026-10/2026-10-16-HIGH-BUG-FIX-CATALOG-IMAGE-PATH-CONTAINMENT.md`

## Dependencies

- Blocked by: none.
- Blocks: this task is required before Phase 2 of `2026-10-13-HIGH-BUGFIX-CATALOG-INDEX-IMAGE-RESOLUTION`. (that task's scope excludes the controller) It is the separate, narrowly scoped hardening task that item 4 of that task anticipates "if approved" (approval is a human decision, not yet given). CONFIRMED: lines 48-50 state "The existing `start_with?` containment check does not establish symlink handling, regular-file validation, or sufficient canonical-root containment. A separate serving-security assessment and, if approved, narrowly scoped hardening task is required." Lines 57-62 list "Catalog image-serving containment, traversal/symlink protection, regular-file validation, and URL behavior are verified or separately approved for hardening" as required before Phase 2.
- Related, not blocking: C3b (`2026-10-11-MEDIUM-BUG-FIX-C3B-RH400-CATALOG-IMAGE-URL`) carries a short note about this finding.

## Completion Report

(Agent fills at closeout per SESSION_CLOSEOUT.md.)

## Handoff Summary

(Agent fills at closeout per SESSION_CLOSEOUT.md.)
