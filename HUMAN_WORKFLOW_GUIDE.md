# Human Workflow Guide
**Audience**: You, the human coordinating agents across projects and laptops
**Based on**: the original Galaxy Game `docs/agent/WORKFLOW_README.md` (March 2026), updated for the current agent-tasks workflow

> **Doc maintenance rule:** this guide uses role names only. Do not add model names here. Which model is good for what changes often; current notes live in `notes/AGENT_NOTES.md`.
> This is a reminder for you, not a rule file. If it disagrees with `rules/GUARDRAILS.md` or the closeout files, those win.

---

## The roles

| Role | Where it runs | What it does | What it does not do |
|---|---|---|---|
| **Planning agent** | A local session | Triage, plan, write tasks and dispatch prompts, curate `status.md`, keep the workspace in order | Implement, research, run tests. It stays the planning session and spawns the others. |
| **Research / implementation agent** | A separate session per task | Does one task from its task file, then closes out | Maintenance, curation, other sessions' files |
| **Web review agent** | A chat session (any model) | Reviews, drafts task files and plans, hands you files or commands | Edit the repo or run git. You commit. |

One session, one role. New work in another role is a new session. Long sessions drift, so start a fresh one from a handoff.

---

## How a session works

```
You talk to the planning agent (local)
  └─ It reads status, triages, and writes or picks a task file
  └─ It writes a dispatch prompt for the task
       └─ You choose the agent for this task and paste the prompt into a new session
            └─ The agent moves the task to active/, writes a synthesis report to summaries/
            └─ You approve or redirect
            └─ The agent does the work, runs targeted tests, reports
            └─ The agent closes out (SESSION_CLOSEOUT.md), or stops early if unfinished
       └─ You bring the result back to the planning agent
  └─ The planning agent updates status and writes a session handoff
  └─ You commit and push (agents never commit or push on their own)
```

You are the bridge: approving, carrying results between sessions, and setting priorities.

---

## Starting a planning session

Give it, in this order:
1. The role: "planning agent", and which project.
2. It reads `PLANNING_AGENT_SESSION_START.md`, then `PLANNING_AGENT_CLOSEOUT.md`, then `notes/AGENT_NOTES.md`.
3. The most recent session handoff: `projects/<project>/handoffs/<agent>/session_handoff_YYYY-MM-DD_<TOPIC>.md`.
4. Your priority for the day.

For a web review session, give it `WEB_AGENT_SESSION_START.md` first.

Before you start, check:
- `git status --short` and `git log --oneline origin/main..HEAD` on the laptop you are on.
- Which laptop holds work that is not on origin (see "Several laptops" below).
- Project-specific startup (Galaxy: the baseline test log, Docker running) is in `projects/<project>/SESSION_GUIDANCE.md`.

---

## Dispatching a task

