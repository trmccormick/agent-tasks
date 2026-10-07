# Complete Workflow Edits — Task File

**Created**: 2026-10-06  
**Purpose**: Coordinate remaining workflow simplification edits across six files  
**Status**: ✅ COMPLETE — all seven steps done, closeout recorded below

---

## Execution Order

1. ✅ **Step 1: Create SESSION_CLOSEOUT.md** — DONE (saved)
2. ✅ **Step 2: Finish PLANNING_AGENT_SESSION_START.md** and remove contradictions — DONE (saved)
3. ✅ **Step 3: Simplify REVIEW_AGENT_GUIDE.md** — DONE (saved)
4. ✅ **Step 4: Simplify PLANNING_AGENT_WORKFLOW.md** — DONE (saved)
5. ✅ **Step 5: Simplify REVIEW_AGENT_WORKFLOW.md** — DONE (saved)
6. ✅ **Step 6: Reduce QUICK_START_PLANNING_SESSION.md to a pointer and dispatch prompt** — DONE (saved)
7. ✅ **Step 7: One consistency check across those files** — DONE (saved)

---

## Requirements (from authorized editing brief)

### SESSION_CLOSEOUT.md (Step 1 — complete)
- Mandatory status.md maintenance with condensing rules
- Task lifecycle reconciliation (completed → completed/, held → backlog/current/ with reason + restart condition, active/ for ongoing only)
- Move-not-copy verification (git mv, one canonical copy)
- Relevant artifact cleanup (archive useful, remove disposable, no broad audit)
- Concise handoff statement (maintenance performed + exceptions)
- Account for sessions with and without filesystem access

### PLANNING_AGENT_SESSION_START.md (Step 2)
- Model-neutral session activities and actual access capabilities
- Reconcile active tasks, status.md, NEEDS_REVIEW.md, relevant handoffs
- Distinguish current observations from dated claims and uncertainty
- Report OPEN review entries without making them automatic priorities
- Test log check with Docker Compose volume mapping fallback; ask before fresh run
- Keep status.md and task/artifact maintenance mandatory at closeout
- Startup check for missed closeout
- Remove project-specific phase-folder procedure

### REVIEW_AGENT_GUIDE.md (Step 3)
- Shared responsibilities, authority, evidence standards
- Distinguish repository-access sessions from supplied-evidence sessions
- Model-neutral; Tracy controls priorities and dispatch
- Reference startup/closeout procedures rather than repeat them

### PLANNING_AGENT_WORKFLOW.md (Step 4)
- Short procedure for explicitly assigned stale/overlapping-task audits
- Check relevant current code, dependencies, prior decisions, overlap
- Default to one concise evidence-backed audit artifact
- Recommend keep, targeted correction, hold, close, or consolidation
- Remove mandatory four-document preparation, fixed dated paths, automatic research → Gemini → Claude chain

### REVIEW_AGENT_WORKFLOW.md (Step 5)
- Short focused review procedure: assignment → relevant evidence → targeted review → actionable disposition
- Disposition: proceed, proceed with caution, or stop for a named decision
- No automatic second reviewer or universal synthesis gate
- Reference shared guide and closeout

### QUICK_START_PLANNING_SESSION.md (Step 6)
- A genuinely short dispatch convenience
- Reference PLANNING_AGENT_SESSION_START.md
- Include project, assignment, and optional availability/preferences
- Explain supplying files when repository access is unavailable
- Remove embedded task template, duplicate startup procedure, implementation dispatch instructions

### Consistency Check (Step 7)
- No contradictory duplicated operational procedures
- No mandatory named-model approval chain
- No old instruction requiring full substantive reports in chat
- No automatic OPEN-entry priority/global block
- No hardcoded project/date paths in generic operational instructions
- Referenced shared files exist
- Unrelated files and running project tasks remain unchanged

---

## Progress Log

### Step 1 — 2026-10-06
- Created `SESSION_CLOSEOUT.md` at repository root
- Covers: status.md maintenance, task lifecycle reconciliation, move-not-copy verification, artifact cleanup, closeout handoff, sessions without filesystem access
- Saved. No synthesis gate required.

### Step 2 — 2026-10-06
- Replaced rigid file-read list with state-reconciliation step (latest handoff first, consult others only for ongoing context)
- Added repository-access vs supplied-evidence distinction
- Removed explicit-bypass requirement on OPEN entries; clarified they are inputs, not automatic priorities
- Allowed dated historical test results; reserved "current result" for fresh evidence
- Kept test-log check and Docker Compose fallback intact
- Added reference to SESSION_CLOSEOUT.md in closeout step (no duplication)
- Added file-first output note with summaries/ default destination
- Startup closeout check preserved as Step 3.7
- Saved. No synthesis gate required.

