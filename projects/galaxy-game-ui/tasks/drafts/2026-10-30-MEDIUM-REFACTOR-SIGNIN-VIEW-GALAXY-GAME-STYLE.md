---
status: drafts
resume: false
priority: MEDIUM
type: refactor
system_domain: VIEWS (sessions/new only)
mvp_alignment: OTHER
local_worker_safe: true
last_updated: 2026-10-30
---

> Drafted by a web review/planning agent. Facts are labelled VERIFIED (read from GitHub `review/qwen-ui-prototype` or seen in a screenshot) or REPORTED (from a log or draft, not re-checked). Anything marked `[FILL IN]` must be confirmed by an agent with full repo access before dispatch. Left in `drafts/`; the human decides when to assign it.

---

## 🔴 Agent Dispatch Interface (Required)

```
You are **Implementation Agent**.

Project: galaxy-game-ui
Task: <agent-tasks>/projects/galaxy-game-ui/tasks/drafts/2026-10-30-MEDIUM-REFACTOR-SIGNIN-VIEW-GALAXY-GAME-STYLE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy-game-ui/tasks/drafts/2026-10-30-MEDIUM-REFACTOR-SIGNIN-VIEW-GALAXY-GAME-STYLE.md \
         projects/galaxy-game-ui/tasks/active/2026-10-30-MEDIUM-REFACTOR-SIGNIN-VIEW-GALAXY-GAME-STYLE.md
  Then open the moved file and change: status: drafts → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: drafts → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy-game-ui/tasks -name "2026-10-30-MEDIUM-REFACTOR-SIGNIN-VIEW-GALAXY-GAME-STYLE.md"
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

# TASK: Sign-in View — Remove WVU Text and Branding, Restyle With Galaxy Game Tokens

## Prerequisites — READ FIRST

1. `agent-tasks/rules/GUARDRAILS.md`
2. `agent-tasks/projects/galaxy-game-ui/README.md`
3. `agent-tasks/projects/galaxy-game-ui/SESSION_GUIDANCE.md`

## Context

The layout shell is Galaxy Game now (T3a-2, commit `c01b23d`, approved by the human 2026-10-30). The sign-in page inside it is still the WVU Libraries app's.

Facts (VERIFIED from `galaxy-game-ui/app/views/sessions/new.html.erb` on the branch, 33 lines, and from a signed-out screenshot of `/session/new`):
- `content_for :title` is "Sign in — WVU Libraries Systems".
- The subtitle line reads "WVU Libraries Systems — Donor Check-In".
- The card is `bg-white` with `border-wvu-warm-gray-light`; the heading is `text-wvu-blue`; labels use `text-wvu-warm-gray-dark`; the inputs, submit button and the "Forgot password?" link use `wvu-*` colors (accent blue, gold hover).
- On the dark page the white card is readable but clashes with the Galaxy Game look; the input placeholder text is very faint (light gray on white).
- The form is `form_with url: session_path` with `email_address` and `password` fields. **Its behaviour is correct and must not change.**
- Design tokens already exist on the branch (T3a): `--bg`, `--panel`, `--panel2`, `--line`, `--text`, `--muted`, `--accent`, `--green`, `--red`, `--gold`. `--accent` (`#4aa8ff`) is a placeholder pending a human decision, so use the token, never the hex.

Wiki rule (SESSION_GUIDANCE section 3): no intent for sign-in copy has been looked up. `[FILL IN: confirm whether the wiki or docs say anything about sign-in text; if not, the subtitle below is a placeholder and the planning agent adds an OPEN entry to NEEDS_REVIEW.md]`.

## Problem Statement

The sign-in page still shows WVU text and a light WVU-styled card. It should look like part of Galaxy Game, using the existing tokens, without changing how sign-in works.

## Implementation Steps

1. **Inspect** (read-only): list every `wvu` string in `app/views/sessions/new.html.erb`. Also list (do not edit) every `wvu` string in `app/views/passwords/`.
2. **Title**: change the `content_for :title` text to "Sign in — Galaxy Game".
3. **Subtitle**: replace "WVU Libraries Systems — Donor Check-In" with the placeholder "Sign in to continue".
4. **Card**: replace the white WVU card with a dark card using tokens (panel background, `--line` border, `--text` text). Keep `mx-auto max-w-md` and the rounded corners.
5. **Heading, labels, subtitle**: heading and labels use `--text`; subtitle and input placeholders use `--muted`.
6. **Inputs**: `--panel2` background, `--line` border, `--text` text, `--accent` for the focus border and ring.
7. **Submit button**: `--accent` background with readable text on it (check contrast in the screenshot); a visible hover state using tokens.
8. **"Forgot password?" link**: `--accent`.
9. **Rebuild** the Tailwind CSS in the `galaxy_game` container (the same way as in T3a) and reload the page.
10. **Capture** screenshots in `summaries/` for human approval: `/session/new` signed out, and `/session/new` after a failed sign-in (to see the flash message on the dark page).

## Gotchas

1. **View file only.** No controller, route, model or JavaScript changes. Keep `form_with url: session_path`, the field names, `autocomplete`, `autofocus`, `maxlength` and the submit label exactly as they are.
2. **Do not delete `wvu-*` tokens or the passwords views.** The passwords pages are out of scope and will still look WVU-styled; that is a separate small task. The "Forgot password?" link must still reach them.
3. **No hard-coded colors.** Use the tokens; if a needed color has no token, stop and ask.
4. **The built CSS changes.** Rebuilding rewrites `app/assets/builds/tailwind.css`. Whether to commit it is the human's decision; report it in `git diff --stat`, do not commit.
5. **Out of scope** (each is its own task): the WVU favicon, the unstyled landing page, the passwords pages.
6. **Run everything in the UI container, `galaxy_game`.** `web` is the main game's container; never run UI commands there.

## Acceptance Criteria

- [ ] `grep -in "wvu" app/views/sessions/new.html.erb` returns nothing
- [ ] `/session/new` shows no WVU text and no white WVU card (screenshot)
- [ ] Heading, labels, placeholders and the submit button are all readable on the new background (screenshot)
- [ ] A failed sign-in shows the flash message readably (screenshot)
- [ ] Signing in with a valid account still works, and the "Forgot password?" link still reaches the passwords page (manual check, noted in the report)
- [ ] Screenshots saved in `summaries/` and **approved by the human**
- [ ] `git diff --stat` pasted; only `sessions/new.html.erb` and the rebuilt `tailwind.css` changed; nothing staged or committed

## Stop Conditions

- Any step needs a controller, route, new asset or new token → stop and ask.
- Sign-in stops working → revert the view change and report with raw output.
- The same check or command is repeated three times with no new information → stop and report.

## Dependencies

**Depends on**: `2026-10-23-HIGH-UI-CSS-FOUNDATION` and `2026-10-23-HIGH-UI-LAYOUT-SHELL` (both approved).
**Blocks**: nothing. Independent of `2026-10-23-HIGH-JSON-FIXTURE-LOADER-SYSTEMS` (T3b); the human decides the order.
