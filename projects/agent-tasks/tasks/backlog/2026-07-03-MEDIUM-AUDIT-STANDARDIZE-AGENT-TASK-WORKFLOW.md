---
status: active
priority: MEDIUM
type: documentation
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/2026-07-03-MEDIUM-AUDIT-STANDARDIZE-AGENT-TASK-WORKFLOW.md

READ FIRST: Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Create STATUS SYNTHESIS REPORT in chat BEFORE starting any work (template in task file).
```

---

# TASK: Audit and Standardize Agent Workflow Documentation
**Status**: ACTIVE
**Priority**: MEDIUM
**Type**: documentation
**Created**: 2026-07-03
**Last Updated**: 2026-07-03

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Guardrails**: `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md`
3. **Decisions**: `/Users/tam0013/Documents/git/agent-tasks/rules/DECISIONS.md`
4. **Routing**: `/Users/tam0013/Documents/git/agent-tasks/rules/AGENT_ROUTING.md`
5. **Task Template**: `/Users/tam0013/Documents/git/agent-tasks/TASK_TEMPLATE.md`
6. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context
The agent-tasks repository is the shared workflow system for all projects (Galaxy Game, Samvera, WVU Libraries). It contains task templates, guardrails, routing logic, and decision records. We need to verify that the documented workflow matches reality and identify gaps where roles, approval gates, or handoff standards are unclear or contradictory.

**Relevant Architecture Docs** — read before starting:
- `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md` — execution rules
- `/Users/tam0013/Documents/git/agent-tasks/rules/DECISIONS.md` — locked architectural decisions
- `/Users/tam0013/Documents/git/agent-tasks/rules/AGENT_ROUTING.md` — routing tables
- `/Users/tam0013/Documents/git/agent-tasks/TASK_TEMPLATE.md` — task file standard

> If a doc doesn't exist for this area, do not create one during this task. Flag the gap in your completion report instead.

---

## Problem Statement

The agent-tasks repository contains workflow documentation spread across multiple files (README, GUARDRAILS, DECISIONS, AGENT_ROUTING, TASK_TEMPLATE, COMMUNICATION_PROTOCOL, SESSION_STRATEGIST). We need to verify that:
1. The documented workflow matches the intended multi-agent process (task generation → execution → review → approval → continuation)
2. Role boundaries are clearly separated (no agent both does and validates its own work)
3. Task files follow the contract format, not open-ended prompts
4. Handoff messages are standardized

**Current behavior**: Workflow docs exist but may have contradictions, gaps, or unclear approval points.
**Expected behavior**: A coherent, audited workflow where every step has clear ownership and explicit gates.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1: This is a READ-ONLY audit — no implementation**
- ❌ Wrong: Modify any workflow docs, task templates, or repo structure
- ✅ Right: Produce a synthesis report identifying gaps and recommending changes
- Why: Changes require human approval after the audit identifies what needs fixing

⚠️ **GOTCHA 2: The repo is symlinked — use agent-tasks paths**
- ❌ Wrong: Use `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/` for reading workflow docs
- ✅ Right: Use `/Users/tam0013/Documents/git/agent-tasks/` as the source of truth
- Why: `docs/new_agent` is a symlink; agent-tasks is the canonical repo

⚠️ **GOTCHA 3: Multiple projects share this workflow**
- ❌ Wrong: Audit only Galaxy Game context
- ✅ Right: Check that workflow docs are project-agnostic and apply to all repos
- Why: The workflow system serves Galaxy Game, Samvera, WVU Libraries simultaneously

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template**:
```
## STATUS SYNTHESIS REPORT

**Task**: Audit and Standardize Agent Workflow Documentation
**Status**: backlog → active → completed
**Date**: YYYY-MM-DD

### What I'm About to Do
[2-3 sentences: audit agent-tasks workflow docs, identify gaps/contradictions, produce recommendations]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `/Users/tam0013/Documents/git/agent-tasks/README.md` | Main workflow guide | pending |
| `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md` | Execution rules | pending |
| `/Users/tam0013/Documents/git/agent-tasks/rules/DECISIONS.md` | Locked decisions | pending |
| `/Users/tam0013/Documents/git/agent-tasks/rules/AGENT_ROUTING.md` | Routing tables | pending |
| `/Users/tam0013/Documents/git/agent-tasks/TASK_TEMPLATE.md` | Task file standard | pending |

### Prerequisites Completed
- ✅ Read README.md EXECUTOR section
- ✅ Read GUARDRAILS.md
- ✅ Read DECISIONS.md
- ✅ Read AGENT_ROUTING.md
- ✅ Read TASK_TEMPLATE.md
- ✅ Understand architecture gotchas above
- ✅ Know this is read-only audit — no changes without approval

### Expected Outcomes
A synthesis report with: what currently exists, what aligns with intended flow, what is missing or inconsistent, recommended doc changes. No implementation work begins without review approval.

### Critical Gotchas I Will Avoid
- ❌ Modifying workflow docs during audit — instead ✅ produce recommendations only
- ❌ Using galaxyGame/docs/new_agent paths for workflow docs — instead ✅ use agent-tasks/ canonical paths
- ❌ Auditing only Galaxy Game context — instead ✅ check project-agnostic applicability