### Step 3 — 2026-10-06
- Replaced Claude-specific title and role with generic "Review & Planning Agent Guide"
- Clarified session activities (planning, research, implementation, review) are not permanent agent identities
- Added authority section: Tracy controls priorities, assignments, dispatch, scope changes; sessions may recommend agents with rationale but do not auto-dispatch
- Added evidence standards: direct verification, supplied evidence, dated claims — all reports must distinguish these three types
- Clarified output policy: substantive output to files, brief chat report with outcome + location + decision needed
- Simplified escalation criteria: consequential risks only (architectural conflicts, scope changes, ownership conflicts, safety risks), not ordinary details
- Added references to PLANNING_AGENT_SESSION_START.md, REVIEW_AGENT_WORKFLOW.md, SESSION_CLOSEOUT.md instead of duplicating procedures
- Removed obsolete embedded dispatch/handoff templates (dispatch pattern, workflow diagram, handoff format template, executive summary for executor)
- Preserved Copilot/Continue setup notes as clearly nonbinding reference material
- Saved. No synthesis gate required.

### Step 4 — 2026-10-06
- Replaced the old multi-step Qwen → Gemini → Claude chain with a concise 5-step generic audit procedure
- Removed fixed project/date paths, timing quotas, mandatory four-document preparation, and obsolete template/output requirements
- Added: confirm assignment → inspect context → assess readiness/unknowns → save one audit report → brief chat report
- Audit report includes: task reference, relevance assessment, evidence findings, disposition recommendation (keep/correction/hold/close/consolidate), decision needed
- Additional research/review is optional with concrete need; no automatic model chain
- Added references to REVIEW_AGENT_GUIDE.md and SESSION_CLOSEOUT.md instead of repeating shared rules
- Added "Do Not" section: do not modify/move/close/dispatch audited task without authorization
- Saved. No synthesis gate required.

### Step 5 — 2026-10-06
- Replaced the duplicated workflow (344 lines of embedded templates, model chains, and dispatch instructions) with a concise 5-step review procedure
- Added: confirm assignment → read evidence (latest handoff first, verify claims) → review for consequential issues → save one artifact → brief chat report
- Disposition options: proceed, proceed with specific caution, or stop for named decision
- Removed: universal synthesis gates, Tier 2 optional reviews, fixed model/provider chains, one-role-per-session restrictions, latest-handoff-only rules, embedded handoff templates, implementation dispatch instructions, "all reviewers lack filesystem access" claims
- Added: do-not section (don't reopen accepted decisions without material evidence, don't auto-dispatch or add reviewers, respect authorized checkpoints)
- Added references to REVIEW_AGENT_GUIDE.md and SESSION_CLOSEOUT.md instead of duplicating shared rules
- Saved. No synthesis gate required.

---

## Remaining Decisions (if any)

None for Step 1. Steps 2–7 await execution.

---

## Closeout — 2026-10-06

**Maintenance performed**:
- All seven workflow-editing steps completed across six files
- SESSION_CLOSEOUT.md created at repository root
- Consistency check passed all eight criteria; one contradiction fixed (duplicate Known Failure Modes in REVIEW_AGENT_GUIDE.md)
- Change summary saved to `workflow-updates/2026-10-06-WORKFLOW-SIMPLIFICATION.md`

**Task status**: This task file (`workflow-updates/2026-10-06-COMPLETE-WORKFLOW-EDITS.md`) is the canonical record. It lives in `workflow-updates/` as a coordination artifact, not in a standard tasks/ directory — no conventional move applies. Status set to COMPLETE.

**Exceptions**:
- The transit task (`projects/galaxy_game/tasks/active/2026-09-30-HIGH-ARCHITECTURE-TRANSIT-ENGINE-TOPOLOGY-CONTAINMENT.md`) was moved from backlog/current → active by a prior session and is preserved as-is. No edit, close, or move performed on that task.
- All other session-owned work (handoffs, summaries) preserved.
- No staging, commit, or push performed.

**Outcome**: Workflow simplification assignment complete. Six workflow files simplified to model-neutral, shared-procedure-referencing documents. One new file created (SESSION_CLOSEOUT.md). All changes uncommitted and unstaged.
