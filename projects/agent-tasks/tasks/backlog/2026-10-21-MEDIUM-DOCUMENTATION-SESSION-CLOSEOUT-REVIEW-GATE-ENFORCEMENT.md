---
status: backlog
resume: false
priority: MEDIUM
type: documentation
system_domain: WORKFLOW_GOVERNANCE
mvp_alignment: AGENT_SESSION_WORKFLOW_ENFORCEMENT
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

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**

```
You are **Review Agent**.

Project: agent-tasks
Task: <agent-tasks>/projects/agent-tasks/tasks/backlog/2026-10-21-MEDIUM-DOCUMENTATION-SESSION-CLOSEOUT-REVIEW-GATE-ENFORCEMENT.md

STEP 0 — Move task file (no exceptions):
  git mv projects/agent-tasks/tasks/backlog/2026-10-21-MEDIUM-DOCUMENTATION-SESSION-CLOSEOUT-REVIEW-GATE-ENFORCEMENT.md \
         projects/agent-tasks/tasks/active/2026-10-21-MEDIUM-DOCUMENTATION-SESSION-CLOSEOUT-REVIEW-GATE-ENFORCEMENT.md
  Then open the moved file and change: status: backlog → status: active
  Paste both command outputs in chat before proceeding.

LIFECYCLE: backlog → active → completed
  Verify with: find projects/agent-tasks/tasks -name "2026-10-21-MEDIUM-DOCUMENTATION-SESSION-CLOSEOUT-REVIEW-GATE-ENFORCEMENT.md"
  Only ONE result should exist at active/. Paste before proceeding.

READ FIRST (after Step 0): Task file contains complete background, proposed changes, and acceptance criteria.

CRITICAL: Save synthesis report to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/agent-tasks/summaries/
  Filename pattern: YYYY-MM-DD-REVIEW-[DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat.

Closeout: follow SESSION_CLOSEOUT.md only.
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

---

# TASK: SESSION_CLOSEOUT.md Review Gate Enforcement

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: documentation
**Created**: 2026-10-21
**Last Updated**: 2026-10-21

## Background

### Finding Source
Qwen Implementation Agent session (2026-10-21) for Galaxy Game UI Dashboard Task 2.

**What worked:**
- ✅ Task file lifecycle management (backlog → active)
- ✅ Synthesis report creation
- ✅ Implementation of acceptance criteria (all 9 verified)
- ✅ Handoff file creation with complete documentation
- ✅ Self-detection of workflow violation after human correction

**What failed:**
- ❌ Review gate enforcement
- ❌ Attempted `git commit` before reviewer disposition
- ❌ Attempted `git mv` (task lifecycle finalization) before human authorization
- ❌ Attempted to update status.md with completion status prematurely
- ❌ Continued attempting closeout actions after first correction

### Root Cause Analysis

**The documents are correct. The problem is not the rules — it is the mental model.**

**Current SESSION_CLOSEOUT.md language (Review Gate Pre-Condition section):**
> "Before proceeding with the steps below, you MUST satisfy the review gate..."
> "Before you commit, check for approval..."

This creates a mental model of: **"Follow this rule while you're doing things."**

**Result:** Agent naturally completes the full closeout cycle (commit, move files, update status) and only checks the gate *during* those actions.

**Proposed mental model:** **"Your work is done. Stop. Wait for authorization."**

This requires a clear, explicit STOP point that signals:
- Implementation work has ended
- New phase begins (review + authorization)
- No filesystem operations are permitted until both conditions are met

### Example: Current vs. Proposed Structure

**Current (pre-condition model):**
```
Session Work → Complete Implementation → Create Handoff
                                            ↓
                                      Continue with:
                                      - git commit
                                      - task lifecycle (mv active/ → completed/)
                                      - BUT FIRST check: "have I got approval?"
```

**Proposed (explicit stop model):**
```
Session Work → Complete Implementation → Create Handoff
                                            ↓
                                    🛑 STOP HERE 🛑
                                    Do not proceed.
                                            ↓
                                    Wait for:
                                    1. Reviewer disposition (GO/REVISE/STOP)
                                    2. Human authorization
                                            ↓
                                    Only after BOTH: Continue with closeout
```

---

## Task Scope

**Objective:** Update workflow governance documents to express the review gate as an explicit execution STOP rather than a pre-condition, preventing implementation agents from attempting lifecycle changes before authorization.

**Deliverables:**

1. **SESSION_CLOSEOUT.md** — Add explicit STOP section after "Review Gate Pre-Condition"
   - Position: Between "Review Gate Pre-Condition" section and "0. Scope" section
   - Purpose: Clarify that review gate is a session boundary, not an approval check during closeout
   - Content: See "Proposed Changes" below

2. **IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md** — Add explicit STOP section
   - Position: New Section 0b, immediately after "Scope" (Section 0a)
   - Purpose: Make agent session termination explicit and mandatory
   - Content: See "Proposed Changes" below

3. **Optional: PLANNING_AGENT_CLOSEOUT.md** — Review for similar clarification
   - Planning agents do not use the review gate (different workflow)
   - However, they may benefit from similar STOP language when handing off to other agents
   - Recommendation: Read, assess, propose if warranted. Not required for this task.

---

## Proposed Changes

### Change 1: SESSION_CLOSEOUT.md — New Section

**Location:** Immediately after "Review Gate Pre-Condition" section (after line ~130)

**Insert:**

```markdown
---

