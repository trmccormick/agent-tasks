---
status: backlog
resume: false
priority: HIGH
type: documentation
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] Acceptance Criteria are measurable
- [ ] All file paths are verified to exist
- [ ] No placeholder text remains in Implementation Steps

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Documentation Agent**.

Project: agent-tasks (root workflow repository)
Task: <agent-tasks>/projects/agent-tasks/tasks/backlog/2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-AND-ROUTING-FRAMEWORK.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/agent-tasks/tasks/backlog/2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-AND-ROUTING-FRAMEWORK.md \
         projects/agent-tasks/tasks/active/2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-AND-ROUTING-FRAMEWORK.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of the git mv command in chat.
  Do NOT read the task file content until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find projects/agent-tasks/tasks -name "2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-*.md"
    Only ONE result should exist. Paste this output before proceeding.

READ FIRST (after Step 0): WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md contains full scope and context.

CRITICAL: Save synthesis report as MD file to projects/agent-tasks/summaries/ BEFORE starting changes.
  Filename: YYYY-MM-DD-SYNTHESIS-EVIDENCE-AND-ROUTING-FRAMEWORK.md
  Chat is for questions only — full synthesis goes in summaries folder.

Closeout: Follow SESSION_CLOSEOUT.md when complete.
```

---

# TASK: Add Evidence Classification and Reviewer Routing to Planning Synthesis

**Status**: BACKLOG  
**Priority**: HIGH  
**Type**: documentation  
**Created**: 2026-10-19  
**Last Updated**: 2026-10-19  

---

## Agent Assignment (Human-filled)

**Assigned To**: Qwen local via Copilot (primary)  
**Why This Agent**: Documentation writing, no codebase execution needed  
**Supervision Level**: Standard (well-specified documentation task)

---

## Prerequisites — READ FIRST (Sequential Order)

1. `WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md` — Change Sets 1 & 2 specification
2. `PLANNING_AGENT_SESSION_START.md` — current file being updated
3. `REVIEW_AGENT_GUIDE.md` § "Evidence Standards" — existing framework (reference only)
4. This task file — acceptance criteria and implementation guidance

---

## Context

The agent-tasks workflow system uses multi-agent coordination for planning, implementation, and review sessions. Planning agents currently create synthesis reports to demonstrate understanding of project state before taking action. Two improvements strengthen this:

1. **Evidence Classification**: Formalize how planning agents label evidence (direct verification vs. reported claims vs. inferred conclusions) using VERIFIED/REPORTED/ASSUMED labels
2. **Reviewer Routing**: Add optional metadata recommending whether planning outcomes require review, with reasoning

Both improvements are small, additive documentation changes to PLANNING_AGENT_SESSION_START.md that align with existing REVIEW_AGENT_GUIDE principles and improve traceability.

**Architecture Context** — Read before starting:
- `REVIEW_AGENT_GUIDE.md` § "Evidence Standards" — the authoritative principle being formalized
- `SYSTEM_INTENT.md` — overall workflow philosophy (why we need clear evidence)

---

## Problem Statement

**Current state:**
- PLANNING_AGENT_SESSION_START.md does not explicitly require evidence classification in synthesis reports
- REVIEW_AGENT_GUIDE.md defines evidence standards (VERIFIED/REPORTED/ASSUMED) but planning guidance doesn't reference them
- Planning agents lack explicit guidance on when to recommend review

**Impact:**
- Traceability gaps when reviewing planning decisions (unclear whether facts are live or dated)
- Missing reviewer routing guidance creates extra back-and-forth
- Opportunity to strengthen planning quality at low cost

**Goal:**
Add evidence classification framework and reviewer routing guidance to planning synthesis reporting.

---

## Files Involved

### Primary Files — you will edit these

| File | Purpose | Section to Edit |
|---|---|---|
| `PLANNING_AGENT_SESSION_START.md` | Planning agent startup guide | After "Step 1 — Confirm your role and project", before "Qwen — Three Modes" |

### Reference Files — read but do not edit

| File | Why You Need It |
|---|---|
| `REVIEW_AGENT_GUIDE.md` | Evidence Standards section (the principle being formalized) |
| `WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md` | Change Sets 1 & 2 — detailed spec |
| `REVIEW_AGENT_WORKFLOW.md` | Existing review workflow (for context on routing) |

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
# From inside agent-tasks repo root:
git mv projects/agent-tasks/tasks/backlog/2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-AND-ROUTING-FRAMEWORK.md \
       projects/agent-tasks/tasks/active/2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-AND-ROUTING-FRAMEWORK.md
```

