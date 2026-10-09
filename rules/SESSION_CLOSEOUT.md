# Session Closeout

Applies to every agent, local or web, at the end of a work session. Universal rules only. Project-specific guidance lives in `projects/<project>/SESSION_GUIDANCE.md`. Which model or tool an agent runs on is not part of this document.

## 1. Who does what

| Role | Owns |
|---|---|
| Session agent (does the work) | Appends one entry to the end of its own project's `status.md`. Nothing else in that file. |
| Planning agent | The `status.md` header, trimming, and archiving. Cleans at session start or at closeout. |
| Human | Commits and pushes (Rule 26). Approves any deletion (Rule 29). |

## 2. Session agent: append one entry

1. Open the status file of the project you worked in. Never another project's.
2. Go to the end of the file. Add `---`, then this heading exactly:

   `## 📝 Session Log — YYYY-MM-DD (short title)`

3. Under it, write four bullets and no sub-headings:
   - **Changed:** files touched, by path.
   - **Committed:** commit hashes, or "none".
   - **Not committed:** what is left uncommitted, or "none".
   - **Stop condition:** why you stopped, or "none" if the work is finished.
4. Every fact must name a command that shows it (for example `git status --short`, `git log -1 --stat`). If you did not run a command that shows it, do not state it.
5. Do not edit the header or any earlier entry. Do not stage or commit.
6. If the session was read-only, still write the entry and say "Changed: none (read-only)".

## 3. Planning agent: curate

Do this at session start or at closeout, never in the middle of other work.

1. Update the header: `Last Updated: YYYY-MM-DD — <what happened>`.
2. Check every claim in the file that could be stale (for example "None Currently Active", "All recent work has been committed", cited commit hashes) with a live command. Keep it only if the command confirms it.
3. Trim old entries by moving them, verbatim, to `archive/status-log-YYYY-MM.md`. Add a short note above each moved entry saying why it moved.
4. Never silently drop a claim. It is either verified live, or preserved in the archive.
5. An entry that was appended to the wrong project goes to the archive with a note, not into the right project's file.
6. Show the human the diff. The human commits.

## 4. Evidence labels

- **VERIFIED**: confirmed by a command in this session.
- **REPORTED**: stated by a person or an earlier entry, not re-checked.

An entry never labels something VERIFIED without naming the command.

## 5. Pause or close a project

Put the project state in the first lines of its `status.md`: `Project state: active | paused | archived`, plus one sentence on why. Quiet tracking is not inactivity. Use `paused`, never delete, and never archive without the human's approval.
