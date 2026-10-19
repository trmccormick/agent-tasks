# Implementation Agent Closeout

For **implementation** sessions only (code, specs, task execution).  
Model-neutral. One session, one role.

- **Planning sessions** use `PLANNING_AGENT_CLOSEOUT.md` — not this file.
- **Research / review** sessions may use `SESSION_CLOSEOUT.md` instead.
- This file is the procedure to point **open implementation sessions** at when ending work.

**Closeout covers only this session’s task work.** Workspace cleanup, status header curation, and other sessions’ files belong to the planning agent and the human.

---

## 0. Scope — only what you touched

- Close out only files **this session** created or changed.
- Tasks in `active/`, uncommitted changes, and untracked files you did **not** create = **paused session**. Leave them alone.
- A missing closeout means **paused**, not abandoned.
- **Stage explicit paths only.** Never `git add .`, `git add -A`, or `git commit -a`.
- Forbidden during closeout: `git stash`, `git reset`, `git checkout` (of files), `git restore`, `git clean`, plain `git pull` on a dirty tree. Use `git fetch` and report if needed.
- Duplicate task file? `find . -name "<task file name>"` — if more than one copy, **stop and report**. Do not move or delete.
- Do not write "complete" / "closed out" in status if a **review is still pending**. Name the pending review under **Stop condition**.

---

## 1. Task file (mandatory)

Do this for **your** task only.

### 1a. Task finished

1. Record outcome and verification in the task file (what shipped, commands run, results).
2. Set YAML: `status: completed` (and clear `resume` if set).
3. `git mv` the task from `active/` → `completed/` (use the project’s completed layout, e.g. `completed/YYYY-MM/`).
4. Confirm one copy: `find . -name "<task file name>"` → exactly one path under `completed/`.

### 1b. Task unfinished — another session will continue (stop early)

1. Update **Progress / Resume**: done so far, next step, last verified fact (+ command), local-only commits (`git log --oneline origin/main..HEAD`).
2. YAML: `status: backlog`, `resume: true`.
3. `git mv` from `active/` → `backlog/` (or the folder `projects/<project>/SESSION_GUIDANCE.md` names, e.g. Galaxy `backlog/current/`).
4. Confirm one copy with `find`.
5. Status entry **Stop condition** = `INCOMPLETE` + pointer to the task file. Detail stays in the task file.

### 1c. Task paused — **this same session** will resume later

- Leave the task in `active/`.
- Update Progress / Resume with the exact resume point.
- Do **not** set `resume: true` or move to backlog.
- Still write a status.md entry (section 2) with **Stop condition**: `paused`.

### 1d. Do not

- Copy tasks (always `git mv` for tracked moves).
- Touch tasks owned by another session.
- Leave a finished or held task in `active/`.

---

## 2. status.md — append only (mandatory)

**You append one Session Log entry at the end of this project’s status.md. Nothing else in that file.**

| Role | Owns |
|------|------|
| Implementation session | One new entry at the **end** of `projects/<project>/status.md` |
| Planning agent | Header, trimming, archiving (`PLANNING_AGENT_CLOSEOUT.md`) |
| Human | Commits and pushes (Rule 26) |

### 2a. How to append

1. Open `projects/<project>/status.md` only (never another project’s).
2. Go to the **end** of the file. Add `---`, then:

   `## 📝 Session Log — YYYY-MM-DD (short title)`

3. Four bullets only — no sub-headings:

   - **Changed:** files touched, by path.
   - **Committed:** commit hashes, or `none`.
   - **Not committed:** left uncommitted, or `none`. List other sessions’ files as `left as-is, not this session`.
   - **Stop condition:** why you stopped, or `none` if finished. Include next action and who owns it. Use `paused` / `INCOMPLETE` when section 1b or 1c applies.

4. Every fact must name a command that shows it (`git status --short`, `git log -1 --stat`, etc.). No command → do not state it.
5. **Do not** edit the header, earlier entries, In Flight tables, or fold/archive logs.
6. **Do not** stage or commit unless the human asked you to (and then only your paths).

### 2b. Evidence labels

- **VERIFIED** — confirmed by a command in this session (name the command).
- **REPORTED** — from a person or earlier entry, not re-checked this session.

### 2c. If you cannot write the entry

Example: `status.md` already has another session’s uncommitted edits. **Say so in chat and stop on that item.** Do not skip silently. Do not stage the file as it stands.

### 2d. Read-only sessions

If you produced findings another session needs: append an entry with `Changed: none (read-only)`.  
If you changed nothing and left nothing for anyone else: no status entry; say so in the closeout statement (section 4).

---

## 3. Your own artifacts only

- Clean up only disposable drafts/reports **this session created**.
- Never delete task files without human approval (Rule 29).
- Age or “untracked” is not a reason to touch someone else’s file.

---

## 4. Closeout statement (chat)

Report briefly:

1. Task outcome: completed / incomplete / paused — and task path.
2. status.md: entry written (yes/no); if no, why.
3. Commits (hashes) or `none`; push only if human approved.
4. Left as-is (other sessions’ files).
5. Next action and owner.

---

## 5. No filesystem access

- Draft the status entry and task updates as text for a local session to apply.
- Do not claim files were saved when they were not.
- Include full content so a local agent can write them.

---

## 6. Not yours

Do **not**:

- Curate status.md header or archive old session logs.
- Reconcile the whole `active/` or `backlog/` tree.
- Move, archive, or delete paused sessions’ work.
- Pause or archive the project.

That is planning + human work (`PLANNING_AGENT_CLOSEOUT.md`).

---

## Quick checklist

- [ ] Task file updated (Progress / YAML / outcome)
- [ ] Task in the right folder (`completed/` / `backlog/`+`resume: true` / still `active/` if paused)
- [ ] One canonical copy (`find`)
- [ ] One Session Log appended at end of `projects/<project>/status.md`
- [ ] No header or earlier status edits
- [ ] Explicit-path staging only; human approves commit/push
- [ ] Closeout statement posted in chat