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

The second and third rows demonstrate that the current string-prefix guard accepts path strings that are not contained by the catalog root. The human's check establishes the path-building/guard result only; it is not an end-to-end HTTP test and does not establish whether the request reaches the controller unchanged. The existing traversal test (`rejects path traversal attempts`, spec line 208 on origin) only sends the first row, so the suite passes while these bypasses remain possible. A symlink inside the catalog root pointing outside it is a relevant escape case, reported by the 2026-10-15 catalog-index pre-implementation report but not independently re-verified here.

**Supersedes a claim on record:** that pre-implementation report (in `projects/galaxy_game/summaries/`, `2026-10-15-PREIMPL-CATALOG-INDEX-IMAGE-RESOLUTION.md`, committed in `7c07377`) says `Pathname.join` "normalizes `..` sequences, blocking simple `../../../etc/passwd` attacks". The table above shows that is true only for a leading `..`; embedded `..` segments are kept. Do not edit that report. This task supersedes that line.

## Critical Information for This Task

1. **One objective.** Serve only regular files whose resolved real path is inside the catalog root's real path. Nothing else.
2. **Do not change** the route, the URL helper `admin/catalog_image_path(path:)`, the JSON/response shape, or which image is selected for any asset. Canonical-render selection is an unresolved human decision (C3b / catalog-index lane). Do not choose or touch it.
3. **Resolve both sides.** Resolve the configured catalog root and requested file to real paths before deciding containment. Treat a file as contained only when its resolved path is strictly beneath the resolved root, using a separator-aware boundary so `catalog-private` does not match `catalog`. The configured root may itself be reached through a symlink; valid files beneath that resolved root must still work.
4. **Handle resolution failures as rejection.** A missing path, dangling symlink, inaccessible path, or other real-path resolution failure must produce the existing not-found rejection (404), not an unhandled exception or a 500 response. Reject directories and other non-regular filesystem objects.
5. **Test isolation.** The current spec hardcodes `/home/galaxy_game/app/data/images/catalog` and creates a zero-byte PNG there if missing. New tests must use a temp directory / stubbed root and create any test files and symlinks only there. Do not write into the real data directory.
6. **Green tests are not live behavior.** Also confirm with actual HTTP requests (see AC5). Not yet verified: whether routing or the web server normalizes `..` or percent-encoded traversal such as `%2e%2e` before the controller, and whether `/admin` requires authentication. Record observations; do not expand scope to change authentication, routing, or URL behavior.
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

`catalog_image` (action defined at `galaxy_game/app/controllers/admin/catalog_controller.rb` line 39; the `start_with?` check at line 45, as read from origin on 2026-10-16) accepts path strings that can resolve outside the catalog root. Change only the serving containment decision so the action serves a requested target if and only if it is a regular file whose resolved real path is strictly inside the catalog root's resolved real path. Prove the boundary with regression tests that fail before the fix and pass after it. Whether the HTTP stack delivers traversal strings to the controller unchanged remains unverified and must be reported separately from the controller-level test results.

## Files Involved

| File | Role | Verified |
|---|---|---|
| `galaxy_game/app/controllers/admin/catalog_controller.rb` | `catalog_image` containment and rejection behavior | yes, origin read 2026-10-16 |
| `galaxy_game/spec/controllers/admin/catalog_controller_spec.rb` | existing traversal test at line 208; add isolated regression tests | yes, origin read 2026-10-16 |

Change only these two files. Do not add a helper or shared module for this narrowly scoped fix. Changing any other file is a Stop Condition.

## Implementation Steps

Outline only; the implementation agent confirms exact code and commands.

- **Step 0:** move the task file per the Dispatch Interface; paste both outputs.
- **Step 1:** write the synthesis report (above).
- **Step 2 (red first):** add isolated regression tests for: embedded `..` traversal; sibling-prefix (`catalog-private`) paths; an absolute-path parameter; a percent-encoded traversal request where the test layer can exercise it; an in-root symlink whose target resolves outside the root; a missing target; a directory target; a valid regular file; and a catalog root whose configured path is reached through a symlink. Assert rejected targets return 404 and do not raise; assert valid in-root files are served. Run the spec before changing production code and paste output showing the applicable security regression tests fail on the original guard.
- **Step 3:** change only the containment decision in `catalog_image`. Resolve the configured root and candidate target to real paths, handle resolution failures as rejection, enforce a separator-aware strict-descendant boundary, and require the resolved target to be a regular file before sending it. Do not alter routes, helpers, response shape, authentication, or image selection.
- **Step 4:** run the controller spec in isolation (per SESSION_GUIDANCE.md RSpec entry: container working directory is `/home/galaxy_game`; use the log-redirect form and inspect with `tail -50`):
  `docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/controllers/admin/catalog_controller_spec.rb > /home/galaxy_game/log/rspec_catalog_controller_$(date +%s).log 2>&1'`
  Then: `docker exec web tail -50 /home/galaxy_game/log/rspec_catalog_controller_<timestamp>.log`. Paste output. Run relevant related specs if the project guidance identifies them; do not broaden into a suite-wide refactor.