Then open the moved file and change the YAML status field from `backlog` to `active`.

Then verify only one copy exists:
```bash
find projects/agent-tasks/tasks -name "2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-AND-ROUTING-FRAMEWORK.md"
```

**Paste the output of the find command in chat before proceeding.**

---

### Step 1 — Synthesis Report (Before Making Any Changes)

**Save as**: `projects/agent-tasks/summaries/2026-10-19-SYNTHESIS-EVIDENCE-AND-ROUTING-FRAMEWORK.md`

**Template**:
```markdown
## SYNTHESIS REPORT: Evidence Classification and Reviewer Routing

**Task**: 2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-AND-ROUTING-FRAMEWORK.md
**Status**: SYNTHESIS IN PROGRESS
**Date**: 2026-10-19

### What I'm About to Do
Add two subsections to PLANNING_AGENT_SESSION_START.md synthesis report guidance:
1. Evidence Classification (VERIFIED/REPORTED/ASSUMED) framework
2. Reviewer Routing Recommendation (optional metadata)

The goal is to improve planning synthesis traceability and clarify when planning outcomes need review.

### Files I'll Examine
| File | Purpose | Status |
|---|---|---|
| `PLANNING_AGENT_SESSION_START.md` | Target file | not started |
| `WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md` § "Change Set 1 & 2" | Detailed spec | reference |
| `REVIEW_AGENT_GUIDE.md` § "Evidence Standards" | Existing principle | reference |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv
- ✅ YAML status updated from backlog → active
- ✅ Read WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md Change Sets 1 & 2
- ✅ Read PLANNING_AGENT_SESSION_START.md current structure
- ✅ Read REVIEW_AGENT_GUIDE.md Evidence Standards section
- ✅ Understand this task file

### Expected Outcomes
- PLANNING_AGENT_SESSION_START.md has new subsection on synthesis reporting
- Subsection includes:
  - Evidence labeling guidance (VERIFIED/REPORTED/ASSUMED with 1-2 line definitions)
  - Example synthesis report showing proper labeling
  - Reviewer Routing metadata format (YES/NO + reason)
  - Note that REVIEW_AGENT_GUIDE defines evidence categories in full
  - Routing recommendation should NOT name specific agents, only capabilities needed
- Total addition: ~40 lines (30 for evidence + 10 for routing)
- All new content is additive (no restructuring existing sections)
- No changes to other files

### Critical Gotchas I Will Avoid
- ❌ Repeating full REVIEW_AGENT_GUIDE § "Evidence Standards" — instead ✅ summarize for planning context and reference original
- ❌ Suggesting auto-dispatch based on routing recommendation — instead ✅ make clear this is input to human decision
- ❌ Making evidence classification mandatory for all planning tasks — instead ✅ make it part of synthesis reporting guidance
- ❌ Recommending specific agent names — instead ✅ name required capabilities (human decides who provides them)

---

**SYNTHESIS COMPLETE.** Awaiting approval to proceed with draft composition.
```

---

### Step 2 — Draft Changes to PLANNING_AGENT_SESSION_START.md

Read WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md § "Change Set 1 & 2" for exact content spec.

**Location in file**: After the closing of "Step 1 — Confirm your role and project" section, immediately before "Qwen — Three Modes" section.

**Subsection 1: Evidence Classification in Synthesis Reports**

Create a new subsection titled something like:
```
## Synthesis Report Structure — Evidence Framework
```

Content should cover:
- What a synthesis report is (planning agent proves understanding before acting)
- Why evidence labeling matters (traceability, distinguishing live facts from dated claims)
- Three evidence types (with 1-2 line definition for each):
  - `(VERIFIED)` — you checked this in this session (command, direct inspection)
  - `(REPORTED)` — from status.md, handoff, or human statement (include date)
  - `(INFERRED)` — conclusion based on evidence; if material, flag for verification