## ⚠️ MANDATORY STOP — Review Gate Execution Halt

**This section applies to: Research agents, Implementation agents, Web agents, and Review agents that create implementation artifacts.**

**Planning agents skip this section.** See `PLANNING_AGENT_CLOSEOUT.md` instead.

### After Handoff Creation: Your Session Ends

When you have completed:
- Synthesis report (saved to `projects/<project>/summaries/`)
- Handoff file (saved to `projects/<project>/handoffs/<agent>/<date>-TYPE-SESSION.md`)

**YOUR SESSION IS COMPLETE.**

### What You MUST Do Now

**STOP.** Do not proceed with any of the following until you receive BOTH:
1. **Reviewer disposition**: GO / REVISE / STOP (from reviewer or human)
2. **Human authorization**: Explicit approval to finalize closeout

### Forbidden Until Authorization

You CANNOT execute any of these:
- `git commit` (any commits)
- `git push` (any pushes)
- `git mv` (task lifecycle moves, e.g., active/ → completed/)
- Update task file YAML: set `status: completed`
- Update `status.md` with completion or resolution language
- Move, copy, rename, or delete any files
- Perform cleanup activities
- Create additional artifacts or commits

### Required Output: Session Completion Report

Output exactly this format:

```
IMPLEMENTATION COMPLETE.

Handoff: projects/<project>/handoffs/<agent>/<date>-TYPE-SESSION.md
Synthesis: projects/<project>/summaries/<date>-SYNTHESIS.md

Status: Awaiting reviewer disposition (GO / REVISE / STOP).

END OF SESSION.
```

### What Happens Next

- **REVIEWER ASSESSMENT** (first): Reviewer reads synthesis, handoff, and code. Reviews for correctness, completeness, and alignment.
  - GO: Work is acceptable. Proceed to human authorization.
  - REVISE: Issues found. Return to this session for corrections.
  - STOP: Blocking issue. Await human guidance.

- **HUMAN AUTHORIZATION** (second, after reviewer GO): Human reviews the same artifacts and decides whether to finalize.
  - Approve: Proceed with closeout (Sections 1-4 of SESSION_CLOSEOUT.md).
  - Request revisions: Return to session.
  - Hold: Keep in active/, pending other work.

- **AFTER BOTH GO + AUTHORIZATION**: Only then proceed to Section 1 of SESSION_CLOSEOUT.md for lifecycle finalization.

### Why This Matters

The review gate is not just a check-before-commit rule. It is the **boundary between implementation and authority**. Implementation agents create work; reviewers and humans authorize it. Mixing these responsibilities causes violations and prevents proper review.

This stop point makes that boundary explicit.

---
```

### Change 2: IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md — New Section 0b

**Location:** Immediately after "Scope" section (Section 0), before Section 1

**Current Section 0 ends at:** "Do not write "complete" / "closed out" in status if a **review is still pending**."

**Insert new Section 0b:**

```markdown

---

## 0b. 🛑 MANDATORY HALT — Session End After Handoff

**This applies to ALL implementation sessions.**

### You Have Completed Implementation Work

When you have saved:
- Synthesis report to `projects/<project>/summaries/YYYY-MM-DD-IMPLEMENTATION-[DESCRIPTION].md`
- Handoff file to `projects/<project>/handoffs/implementation/YYYY-MM-DD-IMPLEMENTATION-[TASK-NAME].md`

**Your implementation session is OVER.**

### DO NOT PROCEED

**STOP HERE. Do not execute any of Section 1, 2, or beyond.**

Do not:
- `git commit` (any commits, even partial)
- `git push`
- `git mv` (do not move tasks from active/ to completed/)
- Update task file YAML (`status: completed`, `resume:`, etc.)
- Update `status.md`
- Move, delete, or rename files
- Create additional commits or cleanup

### Required Output

Report exactly:

```
IMPLEMENTATION COMPLETE.

Task: <relative path to task file in active/>
Handoff: projects/<project>/handoffs/implementation/<date>-IMPLEMENTATION-<name>.md
Synthesis: projects/<project>/summaries/<date>-IMPLEMENTATION-<description>.md

Status: Awaiting reviewer disposition.

END OF IMPLEMENTATION SESSION.
```

### What Happens Next

Reviewer will assess your work. You will receive:
1. **Reviewer disposition** (GO / REVISE / STOP)
2. **Human authorization** (after reviewer GO)

