# Session Summary — Stale Task Audit Workflow Redesign
**Date**: 2026-07-02
**Role**: Claude (web, free tier) — Review/Planning
**Purpose**: For another Claude agent to use in updating `status.md`

---

## What This Session Covered

Reviewed and redesigned the stale task audit workflow (originally Haiku-drafted from a
prior session) to align with actual project constraints. Produced two working files,
ready to test tomorrow. Also reviewed a prior real attempt at the GUARDRAILS.md
consolidation as reference input for that test.

---

## Key Corrections Made During Review

- **Access model clarified**: filesystem/codebase access (audit Steps 1–3) requires a
  **local agent via GitHub Copilot custom agent config** (Qwen currently) — not a model
  preference, a hard constraint. Web agents (Gemini, Claude, Perplexity) cannot reach the
  filesystem and only work from pasted/uploaded content. Continue is installed but
  confirmed inactive/unused for this workflow.
- **Cost tier ≠ access tier**: Copilot's own hosted agents *can* touch the filesystem but
  lost their free (0x) tier — local Qwen-via-Ollama is the 0x replacement. ChatGPT free
  tier is reserved for image generation; usable as a last-resort design-pass option, not
  a rotation default.
- **Role alignment**: audit work is STRATEGIST role per `agent-tasks/README.md`, not the
  earlier draft's invented "Planning Agent (Research Phase)" role.
- **Evidence discipline**: findings saved to `summaries/` as files (not chat-paste),
  enabling direct handoff between agents without retyping. Summaries folder confirmed as
  **working memory, not permanent archive** — cleared once findings land in a task file
  or `status.md`.
- **TASK_TEMPLATE.md corrected**: fixed a stale line claiming "local models... cannot run
  commands or access the DB" (a leftover Continue-era assumption). Now correctly states
  Copilot-run Qwen has terminal/tool-use access. Full corrected file produced directly
  (Claude originally authored the template, so fixed in place rather than task-dispatched
  to another agent).

---

## New Workflow Structure

**Files produced**: `STALE_TASK_AUDIT_WORKFLOW.md` + `DISPATCH_STALE_TASK_AUDIT.md`
(placed in `refactored-task-files/` at repo root; original Haiku drafts moved to
`haiku-draft-md-files/` for reference)

Six-step pipeline, local-agent steps clearly separated from web-agent steps:

1. **Overlap & Related-Artifact Scan** (local agent) — new step, added after reviewing
   real examples of both failure modes (see below)
2. **Fast Resolution Check** (local agent) — cheap grep/status.md/commit check before
   deep audit; lets `STALE_SOLVED` tasks exit immediately without full pipeline cost
3. **Full Audit & Classification** (local agent) — classifies as `STALE_SOLVED` |
   `STALE_OBSOLETE` | `INCOMPLETE` | `BLOCKED`; also now flags any out-of-scope follow-up
   work discovered as a **recommended new task** rather than scope-creeping the current one
4. **Design Pass** (web agent — Gemini/Claude/Perplexity, ChatGPT last resort) — human
   manually bridges the local agent's summary file into the web session, since web agents
   can't reach `summaries/` themselves
5. **Draft/Refine Task File** (using `TASK_TEMPLATE.md` structure)
6. **File** — archive to `tasks/reference/` or route to correct phase folder; standard
   REVIEWER Tier 1/Tier 2 gate before commit (per GUARDRAILS Rule 26 — no autonomous commits)

---

## Why Step 1 (Overlap Scan) Was Added

Reviewed real evidence of two distinct overlap failure modes that the original workflow
had no check for:

- **Duplicate/competing task files**: `TASK_GUARDRAILS_SPLIT.md` and
  `TASK_WEDNESDAY_SURGICAL_AUDIT.md` — same source file (`docs/GUARDRAILS.md`), same
  creation date (2026-06-21), no cross-reference between them, different priority/phase/
  scope specified in each.
- **Orphaned supporting artifacts**: reviewed the first full test run of the audit
  pipeline (settlement-view-phase3, a 6-file handoff chain run 2026-07-01). Qwen's
  research incidentally surfaced `PHASE_3_SPRITE_SHEET_PROMPT.md`,
  `CHROMAKEY_PHASE3_README.md`, and a reusable `chromakey_spritesheet.py` script — none
  of which were referenced by the original stale task or by the planning stage. They
  only surfaced because Qwen happened to go looking during codebase research.
