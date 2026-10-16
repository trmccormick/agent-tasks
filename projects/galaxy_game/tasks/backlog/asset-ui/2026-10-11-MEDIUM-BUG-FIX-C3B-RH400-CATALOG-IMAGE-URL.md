---
status: backlog
priority: MEDIUM
type: bug-fix
system_domain: OTHER
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
created: 2026-10-11
last_updated: 2026-10-11
---

# TASK: C3b — Verify and Correct RH-400 Catalog Image URL

**Status**: BACKLOG  
**Priority**: MEDIUM  
**Type**: bug-fix  
**Created**: 2026-10-11  
**Last Updated**: 2026-10-11

---

## 🔴 CRITICAL: Task Readiness Checklist

**Do not dispatch until all boxes are checked.**

- [ ] Agent Dispatch Interface is complete and contains no placeholders.
- [ ] Implementation steps and stop conditions are clear.
- [ ] Synthesis-report template is included.
- [ ] Referenced file paths have been verified on the work machine.
- [ ] The exact Docker/RSpec invocation has been checked against `projects/galaxy_game/SESSION_GUIDANCE.md`.
- [ ] Acceptance criteria are measurable.
- [ ] Dependencies and blocked work are recorded.
- [ ] The current session has been closed out and pushed before this task is dispatched.

---

## Agent Dispatch Interface

```text
You are Implementation Agent.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/asset-ui/2026-10-11-MEDIUM-BUG-FIX-C3B-RH400-CATALOG-IMAGE-URL.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE:
  From the agent-tasks repository root, move this tracked file with git mv
  to projects/galaxy_game/tasks/active/2026-10-11-MEDIUM-BUG-FIX-C3B-RH400-CATALOG-IMAGE-URL.md.
  Change its YAML status from backlog to active.
  Verify that exactly one copy exists and paste the find output in chat.
  Do not begin implementation before completing this step.

LIFECYCLE: backlog → active → completed.
Follow the established task lifecycle and session-closeout workflow.
Save the required synthesis report as a Markdown file in the project
summaries directory before implementation begins.

Read the task file and follow its scope, stop conditions, and commit rules.
```

---

## Agent Assignment

**Assigned To**: Qwen local via GitHub Copilot (primary)  
**Why This Agent**: Bounded Rails implementation and integration-test task.  
**Local attempts before cloud**: N/A  
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST

1. Read the `EXECUTOR` section of the `agent-tasks` repository README.
2. Read the `projects/galaxy_game/README.md` project guide.
3. Read `projects/galaxy_game/SESSION_GUIDANCE.md`.
4. Read this task file completely.
5. Review the completed C3 task and the C2/C3 vs B2 boundary assessment supplied by Tracy.

**Reference commits**

- GalaxyGame C3 implementation: `b816d8e`
- `agent-tasks` state at the prior review: `f0317b2`

Verify the actual current `main` state before relying on these commit references.

---

## Context

C3 implemented the RH-400 catalog vertical slice, but its controller spec stubs `CatalogService#catalog_data` and supplies a browser-style image path. The actual registry appears to return filesystem paths discovered under `GalaxyGame::Paths::JSON_DATA/images/catalog`. The existing view passes the returned path to `image_tag`, while the asset-serving endpoint is `/api/assets/*path`.

This task verifies and, if necessary, corrects the image URL used by the real catalog implementation. It does not implement the future catalog manifest architecture.

### Relevant Architecture Reference

- B2 catalog presentation contract and the C2/C3 boundary assessment.
- `docs/reference/asset-generation/VISUAL_CONTRACT.md`, where relevant.
- `projects/galaxy_game/SESSION_GUIDANCE.md` for testing and execution requirements.

---

## Critical Information for This Task

### Architecture Gotchas

**GOTCHA 1 — Do not confuse a filesystem path with a browser URL.**

- Wrong: Passing an absolute container path such as `/home/galaxy_game/app/data/images/catalog/.../rh400_concept.png` directly to `image_tag`.
- Right: Verify the existing asset-serving route and use a URL that route actually serves, expected to be `/api/assets/<path relative to ASSETS_PATH>`.
- Why: A file existing inside the container does not establish that a browser can retrieve it.

**GOTCHA 2 — Do not choose a canonical RH-400 render.**

- Wrong: Selecting `rh400_concept.png` or `rh400_regolith_harvesting_rover.png` based on preference.
- Right: Report every candidate returned by the registry's actual `Dir.glob`. If more than one candidate exists, stop and ask for a decision.
- Why: The canonical catalog render is an unresolved product/architecture decision.

**GOTCHA 3 — Do not expand into manifest architecture.**

- Wrong: Rewriting `AssetRegistry`, creating a manifest generator, or redesigning `CatalogService` to satisfy the B2 boundary in this task.
- Right: Make only the smallest change needed to correct the URL and prove the asset is servable.
- Why: Manifest generation is a separate prerequisite task. It blocks D2 and further runtime catalog work, not C4.

**GOTCHA 4 — Preserve all unrelated Git state.**

