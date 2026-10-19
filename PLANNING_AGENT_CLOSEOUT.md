# Planning Agent Closeout and Workspace Maintenance

For the planning session only. Research and implementation sessions use `SESSION_CLOSEOUT.md` and do none of this.
Model-neutral: the role comes from the prompt, not the model. One session, one role.

## 0. What the planning session does and does not do

- **Does:** read status, curate `status.md`, check `active/` and `backlog/`, plan, write tasks and dispatch prompts, keep the workspace in order, propose actions to the human.
- **Does not:** run tests, edit application code, or do research or implementation itself. Write a task or dispatch prompt and hand it to a new research or implementation session. This also keeps the planning session's context short.
- **Context rule:** when the planning session gets long, end it with a handoff file (section 6) and start a new planning session from that handoff.
- **Handoff file is primary continuity mechanism:** The next planning session reads your handoff file first to understand current state. status.md is a fast human-readable summary; the handoff file is the detailed technical record.
- **Never without the human's approval:** deleting or archiving task files (Rule 29), moving a task that another session owns, committing or pushing (Rule 26).
- **Forbidden commands during maintenance:** `git stash`, `git reset`, `git checkout` (of files), `git restore`, `git clean`, a plain `git pull` on a dirty tree. Use `git fetch` and report. Do not unstage another session's staged changes.

## 1. Inventory (read-only, change nothing)

Run for agent-tasks and, where a task points to one, the project repo:

1. `git status --short` and `git log --oneline origin/main..HEAD` (local-only commits).
2. `ls` of `projects/<project>/tasks/active/`, `backlog/` and `backlog/*/`.
3. For every task file in `active/`: its YAML `status:`, `last_updated`, and its Progress / Resume block.
4. `find . -name "<task file name>"` for any task that may be duplicated.
5. The Open Sessions table and the last Session Log entries in `status.md`.

## 2. Classify each item by owner

- this planning session
- a session the human says is finished
- a paused or in-progress session
- unknown (treat as paused)

A task with `resume: true` in `backlog/` is **incomplete work waiting for a new session**. It is not finished, not stale and not abandoned.

## 3. Checks

1. **Active tasks.** Every `active/` task should have an owner session, a recent update, and an accurate next action. Flag: finished but still in `active/`, duplicate copies, no session behind it.
2. **Resume tasks.** List every `resume: true` task with its next step. They are the first candidates for dispatch.
3. **Status vs reality.** For each task a status entry calls complete, confirm it with a command: the commit exists on origin (`git log origin/main`), the task file is in `completed/`, and tests were run (VERIFIED), or it is REPORTED only. Local-only commits are not "complete".
4. **Stale claims in `status.md`.** Anything like "None Currently Active" or "All recent work has been committed" is checked with a live command before it stays.
5. **Open Sessions.** Compare the table with `active/` and `git status`. Flag a task with no row, a row with no live window, or local-only commits.
6. **Paused sessions are protected.** Their files are never moved, archived or removed because they look old.

## 4. Propose, then wait

Give the human one list: item, owner, evidence, recommended action. Wait. Do not act on items owned by a paused or unknown session unless the human names the item and the action.

## 5. Act on approved items only

- `git mv` for lifecycle moves (backlog → active → completed) and into archive locations. Keep one canonical copy per task. If copies conflict, stop and ask.
- Curate `status.md`: update the header (`Last Updated: YYYY-MM-DD — <what happened>`); fold entries older than about a week into a short summary (what shipped with commit hashes, open blockers, standing lessons); move originals verbatim to `archive/status-log-YYYY-MM.md` with a note on why, and leave a one-line pointer; never silently drop a claim; an entry written to the wrong project goes to the archive with a note.
- Maintain the **Open Sessions** table in `status.md`: window or session label (named after the task ID), agent, task ID, state (`running` / `paused` / `blocked` / `needs-review` / `incomplete`), last touched, local-only commits yes/no.
- Pausing or closing a project: put `Project state: active | paused | archived` and one sentence why in the first lines of its `status.md`. Use `paused`, not deletion. Never archive without approval.
- Verify with `git status --short` and the relevant listing; confirm only the approved changes happened. The human commits.

## 6. Create handoff file (end of every planning session) — REQUIRED

Every planning session MUST create a formal handoff file so the next session (planning, research, or implementation) can quickly resume without re-reading everything.

**File location:** `projects/<project>/handoffs/<agent>/<date>-<type>-CLOSEOUT.md`
- `<agent>`: Your agent folder name (e.g., `haiku-copilot`, `qwen-planning`, `claude-web`)
- `<date>`: Session date (YYYY-MM-DD)
- `<type>`: Session type (PLANNING, RESEARCH, IMPLEMENTATION, or specific domain)

**Handoff file MUST document:**
1. **What was checked:** Inventory findings (active/, backlog/, git state, session ownership)
2. **What was changed (approved only):** Files moved, status updated, tasks created/dispatched
3. **What is waiting:** Next steps for human, which resume:true tasks are ready, blockers
4. **Open sessions:** Current agent dispatch status (running/paused/blocked/needs-review)
5. **Housekeeping slice:** (if applicable) What was worked on from HOUSEKEEPING.md

**Also update** `status.md` header with `Last Updated: YYYY-MM-DD — <brief summary>` to point to the handoff.

**Why this matters:** The next planning session starts by reading the handoff file to understand current state. Without it, they re-inventory everything, wasting time and risking missed context. The handoff file is the primary continuity mechanism.

**Do NOT rely on status.md alone.** The handoff file is the detailed record; status.md is the fast summary for humans.

## 7. Housekeeping slice (one per session, if applicable) — OPTIONAL

**When to do this**: After creating the handoff file (section 6), **if the project has a `HOUSEKEEPING.md` file**, pick one small slice of work.

1. **Read the housekeeping file** (e.g., `projects/<project>/HOUSEKEEPING.md`). Pick the first item with `status: todo` and no open dependencies.
2. **Do its read-only first step** (no changes, no deletions). Propose at most 10 files in the normal proposal table.
3. **Wait for approval.** Act only on approved items.
4. **Record the slice:**
   - Update the housekeeping file: set item to `in progress` or `done`, add a row to the Session log
   - Commit with a message referencing the item ID
   - Add a "Housekeeping Slice" section to the session handoff (section 6 above) documenting what was worked on
5. **Stop there.** Even if more items are ready, next session picks up the next one.

**Purpose:** Prevents big-bang cleanups and recurring issues (file duplication, stale temp files, etc.) from accumulating. Each session handles one small slice.

**File format:** See `projects/galaxy_game/HOUSEKEEPING.md` or `projects/<project>/HOUSEKEEPING.md` for structure and conventions.

