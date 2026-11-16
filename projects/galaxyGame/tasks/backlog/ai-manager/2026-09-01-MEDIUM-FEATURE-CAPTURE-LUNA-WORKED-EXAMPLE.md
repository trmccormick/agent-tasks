---
status: backlog
priority: MEDIUM
type: feature
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

- [ ] Agent Dispatch Interface section below is complete and accurate
- [ ] All steps clear and actionable
- [ ] Synthesis report template provided
- [ ] No placeholders remain
- [ ] File paths verified
- [ ] Gotchas specific
- [ ] Acceptance criteria measurable
- [ ] Dependencies clear

---

## 🔴 Agent Dispatch Interface (Required)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/ai-manager/2026-09-01-MEDIUM-FEATURE-CAPTURE-LUNA-WORKED-EXAMPLE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/ai-manager/2026-09-01-MEDIUM-FEATURE-CAPTURE-LUNA-WORKED-EXAMPLE.md \
         projects/galaxy_game/tasks/active/2026-09-01-MEDIUM-FEATURE-CAPTURE-LUNA-WORKED-EXAMPLE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-01-MEDIUM-FEATURE-CAPTURE-LUNA-WORKED-EXAMPLE.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-09-01-FEATURE-CAPTURE-LUNA-WORKED-EXAMPLE.md
  Chat is for questions only — never paste synthesis into chat.
```

---

# TASK: Capture Luna Settlement Loop as Structured Worked Example
**Status**: BACKLOG  
**Priority**: MEDIUM  
**Type**: feature  
**Created**: 2026-09-01  
**Last Updated**: 2026-09-01  

---

## Context

The Luna settlement loop is the current primary focus and the most mature “exhaust local options first” pattern. Once it is solid, that knowledge should be captured as a structured worked example that a future resource-first foothold planner can treat as training / reference data — not as a hard-coded runtime pattern.

This task produces that structured capture.

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: Capture after the loop is working, not while it is still unstable**
- ❌ Wrong: Document speculative or half-working sequences
- ✅ Right: Wait until the core Luna loop is tested and stable, then capture
- Why: Bad examples poison future learning.

⚠️ **GOTCHA 2: Structure for machine use, not just human reading**
- ❌ Wrong: Free-form narrative only
- ✅ Right: Clear sections for local resources present, decisions made, sequence, import vs local split, success criteria, failure modes
- Why: The foothold planner needs structured data.

---

## Problem Statement

Successful Luna settlement knowledge currently lives in code, tests, and agent sessions. It is not yet available as a clean, reusable worked example for the AI Manager.

**Expected behavior**: A structured document (or data file) that records how Luna was actually settled, suitable for later planner reference.

---

## Implementation Steps

1. Confirm Luna core loop is stable enough to document.
2. Extract:
   - Local resources that were actually available / used
   - Key decision points (what was deployed first, why)
   - Deployment sequence
   - What had to be imported vs produced locally
   - Success / self-sustaining criteria
   - Notable failure modes or dead-ends avoided
3. Write the capture in a consistent structured format.
4. Place it where the future foothold planner can find it (location to be decided with human if unclear).

---

## Acceptance Criteria

- [ ] Structured capture exists (not pure narrative)
- [ ] Includes local resources, sequence, import vs local, success criteria
- [ ] Reflects the actual working Luna loop, not aspirational design
- [ ] Location is documented so later AI Manager work can reference it

---

## Dependencies

**Blocked by**: Core Luna settlement loop reaching a stable, testable state  
**Blocks**: Higher-quality training data for the resource-first planner  
**Related**: Resource-First Foothold Planner architecture task