**Only after both are received**, proceed to Section 1 of this document.

If reviewer says REVISE, you may be recalled to this same session for corrections. Leave your work as-is until then.

---
```

---

## Architecture Gotchas

**GOTCHA 1: This is NOT about rule violations.**
- The Qwen agent was not "breaking the rules" — it was following the implicit mental model of "proceed with these steps, checking approval before each one"
- The fix is not to blame agents, but to make the workflow boundary explicit
- Other agents may have the same pattern; this prevents it

**GOTCHA 2: The stop point must come BEFORE lifecycle changes.**
- Current documents list the review gate *within* the task lifecycle section
- This positions the gate as "check before moving to completed"
- Instead, gate should prevent agents from ever reaching that section
- Restructuring signals: "Session end happens here, lifecycle finalization is authorized-only later"

**GOTCHA 3: Synthesis + handoff are delivery artifacts, not authorization.**
- Creating them means "here is what I made" — not "I'm ready to commit"
- Distinction is critical: delivery ≠ authorization
- Stop point clarifies this boundary

---

## Acceptance Criteria

**Synthesis report must confirm:**

1. ✅ **Both documents updated**: SESSION_CLOSEOUT.md and IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md have new explicit STOP sections
2. ✅ **Stop point positioning**: STOP sections appear *before* any lifecycle operations (before Section 1 in both files)
3. ✅ **Forbidden operations listed**: Each STOP section explicitly lists `git commit`, `git push`, `git mv`, and `status.md` updates as forbidden
4. ✅ **Required output template**: Both sections include the exact output template agents must use to report completion
5. ✅ **Clarified authority boundary**: New language clearly distinguishes delivery (synthesis + handoff = agent work) from authorization (reviewer GO + human approval = session boundary)
6. ✅ **Mental model shift documented**: Explicit language that gate is "session end" not "rule check"
7. ✅ **PLANNING_AGENT_CLOSEOUT.md assessed**: Reviewer should read this file and note whether similar clarification is warranted (optional deliverable, document recommendation in synthesis)

**Verification:**

Reviewer will:
- Read both updated sections
- Confirm that the stop point prevents agents from proceeding to lifecycle finalization without authorization
- Verify that the required output template is clear and actionable
- Assess whether the language resolves the mental model issue observed in Qwen session

---

## Implementation Notes

### Style & Tone
- Use imperative language: "STOP", "Do not proceed", "MANDATORY HALT"
- Avoid soft language: not "should", not "please consider", not "it is recommended"
- Reference the Qwen session finding but frame as workflow improvement, not agent failure

### Integration with Existing Text
- Do NOT remove or modify existing review gate section
- Do NOT contradict existing rules (the gate already exists; this makes it explicit)
- Sections should cross-reference: new STOP sections should link back to existing gate documentation
- Maintain consistent formatting with existing sections (same header style, same markdown conventions)

### Length Consideration
- Keep each new section under 200 words (concise, scannable by agents)
- Use lists and tables for clarity
- Use code blocks for required output template (exact formatting matters)

---

## Blocked/Blocks

- **Blocks:** No tasks are blocked by this one. It is a documentation improvement.
- **Blocked by:** None. Can proceed immediately.
- **Depends on:** None.
- **Related:** 
  - SESSION_CLOSEOUT.md (modified)
  - IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md (modified)
  - Galaxy Game UI Task 2 session (finding source, for reference)

---

## Next Action

After review and approval:
1. Human authorizes changes
2. Changes are committed to agent-tasks repository
3. New sections become effective for all subsequent implementation agent sessions
4. Consider notifying implementation agents (via dispatch interfaces) of the updated workflow if they are currently active

---

## Synthesis Report Template

When this task reaches review, create a report at:
```
projects/agent-tasks/summaries/2026-10-21-REVIEW-SESSION-CLOSEOUT-ENFORCEMENT.md
```

**Template sections:**

1. **Task Understanding**: What changes were requested? Why? (Reference Qwen session finding)
2. **Changes Made**: Line-by-line summary of edits to both files
3. **Mental Model Shift**: How does the new language change agent behavior? 
4. **Verification**: Confirm all acceptance criteria met
5. **Optional: PLANNING_AGENT_CLOSEOUT.md Assessment**: Should similar changes be made? (yes/no + reasoning)
6. **Ready for Approval**: Yes / Revision Needed / Blocked

---

## Stop Condition

Task will be complete when:
- ✅ SESSION_CLOSEOUT.md has explicit STOP section with required output format
- ✅ IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md has Section 0b STOP with required output format  
- ✅ Synthesis report confirms all acceptance criteria met
- ✅ Reviewer has approved changes
- ✅ Human has authorized finalization

**Not a blocker:** PLANNING_AGENT_CLOSEOUT.md assessment is optional. If review agent recommends changes to that file, create a follow-up task.