- Do not unstage, reset, stash, or clean up other work.
- Do not use broad staging commands.
- Commit only explicit paths owned by this task.

---

## Required Status Synthesis Report

Before implementation, save a Markdown synthesis report in:

`projects/galaxy_game/summaries/`

Use the established filename convention:

`YYYY-MM-DD-BUG-FIX-C3B-RH400-CATALOG-IMAGE-URL.md`

The report must include:

```markdown
## STATUS SYNTHESIS REPORT

**Task**: C3b — Verify and Correct RH-400 Catalog Image URL
**Status**: backlog → active
**Date**: 2026-10-11

### Objective
Verify the actual RH-400 catalog image candidate and the complete
controller-to-service-to-view image URL path. Correct the URL only if
there is exactly one candidate and the defect can be fixed without
making an unresolved architecture decision.

### Files to Inspect
- Admin catalog controller
- CatalogService
- AssetRegistry
- Catalog detail view
- Existing asset-serving route/controller
- Relevant controller/service/request specs
- SESSION_GUIDANCE.md

### Expected Outcome
A real, non-stubbed test proves that the generated image URL maps to
an existing asset-serving route and successfully retrieves the image,
or the task stops with precise evidence of the blocker.

### Constraints
- Do not select among multiple catalog-render candidates.
- Do not implement manifest generation.
- Do not alter C4 or unrelated transit-engine work.
- Do not unstage, reset, or stash existing changes.
- Do not commit files outside this task's explicit scope.

**SYNTHESIS COMPLETE.**
```

---

## Problem Statement

**Current behavior**

The registry appears to return absolute filesystem paths from `Dir.glob`. The catalog view passes the result to `image_tag`. The C3 spec stubs the catalog data and therefore does not prove that the real image URL works.

**Expected behavior**

When exactly one RH-400 catalog-render candidate is returned, the real catalog implementation produces a URL served by the existing asset-serving endpoint. A non-stubbed test verifies that the asset request succeeds.

**Known likely route**

`/api/assets/<path relative to ASSETS_PATH>`

Verify the current route and path conventions in code before implementation. Do not assume the example URL is correct without checking the actual file location.

---

### Known issue found in review of `d7894ca3` (verified)

The `catalog_image` action's path check (`start_with?` on an unnormalized `Pathname#join`) accepts `images/catalog/../../../etc/passwd` and `images/catalog-private/x.png`. The existing traversal test only covers `../../etc/passwd`, which is blocked lexically, so the suite passes while the bypass exists. Verified by a Ruby check of the path-building logic (not an end-to-end request). Not yet verified: whether routing or the web server normalizes `..` before the controller, and whether the `/admin` namespace requires authentication.

Fix in `galaxy_game/app/controllers/admin/catalog_controller.rb` (action at line 39): resolve the requested path to its real location, then compare against the catalog directory with a trailing separator. Add regression tests for both bypass cases in `galaxy_game/spec/controllers/admin/catalog_controller_spec.rb` (existing traversal test at line 208).

## Files Involved

Inspect these existing files first. Confirm exact paths and any additional required files before editing.

### Primary files — edit only if needed

| File | Purpose |
|---|---|
| `app/controllers/admin/catalog_controller.rb` | Catalog detail request flow |
| `app/services/catalog_service.rb` | Builds catalog data and resolves the render path |
| `app/views/admin/catalog/show.html.erb` | Renders the catalog image |
| Relevant existing catalog controller/service specs | Regression coverage for the real implementation |

### Reference files — do not edit unless a narrowly justified change is necessary

| File | Purpose |
|---|---|
| `app/services/asset_registry.rb` | Current image discovery and representation paths |
| `app/controllers/api/assets_controller.rb` | Asset-serving behavior |
| `config/initializers/game_data_paths.rb` | `ASSETS_PATH` and JSON data path definitions |
| `config/routes.rb` or the actual route definition | Verify `/api/assets/*path` |
| `projects/galaxy_game/SESSION_GUIDANCE.md` | Required test execution and logging convention |

No migration is expected.

---

## Implementation Steps

### Step 1 — Verify the actual candidate set

Inspect the real registry discovery logic and run a read-only check against the current RH-400 catalog directory.

Report:

- The exact `Dir.glob` patterns used.
- Every matching catalog-render candidate.
- Each candidate's path relative to `ASSETS_PATH`.
- Whether the candidate file exists and is readable in the execution environment.

**Stop conditions:**

- If there are zero candidates, stop and report the search patterns and results.
- If there is more than one candidate, stop and report all candidates.
- Do not pick a preferred render or change the glob patterns to force a single result.

Continue only if exactly one candidate exists.

### Step 2 — Trace the real URL path

Trace the actual request flow:

`Admin::CatalogController → CatalogService → AssetRegistry → catalog view → image_tag`

Verify:

1. What value the registry returns.
2. How that value is represented in catalog data.
3. What `src` the rendered view produces.
4. Which route serves the underlying file.
5. How that route resolves the URL to a file beneath `ASSETS_PATH`.

Do not stub `CatalogService#catalog_data` for this check.

### Step 3 — Make the smallest justified correction

