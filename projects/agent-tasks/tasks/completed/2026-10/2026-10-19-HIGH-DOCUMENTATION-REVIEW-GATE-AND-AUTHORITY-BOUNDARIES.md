---
status: completed
resume: false
priority: HIGH
type: documentation
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)
**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section above is complete and accurate (no placeholders)
- [x] All Step 0-N instructions are clear and actionable (not vague)
- [x] Synthesis report template is provided (copy/paste ready, not as example)
- [x] Acceptance Criteria are measurable
- [x] All file paths are verified to exist
- [x] No placeholder text remains in Implementation Steps

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Documentation Agent**.

Project: agent-tasks (root workflow repository)
Task: <agent-tasks>/projects/agent-tasks/tasks/active/2026-10-19-HIGH-DOCUMENTATION-REVIEW-GATE-AND-AUTHORITY-BOUNDARIES.md

READ FIRST: WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md contains full context.

CRITICAL: Save synthesis report as MD file to projects/agent-tasks/summaries/ BEFORE starting changes.
  Filename: YYYY-MM-DD-SYNTHESIS-REVIEW-GATE-AND-AUTHORITY-BOUNDARIES.md
  Chat is for questions only — full synthesis goes in summaries folder.

Closeout: Follow SESSION_CLOSEOUT.md when complete.
```

---

# TASK: Add Review-Gate Checkpoint and Clarify Authority Boundaries

**Status**: ACTIVE  
**Priority**: HIGH  
**Type**: documentation  
**Created**: 2026-10-19  
**Last Updated**: 2026-10-20  
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
Task: <agent-tasks>/projects/agent-tasks/tasks/backlog/2026-10-19-HIGH-DOCUMENTATION-REVIEW-GATE-AND-AUTHORITY-BOUNDARIES.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/agent-tasks/tasks/backlog/2026-10-19-HIGH-DOCUMENTATION-REVIEW-GATE-AND-AUTHORITY-BOUNDARIES.md \
         projects/agent-tasks/tasks/active/2026-10-19-HIGH-DOCUMENTATION-REVIEW-GATE-AND-AUTHORITY-BOUNDARIES.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of the git mv command in chat.
  Do NOT read the task file content until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find projects/agent-tasks/tasks -name "2026-10-19-HIGH-DOCUMENTATION-REVIEW-GATE-*.md"
    Only ONE result should exist. Paste this output before proceeding.

READ FIRST (after Step 0): WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md contains full context.

CRITICAL: Save synthesis report as MD file to projects/agent-tasks/summaries/ BEFORE starting changes.
  Filename: YYYY-MM-DD-SYNTHESIS-REVIEW-GATE-AND-AUTHORITY-BOUNDARIES.md
  Chat is for questions only — full synthesis goes in summaries folder.

Closeout: Follow SESSION_CLOSEOUT.md when complete.
```

---

# TASK: Add Review-Gate Checkpoint and Clarify Authority Boundaries

**Status**: BACKLOG  
**Priority**: HIGH  
**Type**: documentation  
**Created**: 2026-10-19  
**Last Updated**: 2026-10-19  

---

## Agent Assignment (Human-filled)

**Assigned To**: Qwen local via Copilot (primary)  
**Why This Agent**: Documentation writing, multi-file coordination  
**Supervision Level**: Standard (foundational workflow clarification)

---

## Prerequisites — READ FIRST (Sequential Order)

1. `WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md` — Full context
2. `TASK_TEMPLATE.md` — Current task template structure
3. `REVIEW_AGENT_WORKFLOW.md` — Current review workflow
4. `SESSION_CLOSEOUT.md` — Current closeout procedure
5. `IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md` — Implementation closeout (reference)
6. This task file — acceptance criteria and implementation guidance

---

## Context

During workflow governance review, a critical authority boundary gap was identified:

**Current State:** Task templates do not include explicit review-gate checkpoint. Implementation agents complete verification and proceed directly to commit/push, bypassing reviewer assessment.

**Intended State:** Implementation agents should verify work, create synthesis, generate closeout, then STOP and await:
1. Reviewer assessment (GO | REVISE | STOP)
2. Explicit human authorization for commit/push/task completion

**Impact:**
- Reviewers lack authority to block commits
- Implementation agents are unaware they should wait for approval
- Authority boundaries between implementation, reviewer, and human are undefined
- Future tasks will inherit the same defect unless fixed at template level

**Goal:**
Add review-gate checkpoint to implementation workflows and clarify authority boundaries across all workflow documentation.

---

## Authority Model (Reference Only — Do NOT Edit)

