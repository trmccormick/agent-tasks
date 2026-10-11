# Session Closeout — Shared Procedure

Applicable to planning, research, implementation, review, or any combination.
Model-neutral. Applies regardless of which agent ran the session.
Project-specific guidance lives in `projects/<project>/SESSION_GUIDANCE.md`.

**Session closeout covers the task work done in that session, and nothing else.** Maintenance of everything else (stale or duplicate files, other sessions' drafts, archive housekeeping) belongs to the planning agent and the human (section 7).

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
| Planning agent | The `status.md` header, trimming and archiving. Cleans at session start or at closeout. |
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

### 1c. Planning agent: curate

Do this at session start or at closeout, never in the middle of other work.

1. Update the header: `Last Updated: YYYY-MM-DD — <what happened>`.
2. Check every claim that could be stale (for example "None Currently Active", "All recent work has been committed", cited commit hashes) with a live command. Keep it only if the command confirms it.
3. Fold entries older than roughly a week into a short summary block. Keep what shipped (commit hashes), open blockers and follow-ups, and standing guardrails and lessons.
4. Move the original entries verbatim to `archive/status-log-YYYY-MM.md`, with a short note above each saying why it moved, and leave a one-line pointer in status.md.
5. Never silently drop a claim. It is either verified live, or preserved in the archive.
6. An entry appended to the wrong project goes to the archive with a note, not into the right project's file.
7. Curation covers `status.md` only. It does not move, archive or delete another session's task files, drafts or reports (section 0).
8. Show the human the diff. The human commits.

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

## 6. Pausing or Closing a Project

Put the project state in the first lines of its `status.md`: `Project state: active | paused | archived`, plus one sentence on why. Quiet tracking is not inactivity. Use `paused` rather than deleting, and never archive without the human's approval.

---

## 7. Maintenance (planning agent, with the human)

The planning agent keeps everything outside a single session's task work in order: stale or duplicate task files, tasks in the wrong folder, untracked drafts and reports, old summaries and handoffs, archive housekeeping, and `status.md` curation (section 1c). Session agents do not do this (section 0).

1. **Inventory first and change nothing.** List what looks out of place, for example `git status --short`, a task that exists in two folders, or an `active/` task with no recent entry.
2. **Classify each item by owner:** this planning session, a session the human says is finished, a paused or in-progress session, or unknown. Treat unknown as paused.
3. **Propose, then wait.** Give the human the list with a recommended action for each item. Do not act on anything owned by a paused or unknown session unless the human names it and says what to do.
4. **Act on the approved items only.** Use `git mv` into an archive location rather than deleting. Never delete a task file without verification and the human's approval (Rule 29). Keep one canonical copy per task. If two copies conflict, stop and ask, as the duplicate-copy rule in `rules/GUARDRAILS.md` requires.
5. **Verify and report.** Run `git status --short` and the relevant listing, confirm the result is what was approved and nothing more, and list what was left alone and why.
