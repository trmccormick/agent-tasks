---
status: completed
resume: false
priority: HIGH
type: feature
system_domain: CONTROLLERS
mvp_alignment: GALAXY_GAME_UI
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [x] All Step 0-N instructions are clear and actionable (not vague)
- [x] Synthesis report template is provided (copy/paste ready, not as example)
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is READY for dispatch.**

---

## � Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Planning Agent**.

Project: galaxy-game-ui
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/tasks/backlog/2026-10-21-HIGH-FEATURE-DASHBOARD-TASK-REWRITE-2-4.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy-game-ui/tasks/backlog/2026-10-21-HIGH-FEATURE-DASHBOARD-TASK-REWRITE-2-4.md \
         projects/galaxy-game-ui/tasks/active/2026-10-21-HIGH-FEATURE-DASHBOARD-TASK-REWRITE-2-4.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find projects/galaxy-game-ui/tasks -name "2026-10-21-HIGH-FEATURE-DASHBOARD-TASK-REWRITE-2-4.md"
    Only ONE result should exist at active/. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, specifications, and implementation steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: projects/galaxy-game-ui/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

---

## 📋 Task Summary

**Previous Context**: Design session completed 2026-10-21. Gemini approved all 4 design deliverables.

**Your Role**: Translate approved design into three implementation task files (Tasks 2, 3, 4) using exact specifications from approved design output.

**Deliverable**: Three task files, each ≤150 lines, following TASK_TEMPLATE.md format exactly.

**Timeline**: ~1 hour to create all 3 task files + post synthesis reports.

---

## 🎯 Task Description

You have successfully completed the **Dashboard Design Session**. Your design was reviewed by Gemini and **approved with all checks passed** ✅

Now you must rewrite **Tasks 2–4** from the approved design specifications. This is a translation phase — converting design deliverables into implementation task files that are ready for Implementation Agent dispatch.

### What Changed Since Design Session?

**Design Status**: ✅ APPROVED (no revisions needed)

**Gemini's Validation Results**:
- ✅ All 4 deliverables present and complete
- ✅ Strict adherence to MockDataHelper constraints (no fiction)
- ✅ Galaxy Game terminology only (no EVE Online leakage)
- ✅ Responsive layout with mobile-first stacking
- ✅ Glassmorphic styling consistent throughout
- ✅ Data bindings match verified method list
- ✅ Zero deviations from project constraints
- Note: Future consideration — clamp progress bar percentages (0–100%) to avoid overflow

### Your Approved Design Specifications

**Location**: `projects/galaxy-game-ui/summaries/2026-10-21-DESIGN-SESSION-OUTPUT-QWEN.md`

**Contains**:
1. ✅ Panel List — 6 panels + header with MockDataHelper method mapping
2. ✅ Layout Diagram — 2-column responsive grid with mobile 1-column stacking
3. ✅ Data Binding Map — 9 template variables fully mapped to MockDataHelper methods with returned structures
4. ✅ Styling Notes — Galaxy Game color palette, Tailwind classes, status badges, typography hierarchy

---

## 📝 Implementation Steps

### Step 1: Read Approved Design Output
**File**: `projects/galaxy-game-ui/summaries/2026-10-21-DESIGN-SESSION-OUTPUT-QWEN.md`

Review all 4 deliverables. **This is your source of truth** — do not deviate from it.

Pay special attention to:
- Panel list (which panels, what data from which methods)
- Data binding map (exact template variable names and MockDataHelper methods)
- Styling notes (specific Tailwind classes, color values, component patterns)
- Design decisions (why layout is 2-column, why tabs are needed, why research is behind toggle)

### Step 2: Read Complete Task Dispatch Specifications
**File**: `projects/galaxy-game-ui/summaries/2026-10-21-QWEN-TASK-REWRITE-DISPATCH.md`

