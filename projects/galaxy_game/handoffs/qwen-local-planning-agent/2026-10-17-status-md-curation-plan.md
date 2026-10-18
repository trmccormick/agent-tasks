# Handoff: status.md Curation Plan

**Created:** 2026-10-17
**Author:** qwen (planning agent)
**Project:** galaxy_game
**Status:** WAITING FOR HUMAN APPROVAL — nothing changed yet

---

## Objective

Curate `projects/galaxy_game/status.md` for the next planner: update header date, add a Current Snapshot block, flag stale In Flight items, and keep the file scannable. **No other edits.**

---

## Lane Discipline

- **asset-ui tasks = ChatGPT lane.** Do NOT move, dispatch, or reclassify any task under `tasks/backlog/asset-ui/` or `tasks/active/asset-ui/`. Those belong to the ChatGPT workflow.
- All other curation actions below are galaxy_game general housekeeping.

---

## Approved Next Actions (1–6)

### 1. Update Header Date
- Change `Last Updated: 2026-10-11` → `Last Updated: 2026-10-17`
- Add brief summary of latest shipped work (origin/main tip `98d200e`: "docs: resolve request construction and base URL in C verification draft")

### 2. Add Current Snapshot Block
- Insert a short snapshot section after the header noting:
  - agent-tasks: clean, main == origin/main
  - galaxyGame: clean, main == origin/main
  - Active tasks: 1 (asset-ui/asset-generation-standalone-execution.md)
  - Drafts: 4 (catalog_image containment parent + A/B/C split)

### 3. In Flight "Needs Re-Verify" Banner
- Add a banner or inline note to both In Flight items:
  - **Inventory add_item Diagnostic (2026-09-27)** — last activity ~20 days ago; mark `NEEDS RE-VERIFY`
  - **Read-Only RSpec Investigation (2026-10-01)** — last activity ~16 days ago; mark `NEEDS RE-VERIFY`
- Do NOT close them out. Just flag for the next session to confirm relevance.

### 4. Note Orphan Active asset-ui Task
- Add a one-line note in In Flight or as a separate callout:
  - `2026-09-06-HIGH-FEATURE-ASSET-GENERATION-STANDALONE-EXECUTION.md` has no session owner, checklist incomplete (2/3 boxes unchecked), last updated 2026-09-06. Flag for ChatGPT lane review only.

### 5. Fold Early-Oct Session Logs (conditional)
- If human approves an archive path: fold session log entries older than ~7 days (2026-10-06 through 2026-10-08) into a summary block or move originals to `archive/status-log-2026-10.md` with one-line pointers.
- **Conditional** — do not proceed without explicit approval.

### 6. Leave Alone
- **Untracked files** (5 items): belong to paused sessions, protected per PLANNING_AGENT_CLOSEOUT.md Rule 6
- **Drafts** (4 files under `backlog/drafts/`): waiting for human review, `resume: false`
- **Superseded folder** (7 tasks): propose archive in a future session, not today
- **Deferred-cleanup folder** (16 tasks): propose review in a future session, not today

---

## Next Step

Wait for human approval. On approval, edit `status.md` only — apply actions 1–4 immediately, action 5 conditionally, action 6 as noted. No commits by the planning agent; the human commits and pushes.

---

## Stop Condition

Handoff written. Nothing else changed. Awaiting human go-ahead.
