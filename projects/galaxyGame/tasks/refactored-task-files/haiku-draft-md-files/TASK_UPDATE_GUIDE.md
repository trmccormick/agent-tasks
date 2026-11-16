# Task Update & Reconciliation Guide

**Purpose**: To provide a standardized process for reviewing, updating, and re-activating stale tasks that predate recent architectural decisions or refactors.

---

## 1. The Audit Protocol (Before Updating)
When a task is flagged as "stale" (pre-dating major refactors), perform this audit:
* **Version Check**: Does the task reference legacy models?
* **Audit Link**: Does an audit report (`RESEARCH-*.md`) already exist? If not, a research task is required first.
* **Dependency Check**: Does the stale task rely on code moved or refactored since the task's creation?

## 2. Update Checklist (The Reconciliation)
All updated tasks **must** meet these requirements to be considered valid for assignment:

- [ ] **Rename**: Update filename to `YYYY-MM-DD-PRIORITY-TYPE-NAME.md`.
- [ ] **Template Compliance**: Ensure the `TASK_TEMPLATE.md` structure (located at `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/TASK_TEMPLATE.md`) is strictly followed.
- [ ] **Agent Handoff**: Include the specific "Read First" block and "Status Synthesis Report" requirements found in `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/agent handoff examples/`.
- [ ] **Verification**: Ensure `docker exec` test commands are present.

## 3. The "Phase" Assignment
* **`tasks/active/`**: Only for the current, immediate sprint.
* **`tasks/backlog/phase{num}+/`**: For organized future development phases.
* **`tasks/deprecated/`**: Move original stale file here for archival once the new version is created and approved.

---

## 4. Reconciliation Workflow (Handoff)
Use this header for all reconciled tasks:

```markdown
# TASK RECONCILIATION: [Original Task Name]
**Status**: RECONCILED
**Reference**: [Link to Original Stale Task]

## Changes made during reconciliation:
1. Updated code paths to reflect post-Phase 1 architecture.
2. Removed references to legacy currency models.
3. Updated verification commands for Docker container compatibility.
4. Integrated mandatory Handoff Templates (Read First/Status Synthesis).