This document contains:
- Complete scope for Task 2, Task 3, Task 4
- Acceptance criteria for each task (derived from approved design)
- Data binding examples (exact ERB variable references)
- Implementation notes (where files go, what controllers set, which Stimulus controllers needed)
- Testing checklist for each task

**This is your task specification document** — follow it exactly.

### Step 3: Create Task 2 — Dashboard Header & Bay Inventory
**File Name**: `2026-10-21-HIGH-FEATURE-DASHBOARD-HEADER-AND-INVENTORY.md`
**Location**: `projects/galaxy-game-ui/tasks/backlog/`
**Format**: TASK_TEMPLATE.md format exactly
**Max Length**: 150 lines

**Scope** (from dispatch specs): Implement panels H (header) + 1 (inventory)

**Panels Implemented**:
- Panel H: Location & Character Header (displays character name, settlement, GCC balance, timestamp)
- Panel 1: Local Bay Inventory (displays 4 resources with progress bars)

**Data Binding** (from approved design):
- `@system_status = MockDataHelper.system_status`
- `@bay_items = MockDataHelper.bay_inventory`

**Key Acceptance Criteria**:
- Header displays character name + settlement + GCC + timestamp
- Bay Inventory iterates through all 4 resources (Water Ice, Oxygen, Deuterium, Regolith)
- Progress bars render with quantity/max_qty percentages
- Glassmorphic styling applied (from design notes)
- Mobile responsive (stacks to 1-column on screens < 1024px)

**Read from dispatch**: TASK 2 section in `2026-10-21-QWEN-TASK-REWRITE-DISPATCH.md`

### Step 4: Create Task 3 — Ships & Manufacturing Queue
**File Name**: `2026-10-21-HIGH-FEATURE-DASHBOARD-SHIPS-AND-MANUFACTURING.md`
**Location**: `projects/galaxy-game-ui/tasks/backlog/`
**Format**: TASK_TEMPLATE.md format exactly
**Max Length**: 150 lines

**Scope** (from dispatch specs): Implement panels 2 (ships) + 3 (manufacturing + research toggle)

**Panels Implemented**:
- Panel 2: Ships & Active Units (displays all player ships with status badges)
- Panel 3: Pending Operations (tabbed: Manufacturing Queue + Research Queue toggle)

**Data Binding** (from approved design):
- `@ships = MockDataHelper.player_ships`
- `@mfg = MockDataHelper.manufacturing_queue`
- `@research = MockDataHelper.research_queue`

**Key Acceptance Criteria**:
- Ships panel displays all ships with correct status badges (Docked green, In Transit cyan, Other gray)
- Manufacturing tab shows all jobs with progress bars
- Research tab hidden by default (toggle to reveal via Stimulus JS)
- Tab toggle works correctly
- Progress bars render with correct percentages
- Mobile responsive

**Read from dispatch**: TASK 3 section in `2026-10-21-QWEN-TASK-REWRITE-DISPATCH.md`

### Step 5: Create Task 4 — Open Orders & Market Footer
**File Name**: `2026-10-21-HIGH-FEATURE-DASHBOARD-ORDERS-AND-MARKET.md`
**Location**: `projects/galaxy-game-ui/tasks/backlog/`
**Format**: TASK_TEMPLATE.md format exactly
**Max Length**: 150 lines

**Scope** (from dispatch specs): Implement panels 4 (orders) + 5 (market footer)

**Panels Implemented**:
- Panel 4: Open Orders (tabbed: Buy Orders + Missions)
- Panel 5: Market Quick-Panel Footer (displays commodity prices)

**Data Binding** (from approved design):
- `@orders = MockDataHelper.npc_buy_orders`
- `@missions = MockDataHelper.active_missions`
- `@market = MockDataHelper.market_quick_panel`

**Key Acceptance Criteria**:
- Buy Orders tab displays all NPC orders with price breakdowns
- Missions tab shows all active missions with deadlines
- Tab toggle works correctly
- Market footer displays commodity names + buy/sell prices + trend
- Table headers use cyan accent color
- Mobile responsive

