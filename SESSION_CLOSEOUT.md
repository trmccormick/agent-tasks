# Session Closeout — Shared Procedure

For research and implementation sessions (and review sessions). One session, one role: a session keeps its role for its whole life, and new work in another role starts a new session.
Model-neutral. Applies regardless of which agent ran the session.
Project-specific guidance lives in `projects/<project>/SESSION_GUIDANCE.md`.

**Planning sessions do not use this file.** They use `PLANNING_AGENT_CLOSEOUT.md`. If you are a research or implementation session, the planning and maintenance duties are not yours.

**Session closeout covers the task work done in that session, and nothing else.** Maintenance of everything else (stale or duplicate files, other sessions' drafts, archive housekeeping, `status.md` curation) belongs to the planning agent and the human.

**Which exit applies?** If the task is finished, use sections 1-4. If you are stopping before it is finished and no one will resume in this same session, use section 2a (stop early). If this same session will resume after a usage limit, it is paused: see section 1b item 7.

---

## 0. Scope: only what this session touched

Closeout covers only the files this session created or changed. Another session's work is off limits, whether that session is running, paused or finished: its task files, drafts, reports, uncommitted changes and untracked files.

- **Paused is normal.** Sessions often stop mid-work when a usage limit runs out and resume later, sometimes days later. Treat any task in `active/`, any uncommitted change and any untracked file you did not create as belonging to a paused session. A missing closeout entry means "paused", not "abandoned". Removing or moving a paused session's files can lose work that cannot be recovered.
- **Only the human decides that work is abandoned.** Nothing is deleted or moved to tidy up.
- **How to tell what is yours.** Compare `git status --short` at closeout with the state at session start, or use the session's own record of the files it changed. Anything outside your own changes is not yours. An untracked file is not yours just because nobody has committed it.
- **Not yours: leave it alone.** Do not stage, move, rename, archive, delete or "clean up" it. List it under "Not committed" in your status entry as "left as-is, not this session" and stop.
- **Only exception:** the human names the file and says what to do with it.
- **Unsure whether a file is yours:** leave it and ask.
- **Stage explicit paths only.** Never `git add .`, `git add -A` or `git commit -a`. Before committing, `git diff --cached --stat` must list only your files.
- **Cleanup of anything outside your own task work is not part of closeout.** It is the planning agent's job, done with the human (section 7).
- **Commands that can hide or destroy other sessions' work are off limits during closeout:** `git stash`, `git reset`, `git checkout` (of files), `git restore`, `git clean`, and a plain `git pull` on a dirty tree. If a command seems to need one of these, stop and report. If you need the remote's changes, run `git fetch` and `git log --oneline HEAD..origin/main`, then ask.
- **Another session's staged changes are theirs too.** `git status --short` can show staged entries (a letter in the first column) that you did not stage, for example a task move another session made with `git mv`. Do not unstage, restore or reset them: that changes the other session's index and can drop its move from its next commit. Stage your own files by explicit path, then commit only your paths: `git commit -m "<message>" -- <your path> <your other path>` (for a `git mv`, list both the old and the new path). Run `git show --stat HEAD` and confirm it lists only your files.
- **Found a second copy of a task you worked on?** Run `find . -name "<task file name>"` before moving it. If more than one copy exists, do not move, copy or delete any of them. List the paths and their `status:` lines in your report and stop (the duplicate-copy rule in `rules/GUARDRAILS.md`).
- **Do not write "closed out", "complete" or "unblocked" in the status entry if a review is still pending.** Say what was done and name the pending review under **Stop condition**.

---

## 1. Status.md Maintenance (mandatory)

### 1a. Roles

| Role | Owns |
|---|---|
| Session agent (does the work) | Appends one entry to the end of its own project's `status.md`. Nothing else in that file. |
| Planning agent | The `status.md` header, trimming and archiving (see `PLANNING_AGENT_CLOSEOUT.md`). |
| Human | Commits and pushes (Rule 26). Approves any deletion (Rule 29). |

### 1b. Session agent: append one entry

1. Open the status file of the project you worked in. Never another project's.
2. Go to the end of the file. Add `---`, then this heading exactly:

   `## 📝 Session Log — YYYY-MM-DD (short title)`

3. Under it, write four bullets and no sub-headings:
   - **Changed:** files touched, by path.
   - **Committed:** commit hashes, or "none".
   - **Not committed:** what is left uncommitted, or "none".
   - **Stop condition:** why you stopped, or "none" if the work is finished. Include the next action and who owns it.
4. Every fact must name a command that shows it (for example `git status --short`, `git log -1 --stat`). If you did not run a command that shows it, do not state it.
5. Do not edit the header or any earlier entry. Do not condense, trim or archive. Do not stage or commit.
6. If the session was read-only, still write the entry and say "Changed: none (read-only)".
7. If you stop before the work is finished (for example you hit a usage limit), still write the entry if you can. Set **Stop condition** to "paused", with the reason and the exact point to resume from. Leave your own files and tasks as they are: a task stays in `active/`, uncommitted work stays uncommitted, nothing is cleaned up.

8. If you cannot write your entry (for example `status.md` already has another session's uncommitted changes), say so in your report and stop on that item. Do not skip it silently, and do not stage the file as it stands.

### 1c. Planning-agent curation

Curation of `status.md` (header, stale claims, archiving) is the planning agent's job and is described in `PLANNING_AGENT_CLOSEOUT.md`. Do not do it.

### 1d. Evidence labels

- **VERIFIED**: confirmed by a command in this session.
- **REPORTED**: stated by a person or an earlier entry, not re-checked.

Never label something VERIFIED without naming the command.

---

## 2. Task Lifecycle Reconciliation

Move canonical task files using `git mv`. Verify one canonical copy exists with no stale source copy.

- **Completed work**: record outcome and verification, update status, move to `completed/`
- **Held work**: record hold reason and restart condition, update status, move to `backlog/current/`
- **Genuinely continuing**: keep in `active/` with an accurate next action

**Do not**:
- Copy tasks — use `git mv` for tracked moves
- Change tasks owned by another session, running or paused
- Leave completed or held tasks in `active/`

### 2a. Stop early (unfinished, to be picked up later)

Use this when you end a session with the task unfinished and a different session or agent will continue it (window closed, agent swapped, out of budget for the day). It replaces the full closeout. Do only these steps, and only for your own task:

1. Update the task file's **Progress / Resume** block: done so far, next step, last verified fact (with the command), and any commits that exist only locally (`git log --oneline origin/main..HEAD`).
2. In the task file's YAML set `status: backlog` and `resume: true`.
3. Move the task from `active/` to `backlog/` with `git mv`. Run `find . -name "<task file name>"` and confirm exactly one copy exists. If more than one exists, stop and report (section 0).
4. Add one `status.md` Session Log entry (section 1b) with **Stop condition** set to `INCOMPLETE`, a pointer to the task file, and the next action. Detail lives in the task file, not in `status.md`.
5. Do not stage or commit other files, and do not clean up anything else (section 0). Commit only your own explicit paths if the human has asked you to commit; otherwise leave them uncommitted and say so.

A session that is only **paused** (it will resume itself) does not do this. Its task stays in `active/` and it only notes the pause in the task's Progress block.

---

## 3. Cleanup of This Session's Own Artifacts

Clean up the stale drafts, superseded reports and duplicate artifacts that **this session created** (section 0 decides what that means):
- Archive useful history; remove only disposable material this session itself created
- Task files are never deleted without verification and human approval (Rule 29)
- Age, or being untracked, is not grounds for touching a file. Ownership decides.
- Do not turn cleanup into a broad audit, and do not tidy anything outside this session's changes

---

## 4. Closeout Handoff

Leave a brief statement of:
- Maintenance performed (what was updated, moved, archived)
- Exceptions (files that belong to other sessions and were left as-is, unresolved restrictions, anything else left as-is with reason)

---

## 5. Sessions Without Filesystem Access

If the session has no filesystem access:
- Draft closeout artifacts as text for an authorized local session to save
- Do not claim artifacts were saved when they were not
- Include full content in the handoff so a local session can apply it

---

---

## 6. Not yours

Pausing or closing a project, `status.md` curation, archiving, and workspace maintenance are the planning agent's, with the human (`PLANNING_AGENT_CLOSEOUT.md`). Do not do them.
