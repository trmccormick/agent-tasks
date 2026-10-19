---
status: backlog
resume: false
priority: MEDIUM
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
Task: <agent-tasks>/projects/agent-tasks/tasks/backlog/2026-10-19-MEDIUM-DOCUMENTATION-STOP-CONDITION-ENHANCEMENT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/agent-tasks/tasks/backlog/2026-10-19-MEDIUM-DOCUMENTATION-STOP-CONDITION-ENHANCEMENT.md \
         projects/agent-tasks/tasks/active/2026-10-19-MEDIUM-DOCUMENTATION-STOP-CONDITION-ENHANCEMENT.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of the git mv command in chat.
  Do NOT read the task file content until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find projects/agent-tasks/tasks -name "2026-10-19-MEDIUM-DOCUMENTATION-STOP-*.md"
    Only ONE result should exist. Paste this output before proceeding.

READ FIRST (after Step 0): WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md contains full scope and context.

CRITICAL: Save synthesis report as MD file to projects/agent-tasks/summaries/ BEFORE starting changes.
  Filename: YYYY-MM-DD-SYNTHESIS-STOP-CONDITION-ENHANCEMENT.md
  Chat is for questions only — full synthesis goes in summaries folder.

Closeout: Follow SESSION_CLOSEOUT.md when complete.
```

---

# TASK: Enhance Stop Condition Guidance for Risk and Blocker Surfacing

**Status**: BACKLOG  
**Priority**: MEDIUM  
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

1. `WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md` — Change Set 4 specification
2. `SESSION_CLOSEOUT.md` § "1b. Session agent: append one entry" — current stop condition guidance
3. This task file — acceptance criteria and implementation guidance

---

## Context

The workflow system requires all implementing sessions to write a brief status.md entry when closing out, including a "Stop condition" bullet explaining why work stopped. Currently this field has minimal guidance and is often treated as a simple yes/no "task complete/not complete" indicator.

This task strengthens the "Stop condition" field to surface blockers and known risks explicitly, without requiring a new separate "Risk Register" artifact. This:
- Improves visibility of blockers when planning next session
- Surfaces session-specific risks (not architectural risks) in the status.md log
- Keeps the system lightweight (leverages existing field, no new maintenance overhead)

The enhancement adds guidance subsections after step 3 of SESSION_CLOSEOUT.md § "1b" explaining how to express normal stops, blocked stops, and risk stops with realistic examples.

**Architecture Context** — Read before starting:
- `PLANNING_AGENT_CLOSEOUT.md` — how planning agents use status.md for continuity
- `SYSTEM_INTENT.md` § "What Gets Remembered" — why status.md is critical for handoff chaining

---

## Problem Statement

**Current state:**
- "Stop condition" guidance in SESSION_CLOSEOUT.md § "1b" is minimal ("why you stopped, or 'none' if the work is finished")
- Implementing sessions often write "task paused" or "blocked" without explaining the blocker
- Risks discovered during work are not systematically surfaced
- Planning agents must infer blocker details from handoff files, not quick status.md summary

**Impact:**
- Next planning session may miss critical blockers and try to resume work on something that's actually blocked
- Known risks from implementation sessions are not visible at planning layer
- Opportunity to strengthen information flow using existing artifacts

**Proposed solution (REVISE approach):**
Instead of adding new Risk Register artifact, enhance guidance for existing "Stop condition" field to explicitly surface:
- Normal stops (where resume is straightforward)
- Blocked stops (external dependency, can't proceed)
- Risk stops (task paused, but here's what could go wrong)

All optional; only used when relevant. No new mandatory fields.

**Goal:**
Add clear, realistic examples of how to express stops, blockers, and risks using the existing "Stop condition" field.

---

## Files Involved

### Primary Files — you will edit this

| File | Purpose | Section to Edit |
|---|---|---|
| `SESSION_CLOSEOUT.md` | Closeout procedure for all sessions | § "1b. Session agent: append one entry", after step 3 (the four-bullet description) |

### Reference Files — read but do not edit

| File | Why You Need It |
|---|---|
| `WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md` | Change Set 4 — detailed spec |
| `PLANNING_AGENT_CLOSEOUT.md` | How planning agents read and use status.md |

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
# From inside agent-tasks repo root:
git mv projects/agent-tasks/tasks/backlog/2026-10-19-MEDIUM-DOCUMENTATION-STOP-CONDITION-ENHANCEMENT.md \
       projects/agent-tasks/tasks/active/2026-10-19-MEDIUM-DOCUMENTATION-STOP-CONDITION-ENHANCEMENT.md
```

Then open the moved file and change the YAML status field from `backlog` to `active`.

Then verify only one copy exists:
```bash
find projects/agent-tasks/tasks -name "2026-10-19-MEDIUM-DOCUMENTATION-STOP-*.md"
```

**Paste the output of the find command in chat before proceeding.**

---

### Step 1 — Synthesis Report (Before Making Any Changes)