**Read from dispatch**: TASK 4 section in `2026-10-21-QWEN-TASK-REWRITE-DISPATCH.md`

### Step 6: Post Synthesis Reports (Before Committing)
**Do NOT commit yet** — post synthesis reports to chat first.

**Post one synthesis report per task** (3 total):

**Format**:
```
## Synthesis Report — Task 2 (or 3 or 4)

**Task**: Dashboard [panel names]
**Approach**: [Brief summary of what task file specifies]
**Verified Against**: Approved design output + Gemini validation
**Constraints Confirmed**: [List 3-5 critical constraints that will be followed]
**Ready for Implementation Dispatch**: YES ✅

[Paste the Agent Dispatch Interface section from the task file you just created]
```

### Step 7: Commit After Synthesis Approval
**Only commit after synthesis reports are approved** — user will confirm in chat.

**Commit Command**:
```
git add projects/galaxy-game-ui/tasks/backlog/2026-10-21-HIGH-*.md
git commit -m "task: Dashboard implementation tasks 2-4 (rewritten from approved design) [2026-10-21]

- Task 2: Header + Bay Inventory (panels H, 1)
- Task 3: Ships + Manufacturing Queue (panels 2, 3 with Research toggle)
- Task 4: Open Orders + Market Footer (panels 4, 5)
- All tasks follow TASK_TEMPLATE.md format exactly
- All tasks reference approved Gemini design (zero deviation)
- All tasks use only verified MockDataHelper methods (no fiction)
- All tasks ready for implementation dispatch phase"
```

---

## 🎯 Acceptance Criteria

**Task file creation**:
- [ ] Task 2 file created (≤150 lines, TASK_TEMPLATE.md format)
- [ ] Task 3 file created (≤150 lines, TASK_TEMPLATE.md format)
- [ ] Task 4 file created (≤150 lines, TASK_TEMPLATE.md format)
- [ ] All three files in `projects/galaxy-game-ui/tasks/backlog/`
- [ ] All three files follow naming convention: `2026-10-21-HIGH-FEATURE-DASHBOARD-*.md`

**Content verification**:
- [ ] Each task includes Agent Dispatch Interface section
- [ ] Each task references approved design specs
- [ ] Each task specifies exact MockDataHelper methods (no fiction)
- [ ] Each task includes acceptance criteria derived from design
- [ ] Each task includes data binding examples (template variable names)
- [ ] Each task specifies Tailwind styling from approved design
- [ ] No EVE Online terminology in any task file
- [ ] No database queries mentioned in any task file
- [ ] All tasks reference GET /dashboard route (already exists)

**Synthesis reports**:
- [ ] Synthesis report posted for Task 2 (before committing)
- [ ] Synthesis report posted for Task 3 (before committing)
- [ ] Synthesis report posted for Task 4 (before committing)
- [ ] Each synthesis report includes Agent Dispatch Interface section
- [ ] User confirms all synthesis reports approved

**Commit**:
- [ ] All three task files staged: `git add projects/galaxy-game-ui/tasks/active/2026-10-21-HIGH-*.md`
- [ ] Commit message includes all three task names
- [ ] Commit log shows `git log --oneline -1`

---

## 🚀 Starting Instructions

### Reference Materials to Prepare

**Design Output** (your approved spec):
- `projects/galaxy-game-ui/summaries/2026-10-21-DESIGN-SESSION-OUTPUT-QWEN.md`

**Task Specifications** (complete detail for all 3 tasks):
- `projects/galaxy-game-ui/summaries/2026-10-21-QWEN-TASK-REWRITE-DISPATCH.md`

**Template Reference**:
- `TASK_TEMPLATE.md` (at repo root)

**Project Starting Guides**:
- `PLANNING_AGENT_SESSION_START.md` (review before starting any task creation)
- `projects/galaxy-game-ui/README.md` (project context)
- `projects/galaxy-game-ui/status.md` (current project phase)

### Begin Now

