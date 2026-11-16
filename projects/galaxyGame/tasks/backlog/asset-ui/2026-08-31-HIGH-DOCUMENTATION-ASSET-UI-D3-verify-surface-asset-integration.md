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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-DOCUMENTATION-ASSET-UI-D3-verify-surface-asset-integration.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-DOCUMENTATION-ASSET-UI-D3-verify-surface-asset-integration.md \
         projects/galaxy_game/tasks/active/2026-08-31-HIGH-DOCUMENTATION-ASSET-UI-D3-verify-surface-asset-integration.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-31-HIGH-DOCUMENTATION-ASSET-UI-D3-verify-surface-asset-integration.md"
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

# TASK: D3 — Verify Surface Asset Integration
**Status**: BACKLOG
**Priority**: HIGH
**Type**: documentation
**Created**: 2026-08-31
**Last Updated**: 2026-08-31

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS — all RSpec commands use the Docker wrapper.
- **MVP Alignment**: AI_MANAGER_LUNA_SETTLEMENT — verifies surface asset integration against B3 contract.
- **MVP Impact Note**: Verifies C5 against B3 and the existing surface renderer behavior.
- **Action Line**: NEEDS C5 COMPLETED BEFORE DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Requires surface rendering verification and asset integrity checks.
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. `/Users/tam0013/Documents/git/agent-tasks/README.md`
2. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. This task file
4. **B3 approved design** — the surface asset representation contract to verify against
5. **C5 completed** — the surface asset integration implementation

---

## Context

Verify C5 against B3 and the existing surface renderer behavior. This confirms the surface asset path without coupling surface rendering to catalog presentation. Do not modify generated assets during verification. Record pass/fail and any follow-up work. Run focused surface-layer checks where available.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions.
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules.
- `TASK_TEMPLATE.md` — task lifecycle and agent contract.
- B3 approved design — the surface asset representation contract to verify against.

---

## Critical Information for This Task

### Credentials
None required.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Do not modify generated assets during verification.
- ❌ Wrong: Regenerate or alter surface sprites to make them "look right" during verification.
- ✅ Right: Verify the existing asset as-is; report format/orientation issues without modifying them.
- Why: Verification must be honest — modifying assets hides integration problems.

⚠️ **GOTCHA 2**: Catalog assets must remain separate from surface assets.
- ❌ Wrong: Accept that catalog render/icon files were modified or substituted during C5 implementation.
- ✅ Right: Verify catalog assets are untouched; surface integration uses separate paths only.
- Why: Catalog and surface have different consumers, different technical contracts, and different update cycles.

⚠️ **GOTCHA 3**: Surface renderer may require a broader asset architecture.
- ❌ Wrong: Accept surface rendering as "working" if it only works for RH-400 with hardcoded paths.
- ✅ Right: Report whether the integration is narrow (RH-400-specific) or generalizable; flag broader needs.
- Why: This verification informs whether C5's approach scales to other asset families.

### Multi-Domain / Multi-Tenant Routing (if applicable)
Not applicable — this is a verification task.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: D3 — Verify Surface Asset Integration
**Status**: backlog → active → completed
**Date**: YYYY-MM-DD

### What I'm About to Do
Verify C5 against B3 and the existing surface renderer behavior. Confirm RH-400 surface sprite identity, transparency, orientation, and renderer consumption. Check that catalog assets remain unaffected. Record pass/fail and follow-up work. No asset modifications during verification.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| B3 approved design | Surface asset contract to verify against | reviewed |
| C5 implementation | Code to verify | reviewed/inspected |
| RH-400 surface sprite file | Asset integrity check | inspected |
| Catalog render/icon files | Integrity check (should be untouched) | inspected |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Reviewed B3 design and C5 implementation
- ✅ Understand architecture gotchas above

### Expected Outcomes
RH-400 surface sprite renders through approved path; transparent background remains intact; catalog assets remain separate; no unapproved architecture changes; exact discrepancies recorded.

