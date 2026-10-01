---
status: backlog
priority: MEDIUM
type: documentation
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

- [ ] Agent Dispatch Interface complete
- [ ] Steps clear
- [ ] Synthesis template provided
- [ ] No placeholders
- [ ] Paths verified
- [ ] Gotchas specific
- [ ] Acceptance criteria measurable
- [ ] Dependencies clear

---

## 🔴 Agent Dispatch Interface (Required)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/ai-manager/2026-09-01-MEDIUM-DOCUMENTATION-AI-MANAGER-OUTSIDE-PHASE-STRUCTURE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/ai-manager/2026-09-01-MEDIUM-DOCUMENTATION-AI-MANAGER-OUTSIDE-PHASE-STRUCTURE.md \
         projects/galaxy_game/tasks/active/2026-09-01-MEDIUM-DOCUMENTATION-AI-MANAGER-OUTSIDE-PHASE-STRUCTURE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-01-MEDIUM-DOCUMENTATION-AI-MANAGER-OUTSIDE-PHASE-STRUCTURE.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-09-01-DOCUMENTATION-AI-MANAGER-OUTSIDE-PHASE-STRUCTURE.md
  Chat is for questions only — never paste synthesis into chat.
```

---

# TASK: Document That AI Manager Sits Outside World-Settlement Phases
**Status**: BACKLOG  
**Priority**: MEDIUM  
**Type**: documentation  
**Created**: 2026-09-01  
**Last Updated**: 2026-09-01  

---

## Context

Task organization in agent-tasks is dominated by world-settlement phases (phase05-luna, phase06-lava-tube, phase09-mars, phase10-venus, etc.). The AI Manager does not belong inside that linear sequence. It is a cross-cutting system that should consume and generalize the knowledge produced by the phases.

This is currently tribal knowledge. It should be written down so future agents and sessions do not keep trying to force AI Manager work into a phase folder or treat the lack of an “AI Manager phase” as an oversight.

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: Documentation only — no code changes**
- ❌ Wrong: Start refactoring services or moving files around
- ✅ Right: Clear written statement of the relationship
- Why: This is a coordination/clarity task.

---

## Problem Statement

There is no explicit, durable statement that:
- AI Manager work lives outside the settlement-phase folders
- The phases produce the real settlement knowledge
- The AI Manager is intended to learn from / generalize that knowledge later

**Expected behavior**: Short, visible documentation (status.md note + folder README + optionally a pointer in main AI Manager architecture docs).

---

## Implementation Steps

1. Confirm the new `tasks/backlog/ai-manager/` folder exists and has its README.
2. Add a short section to `projects/galaxy_game/status.md` (or equivalent living status file) stating the above relationship.
3. Optionally add a one-paragraph pointer in `docs/architecture/ai_manager/00_architecture_overview.md` or a lightweight wayfinding note.
4. Keep the change minimal and factual.

---

## Acceptance Criteria

- [ ] `ai-manager/` backlog folder purpose is clear from its README
- [ ] status.md (or equivalent) explicitly notes that AI Manager sits outside the phase sequence
- [ ] No code or service changes
- [ ] Wording is short and unambiguous for future agents

---

## Dependencies

**Blocked by**: None  
**Blocks**: Nothing  
**Related**: All other ai-manager/ backlog tasks
