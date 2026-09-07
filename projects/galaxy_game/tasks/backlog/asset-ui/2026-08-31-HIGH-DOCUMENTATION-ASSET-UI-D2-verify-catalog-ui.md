---
status: backlog
priority: HIGH
type: documentation
system_domain: OTHER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-DOCUMENTATION-ASSET-UI-D2-verify-catalog-ui.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-DOCUMENTATION-ASSET-UI-D2-verify-catalog-ui.md \
         projects/galaxy_game/tasks/active/2026-08-31-HIGH-DOCUMENTATION-ASSET-UI-D2-verify-catalog-ui.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-31-HIGH-DOCUMENTATION-ASSET-UI-D2-verify-catalog-ui.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section is complete and accurate (no placeholders).
- [ ] All Step 0-N instructions are clear and actionable.
- [ ] Synthesis report template is provided and copy/paste ready.
- [ ] No placeholder text remains in Implementation Steps.
- [ ] Repository and task paths have been verified.
- [ ] Architecture Gotchas are specific (not generic).
- [ ] Acceptance Criteria are measurable.
- [ ] Dependencies and Blocked/Blocks relationships are clear.

---

# TASK: D2 — Verify Catalog UI
**Status**: BACKLOG
**Priority**: HIGH
**Type**: documentation
**Created**: 2026-08-31
**Last Updated**: 2026-08-31

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS — all RSpec commands use the Docker wrapper.
- **MVP Alignment**: AI_MANAGER_LUNA_SETTLEMENT — validates catalog contract across Unit and Component object classes.
- **MVP Impact Note**: Verifies RH-400 and I-beam catalog cases against B2 and the static asset roles.
- **Action Line**: NEEDS C3+C4 COMPLETED BEFORE DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Requires UI verification across two object classes.
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. `/Users/tam0013/Documents/git/agent-tasks/README.md`
2. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. This task file
4. **B2 approved design** — the catalog presentation contract to verify against
5. **C2 completed** — catalog data wiring
6. **C3 completed** — RH-400 catalog vertical slice
7. **C4 completed** — I-beam Component catalog case

---

## Context

Verify RH-400 and I-beam catalog cases against B2 and the static asset roles. This validates the catalog contract across Unit and Component object classes. Do not change UI during verification unless a separate fix task is approved. Record pass/fail and exact discrepancies for each case.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions.
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules.
- `TASK_TEMPLATE.md` — task lifecycle and agent contract.
- B2 approved design — the catalog presentation contract to verify against.

---

## Critical Information for This Task

### Credentials
None required.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Do not change UI during verification unless a separate fix task is approved.
- ❌ Wrong: Fix UI issues discovered during verification without a separate approved task.
- ✅ Right: Record exact discrepancies; create follow-up recommendations only.
- Why: Verification must be honest — fixing issues hides them from the record.

⚠️ **GOTCHA 2**: Catalog images must contain no baked UI text.
- ❌ Wrong: Accept generated images with baked-in text as correct.
- ✅ Right: Verify all text is rendered by HTML/UI, never baked into generated images.
- Why: Baked text cannot be localized, styled, or updated without regenerating images.

⚠️ **GOTCHA 3**: Static asset roles must remain distinct.
- ❌ Wrong: Accept catalog render being substituted with surface sprite (or vice versa).
- ✅ Right: Verify each asset file is used for its intended role only.
- Why: Catalog renders and surface sprites have different consumers and technical contracts.

### Multi-Domain / Multi-Tenant Routing (if applicable)
Not applicable — this is a verification task.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: D2 — Verify Catalog UI
**Status**: backlog → active → completed
**Date**: YYYY-MM-DD

### What I'm About to Do
Verify RH-400 and I-beam catalog cases against B2 and the static asset roles. Check image roles, data sections, and UI text for both cases. Record pass/fail and exact discrepancies. No UI changes during verification.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| B2 approved design | Catalog contract to verify against | reviewed |
| C2 wiring | Data assembly evidence | reviewed |
| C3 implementation | RH-400 catalog view | reviewed/inspected |
| C4 implementation | I-beam catalog view | reviewed/inspected |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Reviewed B2, C2, C3, C4
- ✅ Understand architecture gotchas above

### Expected Outcomes
RH-400 catalog case passes all B2 criteria; I-beam catalog case passes all B2 criteria; catalog images contain no baked UI text; static asset roles remain distinct; exact discrepancies recorded.