1. ✅ Review approved design output (above link)
2. ✅ Review complete task dispatch specs (above link)
3. ✅ Create Task 2 per TASK 2 section in dispatch
4. ✅ Create Task 3 per TASK 3 section in dispatch
5. ✅ Create Task 4 per TASK 4 section in dispatch
6. 📝 Post synthesis report for each task (don't commit yet)
7. ✅ After synthesis approval, commit all three files

**Estimated Duration**: ~1 hour task creation + synthesis reporting

**Next Phase**: Each task will be dispatched to Implementation Agent (separate sessions)

**Questions?** Reference dispatch document — all questions answered there.

---

## 📚 Context References

**Previous Session Outputs**:
- Design Session Output: `projects/galaxy-game-ui/summaries/2026-10-21-DESIGN-SESSION-OUTPUT-QWEN.md` (your approved design)
- Gemini Review Handoff: `projects/galaxy-game-ui/summaries/2026-10-21-GEMINI-DESIGN-REVIEW-HANDOFF.md` (approval documentation)
- Task Dispatch Specs: `projects/galaxy-game-ui/summaries/2026-10-21-QWEN-TASK-REWRITE-DISPATCH.md` (this task's instructions)

**Project Resources**:
- Project README: `projects/galaxy-game-ui/README.md`
- Project Status: `projects/galaxy-game-ui/status.md`
- Current Phase: DESIGN APPROVED → Task Rewrite

**System Workflow Resources**:
- Planning Agent Guide: `PLANNING_AGENT_SESSION_START.md`
- Task Template: `TASK_TEMPLATE.md`
- Implementation Workflow: `IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md`

---

## 🔐 Non-Negotiable Constraints

**Design Adherence**:
- ✅ ZERO deviation from approved design specs
- ✅ Use ONLY the panel list specified in design (6 panels + header)
- ✅ Use ONLY the data binding map provided (9 template variables → methods)
- ✅ Apply ONLY approved styling (Tailwind classes from design notes)

**Data Integrity**:
- ✅ Use ONLY verified MockDataHelper methods (audit methods before referencing)
- ✅ NO database queries (MockDataHelper in-memory only)
- ✅ NO ActiveRecord models or migrations
- ✅ NO EVE Online concepts or terminology

**Terminology**:
- ✅ Galaxy Game ONLY: Water Ice, Oxygen, Deuterium, Regolith, GCC
- ✅ NO EVE Online: Blue Ice, Oxygen Isotopes, Heavy Water, ISK, Jita, EVE locations
- ✅ NO generic naming: use exact resource names from MockDataHelper

**Task Format**:
- ✅ Follow TASK_TEMPLATE.md exactly
- ✅ Each task ≤150 lines
- ✅ Include Agent Dispatch Interface section (required)
- ✅ Include all 5 sections: Description, Acceptance Criteria, Data Binding, Notes, Testing
- ✅ File naming: `2026-10-21-HIGH-FEATURE-DASHBOARD-*.md`

**No Implementation**:
- ✅ This task is about CREATING task files, NOT implementing features
- ✅ Do NOT write ERB templates yet (that's for Implementation Agent)
- ✅ Do NOT modify controllers or assets (that's for Implementation Agent)
- ✅ Do NOT test in the browser (that's for Implementation Agent)

---

## 🔄 Post-Task Workflow

**After synthesis reports approved by user**:

1. Commit all 3 task files (in backlog/)
2. Update status.md to reflect Tasks 2–4 created (ready for implementation)
3. Each task will be dispatched to Implementation Agent in separate sessions
4. Implementation Agent will: move task to active/ → read task → synthesize → implement → test → review
5. User will approve each implementation
6. After all 3 tasks implemented, dashboard feature complete

**Your role is DONE after**:
- ✅ All 3 task files created (in backlog/)
- ✅ All 3 synthesis reports posted
- ✅ All 3 files committed
- ✅ Session closeout posted

---

**Ready to begin task file creation? Start with Step 1: Read approved design output.**
