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
Task: <agent-tasks>/projects/agent-tasks/tasks/backlog/2026-10-19-MEDIUM-DOCUMENTATION-REVIEW-ARTIFACT-STANDARDIZATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/agent-tasks/tasks/backlog/2026-10-19-MEDIUM-DOCUMENTATION-REVIEW-ARTIFACT-STANDARDIZATION.md \
         projects/agent-tasks/tasks/active/2026-10-19-MEDIUM-DOCUMENTATION-REVIEW-ARTIFACT-STANDARDIZATION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of the git mv command in chat.
  Do NOT read the task file content until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find projects/agent-tasks/tasks -name "2026-10-19-MEDIUM-DOCUMENTATION-REVIEW-*.md"
    Only ONE result should exist. Paste this output before proceeding.

READ FIRST (after Step 0): WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md contains full scope and context.

CRITICAL: Save synthesis report as MD file to projects/agent-tasks/summaries/ BEFORE starting changes.
  Filename: YYYY-MM-DD-SYNTHESIS-REVIEW-ARTIFACT-STANDARDIZATION.md
  Chat is for questions only — full synthesis goes in summaries folder.

Closeout: Follow SESSION_CLOSEOUT.md when complete.
```

---

# TASK: Standardize Review Agent Artifacts and Handoff Guidance

**Status**: BACKLOG  
**Priority**: MEDIUM  
**Type**: documentation  
**Created**: 2026-10-19  
**Last Updated**: 2026-10-19  

---

## Agent Assignment (Human-filled)

**Assigned To**: Qwen local via Copilot (primary)  
**Why This Agent**: Documentation writing, multi-file coordination  
**Supervision Level**: Standard (well-specified documentation task)

---

## Prerequisites — READ FIRST (Sequential Order)

1. `WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md` — Change Set 3 specification
2. `REVIEW_AGENT_WORKFLOW.md` — current review procedure (section 4 to be expanded)
3. `SESSION_CLOSEOUT.md` — handoff requirements (section to be clarified)
4. This task file — acceptance criteria and implementation guidance

---

## Context

Review agents currently create review artifacts but lack standardized structure guidance. SESSION_CLOSEOUT.md says "research, implementation, and web agents" must create handoff files but doesn't explicitly include review sessions, creating confusion about review session continuity.

This task standardizes:
1. **Review artifact structure** in REVIEW_AGENT_WORKFLOW.md § "4. Save one concise review artifact" (add format guidance + example)
2. **Review handoff requirement** in SESSION_CLOSEOUT.md § "Handoff Files (required)" (clarify review sessions must create handoffs too)
3. **Optional template file** REVIEW_REPORT_TEMPLATE.md (copy-paste reference for review agents)

All changes are additive and clarify existing intent without restructuring workflow.

**Architecture Context** — Read before starting:
- `SYSTEM_INTENT.md` § "Handoff chaining" — why consistency in artifacts matters
- `PLANNING_AGENT_CLOSEOUT.md` § "6. Create handoff file" — example of how handoff files enable continuity

---

## Problem Statement

**Current state:**
- REVIEW_AGENT_WORKFLOW.md requires "one concise review artifact" but doesn't specify structure
- SESSION_CLOSEOUT.md lists "research, implementation, and web agents" for handoff requirement; review agents not explicitly mentioned
- Review agents lack clear guidance on what a review artifact should contain
- Planning agents may miss review findings because handoff expectations are unclear

**Impact:**
- Inconsistent review output formats make it hard for planning agents to consume findings
- Unclear whether review sessions need handoff files; some review agents may not create them
- Opportunity to improve review session continuity at low cost

**Goal:**
Standardize review artifact structure, clarify handoff requirements for review sessions, and provide optional template.

---

## Files Involved

### Primary Files — you will edit these

| File | Purpose | Section to Edit |
|---|---|---|
| `REVIEW_AGENT_WORKFLOW.md` | Review procedure | After step 4, add artifact structure guidance |
| `SESSION_CLOSEOUT.md` | Closeout procedure for all sessions | Handoff requirement section, add review session clarification |

### Optional new file — create if specified

| File | Purpose |
|---|---|
| `REVIEW_REPORT_TEMPLATE.md` | Copy-paste template for review agents (optional but recommended) |

### Reference Files — read but do not edit

| File | Why You Need It |
|---|---|
| `WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md` | Change Set 3 — detailed spec |
| `REVIEW_AGENT_GUIDE.md` | Evidence standards and review principles (reference) |
| `PLANNING_AGENT_CLOSEOUT.md` § "6. Create handoff file" | Handoff pattern to follow for consistency |

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
# From inside agent-tasks repo root:
git mv projects/agent-tasks/tasks/backlog/2026-10-19-MEDIUM-DOCUMENTATION-REVIEW-ARTIFACT-STANDARDIZATION.md \
       projects/agent-tasks/tasks/active/2026-10-19-MEDIUM-DOCUMENTATION-REVIEW-ARTIFACT-STANDARDIZATION.md
```