- **You choose the agent at dispatch.** There is no fixed ladder. Pick by task size, cost and how watched it needs to be.
- Paste the prompt from `SIMPLE_HANDOFF_TEMPLATE.md` (or the task's own Agent Dispatch Interface), with the path placeholders filled for the machine that runs it. Paste the **task file path, not its contents.**
- Step 0 of the prompt moves the task from `backlog/` to `active/`. If the task has `resume: true`, the agent reads its Progress / Resume block first and continues, and does not restart.
- The synthesis report is saved as a file in `summaries/` before any implementation, and chat is for questions.
- Two attempts at a fix, then stop and bring it back to the planning agent for re-diagnosis.

---

## When a session ends

| Situation | What happens |
|---|---|
| Task finished | Agent follows `SESSION_CLOSEOUT.md`: one Session Log entry in `status.md`, task moved to `completed/`, only its own files touched. |
| Stopping unfinished; someone else will pick it up | **Stop early** (`SESSION_CLOSEOUT.md` section 2a): update the task's Progress / Resume block, set `resume: true`, move it back to `backlog/`, one `INCOMPLETE` line in `status.md`. |
| Paused at a usage limit and the same session will come back | Nothing moves. The task stays in `active/`. A missing closeout means paused, not abandoned. |
| Planning session | `PLANNING_AGENT_CLOSEOUT.md`: inventory, propose, wait for you, act on approved items only. |

A session never cleans up another session's files. The planning agent does maintenance, with you.

---

## Task lifecycle

```
Planning agent writes the task     → backlog/
Task is assigned                   → active/       (git mv, status: active)
Session stops unfinished           → backlog/      (resume: true, Progress / Resume filled in)
Work finished and verified         → completed/    (completion report added)
```

Exactly one copy of a task file exists at any time. If you find two, nobody moves or deletes anything until you decide.

---

## Session handoffs

At the end of a long or important session, the planning agent (or web agent) writes one and **you commit it**, because a file that exists only in chat is invisible to your other laptops.

Path: `projects/<project>/handoffs/<agent>/session_handoff_YYYY-MM-DD_<TOPIC>.md` (`<agent>` is one subfolder per agent, for example `claude-web`)

A good handoff has: current state (verified or reported), what landed with commit hashes, what is open, decisions waiting on you, known pre-existing problems, files touched, and the first thing to do next session. Galaxy handoffs also carry the test baseline.

To start fresh: give the next session the handoff path, not a pasted copy.

---

## Several laptops

- Work lives in each laptop's clone until you push. Before trusting a status line, check `git log --oneline origin/main..HEAD` on the machine that did the work.
- Anything another session needs to read goes into the repo and is committed. Chat and downloads do not travel.
- Paths in prompts use `<agent-tasks>` and `<galaxyGame>` placeholders. Fill them for the machine that runs the task.

---

## How web agents hand you changes

Set out in `WEB_AGENT_SESSION_START.md`, "Handing over changes". In short: a whole file → a finished file; a small edit → one command or exact text; edits across files or needing the repo read → a prompt for an implementation agent. No scripts unless you ask. One request at a time, and you check `git diff --stat` before anything is committed.

---

## Request prefixes (carried over from the original guide)

Optional. They keep the planning agent focused. Not written down elsewhere in agent-tasks.

| Prefix | Use for | Example |
|---|---|---|
| `REVIEW:` | Analyze and give feedback | `REVIEW: backlog for overlaps` |
| `PLAN:` | Strategy or roadmap | `PLAN: next phase after the test restoration` |
| `CREATE TASK:` | A task file and its dispatch prompt | `CREATE TASK: fix shell printing spec` |
| `TRIAGE:` | Analyze a failure log | `TRIAGE: [paste output]` |
| `HANDOFF:` | Session handoff document | `HANDOFF: end of session` |

---

## Project-specific details (not repeated here)

- Galaxy Game: nightly full-suite run, test-log checks, the Docker host↔container paths and mount order, and the critical rules are in `projects/galaxy_game/SESSION_GUIDANCE.md`.
- Tool setup notes from the March 2026 guide (not re-checked): Copilot agent mode needs `chat.agent.enabled`, and an update can reset it. Agent terminal access is for local development only, never production or staging.

---

## Common mistakes

- **Skipping the handoff.** Context is lost and the next session starts from scratch.
- **Pasting a task's contents instead of its path.** The agent can read the file, and pasting wastes tokens.
- **Approving a fix without reading the synthesis report.** Read the risk section first.
- **Running the full test suite too early.** Targeted specs first; the full run only when they are green.
- **Writing documentation during an implementation task.** Flag the gap and make it a separate task.
- **A third fix attempt.** Two, then re-diagnose.
- **Letting `git add -A`, `git stash`, `git reset`, `git checkout` or a plain `git pull` on a dirty tree near a session's closeout.** They can sweep in or destroy other sessions' work.
- **Treating "complete" as pushed.** Check origin.
- **Mixing roles in one long session.** Spawn a new session.
- **A web agent giving you two requests at once, or switching delivery methods mid-job.** Ask it to slow down and follow "Handing over changes".

---

## Where things are

| You want | File |
|---|---|
| Rules every agent follows | `rules/GUARDRAILS.md` |
| Planning session start and end | `PLANNING_AGENT_SESSION_START.md`, `PLANNING_AGENT_CLOSEOUT.md` |
| Research/implementation closeout | `SESSION_CLOSEOUT.md` |
| Web review agent start | `WEB_AGENT_SESSION_START.md` |
| Task template and dispatch prompts | `TASK_TEMPLATE.md`, `SIMPLE_HANDOFF_TEMPLATE.md` |
| Current agent and model notes | `notes/AGENT_NOTES.md` |
| A project's paths and rules | `projects/<project>/SESSION_GUIDANCE.md` |
| What is waiting on a second opinion | `projects/<project>/NEEDS_REVIEW.md` |
| Current state of a project | `projects/<project>/status.md` |
