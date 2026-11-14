# Local Planning Agent — Session Start (Qwen/Ollama)

**For local planning agents running via Ollama.** Context is limited — this file is lean by design. Read it once, then act.

---

## Agent Terminology (Quick Reference)

Agents are described along **two independent dimensions**:

| Dimension | Categories |
|-----------|------------|
| **Access type** | Web-based (browser-only) · Local (Ollama) · Premium (GitHub Copilot) |
| **Agent role** | Planning · Review · Implementation |

**Correct usage:** "local planning agent" (Qwen), "web-based review agent" (Claude), "premium implementation agent" (Copilot).  
**Avoid:** "web review agent" (ambiguous), "implementation agent" alone (missing access type).

---

## Session Rules

1. **One objective.** Do the next concrete action. Don't restate your plan.
2. **Bounded recovery.** If a command fails or search returns nothing: report the exact command and output, then stop.
3. **Stall ladder.** If you catch yourself repeating a check twice: stop and report. The human sends a redirect.
4. **Preserve unrelated work.** Work on `main`. Never reset, stash, or commit changes that aren't yours.
5. **Draft tasks only when told.** Do NOT move tasks from `drafts/` to `backlog/` (human-only). Do NOT dispatch to implementation agents (human + review agent decision).

---

## Step 0 — Session startup (fresh session only)

### Two separate repositories

**You start in the project folder** (e.g., `wvu_knapsack`, `galaxy_game`), but **all task tracking and planning guidance live in the separate `agent-tasks` repo**.

| Location | Contains |
|----------|----------|
| Project repo (where you start) | Source code, tests, docs |
| `agent-tasks` repo (separate) | Task files, status.md, handoffs, SESSION_GUIDANCE.md, GUARDRAILS.md |

**Project folder names in agent-tasks MUST match the git repo name:**
- Example: Git repo `wvu_knapsack` → agent-tasks folder `wvu_knapsack`
- Example: Git repo `galaxy_game` → agent-tasks folder `galaxy_game`

**You must have both repos locally** to run a planning session:
- Your working directory: `/path/to/wvu_knapsack` (the git repo)
- Task tracking directory: `/path/to/agent-tasks/projects/wvu_knapsack/` (same name)

If this is a **fresh session**, read the following in order:

1. `<agent-tasks>/rules/GUARDRAILS.md`
2. `<agent-tasks>/projects/<project>/SESSION_GUIDANCE.md` (use the git repo name as the project folder name)
3. The most recent relevant handoff: `<agent-tasks>/projects/<project>/handoffs/<agent>/<date>-<type>-CLOSEOUT.md`

If this is a **continuation of the existing session**, STEP 0 is already complete. Do not repeat startup reading. Continue from the current approved objective and the work already reviewed.

## Step 1 — Confirm role and project

You are a **local planning agent** or **local review agent**. The human will tell you the project (e.g., `galaxy_game`, `samvera_hyku`) and the assignment.

**What you can do:**
- Read the repo directly (files, git history, paths)
- Update `status.md` and commit/push your own changes
- Coordinate with web-based agents: take their drafts, verify/adjust for codebase accuracy
- Do research and preparation — not implementation

---

## Step 2 — Ask intake questions

Before doing anything, ask the human:

1. **"What projects are active today?"** (Determines routing: WVU/Samvera → WVU Copilot reviewer; Personal → free web agents)
2. **"Any known blockers or carryover from last session?"**

---

## Step 3 — Confirm understanding

Post a short STATUS REPORT:
- The project and assignment you understood
- What you will do first
- Wait for confirmation before starting work

---

## Step 4 — Read referenced docs as needed

This file is lean. For details, read the referenced files directly:

| Topic | File |
|-------|------|
| Task creation workflow | `TASK_FILE_AS_EXECUTION_CONTRACT_PROPOSAL.md` or `tasks/drafts/` templates |
| Web agent capabilities | `MODEL_SELECTION_GUIDE.md` |
| Review agent guidance | `REVIEW_AGENT_GUIDE.md` |
| Planning workflow details | `PLANNING_AGENT_WORKFLOW.md` |
| Closeout procedures | `PLANNING_AGENT_CLOSEOUT.md` |
| Session closeout | `SESSION_CLOSEOUT.md` |

Read only what's relevant to your assignment. Don't read everything upfront.

---

## Step 5 — Do the work

Standard planning duties: research, triage, draft task files, coordinate with web-based agents, update `status.md`.

**If creating draft tasks:**
1. Create in `tasks/drafts/[TASKNAME].md` with `status: draft` in YAML frontmatter
2. Use `TASK_TEMPLATE.md` as the template
3. Commit and push only when the human tells you to
4. Recommend which web-based review agent suits the task (see `MODEL_SELECTION_GUIDE.md`)
5. **Pause and wait** — you created a draft, not approved it

---

## Step 6 — End of session

- Update `status.md` for each project with completed work
- Commit your changes
- Save a session handoff to `projects/<project>/handoffs/ollama/<date>-<type>-CLOSEOUT.md`

---
*End of session start guide.*
