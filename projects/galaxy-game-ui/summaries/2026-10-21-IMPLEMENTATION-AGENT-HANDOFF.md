# Implementation Agent — Dashboard Tasks 2–4 Handoff

**Date**: 2026-10-21  
**From**: Planning Agent (Qwen)  
**To**: Implementation Agent  
**Phase**: Dashboard implementation (from approved design)

---

## Planning Agent Phase Complete ✅

**Planning Agent successfully**:
- ✅ Moved master task to active/: `2026-10-21-HIGH-FEATURE-DASHBOARD-TASK-REWRITE-2-4.md`
- ✅ Created 3 implementation task files (all in backlog/)
- ✅ Created 3 synthesis reports (all in summaries/)
- ✅ Committed all deliverables (commit bcd440d)

---

## Three Task Files Ready for Implementation Dispatch

All 3 task files are in `projects/galaxy-game-ui/tasks/backlog/`:

### Task 2: Dashboard Header + Bay Inventory
**File**: `2026-10-21-HIGH-FEATURE-DASHBOARD-HEADER-AND-INVENTORY.md`  
**Scope**: Panels H (header) + 1 (inventory)  
**Status**: ✅ Ready for dispatch  
**Synthesis Report**: `2026-10-21-SYNTHESIS-TASK-2-HEADER-INVENTORY.md`

### Task 3: Ships + Manufacturing Queue
**File**: `2026-10-21-HIGH-FEATURE-DASHBOARD-SHIPS-AND-MANUFACTURING.md`  
**Scope**: Panels 2 (ships) + 3 (manufacturing + research toggle)  
**Status**: ✅ Ready for dispatch  
**Synthesis Report**: `2026-10-21-SYNTHESIS-TASK-3-SHIPS-MANUFACTURING.md`

### Task 4: Open Orders + Market Footer
**File**: `2026-10-21-HIGH-FEATURE-DASHBOARD-ORDERS-AND-MARKET.md`  
**Scope**: Panels 4 (orders) + 5 (market footer)  
**Status**: ✅ Ready for dispatch  
**Synthesis Report**: `2026-10-21-SYNTHESIS-TASK-4-ORDERS-MARKET.md`

---

## Implementation Sequence

**Sequential**: Task 2 → Task 3 → Task 4

Each task builds on previous:
- Task 2 creates grid structure + controller variables
- Task 3 adds ships panel (right column) + manufacturing/research tabs
- Task 4 adds orders panel (full width) + market footer

All 3 tasks implement components in `app/views/dashboard/index.html.erb` with data bound in `DashboardController#index`.

---

## Reference Materials

**Approved Design** (source of truth):
- `projects/galaxy-game-ui/summaries/2026-10-21-DESIGN-SESSION-OUTPUT-QWEN.md`
  - Panel list (6 panels + header)
  - Layout diagram (2-column responsive grid)
  - Data binding map (9 template variables)
  - Styling notes (Galaxy Game palette + Tailwind classes)

**Task Dispatch Specifications**:
- `projects/galaxy-game-ui/summaries/2026-10-21-QWEN-TASK-REWRITE-DISPATCH.md`
  - Detailed scope for each of the 3 tasks
  - Acceptance criteria (from approved design)
  - Data binding examples (template variable names)
  - Implementation notes (where files go, what to add)

**Project Context**:
- `projects/galaxy-game-ui/README.md` — project setup, architecture
- `projects/galaxy-game-ui/status.md` — current phase tracking
- `IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md` — closeout workflow

---

## Next Action: Dispatch Task 2

**Implementation Agent workflow** (for each task):

1. Create fresh session or open new chat
2. Copy the Agent Dispatch Interface from the task file
3. Paste it to the Implementation Agent
4. Implementation Agent will:
   - Step 0: Move task from backlog/ → active/
   - Read full task content
   - Create synthesis report (new file in summaries/)
   - Post synthesis to chat (approval gate)
   - Implement features per task spec
   - Test against acceptance criteria
   - Request review + approval before commit

---

## Critical Notes for Implementation Agent

**Zero Deviation Required**:
- ✅ Use ONLY verified MockDataHelper methods (from design binding map)
- ✅ Use ONLY Galaxy Game terminology (Water Ice, GCC, Oxygen, Deuterium, Regolith)
- ✅ Apply ONLY approved Tailwind classes (from design styling notes)
- ✅ Follow layout exactly (2-column grid, mobile stacking, tabs placement)
- ✅ No database queries (MockDataHelper in-memory only)
- ✅ No EVE Online terminology

**Files Modified**:
- `app/views/dashboard/index.html.erb` — add all 3 task panel groups
- `app/controllers/dashboard_controller.rb` — set instance variables (@system_status, @bay_items, @ships, @mfg, @research, @orders, @missions, @market)
- Possibly: `app/javascript/controllers/dashboard_tabs.js` — Stimulus tab toggle controller (if not already exists)

**Route Already Exists**: `GET /dashboard` (public, no auth required)

---

## Timeline

- Task 2: ~45 min (simplest: header + inventory)
- Task 3: ~60 min (more complex: ships + tabs)
- Task 4: ~60 min (complex: orders tabs + market footer + styling)
- Review gates: ~15 min each
- **Total**: ~3–4 hours for all 3 tasks

---

## Readiness Checklist

Before dispatching Implementation Agent, verify:

- [ ] All 3 task files exist in backlog/
- [ ] All 3 synthesis reports exist in summaries/
- [ ] Approved design document exists and is complete
- [ ] Dispatch specs document exists and is complete
- [ ] This handoff document created (you're reading it)
- [ ] Galaxy Game UI server running: `./up.sh` in galaxy-game-ui/
- [ ] Rails server listening on 0.0.0.0:3000
- [ ] MockDataHelper methods callable without errors

---

## Dispatch Command (Ready to Use)

**When ready, send Implementation Agent**:

Open the Agent Dispatch Interface from:  
`projects/galaxy-game-ui/tasks/backlog/2026-10-21-HIGH-FEATURE-DASHBOARD-HEADER-AND-INVENTORY.md`

Copy the dispatch interface code block and paste to Implementation Agent chat.

---

**Planning Phase Closed.** Implementation phase ready to begin.

Handoff prepared by: Planning Agent (Qwen)  
Handoff date: 2026-10-21  
Status: Ready for Implementation Agent dispatch
