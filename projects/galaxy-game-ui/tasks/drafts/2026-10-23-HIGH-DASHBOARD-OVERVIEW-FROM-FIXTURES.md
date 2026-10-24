---
status: drafts
resume: false
priority: HIGH
type: feature
system_domain: CONTROLLERS / VIEWS
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 DRAFT REVIEW CHECKLIST

- [ ] Acceptance Criteria are clear and measurable
- [ ] Task scope is well-bounded (header + two panels on the existing controller/view)
- [ ] Dependencies listed and verified
- [ ] No contradictions in specification
- [ ] Review agent disposition: GO

> Drafted by a web review/planning agent. Facts are labelled VERIFIED (read from GitHub `review/qwen-ui-prototype`) or REPORTED (from a log or draft, not re-checked). Anything marked `[FILL IN]` must be confirmed by an agent that can read the local repo.

---

## 🔴 Agent Dispatch Interface (Required)

```
You are **Implementation Agent**.

Project: galaxy-game-ui
Task: <agent-tasks>/projects/galaxy-game-ui/tasks/drafts/[FILENAME].md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy-game-ui/tasks/drafts/[FILENAME].md \
         projects/galaxy-game-ui/tasks/active/[FILENAME].md
  Then open the moved file and change: status: drafts → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: drafts → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy-game-ui/tasks -name "[FILENAME].md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/galaxy-game-ui/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

**IMPORTANT: Do not modify or abbreviate the text above.**

---

# TASK: Dashboard Overview From Fixtures (T3c)

## Prerequisites — READ FIRST

1. `agent-tasks/rules/GUARDRAILS.md`
2. `agent-tasks/projects/galaxy-game-ui/README.md`

## Context

First real dashboard, kept deliberately small: a header, a **Systems** panel and a **Connections** panel, all fed by the fixtures from the loader task. Inventory, market, missions and ships panels come later as separate small tasks, each with its own fixture file.

Facts:
- VERIFIED: `DashboardController` (`index`) and a stub `dashboard/index.html.erb` both exist on the branch. Controller: empty `index` action. View: Rails-generated placeholder with `<h1>Dashboard#index</h1>`.
- VERIFIED: the app uses Rails 8 built-in auth, not Devise. There is a `Current` model; there is no `current_user` helper unless defined. The `User` role enum uses `prefix: true`, so predicates are `role_admin?` / `role_player?`.
- VERIFIED: with `resource :session`, the sign-in route helper is `new_session_path`.
- VERIFIED: The dashboard route exists in `config/routes.rb` at line 5: `get "dashboard", to: "dashboard#index"`.

## Problem Statement

The dashboard is a placeholder. The UI needs a first screen that proves the data flow (fixtures → loader → controller → view) and the CSS tokens, using real wayfinding data.

## Implementation Steps

1. **Controller** (`app/controllers/dashboard_controller.rb`, existing): in `index`, call `GameDataJsonLoader.call(:systems)` and `.call(:links)`. Set `@systems`, `@links` (empty arrays on error) and `@data_error` (message or nil). Keep the default authentication requirement; do not skip it.
2. **View** (`app/views/dashboard/index.html.erb`, existing): replace the stub with:
   - **Header**: page title "Dashboard" and the signed-in user's email (`Current.user&.email_address`; VERIFIED: Current model exists with `.user` delegation from `.session`).
   - **Systems panel**: one row per system: name, kind, identifier if present, and a "placeholder" badge when `source` is `placeholder`.
   - **Connections panel**: one row per link: `from → to`, type (`natural` / `artificial` / `unknown`). Resolve system ids to names in the view or controller, not in the JSON.
   - If `@data_error` is present, show a short message in place of the panels (no 500).
3. **Styling**: use only the tokens from the CSS foundation task (`var(--panel)`, `var(--line)`, `var(--accent)`, `var(--muted)`, and so on) and plain Tailwind layout utilities. No inline colors, no new stylesheets.
4. **Verify** in the container and the browser: `GET /dashboard` while signed out (should redirect to sign-in) and signed in (should render). Save a screenshot in `summaries/` for human approval.

## Gotchas

1. **One dashboard only.** No admin/player split. Role-specific views are a later task (use `role_admin?` then, not `admin?`).
2. **No new data sources.** Only `systems` and `links`. Other panels are out of scope.
3. **No `current_user`, no `session_new_path`, no Devise.** Use `Current.user` and `new_session_path`.
4. **No hard-coded colors**; use the tokens or Tailwind utilities.
5. **Symbol keys** from the loader; do not call `dig('name')`.
6. **Run everything in the container.** VERIFIED: the UI app's container is `galaxy_game` (docker-compose.yml).

## Acceptance Criteria

- [ ] `GET /dashboard` signed out redirects to the sign-in page
- [ ] `GET /dashboard` signed in returns 200 and shows Sol, Eden and the generated systems
- [ ] Connections panel lists every link with resolved system names
- [ ] With a deliberately missing fixture, the page shows the error message and still returns 200
- [ ] No inline colors or new stylesheets in the diff
- [ ] Screenshot saved in `summaries/` and **approved by the human**
- [ ] `git diff --stat` pasted; only the controller, the view (and routes if needed) changed; nothing staged or committed

## Stop Conditions

- The CSS foundation task or the loader task is not completed and approved → do not start.
- The page raises the same error twice → stop and report the error text.
- Any step needs a new model, migration or fixture file → stop and ask.

## Dependencies

**Depends on**: `2026-10-23-HIGH-UI-CSS-FOUNDATION` and `2026-10-23-HIGH-JSON-FIXTURE-LOADER-SYSTEMS` (both completed and approved).
**Blocks**: later dashboard panels (inventory, market, missions, ships) and the wayfinding view.