**Save as**: `projects/agent-tasks/summaries/2026-10-19-SYNTHESIS-STOP-CONDITION-ENHANCEMENT.md`

**Template**:
```markdown
## SYNTHESIS REPORT: Stop Condition Enhancement

**Task**: 2026-10-19-MEDIUM-DOCUMENTATION-STOP-CONDITION-ENHANCEMENT.md
**Status**: SYNTHESIS IN PROGRESS
**Date**: 2026-10-19

### What I'm About to Do
Strengthen "Stop condition" guidance in SESSION_CLOSEOUT.md § "1b" to help implementing sessions surface blockers and risks explicitly.

Add subsection after step 3 with three categories:
1. Normal stop (straightforward resume point)
2. Blocked stop (external dependency blocking)
3. Risk stop (task paused, but here's a known risk)

Each category has realistic example. This replaces proposed Risk Register with lightweight guidance.

### Files I'll Examine/Modify
| File | Purpose | Status |
|---|---|---|
| `SESSION_CLOSEOUT.md` | Target file | not started |
| `WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md` § "Change Set 4" | Detailed spec | reference |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv
- ✅ YAML status updated from backlog → active
- ✅ Read WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md Change Set 4
- ✅ Read SESSION_CLOSEOUT.md § "1b" current guidance
- ✅ Understand this task file

### Expected Outcomes
- SESSION_CLOSEOUT.md § "1b" has new subsection after step 3
- Subsection titled something like "Guidance: Expressing Stop Conditions and Risks"
- Three categories with 2-3 line guidance each + realistic example (4-6 lines per example)
- Examples show: normal resume, blocked dependency, known risk with mitigation
- Clear statement that risk fields are optional (use only when real risk exists)
- Clear statement that risks here are session-specific (broader architectural risks belong elsewhere)
- Total addition: ~25-30 lines

### Critical Gotchas I Will Avoid
- ❌ Encouraging over-documentation of speculative risks — instead ✅ only real, discoverable risks
- ❌ Creating mandatory new risk tracking — instead ✅ make optional, use existing field
- ❌ Contradicting existing SESSION_CLOSEOUT guidance on scope — instead ✅ work within existing four-bullet pattern
- ❌ Suggesting risks should be surfaced in other ways — instead ✅ clarify this is session-specific status summary, not architecture tracking

---

**SYNTHESIS COMPLETE.** Awaiting approval to proceed with drafting.
```

---

### Step 2 — Read SESSION_CLOSEOUT.md § "1b" Current Guidance

Locate section "### 1b. Session agent: append one entry"

Current step 3 reads:
```
3. Under it, write four bullets and no sub-headings:
   - **Changed:** files touched, by path.
   - **Committed:** commit hashes, or "none".
   - **Not committed:** what is left uncommitted, or "none".
   - **Stop condition:** why you stopped, or "none" if the work is finished. Include the next action and who owns it.
```

---

### Step 3 — Add New Subsection

**Location**: Immediately after step 3 (after the four bullet descriptions), before step 4.

**New subsection**: Create section titled:
```
#### Guidance: Expressing Stop Conditions and Risks
```

**Content**: Provide guidance on three stop condition patterns with examples.

**Pattern 1: Normal Stop**
- 2-3 line guidance: Task paused at clear resume point, no blockers
- Example (4-6 lines):
  ```
  Stop condition: Task paused at schema migration step. Resume from `db/migrate/202610XX_add_column.rb` line 5. Next: test migration locally. Owner: next implementation session.
  ```

**Pattern 2: Blocked Stop**
- 2-3 line guidance: Task cannot proceed due to external blocker (missing dependency, waiting for approval, etc.)
- Include note on how to document the blocker (evidence of why blocked)
- Example (4-6 lines):
  ```
  Stop condition: Blocked on release of gem X v2.0. Blocker evidence: `Gemfile` currently locks v1.9, v2.0 not yet released (checked rubygems.org 2026-10-19). Cannot proceed without X v2.0 becoming available and security audit from owner.
  ```

**Pattern 3: Risk Stop**
- 2-3 line guidance: Task paused with known risk to flag. Use only if there's actual risk discovered in this session.
- Include: Risk condition, impact if risk occurs, risk level, mitigation approach
- Example (4-6 lines):
  ```
  Stop condition: Task paused. Known risk: endpoint refactor affects 12 API consumers. If consumers not notified before release, will cause breaking changes for downstream. Risk level: HIGH. Mitigation: create task for consumer notification before merge. Review recommended before: production deployment.
  ```

**Additional notes** (3-4 lines):
- "Risk fields are optional. Use only if there's a real risk to surface, not speculative concerns."
- "Risks here are session-specific (what one session discovered). Broader architectural risks belong in handoff files or task notes, not status.md."
- "Normal stops don't need risk fields — risk fields are for stops where there's actual risk to propagate to next session."

**Total addition**: ~25-30 lines (3 patterns × 6 lines each + 8 lines guidance/notes)

---

### Step 4 — Verify Clarity and Realism

