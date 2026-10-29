# Galaxy Game UI — NEEDS_REVIEW

Short, active list of items that need a second opinion or a human decision. Primary interface for review and planning agents: read this first, then `status.md`.

**Last updated:** 2026-10-29 (created by Claude web session; drafted as text, saved by Tracy)
**Evidence labels:** VERIFIED = a command or a read of GitHub `main` shows it. REPORTED = stated by a person or agent, not re-checked.

## How this file works
- One entry per item. State: OPEN (with a specific next action and owner) or RESOLVED (with reasoning). Nothing is silently dropped.
- Move RESOLVED entries to the bottom section; prune them once they are in `status.md`.
- Implementation agents add an entry instead of resolving alone when they:
  - touch gitignored or data paths,
  - mark something complete without same-session re-verification,
  - depend on or conflict with a completed task's architecture,
  - find two docs that disagree about the same system, or
  - catch themselves repeating an identical action three or more times.
- Agents paste raw command output in entries, not summaries of it.

---

## OPEN

### 1. T3a bookkeeping not done
- **State:** OPEN — owner: Qwen (one prompt), Tracy approves
- **Evidence:** T3a is still in `tasks/drafts/` and `tasks/completed/` has no copy (VERIFIED, GitHub `main`, 2026-10-29). Code commit `b1eb0d3` on `review/qwen-ui-prototype` is REPORTED in the 2026-10-26 handoff; not re-checked (app repo not readable from the web session).
- **Next action:** move T3a to `completed/` with a short completion note; set `status: drafts` in `2026-10-23-HIGH-DASHBOARD-OVERVIEW-FROM-FIXTURES.md` (VERIFIED `status: active` while in `drafts/`); refresh `status.md` (header date still 2026-10-23, Open Sessions rows stale, 2026-10-26 closeout entry not appended).

### 2. Two lessons must go into T3a-2 before dispatch
- **State:** OPEN — owner: Claude web (draft text), Tracy applies
- **Detail:** (a) body background/text color is set in the layout in T3a-2, not in CSS; (b) new tokens live in `@theme static`.
- **Next action:** edit the T3a-2 task, then dispatch T3a-2. Do not dispatch T3a-2 on top of unreviewed CSS.

### 3. Seeds draft contradicts a decision
- **State:** OPEN — owner: Tracy
- **Detail:** the seeds draft's criterion 2 deletes `UserCsvLoader`. Tracy decided to keep it and says login/seed work (REPORTED).
- **Next action:** retire the seeds task, or reduce it to "run `db:seed`, confirm login" with the `UserCsvLoader` deletion removed.

### 4. Task lifecycle docs disagree
- **State:** OPEN — owner: planning agent, human decides
- **Detail:** the dispatch block's Step 0 moves `drafts → active`; Rule 12 as summarized in a Haiku log (REPORTED) says `drafts → backlog (human) → active`.
- **Next action:** reconcile in the 2026-10-26 template-wording draft in the agent-tasks project; confirm against the actual GUARDRAILS text first.

### 5. Possible uncommitted `status.md` edits
- **State:** OPEN — owner: Tracy
- **Detail:** a stale Haiku planning session reported uncommitted edits to `projects/galaxy-game-ui/status.md`; the routing table is already on `main`.
- **Next action:** `git status --short` in both repos and `git diff` on `status.md` before staging anything there. Also check whether `~/scratch/qwen-reports/` still exists (cleanup: `rm -r ~/scratch/qwen-reports`).

### 6. Decisions waiting on Tracy
- **State:** OPEN — owner: Tracy
- T3c routing: Claude Sonnet (premium) vs Qwen first.
- Galaxy Game accent color (`--accent` is `#4aa8ff` pending a human choice).
- Eden identifier `AOL-732356` and wormhole link types (fixtures use `unknown` until confirmed).

### 7. Later tasks to draft (not yet started)
- **State:** OPEN — owner: Claude web drafts when asked; do not dispatch
- Port base components from the first attempt (commit `8ce1250`: `eve_dashboard.scss` buttons/forms/bars/badges/utilities; `dashboard.scss` topbar/panel base) into the Tailwind entry file as plain CSS using tokens only. Comes after T3a-2.
- `landing.scss` does not match the current landing view's classes (`hero-section`, `status-grid`, `stat-large`, ...); needs a small new stylesheet. Landing comes after the dashboard.

### 8. Commit authorship is invisible
- **State:** OPEN — owner: Tracy
- **Detail:** all four task commits carry Tracy's git identity, so history does not show which agent acted.
- **Next action:** decide whether to add an `Agent: <name>` line to commit messages.

---

## RESOLVED

(none yet)
