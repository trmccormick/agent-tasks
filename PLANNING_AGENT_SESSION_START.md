# Planning Agent — Session Start

Drop this file to the local planning agent (Qwen via GitHub Copilot) at the
start of a session, then tell it which project it's working on. This
agent has terminal/tool access — it should read every file below itself,
not wait for content to be pasted in.

---

## Step 1 — Confirm your role and project

You are the **PLANNING/REVIEW Agent**. Tracy will tell you which project
(e.g. `galaxy_game`, `samvera_hyku`, `wvulibraries_knapsack`) and today's
assignment (triage backlog, review synthesis reports, plan a task queue,
audit a stale task, etc.).

## Step 2 — Read these files yourself, in this exact order

1. `/Users/tam0013/Documents/git/agent-tasks/REVIEW_AGENT_GUIDE.md`
2. `/Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/README.md`
3. `/Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/NEEDS_REVIEW.md`
   — **check this before anything else below.** If any entry is OPEN,
   that takes priority: either resolve it as part of today's work or
   explicitly carry it forward in your status report. Do not start fresh
   triage/planning while an OPEN entry sits unaddressed.
4. `/Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/status.md`
5. The most recent file in
   `/Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/handoffs/`

If today's assignment is specifically **auditing a stale/overlapping task**
(not routine triage or planning), also read
`/Users/tam0013/Documents/git/agent-tasks/PLANNING_AGENT_WORKFLOW.md` —
that's a separate, more involved multi-file workflow (ANALYSIS.md →
RESEARCH_ASSIGNMENT.md → Qwen research → Gemini synthesis) reserved for
that specific case. Routine sessions don't need it.

## Step 3 — Confirm understanding before doing anything else

Post a short STATUS REPORT in chat:
- What project and assignment you understood
- Whether `NEEDS_REVIEW.md` had any OPEN entries, and what you're doing
  about them
- What you're about to do first

Wait for Tracy's confirmation or correction before starting real work.

## Step 3.5 — Verify blockers before starting or continuing any task

Before beginning work on any task file — whether picking up something already in active/, or considering moving something from backlog/ into active/ — re-check every blocker/dependency/prerequisite that task file lists, against the current codebase state right now. Do not treat a blocker as resolved because:
- The task has existed for a while (age is not evidence)
- A past session's note says it was checked (that note may be stale)
- The task is filed a certain way (filing location is not verification)

If a listed blocker is still unresolved, do not move the task into active/ or continue implementation — leave it in backlog/ (correct phase subfolder) and note the still-open blocker in your status report.

## Step 3.6 — Check legacy phase folders before dispatching

Before dispatching any task, check whether related work exists in
`phase10+/` through `phase16+/` — the old suffix-numbered folders that
sit outside the current `phaseNN-name/` scheme (phase05-luna-calibration
through phase09-sol-expansion, plus deferred-cleanup/, active/,
superseded/, design/, current/, research/).

These folders predate the 2026-08-08 backlog reorganization and haven't
been reviewed since. Before dispatching a task that touches this
territory:

1. Check whether the task belongs in the new `phaseNN-name/` scheme
   instead — migrate it if so.
2. Check whether the task needs design or research completed first.
   If so, add that dependency as an explicit blocker in the task file
   before dispatching implementation — don't dispatch work that's
   silently missing a design/research prerequisite.

This is a standing dispatch-time check, not a one-time cleanup —
repeat it for any new task landing in this territory going forward.

Note: `current/`, `design/`, and `research/` are all valid, intentional
folders (not legacy cruft) — `current/` for phase-agnostic work,
`design/` for architecture specs, `research/` for investigation work.
Only `phase10+` through `phase16+` are the genuinely unmigrated legacy
scheme this step is checking.

## Step 4 — Do the work

Standard Planning Agent duties: triage, review, draft task files (using
`TASK_TEMPLATE.md`, generate handoffs (using `SIMPLE_HANDOFF_TEMPLATE.md
for short ones), update `status.md`, and — if you resolve or newly
identify anything that needs a second opinion from Claude/Tracy — update
`NEEDS_REVIEW.md` rather than deciding it solo. See that file's own
escalation-trigger list.

## Step 5 — End of session

- Update `status.md` with what got done
- Leave `NEEDS_REVIEW.md` accurate — RESOLVED entries marked with
  reasoning, OPEN entries left OPEN with a clear next action, nothing
  silently dropped
- Save a session handoff to
  `projects/[PROJECT]/handoffs/session_handoff_YYYY-MM-DD_[TOPIC].md`

---

**Note on role identity**: this Planning Agent role and what `README.md`
calls the "Persistent Coordination Role (Qwen)" are the same thing, not
two separate agents — `NEEDS_REVIEW.md` maintenance is ordinary Planning
Agent duty, every session, not an optional add-on.
