# Gemini Reconciliation Context — Guardrails Refactoring

**Date**: 2026-07-01  
**Purpose**: Reference file for reconciling stale task against recent audit findings  
**Action**: Gemini reviews this file and produces unified task

---

## YOUR RECENT WORK (2026-07-01)

You completed a surgical audit of `docs/GUARDRAILS.md` producing:

1. **GUARDRAILS_REF_PACKAGE.md** — Detailed audit with:
   - Section-to-folder mapping (681 lines analyzed)
   - Economic constants verification against GLOSSARY_SYSTEM_MECHANICS.md
   - 6-phase refactoring plan

2. **TASK_REF_GUARDRAILS_MIGRATION_V2.md** — Formal task spec with:
   - 4 deep audit findings (section numbering issues, duplicate sections, constants fragmentation, stale notes)
   - Execution workflow with 4 phases
   - Validation checklist

---

## STALE TASK FILE (created 2026-06-21)

**Name**: TASK_GUARDRAILS_SPLIT.md  
**Location**: /docs/new_agent/projects/galaxy_game/tasks/review/  
**Problem**: Same as yours — GUARDRAILS.md mixes agent rules with game design  

**Target folders**: 
- `docs/architecture/*`
- `docs/gameplay/mechanics.md`
- `docs/flavor/EASTER_EGGS.md`
- `docs/systems/em_power_shield_technology.md`

**Acceptance Criteria** (5 items):
- Design/system sections moved
- No duplicated content
- GUARDRAILS.md retains only agent rules
- New EM shield tech doc created
- Routing index consistent

**Implementation Steps** (5 steps, generic)

---

## RECONCILIATION QUESTIONS FOR YOU

1. **Content Gap Analysis**: Does the stale task capture requirements your audit missed? Does your audit reveal issues that make the stale task incomplete?

2. **Target Folder Validation**: Stale task uses `docs/gameplay/mechanics.md`, `docs/flavor/EASTER_EGGS.md`. Does your audit suggest different homes for this content?

3. **Task Supersession**: Should the stale task be REPLACED by your TASK_REF_GUARDRAILS_MIGRATION_V2.md, or is it a precursor?

---

## REQUIRED DELIVERABLE

**File**: `2026-07-01-TASK-GUARDRAILS-UNIFIED-RECONCILIATION.md`  
**Location**: Same folder as this file  

**Sections**:
1. Reconciliation Summary (what each task adds)
2. Refined Acceptance Criteria (merged from both)
3. Refined Execution Plan (consolidated, 3-5 steps)
4. Issues Identified (critical findings list)
5. Risk Flags (dependencies, gotchas)
6. Recommended Priority (HIGH or MEDIUM)
7. Next Step Recommendation (ready for executor? or needs Qwen review?)

---

## CONTEXT FILES AVAILABLE

All in this folder (`refactored-task-files/2026-07-01-TASK-UPDATE-2/`):
- GUARDRAILS_REF_PACKAGE.md (your audit synthesis)
- TASK_REF_GUARDRAILS_MIGRATION_V2.md (your formal task spec)
- TASK_GUARDRAILS_SPLIT.md (stale task — copy/paste below for reference)

---

## STALE TASK FULL TEXT

```
---
name: "Split Guardrails Game Design Content"
priority: MEDIUM
phase: phase8+
created: 2026-06-21
status: backlog
type: documentation
---

# TASK: Split GUARDRAILS.md and Move Game Design Content

**Priority**: MEDIUM  
**Phase**: phase8+  
**Type**: documentation  
**Created**: 2026-06-21  

## Problem Statement

`docs/GUARDRAILS.md` mixes agent operating rules with game design/system intent, making both hard to discover and maintain.

**Current**: Mixed concerns in GUARDRAILS.md  
**Expected**: GUARDRAILS.md keeps agent operating rules only; game design content moved to architecture/gameplay/flavor docs

## Files to Edit

| File | Purpose |
|---|---|
| `docs/GUARDRAILS.md` | Source to trim and keep agent rules |
| `docs/architecture/*` | Target architecture sections |
| `docs/gameplay/mechanics.md` | Gameplay boundaries section |
| `docs/flavor/EASTER_EGGS.md` | Easter egg section |
| `docs/systems/em_power_shield_technology.md` | New system doc |

## Acceptance Criteria

- [ ] Design/system sections moved to correct target docs
- [ ] No duplicated content introduced across target docs
- [ ] GUARDRAILS.md retains only agent rules and environment/process constraints
- [ ] New EM shield technology doc created and linked
- [ ] Routing index remains consistent for future agent updates

## Implementation Steps

1. Extract mapped sections from GUARDRAILS.md by documented section mapping
2. Merge into target docs without overwriting existing canonical content
3. Create `docs/systems/em_power_shield_technology.md` for missing target
4. Remove moved sections from GUARDRAILS.md while preserving required core rules
5. Verify cross-links and table-of-contents consistency

## Commit Instructions

git add docs/GUARDRAILS.md docs/architecture/ docs/gameplay/ docs/flavor/ docs/systems/
git commit -m "docs: split GUARDRAILS and move game design content to canonical docs"
```

---

**Next Step**: Read this context + review files above. Produce unified reconciliation task.
