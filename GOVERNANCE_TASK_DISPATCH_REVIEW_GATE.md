# Dispatch: Review-Gate and Authority Boundaries Workflow Governance Task

**To**: Qwen Implementation Agent  
**From**: Haiku Planning Agent  
**Date**: 2026-10-20  
**Priority**: HIGH  
**Task File**: `projects/agent-tasks/tasks/backlog/2026-10-19-HIGH-DOCUMENTATION-REVIEW-GATE-AND-AUTHORITY-BOUNDARIES.md`

---

## Context

During workflow governance review, a critical authority-boundary gap was identified in implementation workflows: implementation agents were proceeding directly to commit without waiting for reviewer assessment. This violated the intended workflow:

**Intended Workflow:**
```
Implementation → Verify → Synthesis → STOP (review-gate)
    ↓
Reviewer Assessment (GO | REVISE | STOP)
    ↓
Human Authorization
    ↓
Commit → Push → Task Completion
```

**Current Defect** (found in Task 1 execution): Review-gate checkpoint missing from task templates and workflow documentation.

---

## Task Scope

Add review-gate checkpoint and clarify authority boundaries in 5 core workflow files:

1. **TASK_TEMPLATE.md** — Add explicit review-gate step between verification and commit (~8-12 lines)
2. **REVIEW_AGENT_GUIDE.md** — Expand Authority section (~15-20 lines)
3. **REVIEW_AGENT_WORKFLOW.md** — Add reviewer authority section (~12-15 lines)
4. **SESSION_CLOSEOUT.md** — Clarify review-gate requirement (~3-4 lines)
5. **IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md** — Add review-gate guidance (~5-8 lines)

**Total Additions**: ~100-120 lines across 5 files (additive, no restructuring)

---

## Authority Model (Reference)

**Implementation Agent Authority:**
- ✅ Implement work
- ✅ Verify work
- ✅ Create synthesis
- ❌ Commit (requires human authorization after reviewer GO)

**Reviewer Authority:**
- ✅ Assess work
- ✅ Issue GO | REVISE | STOP
- ✅ Block commits (REVISE/STOP)
- ❌ Authorize commits (human's role)

**Human Authority:**
- ✅ Authorize commit/push after reviewer GO
- ✅ Override reviewer assessment if needed
- ✅ Approve task completion

---

## Why This Matters

This task fixes the **root cause** at the template level. Tasks 2 & 3 were manually patched with step-level checkpoints to prevent propagation. This task ensures all **future** implementations inherit correct review-gate workflow from day one.

**Without this task:** Every new task template would need manual review-gate revision  
**With this task:** Review-gate is standard in all new implementations

---

## Recommended Execution Sequence

1. Read the full task file (comprehensive implementation guidance included)
2. Create synthesis report (copy-paste template provided in task file)
3. Submit synthesis for review before making any changes
4. Once approved: implement 5 file updates (clearly scoped)
5. Follow SESSION_CLOSEOUT.md for final steps

---

## No Blockers

- ✅ Task is independent (no dependencies)
- ✅ No code execution required
- ✅ Additive changes only (no restructuring)
- ✅ Templates can be updated without affecting active sessions
- ✅ Estimated completion: ~2-3 hours

---

## Questions?

Full task file contains detailed implementation steps, acceptance criteria, and synthesis template. Start with the task file and ask in chat if anything is unclear.

---

## Proceed?

**GO** — You are authorized to:
1. Read the full task file at: `projects/agent-tasks/tasks/backlog/2026-10-19-HIGH-DOCUMENTATION-REVIEW-GATE-AND-AUTHORITY-BOUNDARIES.md`
2. Execute Step 0 (move task to active/)
3. Follow all implementation steps through completion
4. Submit synthesis for review gate before committing any changes

**Confirm receipt and readiness to begin.**