### Critical Gotchas I Will Avoid
- ❌ Changing UI during verification — instead ✅ Recording pass/fail and exact discrepancies only
- ❌ Accepting baked text in images — instead ✅ Verifying all text is HTML/UI rendered
- ❌ Accepting wrong asset substitution — instead ✅ Verifying catalog render vs. surface sprite roles

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1.
```

---

## Problem Statement

Verify RH-400 and I-beam catalog cases against B2 and the static asset roles.

**Current behavior**: C3 provides RH-400 catalog view; C4 provides I-beam Component catalog view.
**Expected behavior**: Verify both cases against B2 criteria; record pass/fail and exact discrepancies; no UI changes.

---

## Files Involved

### Primary Files — inspect or edit only as specified by this task
| File | Purpose | Key Method/Section |
|---|---|---|
| B2 approved design | Catalog contract to verify against | N/A (review) |
| C3 implementation | RH-400 catalog view | N/A (inspect) |
| C4 implementation | I-beam catalog view | N/A (inspect) |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| A2 findings | Catalog render/icon paths for verification |
| A3 findings | RH-400 asset family mapping |
| `docs/new_agent/rules/DECISIONS.md` | Locked architectural decisions |

### Migration
- [x] No migration needed — verification only

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT. Do not proceed to Step 1 until both are done and approved.

All agents: follow these steps exactly in order. Do not skip steps or reorder them.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-DOCUMENTATION-ASSET-UI-D2-verify-catalog-ui.md \
       projects/galaxy_game/tasks/active/2026-08-31-HIGH-DOCUMENTATION-ASSET-UI-D2-verify-catalog-ui.md
```

Then open the moved file and change: `status: backlog → status: active`

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-31-HIGH-DOCUMENTATION-ASSET-UI-D2-verify-catalog-ui.md"
```

**Paste the output in chat before proceeding.** Expected: exactly one result at `active/` path.

### Step 1 — Review B2, C2, C3, C4

Establish expected behavior for both object classes. Specifically:
- What does B2 require for Units (RH-400)?
- What does B2 require for Components (I-beam)?
- What data does C2 wire for each?
- How does C3 implement the RH-400 view?
- How does C4 implement the I-beam view?

### Step 2 — Verify RH-400

Check image roles, data sections, and UI text. Specifically:
- Is the catalog render displayed (not surface sprite)?
- Is the inventory icon represented where required?
- Are Blueprint/Operational Data/Visual sections correct per B2?
- Is any text baked into generated images?
- Does the view pass all B2 acceptance criteria?

### Step 3 — Verify I-beam

Check Component behavior and absence of fake Operational Data. Specifically:
- Does the I-beam render through the catalog pattern?
- Is Operational Data absent (not empty/fake)?
- Are Blueprint + Visual sections correct per B2?
- Is the shared presentation compatible with RH-400?
- Does the view pass all B2 acceptance criteria?

### Step 4 — Report

Record pass/fail and exact discrepancies for each case. The report should include:
1. RH-400: which B2 criteria pass, which fail, exact discrepancies
2. I-beam: which B2 criteria pass, which fail, exact discrepancies
3. Any static asset role violations (catalog render vs. surface sprite)
4. Any baked text violations
5. Follow-up recommendations (not implementations)

---

## Acceptance Criteria
- [ ] RH-400 catalog case passes all B2 criteria
- [ ] I-beam catalog case passes all B2 criteria
- [ ] Catalog images contain no baked UI text
- [ ] Static asset roles remain distinct (catalog render vs. surface sprite)
- [ ] Exact discrepancies recorded for each case
- [ ] Synthesis report posted to chat before any work began

---

## Stop Conditions — escalate to user immediately if:
- Visual regression or architectural mismatch requires redesign
- Missing canonical asset blocks verification
- C3/C4 implementations cannot be located or inspected

---

## Commit Instructions
No commit is authorized by this task unless explicitly stated in the task steps. Do not commit verification findings without separate approval.

---

## Documentation
- [x] No doc changes needed unless this task explicitly requests documentation output.

---

## Dependencies
**Blocked by**: C3, C4
**Blocks**: none
**Related tasks**: B2

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
Verification findings only — no code changes.

### Issues discovered
[Any discrepancies between C3/C4 implementations and B2 design]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future verification tasks should know]

---

## Handoff Summary
HANDOFF SUMMARY: D2 | [result] | [next action]
