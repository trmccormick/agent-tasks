---
status: backlog
priority: HIGH
type: documentation
system_domain: OTHER
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-31-HIGH-DOCUMENTATION-ASSET-UI-D1-verify-asset-registry-mapping.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-31-HIGH-DOCUMENTATION-ASSET-UI-D1-verify-asset-registry-mapping.md \
         projects/galaxy_game/tasks/active/2026-08-31-HIGH-DOCUMENTATION-ASSET-UI-D1-verify-asset-registry-mapping.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-31-HIGH-DOCUMENTATION-ASSET-UI-D1-verify-asset-registry-mapping.md"
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

# TASK: D1 — Verify Asset Registry Mapping
**Status**: BACKLOG
**Priority**: HIGH
**Type**: documentation
**Created**: 2026-08-31
**Last Updated**: 2026-08-31

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS — all RSpec commands use the Docker wrapper.
- **MVP Alignment**: VALID — verification of first shared implementation ensures mapping integrity.
- **MVP Impact Note**: Verifies C1 against B1 and the canonical Visual Definition relationship.
- **Action Line**: NEEDS C1 COMPLETED BEFORE DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Requires code inspection and test verification against design.
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. `/Users/tam0013/Documents/git/agent-tasks/README.md`
2. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. This task file
4. **B1 approved design** — the design specification to verify against
5. **C1 completed** — the implementation to verify

---

## Context

Verify C1 against B1 and the canonical Visual Definition relationship. This is a verification-only task after the first shared implementation. Do not repair architecture silently. Report discrepancies and create follow-up recommendations only. Run focused tests and record exact results.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions.
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules.
- `TASK_TEMPLATE.md` — task lifecycle and agent contract.
- B1 approved design — the specification to verify against.

---

## Critical Information for This Task

### Credentials
None required.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Do not repair architecture silently.
- ❌ Wrong: Fix implementation discrepancies without reporting them.
- ✅ Right: Report every discrepancy between C1 implementation and B1 design; create follow-up recommendations only.
- Why: Verification must be honest — silent repairs hide architectural debt.

⚠️ **GOTCHA 2**: This is verification-only — no implementation changes.
- ❌ Wrong: Modify code to make it "look right" during verification.
- ✅ Right: Record pass/fail for each B1 acceptance criterion; report exact discrepancies.
- Why: The purpose of D1 is to validate C1, not to improve it.

### Multi-Domain / Multi-Tenant Routing (if applicable)
Not applicable — this is a verification task.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: D1 — Verify Asset Registry Mapping
**Status**: backlog → active → completed
**Date**: YYYY-MM-DD

### What I'm About to Do
Verify C1 implementation against B1 design and the canonical Visual Definition relationship. Inspect tests and code for field ownership and boundaries. Run focused verification. Report pass/fail for each B1 acceptance criterion. Verification only — no code changes.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| B1 approved design | Specification to verify against | reviewed |
| C1 implementation | Code to verify | reviewed |
| C1 tests | Test coverage evidence | reviewed |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Reviewed B1 design and C1 implementation
- ✅ Understand architecture gotchas above

### Expected Outcomes
Every B1 acceptance criterion verified with pass/fail; tests actually run and results recorded; no unapproved scope expansion; discrepancies reported honestly.

### Critical Gotchas I Will Avoid
- ❌ Repairing architecture silently — instead ✅ Reporting every discrepancy between C1 and B1
- ❌ Modifying code during verification — instead ✅ Recording pass/fail for each criterion only

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1.
```

---

## Problem Statement

Verify C1 against B1 and the canonical Visual Definition relationship.

**Current behavior**: C1 implements the B1 mapping; tests have been added.
**Expected behavior**: Verify every B1 acceptance criterion with pass/fail; run focused tests; report exact discrepancies without modifying code.

---

## Files Involved

### Primary Files — inspect or edit only as specified by this task
| File | Purpose | Key Method/Section |
|---|---|---|
| B1 approved design | Specification to verify against | N/A (review) |
| C1 implementation | Code to verify | N/A (inspect) |
| C1 tests | Test coverage evidence | N/A (inspect/run) |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `docs/new_agent/rules/DECISIONS.md` | Locked architectural decisions |
| `docs/new_agent/rules/GUARDRAILS.md` | Execution rules |

### Migration
- [x] No migration needed — verification only

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT. Do not proceed to Step 1 until both are done and approved.

All agents: follow these steps exactly in order. Do not skip steps or reorder them.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/current/2026-08-31-HIGH-DOCUMENTATION-ASSET-UI-D1-verify-asset-registry-mapping.md \
       projects/galaxy_game/tasks/active/2026-08-31-HIGH-DOCUMENTATION-ASSET-UI-D1-verify-asset-registry-mapping.md
```

Then open the moved file and change: `status: backlog → status: active`

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-31-HIGH-DOCUMENTATION-ASSET-UI-D1-verify-asset-registry-mapping.md"
```

**Paste the output in chat before proceeding.** Expected: exactly one result at `active/` path.

### Step 1 — Review B1 and C1

Compare implementation with approved design. Specifically:
- Does C1 implement exactly what B1 specifies (no additions, no omissions)?
- Are field mappings correct per B1?
- Are locked Visual Profiles/Render Templates untouched?

### Step 2 — Inspect tests and code

Verify field ownership and boundaries. Specifically:
- Do the tests cover all B1 acceptance criteria?
- Is there any code that modifies locked specs?
- Is there any docs/ runtime dependency introduced?

### Step 3 — Run focused verification

Use required project test commands:

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [SPEC_PATH] 2>&1 | tail -20'
```

Expected result: X examples, 0 failures

### Step 4 — Report

Pass/fail each B1 acceptance criterion. The report should include:
1. Which B1 criteria pass and which fail?
2. What are the exact discrepancies (if any)?
3. Do tests actually run with clean results?
4. Any follow-up recommendations (not implementations)?

---

## Acceptance Criteria
- [ ] Every B1 criterion verified with pass/fail
- [ ] Tests actually run and results recorded
- [ ] No unapproved scope expansion
- [ ] Discrepancies reported honestly (no silent repairs)
- [ ] Synthesis report posted to chat before any work began

---

## Stop Conditions — escalate to user immediately if:
- Shared/global regression found
- Implementation diverges materially from B1 design
- Tests reveal a contradiction in the mapping

---

## Commit Instructions
No commit is authorized by this task unless explicitly stated in the task steps. Do not commit verification findings without separate approval.

---

## Documentation
- [x] No doc changes needed unless this task explicitly requests documentation output.

---

## Dependencies
**Blocked by**: C1
**Blocks**: none
**Related tasks**: B1

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
Verification findings only — no code changes.

### Issues discovered
[Any discrepancies between C1 implementation and B1 design]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future verification tasks should know]

---

## Handoff Summary
HANDOFF SUMMARY: D1 | [result] | [next action]
