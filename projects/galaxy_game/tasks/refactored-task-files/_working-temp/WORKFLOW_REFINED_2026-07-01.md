# Guardrails Refactoring Workflow — REFINED (2026-07-01)

**Status**: Workflow redesign  
**Goal**: Clear separation of planning → research → review phases  
**Working Location**: `refactored-task-files/_working-temp/`  
**Final Location**: `refactored-task-files/2026-07-01-TASK-UPDATE-2/`

---

## REFINED WORKFLOW ARCHITECTURE

### Phase 1: LOCAL VALIDATION (Qwen)
**Agent**: Qwen (local planning agent)  
**Task**: Validate prerequisites and codebase structure  
**Handoff**: Read from `_working-temp/QWEN_HANDOFF_GUARDRAILS_VALIDATION.md`  
**Output**: `_working-temp/2026-07-01-TASK-GUARDRAILS-VALIDATION-QWEN.md`  
**Decision**: Are pre-work tasks needed? If yes → create them. If no → proceed to Phase 2.

**Qwen's role**: 
- Check if target directories exist
- Identify any pre-work needed
- Verify risk mitigation factors
- Flag blockers

---

### Phase 2: RESEARCH & SYNTHESIS (Gemini)
**Agent**: Gemini (web planning agent)  
**Session**: SEPARATE session from reconciliation  
**Task**: Deep research on content mapping + section extraction  
**Handoff**: Text handoff in a NEW chat session (not from previous reconciliation chat)  
**Output**: Synthesis report saved to `_working-temp/2026-07-01-GEMINI-GUARDRAILS-EXTRACTION-SYNTHESIS.md`

**Gemini's research tasks**:
- Analyze each section of GUARDRAILS.md (681 lines)
- Map exact content to target folders with line numbers
- Identify content that overlaps with existing architecture docs
- Flag sections that are stale or superseded
- Produce extraction checklist (section ID → target file → line numbers)

**Deliverable format**:
```
# Extraction Synthesis Report

## Section-by-Section Mapping
| Section | Current Lines | Content Type | Target File | Status |
|---------|---|---|---|---|
| 1.0 | 15-30 | Agent Protocols | docs/GUARDRAILS.md (keep) | ✅ Keep |
| 2.0 | 31-45 | Game Design | docs/architecture/stations/... | → Move |
...

## Pre-Extraction Verification
- [ ] All economic constants verified against GLOSSARY
- [ ] No overlaps found with existing architecture docs
- [ ] Stale content identified and flagged for archival

## Executor Checklist
Step-by-step extraction order with exact line ranges
```

---

### Phase 3: FINAL REVIEW (Claude)
**Agent**: Claude (local via Copilot or web)  
**Session**: Premium/web Claude (limited time)  
**Task**: Quick approval + placement decision  
**Input**: `_working-temp/2026-07-01-CLAUDE-FINAL-SUMMARY.md` (condensed review)

**Claude's review** (minimized session time):
1. Check unified task quality (AC, execution plan, risks clear?)
2. Review this summary for clarity
3. Make placement decision (final location)
4. Flag any blockers/refinements needed

**Claude's response**: ✅ Approve / ⚠️ Request changes / ❌ Blocker

**Output**: Short response (in same session or email)
- Approval status
- Placement preference (Option A: `/2026-07-01-TASK-UPDATE-2/` | Option B: `/agent-tasks/active/` | Option C: both)
- Any requested changes

**Research files stay in temp** until Claude approves → then decide whether to archive or move with task

---

## FOLDER STRUCTURE (Current + Final)

```
refactored-task-files/
├── 2026-06-30-TASK-UPDATE-1/
│   ├── (Gemini's original 8 tasks)
│   └── (gap analysis, summaries)
│
├── 2026-07-01-TASK-UPDATE-2/
│   ├── 2026-07-01-TASK-GUARDRAILS-UNIFIED-RECONCILIATION.md ✅ FINAL TASK
│   ├── GUARDRAILS_REF_PACKAGE.md (reference for executor)
│   └── TASK_REF_GUARDRAILS_MIGRATION_V2.md (reference for executor)
│
└── _working-temp/  ← ALL INTERMEDIATE FILES HERE
    ├── GEMINI_HANDOFF_GUARDRAILS_RECONCILIATION.md (sent to Gemini)
    ├── GEMINI_RECONCILIATION_CONTEXT.md (context for Gemini)
    ├── QWEN_HANDOFF_GUARDRAILS_VALIDATION.md (ready to send to Qwen)
    ├── 2026-07-01-CLAUDE-FINAL-SUMMARY.md ← Claude reviews THIS (minimal session)
    ├── 2026-07-01-TASK-GUARDRAILS-VALIDATION-QWEN.md (awaiting from Qwen)
    ├── 2026-07-01-GEMINI-GUARDRAILS-EXTRACTION-SYNTHESIS.md (awaiting from Gemini Phase 2)
    └── [other working files]
```

**Cleanup**: After Claude approves and task is placed → Qwen (planning agent) deletes all files from `_working-temp/` to keep workspace clean

---

## SESSION CLEANUP (Planning Agent Responsibility)

**At end of planning session**:
```bash
rm -rf /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/_working-temp/*
```

**What gets deleted**:
- All handoff files (used only for agent communication)
- All synthesis/validation reports (captured in final task)
- All intermediate working files

**What survives** (in permanent location):
- `2026-07-01-TASK-UPDATE-2/2026-07-01-TASK-GUARDRAILS-UNIFIED-RECONCILIATION.md` ✅ FINAL TASK
- Any other approved tasks that moved to `/2026-07-01-TASK-UPDATE-2/`

---

## SEQUENCE (DAY 1 — TODAY)

**✅ Completed**:
1. Stale task identified (TASK_GUARDRAILS_SPLIT.md)
2. Gemini Phase 1: Reconciliation → `2026-07-01-TASK-GUARDRAILS-UNIFIED-RECONCILIATION.md` ✅
3. Qwen handoff created → ready for validation

**⏳ Next**:
4. Qwen validates (TODAY if time allows)
5. If blockers → create pre-work tasks, move to backlog
6. If clear → proceed to Gemini Phase 2 (separate session, tomorrow or later)

**📋 Gemini Phase 2 (SEPARATE SESSION)**:
- Research extraction mapping
- Produce synthesis report
- Save to `_working-temp/`

**👁️ Claude final review**:
- Approve or request changes
- Move approved task to `/2026-07-01-TASK-UPDATE-2/` (final location)
- Document approval rationale

---

## WORKFLOW DECISIONS (LOCKED IN)

✅ **Claude gets condensed final summary** (not all intermediate files)
- File: `2026-07-01-CLAUDE-FINAL-SUMMARY.md`
- Purpose: Minimize premium Claude session time
- Input: Unified task + this summary + placement options
- Output: Approval + placement decision

✅ **Research synthesis files preserved in temp**
- Kept until Claude approves (then decide fate: archive/move/reference)
- Executor can reference them if needed
- Cleaned up after approval

✅ **Placement decision by Claude**
- Option A: Keep in `/2026-07-01-TASK-UPDATE-2/` (with other 2026-07-01 tasks)
- Option B: Move to `/agent-tasks/projects/galaxy_game/tasks/active/` (formal task activation)
- Option C: Both locations with cross-references

---

## REMAINING DECISIONS (If Needed)

If Claude wants different approach, ask:
1. Should we move task to agent-tasks before executor sees it?
2. Should research synthesis files stay accessible for executor reference?
3. Should pre-work tasks be created as separate task files?