Then open the moved file and change the YAML status field from `backlog` to `active`.

Then verify only one copy exists:
```bash
find projects/agent-tasks/tasks -name "2026-10-19-MEDIUM-DOCUMENTATION-REVIEW-*.md"
```

**Paste the output of the find command in chat before proceeding.**

---

### Step 1 — Synthesis Report (Before Making Any Changes)

**Save as**: `projects/agent-tasks/summaries/2026-10-19-SYNTHESIS-REVIEW-ARTIFACT-STANDARDIZATION.md`

**Template**:
```markdown
## SYNTHESIS REPORT: Review Artifact Standardization

**Task**: 2026-10-19-MEDIUM-DOCUMENTATION-REVIEW-ARTIFACT-STANDARDIZATION.md
**Status**: SYNTHESIS IN PROGRESS
**Date**: 2026-10-19

### What I'm About to Do
Standardize review agent output by:
1. Adding artifact structure guidance to REVIEW_AGENT_WORKFLOW.md § "4. Save one concise review artifact"
2. Clarifying that review sessions must create handoff files (SESSION_CLOSEOUT.md § "Handoff Files (required)")
3. Optionally creating REVIEW_REPORT_TEMPLATE.md as copy-paste reference

### Files I'll Examine/Modify
| File | Purpose | Status |
|---|---|---|
| `REVIEW_AGENT_WORKFLOW.md` | Primary target | not started |
| `SESSION_CLOSEOUT.md` | Secondary target | not started |
| `REVIEW_REPORT_TEMPLATE.md` | Optional new file | not started |
| `WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md` § "Change Set 3" | Detailed spec | reference |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv
- ✅ YAML status updated from backlog → active
- ✅ Read WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md Change Set 3
- ✅ Read REVIEW_AGENT_WORKFLOW.md current structure
- ✅ Read SESSION_CLOSEOUT.md § "Handoff Files (required)"
- ✅ Understand this task file

### Expected Outcomes
- REVIEW_AGENT_WORKFLOW.md § "4" has expanded artifact structure guidance
- Guidance includes mandatory sections: Assignment, Evidence Basis, Findings, Open Concerns (optional), Disposition, Rationale, Recommendations (optional)
- Example review artifact (~15-20 lines) showing realistic content
- SESSION_CLOSEOUT.md § "Handoff Files (required)" explicitly includes review sessions
- Optional REVIEW_REPORT_TEMPLATE.md created as standalone reference
- Total additions: ~40 lines across files

### Critical Gotchas I Will Avoid
- ❌ Repeating full evidence standards from REVIEW_AGENT_GUIDE — instead ✅ reference and link
- ❌ Making review artifacts longer than "concise" — instead ✅ keep to 1-2 pages max
- ❌ Contradicting existing REVIEW_AGENT_WORKFLOW.md guidance — instead ✅ expand what's already there
- ❌ Creating duplicate information between artifact and handoff — instead ✅ clarify relationship (artifact is review output; handoff is session record for next agent)

---

**SYNTHESIS COMPLETE.** Awaiting approval to proceed with drafting.
```

---

### Step 2 — Modify REVIEW_AGENT_WORKFLOW.md § "4. Save one concise review artifact"

**Location**: After the current text "For sessions without filesystem access, provide the artifact text for an authorized local session to save; do not claim it was saved."

**Add new subsection** (after that paragraph) titled:
```
### Standard Review Artifact Format
```

Content should specify:
- **Where to save**: `projects/[PROJECT]/summaries/YYYY-MM-DD-REVIEW-[TOPIC].md`
- **Required sections**:
  1. **Assignment** (1 sentence: what reviewed, what decision requested)
  2. **Evidence Basis** (which files reviewed, which commands/searches run; label evidence as VERIFIED/REPORTED/ASSUMED)
  3. **Findings** (2-5 key issues, each with evidence reference)
  4. **Open Concerns** (optional; unresolved questions or edge cases)
  5. **Disposition** (PROCEED | PROCEED WITH CAUTION | STOP — must choose one)
  6. **Rationale** (1 paragraph explaining why)
  7. **Recommendations** (optional; future work or decisions for human/planning agent)
- **Length**: 1-2 pages (concise, not comprehensive audit)
- **Format**: Markdown

Provide a **concrete example** (15-20 lines) showing realistic review artifact with proper sections and evidence labeling.

Keep total addition to ~12 lines of guidance + 20 line example.

---

### Step 3 — Modify SESSION_CLOSEOUT.md § "Handoff Files (required)"

**Location**: First sentence and file location section

**Change 1**: First sentence
```
Current: "Every research, implementation, and web agent session MUST create a formal handoff file..."
New:     "Every research, implementation, web, and review agent session MUST create a formal handoff file..."
```

**Change 2**: After the "File location:" line showing paths for agent/date/type, add one clarifying line:
```
(Review sessions also create handoffs. Save as: handoffs/<agent>/<date>-REVIEW-[TOPIC]-SESSION.md)
```