**Implementation Agent Authority:**
- ✅ Perform implementation work
- ✅ Verify own work
- ✅ Generate synthesis report
- ✅ Generate session closeout
- ❌ Commit (requires human authorization after reviewer GO)
- ❌ Push (requires human authorization after reviewer GO)
- ❌ Complete task (requires human authorization after reviewer GO)

**Reviewer Authority:**
- ✅ Assess work against acceptance criteria
- ✅ Issue GO | REVISE | STOP
- ✅ Block commits (REVISE/STOP means no commit authority granted)
- ❌ Authorize commits (that authority belongs to human)

**Human Authority:**
- ✅ Approve commit/push after reviewer GO
- ✅ Delegate commit authority (with conditions)
- ✅ Request revisions or rejection (override reviewer GO if needed)
- ✅ Authorize task completion

**Default Workflow:**
```
Implementation Agent
  → Implement work
  → Verify work
  → Create synthesis report
  → Create session closeout
  → Present artifacts for review
  → STOP (await reviewer assessment)
    ↓
Reviewer
  → Assess synthesis + implementation
  → Issue: GO | REVISE | STOP
    ↓
Human
  → Review assessment
  → (If GO) Authorize: commit/push/task completion
    ↓
Implementation Agent
  → Commit changes
  → Push to origin
  → Move task to completed/
```

---

## Files Involved

### Primary Files — you will edit these

| File | Purpose | Section to Edit |
|---|---|---|
| `TASK_TEMPLATE.md` | Task template for all implementation agents | After verification step, before commit step |
| `REVIEW_AGENT_GUIDE.md` | Review principles and authority | Add/expand § "Authority" section |
| `REVIEW_AGENT_WORKFLOW.md` | Review procedure | Add section on reviewer authority over commits |
| `SESSION_CLOSEOUT.md` | Generic closeout procedure | Clarify that closeout cannot complete until review gate satisfied |
| `IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md` | Implementation-specific closeout | Add review-gate requirement before commit/push steps |

### Reference Files — read but do not edit

| File | Why You Need It |
|---|---|
| `WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md` | Context for this task |
| `PLANNING_AGENT_CLOSEOUT.md` | Existing closeout pattern (for consistency) |

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
# From inside agent-tasks repo root:
git mv projects/agent-tasks/tasks/backlog/2026-10-19-HIGH-DOCUMENTATION-REVIEW-GATE-AND-AUTHORITY-BOUNDARIES.md \
       projects/agent-tasks/tasks/active/2026-10-19-HIGH-DOCUMENTATION-REVIEW-GATE-AND-AUTHORITY-BOUNDARIES.md
```

Then open the moved file and change the YAML status field from `backlog` to `active`.

Then verify only one copy exists:
```bash
find projects/agent-tasks/tasks -name "2026-10-19-HIGH-DOCUMENTATION-REVIEW-GATE-*.md"
```

**Paste the output of the find command in chat before proceeding.**

---

### Step 1 — Synthesis Report (Before Making Any Changes)

**Save as**: `projects/agent-tasks/summaries/2026-10-19-SYNTHESIS-REVIEW-GATE-AND-AUTHORITY-BOUNDARIES.md`

**Template**:
```markdown
## SYNTHESIS REPORT: Review-Gate and Authority Boundaries

**Task**: 2026-10-19-HIGH-DOCUMENTATION-REVIEW-GATE-AND-AUTHORITY-BOUNDARIES.md
**Status**: SYNTHESIS IN PROGRESS
**Date**: 2026-10-19

### What I'm About to Do
Add review-gate checkpoints and clarify authority boundaries across 5 workflow files:

1. **TASK_TEMPLATE.md** — Add explicit review-gate step between verification and commit
2. **REVIEW_AGENT_GUIDE.md** — Expand authority section to include reviewer authority over commits
3. **REVIEW_AGENT_WORKFLOW.md** — Add section explaining when review happens and its authority
4. **SESSION_CLOSEOUT.md** — Clarify that closeout cannot complete until review gate is satisfied
5. **IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md** — Add review-gate requirement before commit/push/completion steps

### Files I'll Examine/Modify
| File | Purpose | Status |
|---|---|---|
| `TASK_TEMPLATE.md` | Primary target | not started |
| `REVIEW_AGENT_GUIDE.md` | Secondary target | not started |
| `REVIEW_AGENT_WORKFLOW.md` | Secondary target | not started |
| `SESSION_CLOSEOUT.md` | Secondary target | not started |
| `IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md` | Secondary target | not started |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv
- ✅ YAML status updated from backlog → active
- ✅ Read all target files
- ✅ Understand authority model (implementation/reviewer/human)
- ✅ Understand this task file

