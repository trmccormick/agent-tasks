# Session Closeout — Shared Procedure

Applicable to planning, research, implementation, review, or any combination.
Model-neutral. Applies regardless of which agent ran the session.
Project-specific guidance lives in `projects/<project>/SESSION_GUIDANCE.md`.

---

## 0. Scope: only what this session touched

Closeout covers only the files this session created or changed. Another session's work is off limits, whether that session is running, paused or finished: its task files, drafts, reports, uncommitted changes and untracked files.

- **How to tell what is yours.** Compare `git status --short` at closeout with the state at session start, or use the session's own record of the files it changed. Anything outside your own changes is not yours. An untracked file is not yours just because nobody has committed it.
- **Not yours: leave it alone.** Do not stage, move, rename, archive, delete or "clean up" it. List it under "Not committed" in your status entry as "left as-is, not this session" and stop.
- **Only exception:** the human names the file and says what to do with it.
- **Unsure whether a file is yours:** leave it and ask.
- **Stage explicit paths only.** Never `git add .`, `git add -A` or `git commit -a`. Before committing, `git diff --cached --stat` must list only your files.
- **The planning agent's startup closeout check** (see `PLANNING_AGENT_SESSION_START.md`, Step 6) applies only to sessions known to be finished, and only to that session's own files. Paused or in-progress sessions are skipped.

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

### 1c. Planning agent: curate

Do this at session start or at closeout, never in the middle of other work.

1. Update the header: `Last Updated: YYYY-MM-DD — <what happened>`.
2. Check every claim that could be stale (for example "None Currently Active", "All recent work has been committed", cited commit hashes) with a live command. Keep it only if the command confirms it.
3. Fold entries older than roughly a week into a short summary block. Keep what shipped (commit hashes), open blockers and follow-ups, and standing guardrails and lessons.
4. Move the original entries verbatim to `archive/status-log-YYYY-MM.md`, with a short note above each saying why it moved, and leave a one-line pointer in status.md.
5. Never silently drop a claim. It is either verified live, or preserved in the archive.
6. An entry appended to the wrong project goes to the archive with a note, not into the right project's file.
7. Curation covers `status.md` only. It does not move, archive or delete another session's task files, drafts or reports (section 0).
7. Show the human the diff. The human commits.

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
