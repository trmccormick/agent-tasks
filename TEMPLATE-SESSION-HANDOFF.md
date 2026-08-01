# Session Handoff — <Project> — YYYY-MM-DD (time-of-day)

## Purpose of this handoff

- This document summarizes **what was accomplished this session** and **what state the work is in**.
- Created by: web agent (Perplexity) on request, not by the local planning agent.
- Intended readers: any agent or human continuing work next session (Claude, Qwen, local LLMs, humans).
- Assumptions:
  - `status.md` is maintained primarily by local agents (Copilot / Ollama / Qwen) and may be incomplete.
  - Git commit history from the session can be used to reconstruct or cross-check work.
  - Readers may also consult `NEEDS_REVIEW.md` for items requiring decisions or second opinions.

## Session summary

- 3–6 bullets capturing:
  - The main task(s) or areas touched.
  - Whether they were completed, paused, or abandoned.
  - Any major blockers, architectural clarifications, or priority shifts.
- Where possible, anchor bullets to:
  - Concrete outputs (e.g., “Phase 0 verification completed for X task”).
  - Observable changes (e.g., “task file rewritten, commit hash …”).

## Tasks worked on

For each task or significant piece of work touched this session:

### <Task file or short name>

- **Status**: <e.g., Phase 0 complete, implementation paused; or “closed”; or “abandoned”>
- **What was done**:
  - Concrete actions (e.g., “Phase 0 verification: inspected X, Y, Z”).
  - Key findings (e.g., missing data fields, upstream tasks not merged, tests added).
  - If available, reference relevant commit hashes or files changed.
- **Decisions made**:
  - e.g., “pause implementation”, “defer until MVP backend is stable”, “merge as-is”.
- **Open questions / escalations**:
  - If something needs a decision or second opinion, reference the corresponding entry in `NEEDS_REVIEW.md` instead of re-explaining here.

Repeat for each task.

## New standing facts (if any)

- Only include facts that:
  - Affect future work across sessions.
  - Aren’t already obvious from `status.md` or task files.
- Examples:
  - Clarified architectural boundaries.
  - MVP priority shifts.
  - Patterns that should be reused.
  - Known gotchas that future agents should not re-derive.

## Recommended next steps

- 3–5 concrete actions or decision points for the next session:
  - e.g., “Decide whether to create a small data-contract task for X.”
  - e.g., “Confirm whether Y task is actually merged.”
  - e.g., “Prioritize A vs. B for next session.”
- If something is already in `NEEDS_REVIEW.md`, just reference it:
  - “See NEEDS_REVIEW.md entry: ‘<title>’.”

## Links / pointers

- `status.md` — local-agent-maintained log (may be incomplete).
- `NEEDS_REVIEW.md` — items needing decisions / second opinions.
- Git: session commits can be used to reconstruct detailed work if needed.
- Any specific task files worth reading first (optional).