If the current implementation passes an absolute filesystem path as the browser URL, change only the relevant path-to-URL handling so the existing asset-serving route receives the correct relative asset path.

Do not:

- Choose a different catalog render.
- Create a manifest generator.
- Rework the AssetRegistry/runtime boundary.
- Add new asset identities or hardcoded RH-400-only mappings unless the existing design explicitly requires them.
- Modify unrelated files.

If the correction requires an unresolved design decision, stop and report the decision required.

### Step 4 — Add a regression test

Add or extend a test that uses the real implementation rather than stubbing `catalog_data`.

The test must verify, as supported by the current test environment:

- The selected RH-400 candidate produces a browser-accessible URL.
- The URL maps to the existing asset-serving route.
- The asset-serving request returns a successful response when the file exists.
- The resolved file remains within the permitted asset directory.

Do not fabricate a successful result if the test environment cannot access the real asset. Report the limitation and the evidence obtained.

### Step 5 — Run and log the relevant tests

Follow `projects/galaxy_game/SESSION_GUIDANCE.md` exactly for Docker execution and RSpec conventions.

- Redirect complete test output to a log file.
- Do not pipe the test command through `tail`.
- Preserve the test process exit status.
- Report the exact command, log path, example count, failures, and any environmental limitation.

Do not run the full test suite unless the task guidance requires it.

### Step 6 — Report before committing

Summarize:

- Candidate path(s) found.
- Why the original URL did or did not work.
- The exact correction, if one was needed.
- Regression-test coverage.
- Test command and results.
- Any unresolved issue.

Do not commit unrelated changes.

---

## Acceptance Criteria

- [ ] Exactly one catalog-render candidate was identified before attempting a correction.
- [ ] No canonical-render choice was made by the agent.
- [ ] The real implementation, without a stubbed `catalog_data`, produces the expected browser-accessible image URL.
- [ ] The existing asset-serving route successfully serves the selected file, where the test environment permits.
- [ ] A regression test covers the real path-resolution behavior.
- [ ] Relevant RSpec output was redirected to a log file, and the actual exit status was checked.
- [ ] Only task-scoped files were changed.
- [ ] No unrelated Git state was reset, stashed, or unstaged.
- [ ] The commit contains only explicitly named task-owned paths.

---

## Stop Conditions — Escalate Immediately If

- No RH-400 catalog-render candidates are found.
- Multiple catalog-render candidates are found.
- The asset-serving route or path mapping differs from the expected design.
- The correction requires selecting a canonical render.
- The correction requires deciding where the future manifest lives.
- A fix requires redesigning the runtime/development-time boundary.
- The required asset is unavailable in the test environment.
- Relevant tests fail after two focused attempts.
- Unrelated staged or working-tree changes make a safe, scoped commit uncertain.

When stopping, report the evidence and the decision needed. Do not work around the blocker by inventing a path or selecting a render.

---

## Commit Instructions

Run Git commands on the host, not inside the Docker container.

**Do not unstage, reset, stash, or clean up unrelated changes.**

Inspect the working tree and stage only explicit task-owned paths. Commit using path-limited syntax:

```bash
git status --short
git diff -- <explicit task-owned paths>
git diff --cached -- <explicit task-owned paths>

git commit -m "fix: correct RH-400 catalog image URL" -- <explicit task-owned paths>

git show --stat HEAD
```

Replace the path placeholder with the exact files changed by this task. If there are no code changes because the task stopped on a decision, do not create a code commit.

Push only after confirming that the commit contains the intended paths and no unrelated work.

Follow the established session-closeout workflow. Move the task file to `completed/` and update its YAML status only after the task's actual outcome is recorded. Verify that exactly one copy of the task file exists after the move.

---

## Documentation

- [ ] No documentation change needed.
- [ ] Flag a documentation gap if the actual route/path contract differs from the existing guidance. Do not invent a new architecture contract in this task.

---

## Dependencies

**Blocked by**: Current Qwen session must be closed out and pushed before dispatch.  
**Blocks**: None directly.  
**Related tasks**:
- C2 — Catalog Data Wiring
- C3 — RH-400 Catalog Vertical Slice
- C4 — Visual Profile Resolution (not blocked by this task or the manifest prerequisite)
- Future catalog manifest-generation prerequisite (separate task; blocks D2 and further runtime catalog work)

---

## Completion Report

*Fill this section in after the task completes.*

**Completed by**: [agent name]  
**Completion date**: YYYY-MM-DD  
**Final test result**: [actual result]  
**Evidence basis**: [direct test evidence / reviewed code / reported result]

### What changed

- [Exact file and change]

### Verification

- Candidate path(s): [actual paths]
- Generated URL: [actual URL]
- Asset-serving response: [actual result]
- Test command and log: [actual command and log path]
- Commit: [actual commit]

### Issues discovered

[Record any new issue supported by evidence.]

### Follow-up tasks needed

[Record follow-up tasks; do not create them without approval.]

### Lessons learned

[Briefly record what worked or failed.]

---

## Handoff Summary

HANDOFF SUMMARY: [files changed] | [image URL verification result] | [remaining action or next task]