- **Step 5:** perform the live HTTP checks in AC5 if the app can be exercised safely. Record the exact request target as sent, response status, and whether it appears to reach the controller. Include one valid image request. Treat controller-level tests and live HTTP observations as separate evidence. If a traversal form is normalized or rejected upstream, record that result without claiming the controller path was exercised.
- **Step 6:** stage explicit paths only and report `git diff --stat`. Do not push.

## Acceptance Criteria

- [ ] **AC1 — Regression proof:** Before the production change, tests for embedded `..`, sibling-prefix (`catalog-private`), outside-root symlink, and absolute-path parameters demonstrate the current guard's weakness where those cases reach the controller. After the change, all applicable rejection tests pass with 404 and no unhandled exception. Paste both red and green test outputs; do not claim a case was exercised if the test/request layer rewrites it first.
- [ ] **AC2 — Valid file and root symlink:** A regular file inside the resolved catalog root is served with an image content type both when the configured root is a normal path and when the configured root path is reached through a symlink.
- [ ] **AC3 — Invalid filesystem targets:** A directory, nonexistent target, dangling symlink, and outside-root symlink are rejected as 404; real-path resolution failures do not become 500 responses. Percent-encoded traversal (`%2e%2e`, including mixed-case encoding if supported by the request stack) is tested at the HTTP boundary or explicitly recorded as normalized/rejected upstream.
- [ ] **AC4 — Spec results:** `catalog_controller_spec.rb` isolation run has 0 failures, output pasted. Run relevant related specs identified by project guidance and report their results; do not claim unrun specs passed.
- [ ] **AC5 — Live HTTP evidence:** With the app running, make and record one valid-image request and at least one bypass request. Record the exact raw request target, response status, and observed response. If routing/web-server normalization prevents a traversal string from reaching the controller, state that explicitly; this does not replace AC1's controller-level tests. If live checks cannot be run, say so and leave this criterion unmet rather than inferring success from specs.
- [ ] **AC6 — Scope and isolation:** Only the controller and its spec changed (`git diff --stat` pasted); tests create fixtures only in a temp directory/stubbed root, no files are created in the real data directory, and no scratch files are left in the repo root.
- [ ] **AC7 — Preserved behavior/scope:** Existing valid-image behavior and the 404 rejection behavior remain intact. No route, URL helper, response-shape, authentication, or canonical-render selection changes were made.

## Stop Conditions — escalate to user immediately if:

- The `/admin` namespace appears not to require authentication (report the observed evidence and stop; do not implement an authentication change). Authentication status is currently unverified.
- The fix appears to require a route, URL helper, view, service, or file beyond the two listed.
- Valid-image behavior changes in any existing spec, or the existing 404 rejection behavior cannot be preserved.
- A symlink test cannot be created in the test environment; report the limitation and do not silently omit the symlink coverage.
- The same test failure persists after two focused repair attempts; stop and report the evidence instead of continuing open-ended retries.
- A decision about which image is canonical comes up.
- Uncommitted changes you did not make are present in a file you need to touch.
- Correct containment would require changing route behavior, URL encoding/decoding behavior, authentication, or another out-of-scope surface.
- The configured catalog root cannot be resolved safely, or its intended root semantics are unclear; stop rather than guessing.

---

## Commit Instructions

Run git commands on **host only**. The agent stages explicit paths (never `git add .` or `git commit -a`) and reports `git diff --cached --stat`. Per GUARDRAILS.md Rule 26 — "Agents may `git add` (stage) changes. Agents may NOT run `git commit` or `git push` without an explicit human approval message in the current chat session" — present the diff or commit message and STOP; wait for explicit approval before running either command.

**Task file move on completion:** `git mv projects/galaxy_game/tasks/active/2026-10-16-HIGH-BUG-FIX-CATALOG-IMAGE-PATH-CONTAINMENT.md projects/galaxy_game/tasks/completed/2026-10/2026-10-16-HIGH-BUG-FIX-CATALOG-IMAGE-PATH-CONTAINMENT.md`

## Dependencies

- Blocked by: none.
- Blocks: this task is required before Phase 2 of `2026-10-13-HIGH-BUGFIX-CATALOG-INDEX-IMAGE-RESOLUTION` (that task's scope excludes the controller). It is the separate, narrowly scoped hardening task that item 4 of that task anticipates "if approved" (approval is a human decision, not yet given). CONFIRMED: lines 48-50 state "The existing `start_with?` containment check does not establish symlink handling, regular-file validation, or sufficient canonical-root containment. A separate serving-security assessment and, if approved, narrowly scoped hardening task is required." Lines 57-62 list "Catalog image-serving containment, traversal/symlink protection, regular-file validation, and URL behavior are verified or separately approved for hardening" as required before Phase 2.
- Related, not blocking: C3b (`2026-10-11-MEDIUM-BUG-FIX-C3B-RH400-CATALOG-IMAGE-URL`) carries a short note about this finding.

## Completion Report

(Agent fills at closeout per SESSION_CLOSEOUT.md.)

## Handoff Summary

(Agent fills at closeout per SESSION_CLOSEOUT.md.)
