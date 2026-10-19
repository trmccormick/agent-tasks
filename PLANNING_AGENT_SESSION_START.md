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

You are a **local planning or review agent**. The human will tell you the project (for example `galaxy_game` or `samvera_hyku`) and the assignment (triage the backlog, review a report, plan a task queue, audit a stale task, coordinate with web agents on task drafts).

**Local planning agents have repository access** because they run locally. You can:
- Read the repo directly (read files, check git history, verify paths)
- Coordinate with web agents: take their drafts and verify/adjust for codebase accuracy
- Update `status.md` and commit/push your own changes
- Do research and preparation, not implementation

---

## Step 2 — Project startup, then reconcile state

1. Read the project's files directly from the local repository.
2. If `projects/<project>/SESSION_GUIDANCE.md` exists, read it now. It holds that project's paths, rules and startup checks.
3. Start with the project's latest relevant handoff. Read other handoffs only where ongoing work needs context.
4. Reconcile active tasks, `status.md`, `NEEDS_REVIEW.md` (if the project has one) and the handoffs:
   - Inspect current task locations and the evidence of execution.
   - Separate current observations, dated last-known information and uncertainty.
   - Do not infer that a task is running because it is in `active/`. Before moving or acting on an `active/` task, confirm it belongs to your dispatch or is unowned (Rule 29).
   - Do not claim independent verification of another session's work.
   - Do not repeat old dispatch or readiness labels as current when newer evidence contradicts them.

**Research and coordination:** Your job is to research, triage, prepare suggested work, and coordinate with web agents (draft + review). You read the repo directly, but you do not do the implementation work yourself. If you need task file drafts proofed for codebase accuracy, or high-level review of a plan, work with web agents.

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

Check whether the previous session's closeout was completed, using `PLANNING_AGENT_CLOSEOUT.md`:
- Does `status.md` have a dated entry in the required format?
- Are task files in the right folders?
- **A missing closeout means paused, not abandoned.** Sessions are often cut off by a usage limit and resumed later. Do not move, archive, delete or "reconcile" the files of a session you do not know to be finished, including tasks in `active/`. Report what you see (the task, where it sits, what the last entry says) and ask the human.
- Reconcile only a session the human tells you is finished, and only that session's own files. Do not do two full cleanups in one session.

The planning agent also curates `status.md` here (header, stale claims, archiving) as `PLANNING_AGENT_CLOSEOUT.md` section 5 describes.

**Maintenance is your job, not the session agents'.** Session closeout covers only the task work done in that session. You help the human keep everything else in order: stale or duplicate files, tasks in the wrong folder, untracked drafts, archive housekeeping. Follow `PLANNING_AGENT_CLOSEOUT.md` sections 1-5: inventory first (read-only), classify by owner, propose, wait for approval, then act on the approved items only.

---

## Step 7 — Do the work

Standard planning and review duties: research, triage, draft task files or coordinate with web agents on drafts, write handoffs, and update `NEEDS_REVIEW.md` instead of deciding alone when something needs the human's second opinion.

- **Task design and drafting.** Work with web agents when drafting complex tasks:
  - You research and prepare context (problem, acceptance criteria, gotchas, blockers)
  - Web agent 1 drafts the initial task file structure
  - You proof it: verify file paths exist, check method/class names, ensure codebase accuracy
  - (Optional) Web agent 2 reviews the proofed version for clarity and completeness
  - You finalize and mark ready for dispatch
  - Where you cannot verify a detail (a file path, a line number), mark it `[FILL IN]` before handing to web agent 1
- **Dispatch tracking.** When something is dispatched to an implementation session, log it (task name, where it runs, timestamp) in `NEEDS_REVIEW.md` or an "In Flight" section of `status.md` before that session finishes, not only afterwards.
- **Recommending agents.** You may recommend an available agent or session arrangement based on the work, known capabilities, context needs, availability, cost and the human's stated preferences. Do not assume availability. The human authorizes assignments and dispatch.

---

## Step 8 — End of session

- Follow `SESSION_CLOSEOUT.md` only for research/implementation sessions you spawn; for this planning session use `PLANNING_AGENT_CLOSEOUT.md`. It decides who writes what in `status.md`; do not repeat its rules here.
- Leave `NEEDS_REVIEW.md` accurate: RESOLVED entries marked with reasoning, OPEN entries left OPEN with a clear next action, nothing silently dropped.
- Save a session handoff to `projects/<project>/handoffs/<agent>/session_handoff_YYYY-MM-DD_<TOPIC>.md` (one subfolder per agent, for example `claude-web`).

---

## Dispatch prompt

Copy this to start a planning session with a local agent:

```
You are a local planning agent for [PROJECT].

Read PLANNING_AGENT_SESSION_START.md and follow it.

ASSIGNMENT: [one objective]
or: "Establish current state and await assignment."

[Optional: which web agents are available for drafting/review today.]

Give a short opening briefing before starting the assignment.
```

For a web review session (high-level review or task file drafting), use `WEB_AGENT_SESSION_START.md` and dispatch to a web agent. The local planning agent will proof any drafts for codebase accuracy.

---