1. **Clarity check**: Are the three patterns easy to distinguish? Can a new implementing agent understand when to use each?
2. **Realism check**: Are the examples realistic (not overly verbose, not too terse)? Do they match real scenarios?
3. **Consistency check**: Do examples use the same format and level of detail?
4. **Scope check**: Do guidance and examples stay within what SESSION_CLOSEOUT expects (session-specific observations)?

If any fail, revise before proceeding to Step 4.5.

---

### Step 4.5 — WAIT FOR REVIEWER ASSESSMENT AND HUMAN AUTHORIZATION

⚠️ **STOP. Do not commit, push, or complete task until both approvals are received.**

1. Submit your synthesis report to the review channel with:
   - Link to synthesis report file (saved in `projects/agent-tasks/summaries/`)
   - Summary of changes made to SESSION_CLOSEOUT.md
   - Request: Reviewer assessment and approval to proceed

2. Await reviewer response:
   - ✅ **GO** — Reviewer approved work. Proceed to Step 5.
   - 🔄 **REVISE** — Reviewer identified issues. Implement feedback, regenerate synthesis, resubmit for review.
   - 🛑 **STOP** — Reviewer identified blocking issue. Do not commit. Follow reviewer guidance.

3. Await human authorization:
   - Once reviewer issues GO, await explicit human authorization: "Proceed with commit/push/task completion"
   - Human may approve after reviewer GO, or may request additional review

4. Only proceed to Step 5 if you have received:
   - ✅ Reviewer GO (not REVISE, not STOP)
   - ✅ Human explicit authorization for commit/push/task completion

---

### Step 5 — Stage and Commit

```bash
git add SESSION_CLOSEOUT.md
git commit -m "docs: enhance stop condition guidance to surface blockers and risks explicitly"
git push
```

---

### Step 7 — Push Changes

```bash
git push
```

Verify all changes are pushed to origin/main:
```bash
git log origin/main -1 --oneline
```

---

### Step 7 — Move Task to Completed

```bash
git mv projects/agent-tasks/tasks/active/2026-10-19-MEDIUM-DOCUMENTATION-STOP-CONDITION-ENHANCEMENT.md \
       projects/agent-tasks/tasks/completed/2026-10/2026-10-19-MEDIUM-DOCUMENTATION-STOP-CONDITION-ENHANCEMENT.md
git add projects/agent-tasks/tasks/completed/2026-10/2026-10-19-MEDIUM-DOCUMENTATION-STOP-CONDITION-ENHANCEMENT.md
git commit -m "chore: complete stop condition enhancement documentation task"
git push
```

---

## Acceptance Criteria

- [ ] SESSION_CLOSEOUT.md § "1b" contains new subsection "Guidance: Expressing Stop Conditions and Risks"
- [ ] Subsection includes three stop condition patterns: Normal, Blocked, Risk
- [ ] Each pattern has 2-3 lines of guidance and 4-6 line realistic example
- [ ] Blocked pattern includes evidence reference (how to show what's blocking)
- [ ] Risk pattern includes: condition, impact, risk level, mitigation
- [ ] Guidance explicitly notes risk fields are optional (not mandatory)
- [ ] Guidance clarifies risks here are session-specific (not architecture-level)
- [ ] Examples are realistic and use plain language (not abstract)
- [ ] Total addition is ~25-30 lines (does not bloat section)
- [ ] No restructuring of existing "1b" steps; subsection is purely additive
- [ ] Reviewer issued GO (not REVISE or STOP)
- [ ] Human explicitly authorized commit/push/task completion
- [ ] Changes staged, committed, and pushed
- [ ] Task file moved to completed/ and pushed

---

## Stop Conditions — escalate immediately if:
- Unclear whether risk fields should be mandatory or optional
- Guidance contradicts existing SESSION_CLOSEOUT principles on scope
- Examples are too project-specific or unrealistic
- Unsure how to distinguish session-specific risk from architectural risk
- Concern that guidance will encourage over-documentation

---

## Progress / Resume

*Updated by session whenever it stops before task completion*

- **Done so far**: [placeholder]
- **Next step**: [placeholder]
- **Last verified fact**: [placeholder]
- **Owning session**: [placeholder]

---

## Dependencies

**Blocked by**: None (independent task)  
**Blocks**: None  
**Related tasks**: WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md (Change Set 4)

---

## Completion Report

*Filled in by implementing agent when task is complete*

**Completed by**: [agent name]  
**Completion date**: [YYYY-MM-DD]  
**Evidence basis**: Direct file modification and verification

### What was changed
- `SESSION_CLOSEOUT.md` § "1b" — Added ~25-30 lines with subsection "Guidance: Expressing Stop Conditions and Risks" covering three stop patterns (normal, blocked, risk) with realistic examples

### Issues discovered
[Any unexpected gaps or clarifications needed during implementation]

### Follow-up tasks needed
[Any new backlog items identified]

### Lessons learned
[What worked, what could be clearer in future documentation tasks]
