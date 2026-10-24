---
status: drafts
resume: false
priority: HIGH
type: feature
system_domain: VIEWS / ASSETS (CSS only)
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 DRAFT REVIEW CHECKLIST

- [ ] Acceptance Criteria are clear and measurable
- [ ] Task scope is well-bounded (one CSS entry file + layout link; no new pages)
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

# TASK: UI CSS Foundation (T3a) — One Tailwind Entry File With Galaxy Game Tokens

## Prerequisites — READ FIRST

1. `agent-tasks/rules/GUARDRAILS.md`
2. `agent-tasks/projects/galaxy-game-ui/README.md`

## Context

The first attempt failed partly because its CSS never loaded: a `tailwind.config.js` that Tailwind v4 ignores, `.scss` files with no Sass gem, and a hand-compiled file in `public/assets` that nothing linked. This task fixes the foundation **once**, before any page is built on it.

Facts:
- VERIFIED: Gemfile has `tailwindcss-rails ~> 4.6` (Tailwind v4) and no Sass gem.
- VERIFIED: `app/assets/tailwind/application.css` is the file the gem builds; it still contains the WVU brand palette in an `@theme` block.
- VERIFIED: `app/assets/stylesheets/application.css` also exists. `[FILL IN]` contents and whether the layout links it.
- REPORTED: the login/session views still use `wvu-*` classes. The first attempt made the login form unreadable by removing those tokens.
- REPORTED (EVE Dashboard source, `style.css`): `--bg #0e1114`, `--panel #161b21`, `--panel2 #1d242c`, `--line #2a333d`, `--text #d8dee6`, `--muted #8a97a6`, `--accent #4aa8ff`, `--green #3fb950`, `--red #f85149`, `--gold #e3b341`.

## Problem Statement

The app has no Galaxy Game theme, and there is no confirmed working CSS path. Pages cannot be styled consistently until one entry file builds, loads in the browser, and defines the shared tokens.

## Implementation Steps

1. **Inspect** (read-only; report before editing): the layout's `stylesheet_link_tag` line(s), `app/assets/stylesheets/application.css`, and which files use `wvu-` classes (`grep -rn "wvu-" app/`). `[FILL IN]` results.
2. **Add Galaxy tokens** to `app/assets/tailwind/application.css` as CSS custom properties in `:root` (names exactly: `--bg --panel --panel2 --line --text --muted --accent --green --red --gold`) using the values above. Keep `--accent` at `#4aa8ff` until the human picks a Galaxy Game accent. Add a body rule using `var(--bg)` / `var(--text)`.
3. **Keep the WVU tokens** for now. Remove them only in a later task, after `grep` shows nothing uses them (login views included).
4. **Make sure the built file is linked.** The layout must load the gem's build output (`app/assets/builds/tailwind.css`). `[FILL IN]` the exact layout edit after step 1.
5. **Rebuild inside the container** with the gem's task (`bin/rails tailwindcss:build`) and confirm the output file contains `--bg`.
6. **Verify and capture**: load `/` and `/session/new` in the browser. Save screenshots of both in `summaries/` and ask the human to approve them.

## Gotchas

1. **No `.scss`.** There is no Sass gem. Plain `.css` only.
2. **No `public/assets` tricks, no manifest deletes, no `-c config/tailwind.config.js` flag, and no changes to `scripts/startup.sh`.** One entry file, built by the gem.
3. **Do not remove `wvu-*` tokens in this task.** The login page depends on them (REPORTED).
4. **`app/assets/builds/tailwind.css` is generated.** `[FILL IN]` whether it is tracked. If it is, do **not** untrack it here; report it so the human can decide.
5. **Run everything in the container** (`docker exec <container> bash -c 'unset DATABASE_URL && ...'`). `[FILL IN]` the container name: the task drafts say `galaxy_game`; GUARDRAILS is reported to say `web`.
6. **Hard-coded colors are not allowed** in anything this task adds; use the variables.

## Acceptance Criteria

- [ ] Exactly one Tailwind/CSS entry file defines the Galaxy tokens (`app/assets/tailwind/application.css`)
- [ ] The built `app/assets/builds/tailwind.css` contains all ten variables
- [ ] The layout links the built file (exact line pasted in the report)
- [ ] `/` and `/session/new` load with no errors; the login form is still readable
- [ ] Screenshots of both pages saved in `summaries/` and **approved by the human**
- [ ] No `.scss` files, no `public/assets` files, no `startup.sh` change in the diff
- [ ] `git diff --stat` pasted; only intended files changed; nothing staged or committed

## Stop Conditions

- The built CSS does not contain the tokens after **two** rebuild attempts → stop and report.
- The login form becomes unreadable → revert the CSS change and report.
- Any step needs a file outside the list above → stop and ask.

## Dependencies

**Depends on**: none for the CSS itself (the landing and seeds tasks are separate).
**Blocks**: `2026-10-23-HIGH-DASHBOARD-OVERVIEW-FROM-FIXTURES` and every later styled page.
