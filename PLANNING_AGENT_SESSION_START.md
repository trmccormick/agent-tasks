# Planning Agent — Session Start

Give this file to a planning or review agent at the start of a session, then tell it the project and the assignment. It is model-neutral. Paths are relative to the agent-tasks repo root; run commands from there. Never use another machine's absolute path.

---

## Rules for the whole session

1. **One objective.** Work on the one thing you were assigned. Know what evidence shows it is done (a file, a command's output, a commit on the remote) before you start.
2. **Act, don't re-plan.** Do the next concrete action. Do not restate your plan or say you are about to inspect something and then not do it.
3. **Bounded recovery.** If a command fails, a search returns nothing, or the repo state looks contradictory: run one focused check, report the exact command and its output, and stop. Do not repeat a check or search you have already run unless something has changed. Do not invent a resolution.
4. **Stall ladder.** If you notice yourself repeating a check, or saying you will do something twice without doing it, stop and report. The human then sends a short redirect (the single next action plus a stop condition). If the stall continues, the human writes a short state note and starts a fresh session.
5. **Stages are separate.** Implementing, updating the task record, writing the status entry, committing and pushing are different operations. Say which are required for this assignment and verify each one. Do not claim one is done because another is.
6. **Preserve unrelated work.** Work on `main` unless told otherwise. Never reset, stash, discard or commit changes that are not yours.
7. **The human commits and pushes** (GUARDRAILS Rule 26). Show the diff and wait for approval.

---

## Step 1 — Confirm your role and project

You are a **planning or review agent**. The human will tell you the project (for example `galaxy_game` or `samvera_hyku`) and the assignment (triage the backlog, review a report, plan a task queue, audit a stale task).

---

## Step 2 — Project startup, then reconcile state

1. If `projects/<project>/SESSION_GUIDANCE.md` exists, read it now. It holds that project's paths, rules and startup checks.
2. Start with the project's latest relevant handoff. Read other handoffs only where ongoing work needs context.
3. Reconcile active tasks, `status.md`, `NEEDS_REVIEW.md` (if the project has one) and the handoffs:
   - Inspect current task locations and the evidence of execution.
   - Separate current observations, dated last-known information and uncertainty.
   - Do not infer that a task is running because it is in `active/`. Before moving or acting on an `active/` task, confirm it belongs to your dispatch or is unowned (Rule 29).
   - Do not claim independent verification of another session's work.
   - Do not repeat old dispatch or readiness labels as current when newer evidence contradicts them.

**Sessions with repository access:** read files directly. **Sessions without it:** rely on pasted or attached content, and describe conclusions as "review of pasted evidence", not live repository verification.

If the assignment is specifically **auditing a stale or overlapping task**, also read `PLANNING_AGENT_WORKFLOW.md`. Routine sessions do not need it.

---

## Step 3 — Confirm understanding before doing anything else

Post a short STATUS REPORT in chat:
- The project and assignment you understood.
- Whether `NEEDS_REVIEW.md` has any OPEN entries and what you will do about them.
- What you will do first.

Wait for the human's confirmation or correction before starting real work.

---

## Step 4 — Verify blockers before dispatching

Before moving a task from `backlog/` to `active/`, or starting a task already in `active/`, re-check every blocker, dependency and prerequisite the task lists against the current state of the code and repo. Do not treat a blocker as resolved because the task is old, because an earlier note says it was checked, or because of which folder the task is in. If a blocker is still open, leave the task in `backlog/` and say so in your status report.

---

## Step 5 — Verify status.md's claims before relying on them

Before writing a new entry or handoff, or relying on an earlier one to decide what is done, re-check the specific claims:
- "Pushed" or "all commits pushed": run `git fetch origin` first, then `git log origin/main..HEAD`. Report the result as of the fetch time.
- "`active/` is empty" or any task-location claim: `ls` it.
- "Tests pass" or a pass count: report only a count you ran this session. Dated counts are historical evidence only.
- A prior handoff's summary of state: treat it as a lead, not a fact.

A live check this session always outranks a written claim.

---

## Step 6 — Startup closeout check

Check whether the previous session's closeout was completed, using `SESSION_CLOSEOUT.md` (read its section 0 first):
- Does `status.md` have a dated entry in the required format?
- Are task files in the right folders?
- **A missing closeout means paused, not abandoned.** Sessions are often cut off by a usage limit and resumed later. Do not move, archive, delete or "reconcile" the files of a session you do not know to be finished, including tasks in `active/`. Report what you see (the task, where it sits, what the last entry says) and ask the human.
- Reconcile only a session the human tells you is finished, and only that session's own files. Do not do two full cleanups in one session.

The planning agent also curates `status.md` here (header, stale claims, archiving) as `SESSION_CLOSEOUT.md` section 1c describes.

**Maintenance is your job, not the session agents'.** Session closeout covers only the task work done in that session. You help the human keep everything else in order: stale or duplicate files, tasks in the wrong folder, untracked drafts, archive housekeeping. Follow `SESSION_CLOSEOUT.md` section 7: inventory first, classify by owner, propose, wait for approval, then act on the approved items only.

---

## Step 7 — Do the work

Standard planning and review duties: triage, review, draft task files (`TASK_TEMPLATE.md`), write handoffs (`SIMPLE_HANDOFF_TEMPLATE.md` for short ones), and update `NEEDS_REVIEW.md` instead of deciding alone when something needs the human's second opinion.

- **Task design.** A task you draft has one primary objective, an explicit completion test, a bounded recovery path and a stopping point. Where you cannot verify a detail (a file path, a line number), mark it `[FILL IN]` rather than guessing.
- **Dispatch tracking.** When something is dispatched to an implementation session, log it (task name, where it runs, timestamp) in `NEEDS_REVIEW.md` or an "In Flight" section of `status.md` before that session finishes, not only afterwards.
- **Recommending agents.** You may recommend an available agent or session arrangement based on the work, known capabilities, context needs, availability, cost and the human's stated preferences. Do not assume availability. The human authorizes assignments and dispatch.

---

## Step 8 — End of session

- Follow `SESSION_CLOSEOUT.md`. It decides who writes what in `status.md`; do not repeat its rules here.
- Leave `NEEDS_REVIEW.md` accurate: RESOLVED entries marked with reasoning, OPEN entries left OPEN with a clear next action, nothing silently dropped.
- Save a session handoff to `projects/<project>/handoffs/session_handoff_YYYY-MM-DD_<TOPIC>.md`.

---

## Dispatch prompt

Copy this to start a planning or review session:

```
You are a planning or review agent for [PROJECT].

Read PLANNING_AGENT_SESSION_START.md in the agent-tasks repo and follow it using the access you actually have.

ASSIGNMENT: [one objective]
or: "Establish current state and await assignment."

[Optional: which agents are available today.]

Give a short opening briefing before starting the assignment.
```

For a session without repository access, the human supplies this file and the relevant evidence (README, `status.md`, handoffs) as pasted content.

---

**Role identity:** planning and review are session activities, not permanent identities. A session performs the activities the human authorizes, using its actual capabilities. Do not require one activity per session.

**Output:** substantive output goes in files. Save reports, findings, reviews and handoffs as Markdown, in the task's destination or the project's convention (`summaries/` by default). Do not paste full artifacts into chat unless asked. Chat gets a short outcome, the file name and location, and any decision needed.
