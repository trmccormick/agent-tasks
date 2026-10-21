# Galaxy Game UI — Items Needing Review

**Last Updated**: 2026-10-21

---

## Documentation Tasks (Workflow Patterns Discovered)

### Task: Update Planning Agent Dispatch Documentation

**Priority**: MEDIUM  
**Category**: Workflow Documentation  
**Context**: Discovered that Planning Agent can spawn Implementation Agent subagent sessions directly for read-only remediation tasks.

**What needs updating**:

1. **PLANNING_AGENT_SESSION_START.md**
   - Add section: "Spawning Implementation Agent Subagents"
   - Document: when appropriate, how to call runSubagent(), what to pass
   - Example: remediation tasks, read-only audits, parallel discovery work
   - Note: synthesis gates still apply to subagent results

2. **TASK_TEMPLATE.md**
   - Add section: "Subagent Dispatch Suitability"
   - Criteria for tasks suitable for subagent spawn vs. human dispatch
   - Read-only audit ✅ | Code implementation ⚠️ | User interaction ❌

3. **IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md**
   - Clarify: procedure applies whether session spawned by human, Planning Agent, or Copilot chat
   - Same synthesis gate pattern applies regardless of origin

4. **New doc** (optional): `AGENT_DISPATCH_PATTERNS.md`
   - Comprehensive guide to agent spawning patterns
   - When Planning Agent should spawn vs. defer to human
   - Parallel vs sequential task dispatch
   - Synthesis gate requirements for subagent work

**Evidence**:
- Commit: e8c57df (remediation tasks created)
- Qwen Planning Agent (2026-10-21): "I'll execute both tasks directly via separate subagent sessions"
- Both tasks executed in parallel, handoffs created, synthesis gates applied

**Action**: Review after remediation tasks complete (expect handoffs by end of 2026-10-21).

---

## Code Issues (Post-Audit)

### Task: Reconcile MockDataHelper Method Discrepancies

**Priority**: HIGH  
**Category**: Code  
**Blocked by**: Task `2026-10-21-HIGH-AUDIT-MOCKDATA-METHODS.md` (in progress)

**What needs deciding**:
- Dashboard synthesis references `player_contracts`, `pending_operations` → NOT in MockDataHelper
- Admin synthesis references `ai_manager_priorities`, `digital_twins`, `terraforming_scenarios` → NOT in code
- Admin method is `digital_twin_scenarios` not `digital_twins`

**Decision needed**:
- Add missing methods to MockDataHelper?
- Rename existing methods for clarity?
- Reword synthesis reports to match actual methods?
- Combination of above?

**Action**: Review Qwen's audit report. Make reconciliation decision. Update mock data or synthesis reports accordingly.

---

## Design Session (Blocked Gate)

### Task: Design Session — Clarify Dashboard Scope

**Priority**: HIGH  
**Category**: UX/Design  
**Blocks**: Tasks 2 (Dashboard), 2a–2d (subtasks)

**What needs deciding**:
- Which panels should Dashboard display? (4? 9? custom selection?)
- Task Context lists 9 sections | Task Steps list 4 panels | Qwen Synthesis lists 3 panels (conflicting)
- Design must reference Galaxy Game prototype + EVE Dashboard for inspiration

**Decision needed**:
- Schedule Gemini design session (or manual review)
- Approve specific panel list and layout
- Document output: screen mockup descriptions, data flow, acceptance criteria

**Action**: Schedule after remediation tasks complete and user approves method reconciliation.

---

## Process/Governance

### Task: Task 1 File Remediation Complete — Move to Completed

**Priority**: HIGH  
**Category**: Process  
**Blocked by**: Task `2026-10-21-HIGH-REMEDIATE-TASK-1-CLEANUP.md` (in progress)

**What needs doing**:
- Qwen completes Task 1 cleanup
- Creates handoff file
- Human reviews + gives GO disposition
- Move task file from `active/` → `completed/2026-10/`
- Commit with explicit paths (per GUARDRAILS)

**Action**: Execute after remediation GO.

---

## Summary

| Item | Status | Blocker? | ETA |
|------|--------|----------|-----|
| Workflow doc update | 🔴 NOT STARTED | No | Post-2026-10-21 |
| MockDataHelper reconciliation | 🟡 IN PROGRESS | Yes (Task 2) | 2026-10-21 |
| Design session scheduling | 🔴 NOT STARTED | Yes (Tasks 2–4) | Post-2026-10-21 |
| Task 1 file completion | 🟡 IN PROGRESS | No (Task 1) | 2026-10-21 |