---

**SYNTHESIS COMPLETE.** Ready to proceed with audit.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Scope

Review:
- README files (agent-tasks root + per-project)
- Task templates (TASK_TEMPLATE.md, SIMPLE_HANDOFF_TEMPLATE.md, HANDOFF_TEMPLATE.md)
- Workflow / process documentation (GUARDRAILS.md, DECISIONS.md, AGENT_ROUTING.md, COMMUNICATION_PROTOCOL.md, SESSION_STRATEGIST.md)
- Agent instruction files
- Approval / handoff language

## Deliverable
Produce a synthesis report with:
- What currently exists (inventory of all workflow docs and their purpose)
- What aligns with the intended flow (task generation → execution → review → approval → continuation)
- What is missing or inconsistent (gaps, contradictions, unclear gates)
- Recommended doc changes (specific file + section + proposed change)

## Out of scope
- No code changes.
- No task execution changes.
- No repo-wide structural refactor unless explicitly approved.

---

## Files Involved

### Primary Files — read only (audit target)
| File | Purpose | What to Check |
|---|---|---|
| `/Users/tam0013/Documents/git/agent-tasks/README.md` | Main workflow guide | Role clarity, handoff standardization |
| `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md` | Execution rules | Approval gates, role separation |
| `/Users/tam0013/Documents/git/agent-tasks/rules/DECISIONS.md` | Locked decisions | Consistency with workflow docs |
| `/Users/tam0013/Documents/git/agent-tasks/rules/AGENT_ROUTING.md` | Routing tables | Completeness, accuracy |
| `/Users/tam0013/Documents/git/agent-tasks/TASK_TEMPLATE.md` | Task file standard | Contract format enforcement |
| `/Users/tam0013/Documents/git/agent-tasks/SIMPLE_HANDOFF_TEMPLATE.md` | Handoff standard | Standardization across projects |
| `/Users/tam0013/Documents/git/agent-tasks/HANDOFF_TEMPLATE.md` | Advanced handoff | When to use vs simple template |
| `/Users/tam0013/Documents/git/agent-tasks/COMMUNICATION_PROTOCOL.md` | Output formatting | Consistency with task templates |

### Reference Files — read for context
| File | Why You Need It |
|---|---|
| Per-project READMEs under `projects/*/README.md` | Check project-specific overrides |
| Sample task files in `tasks/backlog/` and `tasks/active/` | Verify conformance to TASK_TEMPLATE.md |

---

## Implementation Steps

### Step 1 — Inventory all workflow docs
List every file that defines or describes the agent workflow. Note its purpose, last modified date, and whether it's project-specific or shared.

```bash
find /Users/tam0013/Documents/git/agent-tasks -name "*.md" -type f | sort
```

### Step 2 — Audit each doc against intended flow
For each workflow doc, check:
- Does it clearly define role boundaries?
- Are approval points explicit (who approves what)?
- Is the work/review separation clean?
- Are there contradictions with other docs?

### Step 3 — Check task file conformance
Sample 3-5 task files from `tasks/backlog/` and `tasks/active/`. Check:
- Do they follow TASK_TEMPLATE.md filename convention?
- Do they have YAML frontmatter?
- Do they have required sections (gotchas, synthesis gate, stop conditions)?
- Are they written as contracts or open-ended prompts?

### Step 4 — Check handoff standardization
Review handoff templates and recent handoffs:
- Are they standardized across projects?
- Is the minimal handoff pattern followed?
- Do they point to task files (not duplicate content)?

### Step 5 — Produce synthesis report
Create `/Users/tam0013/Documents/git/agent-tasks/projects/agent-tasks/summaries/2026-07-03-WORKFLOW-AUDIT-SYNTHESIS.md` with:
- Inventory table of all workflow docs
- Alignment assessment (what works, what doesn't)
- Gap analysis (missing gates, unclear roles, contradictions)
- Recommended changes (specific file + section + proposed text)

---

## Acceptance Criteria
- [ ] All workflow docs inventoried and assessed
- [ ] Gaps and inconsistencies identified with specific file/section references
- [ ] Clear recommendation provided for standardization
- [ ] Synthesis report saved to summaries folder
- [ ] No implementation work begins without review approval

---

## Stop Conditions — escalate to user immediately if:
- A workflow doc is missing that the task depends on (flag as gap, don't create)
- Contradictions between docs require architectural decision to resolve
- The audit reveals a critical process gap that blocks current work
- Any change to workflow docs is requested before approval

---

## Dependencies
**Blocked by**: none
**Blocks**: Workflow standardization changes (pending audit approval)
**Related tasks**: None currently

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was audited
- [list of docs reviewed]

### Gaps identified
- [gap 1: file, section, issue]
- [gap 2: file, section, issue]

### Recommendations
- [recommendation 1: file + proposed change]
- [recommendation 2: file + proposed change]

### Issues discovered
[Any problems found during audit that weren't in the original task]

---

## Handoff Summary
HANDOFF SUMMARY: [files audited] | [gaps identified] | [next action needed]
