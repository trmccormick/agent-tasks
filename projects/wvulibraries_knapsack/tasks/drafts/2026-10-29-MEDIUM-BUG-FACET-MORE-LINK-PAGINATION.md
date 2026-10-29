# TASK: Facet "More" Link Modal — Shows Limited Results Instead of All Values on hykudev

**Status**: DRAFT
**Priority**: MEDIUM
**Type**: bug-fix
**Created**: 2026-10-29
**Last Updated**: 2026-10-29

---

## 🟡 DRAFT STATUS NOTE (for tasks in tasks/drafts/)

**This section applies ONLY if status: draft above.**

This task is in draft status and awaiting review approval. It has NOT yet been approved for backlog.

**Current State**: In `tasks/drafts/` committed to git  
**Next Step**: Review agent assessment → Human approval → Move to `tasks/backlog/` by human  
**Do NOT dispatch to implementation agents** until this task is in `tasks/backlog/` with `status: backlog`

See PLANNING_AGENT_SESSION_START.md § "Task Creation Workflow" for full procedure.

## 🔴 DRAFT REVIEW CHECKLIST (Human — before approving move from drafts/ to backlog/)

**APPLIES ONLY IF status: draft above. Use this checklist BEFORE moving task to tasks/backlog/.**

After review agent has returned assessment:

- [ ] Acceptance Criteria are clear and measurable (no ambiguity)
- [ ] Task scope is well-bounded (not too broad, not overlapping with active tasks)
- [ ] Dependencies listed and verified against status.md (all prerequisites exist)
- [ ] No contradictions in the task specification
- [ ] Architecture aligns with current codebase patterns (review agent verified)
- [ ] Review agent disposition is GO (not REVISE or STOP)

**Once ALL boxes checked:** Approve by moving task from `tasks/drafts/` to `tasks/backlog/[SUBFOLDER]/` and changing `status: draft → status: backlog`.

---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching to implementation)

**APPLIES ONLY IF status: backlog. STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

**One allowed substitution:** before dispatch, replace `<agent-tasks>` with the absolute path of the agent-tasks clone on the machine that will run the task (run `pwd` from the repo root). Fill `[project]`, `[SUBFOLDER]` and `[FILENAME]` as usual; if the project's backlog is flat (no subfolders), omit the `[SUBFOLDER]/` segment from the paths. Change nothing else.

```
You are **Implementation Agent**.

Project: wvulibraries_knapsack
Task: <agent-tasks>/projects/wvulibraries_knapsack/tasks/active/[FILENAME].md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/wvulibraries_knapsack/tasks/backlog/[SUBFOLDER]/[FILENAME].md \
         projects/wvulibraries_knapsack/tasks/active/[FILENAME].md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/[project]/tasks -name "[FILENAME].md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/wvulibraries_knapsack/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Facet "More" Link Modal — Shows Limited Results Instead of All Values on hykudev
**Status**: BACKLOG | ACTIVE | BLOCKED | COMPLETED
**Priority**: CRITICAL | HIGH | MEDIUM | LOW
**Type**: bug-fix | feature | refactor | architecture | data | documentation
**Created**: 2026-10-29
**Last Updated**: 2026-10-29

---

## Context

After deploying the facet limiting fix (AC #5) via feature/hyku-v7.2.0-upgrade to hykudev, a new bug was discovered on the homepage facets that does not exist in main/production.

**How it works on main branch / production VM** (correct behavior):
- Homepage facets show collapsed values in sidebar
- Clicking "More" redirects to `/catalog/facet/{field_name}?locale=en` (no modal)
- The expanded facet page shows ALL available facet values with pagination (Previous/Next)
- Search results page works correctly: 5 values collapsed, all available via expansion

**Bug on hykudev** (feature/hyku-v7.2.0-upgrade ONLY):
- Homepage facets show a **modal** when clicking "More" instead of redirecting to a separate page
- This modal ALSO limits to only 5 values (same as collapsed view) instead of showing ALL available values
- Both the sidebar AND the modal are limited to 5 — this is the bug

---

## Problem Statement

After deploying feature/hyku-v7.2.0-upgrade to hykudev, the homepage facet "More" link behavior changed:
- **Current behavior**: Modal appears showing only top 5 facet values (limited)
- **Expected behavior**: Redirect to `/catalog/facet/{field_name}?locale=en` showing ALL available values with pagination

This is a regression — main branch/production VM has the correct working behavior. The bug is isolated to feature/hyku-v7.2.0-upgrade on hykudev.

**Related Task**: `2026-10-28-HIGH-REFACTOR-REMOVE-FACET-LIMITING-WORKAROUND.md` — the facet limiting workaround removal may have introduced or exposed this bug.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: This is ONLY reproducible on hykudev with feature/hyku-v7.2.0-upgrade
- Main branch and production VM work correctly — no modal, full expansion via redirect
- The "no modal, redirect to page" behavior is the expected/correct behavior
- The modal behavior introduced on hykudev appears to be an unintended side effect

⚠️ **GOTCHA 2**: `config.default_facet_limit = 5` should NOT apply to facet expansion queries
- The limit applies to collapsed sidebar display (correct behavior)
- Expansion views should show ALL values regardless of the default limit
- Check if Hyku PR #3369 or workaround removal changed how `limit: true` interacts with Blacklight's facet expansion

### Files to Check First

| File | Purpose | Key Section |
|---|---|---|
| `hyrax-webapp/config/initializers/blacklight.rb` | Blacklight config — facet more link | Facet config, `facet_more` settings |
| `config/initializers/catalog_controller_decorator.rb` | WVU customizations | Any facet-related overrides |
| `hyrax-webapp/app/controllers/catalog_controller.rb` | Hyrax base catalog controller | Blacklight 8+ changes, facet actions |
| `hyrax-webapp/config/blacklight.yml` or equivalent | Blacklight config file | Pagination settings |

---

## Acceptance Criteria

- [ ] Root cause identified (which file/config change on hykudev broke the working main branch behavior)
- [ ] Modal behavior replaced with redirect to `/catalog/facet/*` page showing all values
- [ ] Verify fix works on hykudev against feature/hyku-v7.2.0-upgrade
- [ ] Confirm not a regression from related_task (facet limiting workaround removal)
- [ ] No other facets affected by the fix

---

## Stop Conditions — escalate to user immediately if:

- Fix causes new failures in facet display on search results page (AC #5 regression)
- Same issue persists after two debugging attempts
- Root cause is in a shared concern, base class, or factory used across many specs
- A database migration is needed that wasn't anticipated
- Any architectural decision is required

---

## Dependencies

**Blocked by**: None — can investigate independently of Praneeth's date range work  
**Blocks**: HYKU-7.2.0 upgrade testing (must resolve before production deployment)  
**Related tasks**: `2026-10-28-HIGH-REFACTOR-REMOVE-FACET-LIMITING-WORKAROUND.md`

---

## Progress / Resume

*Updated by the session whenever it stops before the task is finished (SESSION_CLOSEOUT.md section 2a).*

- **Done so far:**
- **Next step:**
- **Last verified fact (with command):**
- **Local-only commits (`git log --oneline origin/main..HEAD`):**