### Expected Outcomes
- All 5 target files updated with review-gate and authority clarifications
- Implementation templates now include explicit STOP point before commit
- Reviewer authority clearly documented
- Human authorization requirement stated
- Commit/push authority tied to: reviewer GO + human authorization
- Total additions across files: ~100-120 lines
- All changes additive (no restructuring existing sections)
- Examples show proper review-gate workflow

### Critical Gotchas I Will Avoid
- ❌ Suggesting implementation agents "can never commit" — instead ✅ clarify: CAN commit after approval
- ❌ Suggesting reviewers can commit directly — instead ✅ reviewers assess; humans authorize
- ❌ Making review-gate mandatory for all tasks — instead ✅ make it mandatory for implementation; reference only for others
- ❌ Contradicting existing authority documentation — instead ✅ expand/clarify what's already implied
- ❌ Creating new approval workflow — instead ✅ add checkpoint to existing workflow

---

**SYNTHESIS COMPLETE.** Awaiting approval to proceed with drafting.
```

---

### Step 2 — Modify TASK_TEMPLATE.md

**Location**: Find the verification step (typically after "Step 3" or equivalent) and before the commit step.

**Add new section** between verification and commit, titled:
```
### Step 3.5 — WAIT FOR REVIEWER ASSESSMENT AND HUMAN AUTHORIZATION
```

**Content** (8-12 lines):
- Clear statement: "Do not commit, push, or complete task until both conditions are met"
- Condition 1: Reviewer has assessed work and issued GO
- Condition 2: Human has explicitly authorized commit/push/task completion
- Instruction: Submit synthesis report and implementation artifacts for review
- Guidance: Link to review-assessment location; request GO | REVISE | STOP
- Note: If REVISE or STOP, implement feedback and resubmit; do not commit
- Note: Wait for explicit human approval before proceeding to commit step

**Keep concise**: This is an inserted checkpoint, not a full new task phase.

---

### Step 3 — Modify REVIEW_AGENT_GUIDE.md § "Authority"

**Location**: Find "Authority" section (if it exists) or create it after § "Principles"

**Content** (15-20 lines total):

Create or expand section with subsections:

**Subsection 1: Reviewer Authority**
- Define what reviewers CAN do: assess work, issue dispositions (GO|REVISE|STOP), block commits
- Define what reviewers CANNOT do: commit directly, push, complete tasks
- State: Reviewer authority is advisory to human (human makes final decision)
- Example: "Reviewer issues STOP → Implementation cannot proceed to commit without human override"

**Subsection 2: Commit Authority**
- Commit authority requires TWO approvals: reviewer GO + human authorization
- Implementation agents may only commit after both approvals are received
- Human retains final commit authority (can reject reviewer GO if needed)

**Subsection 3: Scope of Review**
- Reviews assess: acceptance criteria, evidence basis, implementation quality, risks
- Reviews do NOT decide: commit authority (that's human's role)

---

### Step 4 — Modify REVIEW_AGENT_WORKFLOW.md

**Location**: After main workflow steps, add new section

**New section** titled:
```
## Reviewer Authority Over Commits
```

**Content** (12-15 lines):

- When reviewer assessment happens: After implementation agent creates synthesis report and presents for review
- Reviewer's decision authority: Can issue GO (clear to commit), REVISE (implement feedback first), or STOP (do not commit)
- Implementation cannot commit unless: Reviewer issues GO AND human authorizes
- Timing: Review happens before commits (not after)
- Human final authority: Can override reviewer assessment if needed; makes final commit decision
- Communication: Reviewer communicates assessment to both implementation agent and human

---

### Step 5 — Modify SESSION_CLOSEOUT.md

**Location**: At the start of the "Closeout" section or in the introduction

**Add clarification** (3-4 lines):

"Session cannot be closed out (commits cannot be staged) until:
1. Reviewer has assessed synthesis and implementation
2. Reviewer has issued: GO | REVISE | STOP
3. If GO: Human has explicitly authorized commit/push/task completion

If REVISE or STOP: Update progress notes and submit for next iteration; do not close out."

---

### Step 6 — Modify IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md

**Location**: Before the "Task finished" section (§ 1a)

**Add introductory note** (5-8 lines):

"**Important:** Do not proceed with commit or task completion steps until:
1. Reviewer has assessed your work and issued GO (not REVISE or STOP)
2. Human has explicitly authorized: 'proceed with commit/push/task completion'

If you received REVISE: Implement feedback, regenerate synthesis, resubmit for review.
If you received STOP: Do not commit. Document blocker and await human decision.

Task completion steps below require both approvals."

**Location**: In § 1a. Task finished, Step 3 (the git mv command)

**Add step** before the git mv command:

"**Before moving task:** Confirm you have:
- ✅ Reviewer GO assessment
- ✅ Human authorization to commit/push/complete
- ✅ Synthesis report saved to summaries/"

---

### Step 7 — Verify Clarity and Consistency

1. **Consistency check**: Do all 5 files present the same authority model?
2. **Clarity check**: Is it clear when implementation stops and awaits review?
3. **Authority check**: Are authority boundaries between implementation/reviewer/human clear?
4. **Example check**: Do workflow examples show proper review-gate sequence?
5. **Enforcement check**: Do templates prevent agents from skipping review gate?

If any fail, revise before proceeding to Step 8.

---

### Step 8 — Stage and Commit

```bash
# All modified files
git add TASK_TEMPLATE.md REVIEW_AGENT_GUIDE.md REVIEW_AGENT_WORKFLOW.md SESSION_CLOSEOUT.md IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md

