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
You talk to the local planning agent (local)
  └─ Does research, reads status.md, prepares suggested work
  └─ Hands to web agent 1 (review/draft agent) for high-level review and task file draft
       └─ Web agent 1 drafts task files, reviews context, suggests improvements
       └─ You bring draft back to local planning agent
  └─ Local planning agent proofs the draft, adjusts for codebase accuracy, updates paths and context
       └─ (Optional) Hands adjusted draft to web agent 2 (final reviewer) for second opinion
       └─ Web agent 2 reviews the codebase-adjusted version
  └─ Local planning agent finalizes task and writes dispatch prompt
       └─ You dispatch to implementation agent (local with repo access, or web agent with commands)
            └─ Implementation agent does the work, commits/pushes or reports progress
            └─ You bring results back to planning agent
  └─ Local planning agent updates status and writes session handoff
  └─ You commit and push (from the laptop where work lives)
```

**Workflow pattern:** Local agent → web draft → local adjust → (optional) web review → dispatch → execute. Local agents do research, codebase verification, and status curation. Web agents do high-level review and drafting. This keeps context tight and reduces review cycles.
---

## Starting a planning session

Give it, in this order:
1. The role: "local planning agent" or "planning agent" (same role, emphasizes it runs locally)
2. Which project.
3. It reads `PLANNING_AGENT_SESSION_START.md`, then `PLANNING_AGENT_CLOSEOUT.md`, then `notes/AGENT_NOTES.md`.
4. The most recent session handoff: `projects/<project>/handoffs/<agent>/session_handoff_YYYY-MM-DD_<TOPIC>.md`.
5. **Which web agents are available today** (including whether Copilot is available for WVU/Samvera work).
6. Your priority for the day.

**Local planning agent responsibilities:**
- Research and triage
- Read and curate `status.md`
- Prepare suggested work
- Coordinate with web agents for drafting and review (see "Drafting task files with web agents" below)
- Verify codebase context and paths (web agents may miss implementation details)
- Finalize tasks and write dispatch prompts
- Update `status.md` and session handoffs
- Track available agents and suggest pairings based on project and context needs

Before you start, check:
- `git status --short` and `git log --oneline origin/main..HEAD` on the machine running this session.
- Which laptop holds work that is not on origin (see "Several laptops" below).
- Project-specific startup is in `projects/<project>/SESSION_GUIDANCE.md`.
- See `docs/FREE_MODELS_EVALUATION.md` for available models and ongoing evaluation framework for open-source projects.

---

## Dispatching a task

- **You choose the agent at dispatch.** There is no fixed ladder. Pick by task size, cost, capability and how watched it needs to be.
  - **For WVU/Samvera projects:** Use Claude or web agents for primary drafting. Consider GitHub Copilot (experimental) as a **paired verification agent** if available: verification pass on task files before final dispatch, second opinions, codebase accuracy checks. This helps evaluate Copilot's capabilities.
  - **For open-source projects:** Use Claude (primary) or other web agents. Planning agent will research current model capabilities online (LLM Arena, HN, release notes, Reddit) and recommend trying new models as experiments, especially for complex reasoning or code analysis tasks. Track results in the evaluation log with both internal findings and external evidence.
  - **For other projects:** Use web agents (Claude preferred for drafting, others for review/brainstorming)
- Paste the prompt from `SIMPLE_HANDOFF_TEMPLATE.md` (or the task's own Agent Dispatch Interface), with the path placeholders filled for the machine that runs it.
  - Paste the **task file path, not its contents.**
- Step 0 of the prompt moves the task from `backlog/` to `active/`. If the task has `resume: true`, the agent reads its Progress / Resume block first and continues.
- The synthesis report is saved as a file in `summaries/` before any implementation, and chat is for questions.
- Two attempts at a fix, then stop and bring it back to the planning agent for re-diagnosis.

---

## Drafting task files with web agents

Task files are often drafted collaboratively: local planning agent researches, web agent drafts, local agent verifies codebase details.

**Pattern (typical):**
1. **Local planning agent** prepares context (problem, acceptance criteria, blockers, known gotchas)
2. **Web agent 1** (draft agent) creates initial task file from the context, filling in high-level structure
3. **Local planning agent** proofs the draft:
   - Verifies file paths exist and are accurate
   - Confirms method names, class names, and implementation details
   - Adjusts for codebase-specific conventions
   - Updates dispatch interface with correct paths
4. **(Optional) Web agent 2** (final reviewer) reviews the codebase-adjusted version for clarity and completeness
5. **Local planning agent** finalizes and is ready for dispatch

**Why this works:**
- Web agents are good at high-level structure and writing
- Local agents have repo access and catch path/name errors
- Two web agent passes (draft + review) catch writing and clarity issues
- Reduces back-and-forth and speeds up task file quality

**Commands for steps:**
- Web agents: You hand them text or pasted context; they draft
- Local agent: `git ls-files`, `grep`, `find` to verify paths; reads code to check names and conventions

---

## When a session ends

| Situation | What happens |
|---|---|
| Task finished (implementation agent) | Agent follows `SESSION_CLOSEOUT.md`: one Session Log entry in `status.md`, task moved to `completed/`, only its own files touched. Results come back to planning agent. |
| Stopping unfinished; someone else will pick it up | **Stop early** (`SESSION_CLOSEOUT.md` section 2a): agent updates task's Progress / Resume block, sets `resume: true`, moves it to `backlog/`, writes one `INCOMPLETE` line in `status.md`. Results come back to planning agent. |
| Paused at a usage limit and the same session will come back | Nothing moves. The task stays in `active/`. A missing closeout means paused, not abandoned. |
| Planning session (local planning agent) | `PLANNING_AGENT_CLOSEOUT.md`: inventory, research, prepare suggested work, coordinate with web agents, update `status.md`. Local agent commits and pushes changes. Handoff file created in `handoffs/haiku-copilot/` or agent-specific folder. |

A session never cleans up another session's files. The local planning agent does maintenance and curation, with the human's approval.

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

- **Skipping web agent review on task file drafts.** Two passes (draft + review) catch issues faster than one long back-and-forth.
- **Not verifying paths after web agent draft.** Web agents may use plausible paths that don't exist. Local agent must proof.
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