- Also noted (not yet resolved, flagged for future review): that same test run showed an
  unreconciled phase-classification drift — the planning stage (`ANALYSIS.md`) estimated
  Phase 7+, while the research stage (`RESEARCH_FINDINGS.md`) landed on Phase 14+, with
  no file in the chain explicitly reconciling why the estimate moved. Worth a
  phase-drift check in a future workflow revision.

---

## GUARDRAILS.md Resolution (Human-Confirmed)

Resolved the "which GUARDRAILS.md is authoritative" ambiguity that had stalled multiple
prior task-file attempts:

- **`agent-tasks/rules/GUARDRAILS.md`** = authoritative, generic agent execution rules,
  applies across all projects (Galaxy Game, Samvera, WVU Libraries). Correct as-is.
- **`docs/GUARDRAILS.md`** = Galaxy Game–specific, currently mixes several concerns that
  need to be sorted into four destinations, not two:
  1. Generic rules already covered in the `agent-tasks/rules/` version → duplicates,
     discard
  2. Generic rules genuinely missing from the `agent-tasks/rules/` version → real gaps,
     merge in (requires diffing, not assuming)
  3. Galaxy Game–specific process/agent rules → move to a project-specific rules file,
     not named "GUARDRAILS" (to avoid recreating this same confusion)
  4. Game design / system intent content → architecture/gameplay/systems/flavor docs

This four-way sort is written directly into `STALE_TASK_AUDIT_WORKFLOW.md` so tomorrow's
dispatch doesn't need to re-litigate it.

---

## Reviewed: Prior GUARDRAILS Consolidation Attempt (2026-07-01)

Found and reviewed five files from a prior same-day attempt at this exact problem:
`2026-07-01-PRE-WORK-CREATE-DIRECTORIES.md`, `2026-07-01-PRE-WORK-RESOLVE-AMBIGUITIES.md`,
`2026-07-01-TASK-GUARDRAILS-UNIFIED-RECONCILIATION.md`, `GUARDRAILS_REF_PACKAGE.md`,
`TASK_REF_GUARDRAILS_MIGRATION_V2.md`.

**Findings**:
- Independently confirmed the same 680 vs. 425 line drift between the two GUARDRAILS
  files — real, measured evidence.
- Independently flagged the same canonical-file ambiguity as a blocker, never resolved —
  this session's resolution (above) unblocks it.
- Independently flagged the same `em_power_shield_tiers.md` target-path ambiguity
  (`docs/systems/` vs `docs/architecture/stations/` vs `docs/architecture/structures/`)
  — **still unresolved**, carries forward as an open item for tomorrow.
- Real evidence discipline present: fee/tax constants (SCC 0.5%, Broker 0.3%, Sales Tax
  3.37%) were checked against `GLOSSARY_SYSTEM_MECHANICS.md` and confirmed aligned, not
  just asserted.
- The consolidation effort itself was fragmented across five uncoordinated files with no
  single merged task — a live example of the exact overlap problem Step 1 now exists to
  catch. Nothing in this five-file set was ever executed; all marked "awaiting approval"
  or "ready for review."
- Minor cleanup flag: `2026-07-01-TASK-GUARDRAILS-UNIFIED-RECONCILIATION.md` contains
  stray uncleaned `[cite: 5]` artifacts throughout.

---

## Open Items Going Into Tomorrow

1. Test the redesigned workflow against a real stale task — likely candidate is the
   GUARDRAILS consolidation itself, using the 2026-07-01 five-file set as prior research
   input rather than starting from scratch.
2. Resolve `em_power_shield_tiers.md` target path before/during that run.
3. Consider whether a phase-drift reconciliation check should be added to Step 3 (flagged
   from the settlement-view test run, not yet built into the workflow doc).
4. `GUARDRAILS.md` (the agent-execution-rules one referenced everywhere in this project)
   was requested but never actually received in this session — still unreviewed against
   the two workflow files produced today.

---

## Files Produced This Session (in `/mnt/user-data/outputs/`, moved by user to
`refactored-task-files/` at repo root)

- `STALE_TASK_AUDIT_WORKFLOW.md`
- `DISPATCH_STALE_TASK_AUDIT.md`
- `TASK_TEMPLATE.md` (corrected full replacement)
