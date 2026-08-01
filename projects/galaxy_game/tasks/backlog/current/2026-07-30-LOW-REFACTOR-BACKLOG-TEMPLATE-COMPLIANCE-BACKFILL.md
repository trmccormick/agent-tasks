---
status: backlog
priority: LOW
type: refactor
system_domain: OTHER
created: 2026-07-30
---

# Backfill missing template sections in older backlog/current/ task files

## Context
A 2026-07-30 grep audit of every file in
`projects/galaxy_game/tasks/backlog/current/` for six required
`TASK_TEMPLATE.md` sections (Prerequisites, Architecture Gotchas,
REQUIRED Synthesis Report, Stop Conditions, Completion Report, Handoff
Summary) found a clean split: every task file created 2026-07-13 through
2026-07-25 is missing 2-6 of these sections; every file from 2026-07-26
onward is clean or nearly clean.

Root cause (confirmed, not guessed): the pre-2026-07-26 files were
authored by Claude in earlier planning sessions, before the full
7-section template was locked in and consistently applied starting
2026-07-26. This is a template-adoption timing gap, not an
implementation-agent compliance failure — no need to treat it as
something an agent did wrong.

## Known affected files (from the 2026-07-30 audit)
- `2026-07-13-HIGH-FEATURE-CIV4-SURFACE-VIEW-GAMEPLAY.md` — missing Stop
  Conditions, Completion Report, Handoff Summary. Also has an additional
  nuance: Prerequisites and Architecture Gotchas headers are present but
  generic/misordered relative to the rest of the file — needs a full-file
  rewrite of those two sections, not just appending the three missing ones.
- `2026-07-13-HIGH-FEATURE-SPRITE-TILES-SURFACE-VIEW-INTEGRATION.md` —
  missing all 6 sections
- `2026-07-24-LOW-RESEARCH-PROPELLANT-CONSUMPTION-DATA-FOR-RAPTOR.md` —
  missing all 6 sections
- `2026-07-24-MEDIUM-DOCUMENTATION-HIERARCHY-DIAGRAM-NAMESPACE-HISTORY.md`
  — missing Prerequisites, Synthesis Report
- `2026-07-24-MEDIUM-DOCUMENTATION-WORLDHOUSE-DESIGN-CLARIFICATION.md` —
  missing Prerequisites, Synthesis Report
- `2026-07-24-MEDIUM-REFACTOR-REMOVE-ALIENLIFEFORM-ARTIFACT.md` —
  missing Prerequisites, Synthesis Report
- `2026-07-24-MEDIUM-REFACTOR-TERRAFORMING-MANAGER-CLEANUP.md` —
  missing Prerequisites, Synthesis Report
- `2026-07-25-HIGH-BUGFIX-BIOSPHERE-ORPHANED-HABITABILITY-METHOD.md` —
  missing all 6 sections
- `2026-07-25-HIGH-FEATURE-CALCULATE-HABITABILITY-REPLACEMENT.md` —
  missing Architecture Gotchas, Synthesis Report, Stop Conditions,
  Completion Report, Handoff Summary
- `2026-07-25-MEDIUM-BUG-FIX-SLOW-BASE-SATELLITE-SPEC-RUNTIME.md` —
  missing Architecture Gotchas
- `2026-07-25-MEDIUM-BUGFIX-DUPLICATE-TEMPERATURE-DELEGATION-SPEC.md` —
  missing all 6 sections
- `2026-07-26-LOW-BUGFIX-INFRASTRUCTURE-COST-CALCULATOR-DEAD-CODE.md` —
  missing all 6 sections (created just before the 07-26 cutover)
- `2026-07-29-HIGH-BUGFIX-INVENTORY-CAN-STORE-TEST-BYPASS.md` — missing
  only Handoff Summary (minor, near-compliant)

## Expected outcome
Each file above has the missing sections backfilled at the level of
detail the task actually needs — not boilerplate placeholders. Files
still awaiting implementation get real Prerequisites/Gotchas; files
already implemented or superseded may just need Completion
Report/Handoff Summary filled in retroactively, or may turn out to be
stale enough to reconsider entirely (flag rather than force a backfill
if a file looks abandoned).

## Recommended approach
Two-pass, not reactive mid-session patching:
1. This file itself is effectively the catalog pass (already done above)
2. A separate pass decides, file by file: backfill as-is / backfill with
   updates (content may be stale) / reconsider whether the task is still
   relevant at all

## Stop Conditions
- If a file's underlying task looks stale/superseded rather than just
  template-incomplete, flag it rather than backfilling a template for
  work that may not need to happen — don't let this task grow into "also
  re-litigate whether these tasks are still worth doing."