- Required sections for synthesis reports:
  - Assignment summary (what you're doing)
  - Evidence basis (files read, evidence labeled)
  - Findings (what you learned)
  - Next steps (what happens next)
- Concrete example (15-20 lines) showing proper labeling

Keep this to 8-12 lines of guidance + example.

**Subsection 2: Reviewer Routing Recommendation**

Within the same subsection or immediately after, add guidance on optional routing metadata:

Content should cover:
- Optional field in synthesis: "Routing Recommendation"
- Format: YES | NO (structured)
- When to include: Provide reason when recommendation is non-obvious
- Example (5-7 lines):
  ```
  Routing Recommendation: YES
  Reason: Architecture change affecting service contracts; risk of breaking downstream integrations.
  Suggested Reviewer Capability: Architecture review with service/contract knowledge.
  ```
- Note: Name required capability, not specific agents (human decides availability)
- Note: Planning agents can recommend review; cannot auto-dispatch (REVIEW_AGENT_GUIDE § "Authority")

Keep this to 3-5 lines of guidance + example.

---

### Step 3 — Verify Clarity and Integration

1. **Clarity check**: Read your new sections as if you were a planning agent. Are instructions clear?
2. **Integration check**: Do new sections contradict existing PLANNING_AGENT_SESSION_START.md guidance?
3. **Reference check**: Do you reference REVIEW_AGENT_GUIDE and point agents to full details?
4. **Example check**: Are your examples realistic and show proper labeling?

If any of these fail, revise before proceeding to Step 4.

---

### Step 4 — Stage and Commit

Commit only the modified file with a clear message:

```bash
git add PLANNING_AGENT_SESSION_START.md
git commit -m "docs: add evidence classification and reviewer routing framework to planning synthesis guidance"
git push
```

---

### Step 5 — Move Task to Completed

```bash
git mv projects/agent-tasks/tasks/active/2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-AND-ROUTING-FRAMEWORK.md \
       projects/agent-tasks/tasks/completed/2026-10/2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-AND-ROUTING-FRAMEWORK.md
git add projects/agent-tasks/tasks/completed/2026-10/2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-AND-ROUTING-FRAMEWORK.md
git commit -m "chore: complete evidence classification and reviewer routing documentation task"
git push
```

---

## Acceptance Criteria

- [ ] PLANNING_AGENT_SESSION_START.md contains new subsection on synthesis report structure
- [ ] Evidence classification framework is present (VERIFIED/REPORTED/ASSUMED with definitions)
- [ ] Concrete example shows proper evidence labeling (at least 2 of 3 types used)
- [ ] Reviewer Routing Recommendation guidance is present (optional field, format, example)
- [ ] Routing recommendation explicitly notes: planning can recommend, not auto-dispatch
- [ ] All references to existing docs (REVIEW_AGENT_GUIDE) are accurate
- [ ] New content is ~40 lines total (additive, no restructuring)
- [ ] Changes staged, committed, and pushed
- [ ] Task file moved to completed/ and pushed

---

## Stop Conditions — escalate immediately if:
- New content contradicts existing guidance in PLANNING_AGENT_SESSION_START.md
- Example synthesis report is unclear or unrealistic
- Guidance assumes knowledge agents won't have
- Any section requires domain expertise beyond planning workflow
- Unclear whether evidence framework should have additional types beyond VERIFIED/REPORTED/ASSUMED

---

## Progress / Resume

*Updated by session whenever it stops before task completion*

- **Done so far**: [placeholder]
- **Next step**: [placeholder]
- **Last verified fact**: [placeholder]
- **Owning session**: [placeholder]

---

## Dependencies

**Blocked by**: None  
**Blocks**: 2026-10-19-MEDIUM-DOCUMENTATION-REVIEW-ARTIFACT-STANDARDIZATION.md (helpful to have evidence framework in place, but independent)  
**Related tasks**: WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md (Change Sets 1 & 2)

---

## Completion Report

*Filled in by implementing agent when task is complete*

**Completed by**: [agent name]  
**Completion date**: [YYYY-MM-DD]  
**Evidence basis**: Direct file modification and verification

### What was changed
- `PLANNING_AGENT_SESSION_START.md` — Added ~40 lines covering evidence classification (VERIFIED/REPORTED/ASSUMED) and reviewer routing recommendation guidance

### Issues discovered
[Any unexpected gaps or clarifications needed during implementation]

### Follow-up tasks needed
[Any new backlog items identified]

### Lessons learned
[What worked, what could be clearer in future documentation tasks]
