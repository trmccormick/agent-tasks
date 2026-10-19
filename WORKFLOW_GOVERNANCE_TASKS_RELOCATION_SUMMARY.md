# Workflow Governance Tasks — Relocation Summary
**Date**: 2026-10-19  
**Agent**: Haiku (Task Packaging Correction)  
**Status**: Task files relocated to proper repository structure

---

## Correction Applied

**Issue**: Generated task files were created at repository root instead of proper task lifecycle folder.

**Root Cause**: Task file generation did not follow agent-tasks task lifecycle conventions.

**Solution**: Recreated all three task files in the correct location with updated path references.

---

## Updated Task Locations

### Previous Location (Incorrect)
```
/Users/tam0013/Documents/git/agent-tasks/
├── 2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-AND-ROUTING-FRAMEWORK.md
├── 2026-10-19-MEDIUM-DOCUMENTATION-REVIEW-ARTIFACT-STANDARDIZATION.md
└── 2026-10-19-MEDIUM-DOCUMENTATION-STOP-CONDITION-ENHANCEMENT.md
```

**Status**: These files at root should be **deleted** (they are superseded by corrected versions below).

### New Location (Correct)
```
/Users/tam0013/Documents/git/agent-tasks/projects/agent-tasks/tasks/backlog/
├── 2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-AND-ROUTING-FRAMEWORK.md
├── 2026-10-19-MEDIUM-DOCUMENTATION-REVIEW-ARTIFACT-STANDARDIZATION.md
└── 2026-10-19-MEDIUM-DOCUMENTATION-STOP-CONDITION-ENHANCEMENT.md
```

**Status**: ✅ **All three files created and ready for dispatch.**

---

## What Changed in Task Files

### Path References Updated

**In Agent Dispatch Interface (all three files):**

**Before**:
```bash
git mv tasks/2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-*.md \
       projects/agent-tasks/tasks/active/2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-*.md
```

**After**:
```bash
git mv projects/agent-tasks/tasks/backlog/2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-*.md \
       projects/agent-tasks/tasks/active/2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-*.md
```

**In find command verification (all three files):**

**Before**:
```bash
find <agent-tasks>/projects/agent-tasks/tasks -name "2026-10-19-HIGH-*.md"
```

**After**:
```bash
find projects/agent-tasks/tasks -name "2026-10-19-HIGH-*.md"
```

**Why**: Synthesis report path and task location now use `projects/agent-tasks/tasks/backlog/` as the starting point, consistent with task lifecycle conventions.

---

## Task Lifecycle Compliance

All three task files now follow the proper agent-tasks task lifecycle:

```
projects/agent-tasks/tasks/backlog/
    ↓ (implementation agent reads, then git mv)
projects/agent-tasks/tasks/active/
    ↓ (implementation agent completes, then git mv)
projects/agent-tasks/tasks/completed/2026-10/
```

### Verification

Implementing agents will verify tasks are in correct location using:
```bash
find projects/agent-tasks/tasks -name "2026-10-19-*.md"
```

Expected output: Three files in `backlog/` folder (one from each task).

---

## Task File Inventory (Corrected)

### Task 1: Evidence Classification + Reviewer Routing
**File**: `projects/agent-tasks/tasks/backlog/2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-AND-ROUTING-FRAMEWORK.md`
- **Priority**: HIGH
- **Scope**: Add evidence framework (VERIFIED/REPORTED/ASSUMED) to PLANNING_AGENT_SESSION_START.md
- **Target file**: PLANNING_AGENT_SESSION_START.md (~40 lines)
- **Status**: ✅ Ready for dispatch

### Task 2: Review Artifact Standardization
**File**: `projects/agent-tasks/tasks/backlog/2026-10-19-MEDIUM-DOCUMENTATION-REVIEW-ARTIFACT-STANDARDIZATION.md`
- **Priority**: MEDIUM
- **Scope**: Standardize review artifacts; clarify handoff requirements
- **Target files**: REVIEW_AGENT_WORKFLOW.md, SESSION_CLOSEOUT.md, (optional) REVIEW_REPORT_TEMPLATE.md
- **Status**: ✅ Ready for dispatch

### Task 3: Stop Condition Enhancement
**File**: `projects/agent-tasks/tasks/backlog/2026-10-19-MEDIUM-DOCUMENTATION-STOP-CONDITION-ENHANCEMENT.md`
- **Priority**: MEDIUM
- **Scope**: Strengthen risk/blocker surfacing in SESSION_CLOSEOUT.md
- **Target file**: SESSION_CLOSEOUT.md (~25-30 lines)
- **Status**: ✅ Ready for dispatch

---

## Content Preserved

✅ All task contents preserved from original generation  
✅ All synthesis report templates included  
✅ All acceptance criteria unchanged  
✅ All implementation steps unchanged  
✅ All references to WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md preserved  

**Only changes**: Task file locations and path references in git commands.

---

## Next Actions

### For Human (Tracy)

1. **Delete** the three incorrect task files at repository root:
   - `2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-AND-ROUTING-FRAMEWORK.md`
   - `2026-10-19-MEDIUM-DOCUMENTATION-REVIEW-ARTIFACT-STANDARDIZATION.md`
   - `2026-10-19-MEDIUM-DOCUMENTATION-STOP-CONDITION-ENHANCEMENT.md`

2. **Verify** the three correct task files exist at:
   - `projects/agent-tasks/tasks/backlog/2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-AND-ROUTING-FRAMEWORK.md`
   - `projects/agent-tasks/tasks/backlog/2026-10-19-MEDIUM-DOCUMENTATION-REVIEW-ARTIFACT-STANDARDIZATION.md`
   - `projects/agent-tasks/tasks/backlog/2026-10-19-MEDIUM-DOCUMENTATION-STOP-CONDITION-ENHANCEMENT.md`

3. **Authorize** dispatch to implementation agent (Qwen)

4. **Confirm** Haiku as review gate

### For Implementation Agent (when authorized)

1. Start with Task 1 or assigned task
2. Execute Step 0 first (move from backlog/ to active/)
3. Create synthesis report
4. Implement changes per task steps
5. Report to Haiku for review

---

## Verification

**To verify correct structure**, run from agent-tasks root:
```bash
find projects/agent-tasks/tasks/backlog -name "2026-10-19-*.md" | sort
```

**Expected output**:
```
projects/agent-tasks/tasks/backlog/2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-AND-ROUTING-FRAMEWORK.md
projects/agent-tasks/tasks/backlog/2026-10-19-MEDIUM-DOCUMENTATION-REVIEW-ARTIFACT-STANDARDIZATION.md
projects/agent-tasks/tasks/backlog/2026-10-19-MEDIUM-DOCUMENTATION-STOP-CONDITION-ENHANCEMENT.md
```

---

## Summary

| Item | Status |
|------|--------|
| Task 1 relocated to backlog/ | ✅ Complete |
| Task 2 relocated to backlog/ | ✅ Complete |
| Task 3 relocated to backlog/ | ✅ Complete |
| Path references updated in all tasks | ✅ Complete |
| Synthesis templates preserved | ✅ Complete |
| Acceptance criteria preserved | ✅ Complete |
| Implementation steps preserved | ✅ Complete |
| Content unchanged | ✅ Complete |
| No workflow documents edited | ✅ Complete |
| No commits made | ✅ Complete |
| No pushes executed | ✅ Complete |

**Status**: Ready for human cleanup and dispatch authorization.

---

**End of Relocation Summary**

*Three corrected task files are in proper repository locations, ready for implementation agent dispatch.*
