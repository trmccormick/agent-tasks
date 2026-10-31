---
status: draft
resume: false
priority: MEDIUM
type: refactor
system_domain: VIEWS (sessions/new only)
mvp_alignment: OTHER
local_worker_safe: true
last_updated: 2026-10-30
---

## 🟡 DRAFT STATUS NOTE (for tasks in tasks/drafts/)

**This section applies ONLY if status: draft above.**

This task is in draft status and awaiting review approval. It has NOT yet been approved for backlog.

**Current State**: In `tasks/drafts/` committed to git  
**Next Step**: Review agent assessment → Human approval → Move to `tasks/backlog/` by human  
**Do NOT dispatch to implementation agents** until this task is in `tasks/backlog/` with `status: backlog`

See PLANNING_AGENT_SESSION_START.md § "Task Creation Workflow" for full procedure.

## 📋 DRAFT REVIEW CHECKLIST (Human — before approving move from drafts/ to backlog/)

**APPLIES ONLY IF status: draft above. Use this checklist BEFORE moving task to tasks/backlog/.**

After review agent has returned assessment:

- [ ] Acceptance Criteria are clear and measurable (no ambiguity)
- [ ] Task scope is well-bounded (not too broad, not overlapping with active tasks)
- [ ] Dependencies listed and verified against status.md (all prerequisites exist)
- [ ] No contradictions in the task specification
- [ ] Architecture aligns with current codebase patterns (review agent verified)
- [ ] Review agent disposition is GO (not REVISE or STOP)

**Once ALL boxes checked:** Approve by moving task from `tasks/drafts/` to `tasks/backlog/[SUBFOLDER]/` and changing `status: draft → status: backlog`.

> Drafted by a web review/planning agent. Facts are labelled VERIFIED (read from GitHub `review/qwen-ui-prototype` or seen in a screenshot) or REPORTED (from a log or draft, not re-checked). Anything marked `[FILL IN]` must be confirmed by an agent with full repo access before dispatch. Left in `drafts/`; the human decides when to assign it.

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

Project: [project_name]
Task: <agent-tasks>/projects/[project]/tasks/backlog/[SUBFOLDER]/[FILENAME].md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/[project]/tasks/backlog/[SUBFOLDER]/[FILENAME].md \
         projects/[project]/tasks/active/[FILENAME].md
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
  Summaries path: <agent-tasks>/projects/[project]/summaries/
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

Wiki rule (SESSION_GUIDANCE §3): no intent for sign-in copy found. Subtitle is a placeholder. Verified outcome: all 37 pages under `docs/wiki/` in `galaxyGame` were searched on 2026-10-30 for sign-in, log-in, login-page, landing and splash wording with no match (VERIFIED). No deeper docs (`docs/design/`, `docs/architecture/`, `docs/gameplay/`, `docs/developer/`) were searched. "Sign in to continue" is a temporary placeholder pending a human decision; an OPEN entry for this was added to `NEEDS_REVIEW.md` on 2026-10-31.

## Problem Statement

The sign-in page still shows WVU text and a light WVU-styled card. It should look like part of Galaxy Game, using the existing tokens, without changing how sign-in works.

## Implementation Steps

1. **Inspect** (read-only): list every `wvu` string in `app/views/sessions/new.html.erb` only.
2. **Title**: change the `content_for :title` text to "Sign in — Galaxy Game".
3. **Subtitle**: replace "WVU Libraries Systems — Donor Check-In" with the placeholder "Sign in to continue".
4. **Card**: replace the white WVU card with a dark card using tokens (panel background, `--line` border, `--text` text). Keep `mx-auto max-w-md` and the rounded corners.
5. **Heading, labels, subtitle**: heading and labels use `--text`; subtitle and input placeholders use `--muted`.
6. **Inputs**: `--panel2` background, `--line` border, `--text` text, `--accent` for the focus border and ring.
7. **Submit button**: `--accent` background with readable text on it (check contrast in the screenshot); a visible hover state using tokens.
8. **"Forgot password?" link**: `--accent`.
9. **Rebuild** the Tailwind CSS inside the `galaxy_game` container (the UI app's container; never use `web`, which is the main game server). Run:

```bash
docker exec -it galaxy_game bash -c 'rails tailwindcss:build'
```

Then confirm the new token utility classes appear in `app/assets/builds/tailwind.css` (grep for two or three of them), because Tailwind v4 drops classes it does not find. Reload the page.
10. **Capture** screenshots in `summaries/` for human approval: `/session/new` signed out, and `/session/new` after a failed sign-in (to see the flash message on the dark page).

## Gotchas

1. **View file only.** No controller, route, model or JavaScript changes. Keep `form_with url: session_path`, the field names, `autocomplete`, `autofocus`, `maxlength` and the submit label exactly as they are.
2. **Do not delete `wvu-*` tokens or the passwords views.** The passwords pages are out of scope and will still look WVU-styled; that is a separate small task. The "Forgot password?" link must still reach them.
3. **No hard-coded colors.** Use the tokens; if a needed color has no token, stop and ask.
4. **The built CSS changes.** Rebuilding rewrites `app/assets/builds/tailwind.css`. If the dev watcher is running it rewrites this file on every template save, so expect it to show as modified. Whether to commit it is the human's decision; report it in `git diff --stat`, do not commit.
5. **Out of scope** (each is its own task): the WVU favicon, the unstyled landing page, the passwords pages.
6. **Run everything in the UI container, `galaxy_game`.** `web` is the main game's container; never run UI commands there.

## Acceptance Criteria

- [ ] `grep -in "wvu" app/views/sessions/new.html.erb` returns nothing
- [ ] The new token utility classes appear in `app/assets/builds/tailwind.css` after the rebuild (grep output pasted)
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
