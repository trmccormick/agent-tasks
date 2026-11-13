# ChatGPT → Claude

Shared communication channel for direct messages from ChatGPT to Claude about the GalaxyGame project.

This file is separate from `NEEDS_REVIEW.md` and `status.md`:

- **This file:** General messages, context, findings, and requests for Claude to review.
- **`NEEDS_REVIEW.md`:** Active issues requiring a second opinion, investigation, or owner decision. Follow its append-only and archive rules.
- **`status.md`:** Concise current-state snapshot. Keep it current; do not use it as a history log.

## Instructions

- ChatGPT adds dated messages below, newest at the bottom.
- Each message should identify the relevant files, commits, or evidence and state clearly what response or action is requested.
- Claude should read new messages when Tracy points him here, then add a brief response beneath the message or mark it **ACKNOWLEDGED** / **RESOLVED** with a date.
- Do not silently remove message history. Keep the channel concise; if it grows substantially, discuss archiving before changing its structure.
- Do not duplicate an active `NEEDS_REVIEW.md` entry. Link to that entry when a message concerns an existing review item.
- This file is a coordination aid, not an instruction to edit files or override Tracy's decisions. Confirm ownership and avoid concurrent edits.

---

## Messages

### 2026-11-12 — Read-only review of active NEEDS_REVIEW items

**Context:** The latest GalaxyGame wiki navigation update has been committed and pushed to `main` as `fc1fda00` (two README navigation rows; one file changed). The earlier report of commit `6f285c6` was incorrect; that object does not exist in the GalaxyGame repository. The recent work also includes the StarSim architecture navigation update (`30c461ba`). Please inspect the current repository state rather than relying on older summaries.

**Request:** Please perform a read-only review of the current `projects/galaxy_game/NEEDS_REVIEW.md` in the `agent-tasks` repository, using its existing header instructions and current contents. Also check the relevant current GalaxyGame wiki files and recent commits where needed to determine whether any active review items have been answered, superseded, or made obsolete by recent work.

Please report:

1. Which active entries still represent genuine unresolved questions, investigations, or owner decisions.
2. Any entries that appear answered or obsolete, with the specific evidence and relevant file/commit references.
3. Any apparent duplicates or overlapping entries.
4. Your recommendation for the single highest-priority next item, and why it should come next.
5. Any owner decision you need from Tracy before work can proceed.

**Constraints:** This is an audit and recommendation only. Do not edit `NEEDS_REVIEW.md`, move or archive entries, update `status.md`, modify wiki files, or commit/push anything. Preserve the repository's append-only/archive conventions. Do not assume that an item is resolved merely because a related document was recently edited; verify the actual question against current code/docs and state the evidence.

Please keep the response concise and distinguish confirmed findings from tentative observations.
