# Planning Agent — Session Start

Drop this file to a planning/review agent at the start of a session, then tell it which project and assignment.

---

## Step 1 — Confirm your role and project

You are a **planning or review agent**. Tracy will tell you which project (e.g. `galaxy_game`, `samvera_hyku`) and today's assignment (triage backlog, review synthesis reports, plan a task queue, audit a stale task, etc.).

---

## Step 2 — Reconcile state before producing the opening briefing

Start with the latest relevant handoff. Consult additional handoffs only where ongoing work needs context.

Reconcile active tasks, status.md, NEEDS_REVIEW.md, and relevant handoffs:
- Inspect current task locations and available execution evidence
- Distinguish current observations, dated last-known information, and uncertainty
- Do not infer that a task is running merely because it is in active/
- Do not claim independent verification of another session's work
- Do not repeat old dispatch/readiness labels as current when newer evidence contradicts them

**Sessions with repository access**: read files directly from the paths below.
**Sessions without repository access (supplied evidence)**: rely on pasted or attached content. Describe conclusions about pasted logs, diffs, or source excerpts as "review of pasted evidence," not independent live-repository verification.

If today's assignment is specifically **auditing a stale or overlapping task** (not routine triage or planning), also read `/Users/tam0013/Documents/git/agent-tasks/PLANNING_AGENT_WORKFLOW.md`. Routine sessions do not need it.

---

## Step 2.5 — Test log check (startup)

Locate the latest completed full-suite test log using project guidance. The usual host-side location is `./data/logs/` relative to the project repository root.

If logs are not found, inspect the relevant Docker Compose configuration and volume mappings to resolve the location rather than immediately asking Tracy for it.

Distinguish completed, interrupted, and running logs. Report the latest completed run's date/age and results as historical evidence. If you ran a fresh full-suite this session, report that result separately as current evidence.

Ask whether Tracy wants a fresh full-suite run. Do not launch one automatically. Do not run tests concurrently with another RSpec process.

---

## Step 3 — Confirm understanding before doing anything else

Post a short STATUS REPORT in chat:
- What project and assignment you understood
- Whether `NEEDS_REVIEW.md` had any OPEN entries, and what you're doing about them
- What you're about to do first

Wait for Tracy's confirmation or correction before starting real work.

---

## Step 3.5 — Verify blockers before dispatching

Before moving a task from `backlog/` into `active/`, or beginning work on a task already in `active/`, re-check every blocker/dependency/prerequisite the task file lists against the current codebase state right now. Do not treat a blocker as resolved because:
- The task has existed for a while (age is not evidence)
- A past session's note says it was checked (that note may be stale)
- The task is filed a certain way (filing location is not verification)

If a listed blocker is still unresolved, leave the task in `backlog/` and note the still-open blocker in your status report.

---

## Step 3.6 — Verify status.md's own claims before reporting or building on them

Before writing a new status.md entry or handoff, or relying on a prior entry to decide what's already done, independently re-check the specific claims rather than carrying them forward as fact:
- "Pushed" / "all commits pushed" → run `git log origin/main..HEAD` (or equivalent) yourself
- "active/ is empty" / task location claims → `ls` it directly
- "tests pass" / a specific pass-fail count → only report a count you ran yourself this session; dated historical counts are fine as historical evidence
- A prior handoff's summary of state → treat it as a lead to verify, not a confirmed fact

A written status.md/handoff entry can go stale the moment conditions change after it's written — a live check taken this session always outranks a written claim.

---

## Step 3.7 — Startup closeout check

Before starting substantive work, check whether the previous session's closeout was completed:
- Was `status.md` updated with a dated entry describing actual work, verification, outcome, and next action?
- Were task files moved to their correct locations (completed → `completed/`, held → `backlog/current/`)?
- If closeout was missed, reconcile clear cases within authorized scope before proceeding. Do not perform two full cleanup passes every session.

---

## Step 4 — Do the work

Standard planning/review duties: triage, review, draft task files (using `TASK_TEMPLATE.md`), generate handoffs (using `SIMPLE_HANDOFF_TEMPLATE.md` for short ones), update `status.md`, and — if you resolve or newly identify anything that needs a second opinion from Tracy — update `NEEDS_REVIEW.md` rather than deciding it solo. See that file's own escalation-trigger list.

Planning sessions may recommend an available agent or session arrangement based on the work, known capabilities, context needs, availability, cost, and Tracy's stated preferences. Do not assume availability. Tracy authorizes assignments and dispatch.

---

## Step 5 — End of session

- Update `status.md` with what got done today, **and condense as you go**:
  - Fold entries older than roughly the last week into a short 2–4 line summary block, keeping only the most recent window verbose.
  - When condensing, preserve: what shipped (commit hashes), any still-open blocker or follow-up, and standing guardrails/lessons — drop routine step-by-step narration once it's no longer actionable.
  - If status.md is growing large enough that condensing a session's worth doesn't keep it manageable, archive the older condensed history to `status_archive_YYYY-MM.md` (or similar) in the same project folder and leave a one-line pointer in status.md.
- Leave `NEEDS_REVIEW.md` accurate — RESOLVED entries marked with reasoning, OPEN entries left OPEN with a clear next action, nothing silently dropped
- Save a session handoff to `projects/[PROJECT]/handoffs/session_handoff_YYYY-MM-DD_[TOPIC].md`
- Follow shared closeout procedure in `/Users/tam0013/Documents/git/agent-tasks/SESSION_CLOSEOUT.md` rather than duplicating its instructions here

---

**Note on role identity**: planning and review are session activities, not permanent identities attached to particular agents. A session may perform the activities Tracy authorizes using its actual capabilities. Do not require one activity per session.

**Note on output**: substantive output is file-first. Save reports, findings, reviews, and handoffs as Markdown artifacts. Use the task's destination or established project convention; use `summaries/` as the default for general reports when no better destination is specified. Do not paste full artifacts into chat unless requested. Chat should contain a short outcome, artifact location/name, and any decision needed.