# Single commit for all related changes
git commit -m "docs: add review-gate checkpoint and clarify authority boundaries for implementation, reviewer, and human roles"
git push
```

---

### Step 9 — Move Task to Completed

```bash
git mv projects/agent-tasks/tasks/active/2026-10-19-HIGH-DOCUMENTATION-REVIEW-GATE-AND-AUTHORITY-BOUNDARIES.md \
       projects/agent-tasks/tasks/completed/2026-10/2026-10-19-HIGH-DOCUMENTATION-REVIEW-GATE-AND-AUTHORITY-BOUNDARIES.md
git add projects/agent-tasks/tasks/completed/2026-10/2026-10-19-HIGH-DOCUMENTATION-REVIEW-GATE-AND-AUTHORITY-BOUNDARIES.md
git commit -m "chore: complete review-gate and authority boundaries documentation task"
git push
```

---

## Acceptance Criteria

- [ ] TASK_TEMPLATE.md contains new review-gate step (Step 3.5 or equivalent)
- [ ] Review-gate step explicitly requires reviewer GO and human authorization before commit
- [ ] REVIEW_AGENT_GUIDE.md § "Authority" section clearly defines reviewer authority
- [ ] Authority section states reviewers CANNOT commit; humans can authorize commits
- [ ] REVIEW_AGENT_WORKFLOW.md explains when review happens (after synthesis, before commit)
- [ ] SESSION_CLOSEOUT.md clarifies that closeout requires review gate satisfaction
- [ ] IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md § 1a includes review-gate requirement
- [ ] All 5 files present consistent authority model (implementation/reviewer/human)
- [ ] Examples show proper sequence: implement → verify → synthesis → review-gate STOP → reviewer assessment → human authorization → commit
- [ ] Total additions across files: ~100-120 lines (additive, no restructuring)
- [ ] All changes staged, committed, and pushed
- [ ] Task file moved to completed/ and pushed

---

## Stop Conditions — escalate immediately if:
- Unclear whether commit authority should be human vs. reviewer
- Conflicting authority definitions in existing documentation
- Uncertainty about when review-gate should apply (implementation only? all agents?)
- Question about whether implementation agents should ever commit without human approval
- Concern about adding too many approval steps slowing workflow

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
**Blocks**: Tasks 2 and 3 (Review Artifact Standardization, Stop Condition Enhancement) — those tasks should be reviewed against this authority model before dispatch  
**Related tasks**: 2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-AND-ROUTING-FRAMEWORK.md (already accepted), WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md (context)

---

## Completion Report

*Filled in by implementing agent when task is complete*

**Completed by**: [agent name]  
**Completion date**: [YYYY-MM-DD]  
**Evidence basis**: Direct file modification and verification

### What was changed
- `TASK_TEMPLATE.md` — Added ~8-12 lines for review-gate step
- `REVIEW_AGENT_GUIDE.md` — Added ~15-20 lines for authority definitions
- `REVIEW_AGENT_WORKFLOW.md` — Added ~12-15 lines for reviewer authority section
- `SESSION_CLOSEOUT.md` — Added ~3-4 lines clarifying review-gate requirement
- `IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md` — Added ~5-8 lines for introductory clarification + guidance before git mv

### Issues discovered
[Any unexpected gaps or clarifications needed during implementation]

### Follow-up tasks needed
[Any new backlog items identified]

### Lessons learned
[What worked, what could be clearer in future documentation tasks]
