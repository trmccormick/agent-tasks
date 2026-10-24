---
status: drafts
resume: false
priority: HIGH
type: refactor
system_domain: VIEWS (layout only)
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 DRAFT REVIEW CHECKLIST

- [ ] Acceptance Criteria are clear and measurable
- [ ] Task scope is well-bounded (one layout file; no new pages, no new routes)
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

# TASK: UI Layout Shell (T3a-2) — Replace the WVU Masthead/Footer With a Galaxy Game Shell

## Prerequisites — READ FIRST

1. `agent-tasks/rules/GUARDRAILS.md`
2. `agent-tasks/projects/galaxy-game-ui/README.md`

## Context

The layout is still the WVU Libraries app's. Every page, including the landing page, shows WVU branding and a light background, no matter what the page itself contains. This task replaces only the **shell** so the Galaxy Game tokens from the CSS foundation become visible.

Facts (all VERIFIED from `app/views/layouts/application.html.erb` on the branch):
- `<body class="min-h-screen flex flex-col bg-wvu-warm-gray-light">`
- A WVU masthead: `bg-wvu-blue` header, a link to `library.wvu.edu`, `image_tag "logo.png"` with alt "West Virginia University Libraries", and a gold accent rule.
- The masthead's nav already has the right logic: `authenticated?`, `Current.user&.email_address`, a Sign out `button_to`, and a Sign in `link_to new_session_path`. **Keep that logic.**
- `<main class="flex-1 woodburn-hall">` wrapping a `max-w-5xl` container.
- Flash messages styled with `wvu-*` colors.
- A WVU footer with the library's address, phones and links.
- `app/assets/images/logo.png` is the WVU Libraries logo, not a Galaxy Game asset.

## Problem Statement

Pages inherit WVU branding and light colors from the layout, so nothing can look like Galaxy Game until the shell changes.

## Implementation Steps

1. **Inspect** (read-only): confirm the layout matches the facts above and list every `wvu`/`woodburn` string in `app/views/layouts/` and `app/views/sessions/`, `app/views/passwords/` (`grep -rn "wvu\|woodburn" app/views`). VERIFIED: 26 occurrences in application.html.erb (lines 24, 26, 35, 38, 43, 47, 51, 56, 66, 70, 74-76, 83, and others). Sessions/passwords views contain additional `wvu-` references for login form styling.
2. **Body**: replace the body classes with token-based ones (for example `bg-[var(--bg)] text-[var(--text)]`), keeping `min-h-screen flex flex-col`.
3. **Header**: replace the WVU masthead with a simple topbar: the text brand "Galaxy Game" linking to `root_path`; keep the sign-in/sign-out/email logic exactly as it is, restyled with token colors. Do not reference `logo.png`.
4. **Main**: remove `woodburn-hall`; keep the container and `yield`.
5. **Flash messages**: keep `notice` / `alert` rendering; restyle with token colors (`--accent` for notices, `--gold` or `--red` for alerts).
6. **Footer**: replace the WVU footer with a one-line Galaxy Game footer ("Galaxy Game UI prototype").
7. **Check the login pages** (`/session/new`, and the passwords pages if reachable) in the browser. They still contain WVU classes. They must remain readable on the new background.
8. **Capture**: save screenshots of `/`, `/session/new`, and a signed-in page in `summaries/` for human approval.

## Gotchas

1. **Do not delete `wvu-*` tokens, `logo.png` or the sessions/passwords views.** Login depends on them. Removing them is a later cleanup, only after step 1's list shows nothing uses them.
2. **Layout file only.** No new routes, controllers, pages or JavaScript.
3. **No hard-coded colors** in the new markup; use the tokens from the CSS foundation task.
4. **Keep authentication behaviour**: the Sign out button must still `DELETE session_path`.
5. **Run everything in the UI container, `galaxy_game`**. GUARDRAILS says `web`, but that is the **main game's** container; never run UI commands there.

## Acceptance Criteria

- [ ] `grep -in "wvu\|woodburn" app/views/layouts/application.html.erb` returns nothing
- [ ] `/` renders with the new shell and no WVU masthead, footer or address
- [ ] Sign in and sign out both work (manual check, noted in the report)
- [ ] Flash messages still render
- [ ] `/session/new` is readable on the new background (screenshot)
- [ ] Screenshots of `/`, `/session/new` and a signed-in page saved in `summaries/` and **approved by the human**
- [ ] `git diff --stat` pasted; only the layout (and token additions, if any) changed; nothing staged or committed

## Stop Conditions

- The login page becomes unreadable → revert the layout change and report with a screenshot.
- Any step needs a new route, controller or asset → stop and ask.
- The CSS foundation task is not completed and approved → do not start.

## Dependencies

**Depends on**: `2026-10-23-HIGH-UI-CSS-FOUNDATION` (completed and approved).
**Blocks**: `2026-10-23-HIGH-DASHBOARD-OVERVIEW-FROM-FIXTURES` and the landing page's visual pass.