### Critical Gotchas I Will Avoid
- ❌ Modifying generated assets during verification — instead ✅ Verifying existing assets as-is
- ❌ Accepting catalog asset modifications — instead ✅ Verifying catalog files are untouched
- ❌ Accepting hardcoded RH-400 paths as sufficient — instead ✅ Reporting whether integration generalizes

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1.
```

---

## Problem Statement

Verify C5 against B3 and the existing surface renderer behavior.

**Current behavior**: C5 connects the RH-400 surface sprite to the surface renderer per B3 contract.
**Expected behavior**: Verify surface sprite identity, transparency, orientation, and renderer consumption; confirm catalog assets remain unaffected; record pass/fail and follow-up work.

---

## Files Involved

### Primary Files — inspect or edit only as specified by this task
| File | Purpose | Key Method/Section |
|---|---|---|
| B3 approved design | Surface asset contract to verify against | N/A (review) |
| C5 implementation | Code to verify | N/A (inspect) |
| RH-400 surface sprite file | Asset integrity check | N/A (inspect) |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| A3 findings | RH-400 asset family mapping (surface sprite identification) |
| A5 findings | Surface renderer behavior (expected consumption) |
| Catalog render/icon files | Verify they remain untouched by C5 |

### Migration
- [x] No migration needed — verification only

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT. Do not proceed to Step 1 until both are done and approved.

All agents: follow these steps exactly in order. Do not skip steps or reorder them.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-DOCUMENTATION-ASSET-UI-D3-verify-surface-asset-integration.md \
       projects/galaxy_game/tasks/active/2026-08-31-HIGH-DOCUMENTATION-ASSET-UI-D3-verify-surface-asset-integration.md
```

Then open the moved file and change: `status: backlog → status: active`

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-31-HIGH-DOCUMENTATION-ASSET-UI-D3-verify-surface-asset-integration.md"
```

**Paste the output in chat before proceeding.** Expected: exactly one result at `active/` path.

### Step 1 — Review B3 and C5

Establish expected integration. Specifically:
- What identity/path does B3 specify for surface representation?
- How does C5 wire the RH-400 surface sprite per B3?
- What is the expected renderer behavior (from A5)?

### Step 2 — Verify RH-400 surface sprite

Confirm identity, transparency, orientation, and renderer consumption. Specifically:
- Is the correct surface sprite file used (not catalog render)?
- Does the sprite have a transparent background?
- Is the sprite orientation correct for the renderer?
- Does the renderer consume it correctly?

### Step 3 — Check catalog isolation

Ensure catalog assets were not substituted or affected by C5. Specifically:
- Are catalog render/icon files untouched (unchanged from pre-C5 state)?
- Are catalog asset paths unaffected by surface integration?
- Is there any cross-contamination between catalog and surface asset systems?

### Step 4 — Report

Record pass/fail and any follow-up work. The report should include:
1. RH-400 surface sprite: identity correct, transparency intact, orientation correct, renderer consumes correctly?
2. Catalog assets: untouched, paths unaffected, no cross-contamination?
3. Does the integration generalize to other asset families, or is it RH-400-specific?
4. Any follow-up recommendations (not implementations)?

---

## Acceptance Criteria
- [ ] RH-400 surface sprite renders through approved path
- [ ] Transparent background remains intact
- [ ] Catalog assets remain separate and untouched
- [ ] No unapproved architecture changes
- [ ] Exact discrepancies recorded
- [ ] Synthesis report posted to chat before any work began

---

## Stop Conditions — escalate to user immediately if:
- Surface renderer requires a broader asset architecture beyond B3/C5 scope
- Sprite format/orientation fails canonical requirements
- Catalog assets were modified during C5 implementation

---

## Commit Instructions
No commit is authorized by this task unless explicitly stated in the task steps. Do not commit verification findings without separate approval.

---

## Documentation
- [x] No doc changes needed unless this task explicitly requests documentation output.

---

## Dependencies
**Blocked by**: C5
**Blocks**: none
**Related tasks**: B3

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
Verification findings only — no code changes.

### Issues discovered
[Any discrepancies between C5 implementation and B3 design]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future verification tasks should know]

---

## Handoff Summary
HANDOFF SUMMARY: D3 | [result] | [next action]
