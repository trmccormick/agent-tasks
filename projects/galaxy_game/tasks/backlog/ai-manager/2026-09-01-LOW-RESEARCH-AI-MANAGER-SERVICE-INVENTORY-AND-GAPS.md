---
status: backlog
priority: LOW
type: research
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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/ai-manager/2026-09-01-LOW-RESEARCH-AI-MANAGER-SERVICE-INVENTORY-AND-GAPS.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/ai-manager/2026-09-01-LOW-RESEARCH-AI-MANAGER-SERVICE-INVENTORY-AND-GAPS.md \
         projects/galaxy_game/tasks/active/2026-09-01-LOW-RESEARCH-AI-MANAGER-SERVICE-INVENTORY-AND-GAPS.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-01-LOW-RESEARCH-AI-MANAGER-SERVICE-INVENTORY-AND-GAPS.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-09-01-RESEARCH-AI-MANAGER-SERVICE-INVENTORY-AND-GAPS.md
  Chat is for questions only — never paste synthesis into chat.
```

---

# TASK: Lightweight AI Manager Service Inventory & Gap Notes
**Status**: BACKLOG  
**Priority**: LOW  
**Type**: research  
**Created**: 2026-09-01  
**Last Updated**: 2026-09-01  

---

## Context

The AI Manager directory contains a large number of services. Some are solid and data-driven (`PrecursorCapabilityService`, `ISRUEvaluator`), some are pattern-centric, some appear partially stubbed or historical. A lightweight inventory that classifies them and notes gaps relevant to resource-first foothold planning would help future prioritization without requiring a full rewrite campaign.

This is intentionally a research/documentation task, not an implementation drive.

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: Inventory and classify — do not “fix” everything**
- ❌ Wrong: Start rewriting or deleting services during this task
- ✅ Right: Produce a short classified list + gap notes
- Why: Scope control; Luna work remains the priority.

⚠️ **GOTCHA 2: Focus on relevance to resource-first planning**
- ❌ Wrong: Exhaustive line-by-line audit of every method
- ✅ Right: Group services by role (sensor, planner, executor, economic, learning, stub/legacy) and note which ones matter for the foothold direction
- Why: Actionable output, not a novel.

---

## Problem Statement

It is hard for a new session (or another agent) to quickly see which AI Manager pieces are load-bearing for the desired resource-first direction versus which are pattern leftovers or incomplete.

**Expected behavior**: A short inventory document that future AI Manager tasks can reference.

---

## Implementation Steps

1. List services under `app/services/ai_manager/`.
2. Classify roughly: sensor / planner / executor / economic / learning / support / stub-or-legacy.
3. Note which ones are already useful for resource-first foothold work.
4. Note obvious gaps relative to the HIGH foothold-planner architecture task.
5. Write a concise summary (prefer a single markdown file in summaries/ or under ai-manager docs).

---

## Acceptance Criteria

- [ ] Classified list of AI Manager services exists
- [ ] Clear call-out of sensors already suitable for resource-first planning
- [ ] Gaps relative to foothold planner direction are noted
- [ ] No service rewrites or deletions performed in this task

---

## Dependencies

**Blocked by**: None  
**Blocks**: Nothing critical  
**Related**: Resource-First Foothold Planner architecture task