That's all for this file — keep it minimal.

---

### Step 4 — Create Optional REVIEW_REPORT_TEMPLATE.md

**New file location**: `REVIEW_REPORT_TEMPLATE.md` (root of agent-tasks, same level as REVIEW_AGENT_GUIDE.md)

**Content**: Standalone copy-paste template for review agents

**Sections**:
- Title: "Review Report Template — Copy and Customize"
- Brief note on when to use
- Template with placeholders (8 sections matching Step 2 above)
- Embedded example (same as used in Step 2 above) showing how to fill it
- Notes on optional sections
- Reference to REVIEW_AGENT_WORKFLOW.md for full guidance

**Keep it short**: <50 lines total with example

---

### Step 5 — Verify Clarity and Consistency

1. **Consistency check**: Do REVIEW_AGENT_WORKFLOW.md and SESSION_CLOSEOUT.md changes align?
2. **Clarity check**: Are artifact sections clear? Can a new review agent understand what to do?
3. **Example check**: Is the example realistic? Does it show evidence labeling properly?
4. **Reference check**: Do all three files (if template created) point to each other appropriately?

If any fail, revise before proceeding to Step 6.

---

### Step 6 — Stage and Commit

```bash
# Changes to existing files
git add REVIEW_AGENT_WORKFLOW.md SESSION_CLOSEOUT.md

# New template file (if created)
git add REVIEW_REPORT_TEMPLATE.md

# Single commit for all related changes
git commit -m "docs: standardize review artifact structure and clarify handoff requirements for review sessions"
git push
```

---

### Step 7 — Move Task to Completed

```bash
git mv projects/agent-tasks/tasks/active/2026-10-19-MEDIUM-DOCUMENTATION-REVIEW-ARTIFACT-STANDARDIZATION.md \
       projects/agent-tasks/tasks/completed/2026-10/2026-10-19-MEDIUM-DOCUMENTATION-REVIEW-ARTIFACT-STANDARDIZATION.md
git add projects/agent-tasks/tasks/completed/2026-10/2026-10-19-MEDIUM-DOCUMENTATION-REVIEW-ARTIFACT-STANDARDIZATION.md
git commit -m "chore: complete review artifact standardization documentation task"
git push
```

---

## Acceptance Criteria

- [ ] REVIEW_AGENT_WORKFLOW.md § "4" contains new "Standard Review Artifact Format" subsection
- [ ] Subsection specifies 7 required/optional sections (Assignment through Recommendations)
- [ ] Example review artifact is realistic and ~15-20 lines
- [ ] Example shows proper evidence labeling (at least 2 of 3 types)
- [ ] Disposition choices (PROCEED | PROCEED WITH CAUTION | STOP) are clearly stated as mandatory
- [ ] Length guidance ("1-2 pages, concise") is present
- [ ] SESSION_CLOSEOUT.md § "Handoff Files (required)" explicitly includes review sessions
- [ ] Review handoff path is documented (handoffs/<agent>/<date>-REVIEW-[TOPIC]-SESSION.md)
- [ ] Optional REVIEW_REPORT_TEMPLATE.md is created with copy-paste template
- [ ] All changes staged, committed, and pushed
- [ ] Task file moved to completed/ and pushed

---

## Stop Conditions — escalate immediately if:
- Artifact structure contradicts existing REVIEW_AGENT_WORKFLOW.md guidance
- Unclear whether disposition should include other choices beyond PROCEED/CAUTION/STOP
- Example is too specific to one project (should be generic)
- Unsure about relationship between review artifact and handoff file
- Review-session handoff path already documented elsewhere (avoid duplication)

---

## Progress / Resume

*Updated by session whenever it stops before task completion*

- **Done so far**: [placeholder]
- **Next step**: [placeholder]
- **Last verified fact**: [placeholder]
- **Owning session**: [placeholder]

---

## Dependencies

**Blocked by**: 2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-AND-ROUTING-FRAMEWORK.md (optional; helpful to have evidence framework in place, but this task can proceed independently)  
**Blocks**: None  
**Related tasks**: WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md (Change Set 3)

---

## Completion Report

*Filled in by implementing agent when task is complete*

**Completed by**: [agent name]  
**Completion date**: [YYYY-MM-DD]  
**Evidence basis**: Direct file modification and verification

### What was changed
- `REVIEW_AGENT_WORKFLOW.md` — Added ~35 lines covering review artifact structure (7 sections with example)
- `SESSION_CLOSEOUT.md` — Added ~2 lines clarifying review sessions must create handoffs
- `REVIEW_REPORT_TEMPLATE.md` — Created new ~40-line copy-paste template for review agents

### Issues discovered
[Any unexpected gaps or clarifications needed during implementation]

### Follow-up tasks needed
[Any new backlog items identified]

### Lessons learned
[What worked, what could be clearer in future documentation tasks]
