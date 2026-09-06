---
status: backlog
priority: HIGH
type: feature
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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-31-HIGH-FEATURE-ASSET-UI-C3-implement-rh-400-catalog-vertical-slice.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-31-HIGH-FEATURE-ASSET-UI-C3-implement-rh-400-catalog-vertical-slice.md \
         projects/galaxy_game/tasks/active/2026-08-31-HIGH-FEATURE-ASSET-UI-C3-implement-rh-400-catalog-vertical-slice.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-31-HIGH-FEATURE-ASSET-UI-C3-implement-rh-400-catalog-vertical-slice.md"
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

# TASK: C3 — Implement RH-400 Catalog Vertical Slice
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-08-31
**Last Updated**: 2026-08-31

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS — all RSpec commands use the Docker wrapper.
- **MVP Alignment**: AI_MANAGER_LUNA_SETTLEMENT — first UI implementation validating catalog contract against a real Unit.
- **MVP Impact Note**: First UI implementation in this workstream; validates the B2 catalog contract against RH-400.
- **Action Line**: NEEDS C2 COMPLETED BEFORE DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Requires UI implementation and asset integration.
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. `/Users/tam0013/Documents/git/agent-tasks/README.md`
2. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. This task file
4. **B2 approved design** — catalog presentation contract
5. **C2 completed** — catalog data wiring must be implemented first

---

## Context

Build the smallest working RH-400 catalog detail view using the approved B2 contract and the existing catalog render/icon assets. This is the first UI implementation in this workstream and should validate the catalog contract against a real Unit. Do not generalize prematurely. Do not use the surface sprite in place of the catalog render. Keep page text in HTML/UI, never baked into generated images.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions.
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules.
- `TASK_TEMPLATE.md` — task lifecycle and agent contract.
- B2 approved design document — the source of truth for catalog presentation contract.

---

## Critical Information for This Task

### Credentials
None required.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Do not generalize prematurely.
- ❌ Wrong: Build a generic catalog component that handles all object classes before validating with RH-400.
- ✅ Right: Build the smallest working RH-400 detail view; generalize only after C3 is verified and C4 adds Component support.
- Why: Validating the contract against one Unit first reduces risk and provides concrete evidence for later generalization.

⚠️ **GOTCHA 2**: Do not use the surface sprite in place of the catalog render.
- ❌ Wrong: Substitute the transparent surface sprite for the catalog render image.
- ✅ Right: Use the exact catalog render file identified by A2/A3; it has a background appropriate for documentation/UI.
- Why: Surface sprites are transparent gameplay assets; catalog renders have backgrounds for documentation/UI.

⚠️ **GOTCHA 3**: Keep page text in HTML/UI, never baked into generated images.
- ❌ Wrong: Bake unit names, descriptions, or stats into generated sprite images.
- ✅ Right: All text is rendered by the browser; images are purely visual assets.
- Why: Baked text cannot be localized, styled, or updated without regenerating images.

### Multi-Domain / Multi-Tenant Routing (if applicable)
Not applicable — this is an implementation task.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: C3 — Implement RH-400 Catalog Vertical Slice
**Status**: backlog → active → completed
**Date**: YYYY-MM-DD

### What I'm About to Do
Build the smallest working RH-400 catalog detail view using the approved B2 contract and existing catalog render/icon assets. Validate the catalog contract against a real Unit. No generalization, no surface sprite substitution, no baked text.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| B2 approved design | Catalog presentation contract | reviewed |
| C2 wiring | Data assembly for RH-400 | reviewed |
| [UI files to create/modify] | RH-400 catalog view | pending |
| A2/A3 catalog render/icon paths | Asset references | reviewed |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Reviewed B2 design and C2 wiring
- ✅ Understand architecture gotchas above

### Expected Outcomes
RH-400 catalog render displayed; inventory icon represented where contract requires; Blueprint/Operational Data/Visual content follows B2; no text baked into generated images; no surface sprite substituted for catalog imagery.

### Critical Gotchas I Will Avoid
- ❌ Generalizing before validating — instead ✅ Building smallest working RH-400 view only
- ❌ Using surface sprite as catalog render — instead ✅ Using exact catalog render file from A2/A3
- ❌ Baking text into images — instead ✅ Keeping all text in HTML/UI

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1.
```

---

## Problem Statement

Build the smallest working RH-400 catalog detail view using the approved B2 contract and the existing catalog render/icon assets.

**Current behavior**: C2 provides data wiring for RH-400 Unit data.
**Expected behavior**: Build only the RH-400 catalog detail presentation; validate the B2 contract against a real Unit; stop before generalizing to other object classes.

---

## Files Involved

### Primary Files — inspect or edit only as specified by this task
| File | Purpose | Key Method/Section |
|---|---|---|
| [UI files to create/modify] | RH-400 catalog detail view | As needed for vertical slice |
| [Controller/service if needed] | Data assembly for RH-400 view | As specified by B2/C2 |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| B2 approved design | Catalog presentation contract |
| C2 wiring | Data assembly for RH-400 |
| A2/A3 catalog render/icon paths | Asset references |

### Migration
- [x] No migration needed — UI implementation only

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT. Do not proceed to Step 1 until both are done and approved.

All agents: follow these steps exactly in order. Do not skip steps or reorder them. Debug prints OK for complex callbacks — add temporary `puts` statements, remove after verification.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/current/2026-08-31-HIGH-FEATURE-ASSET-UI-C3-implement-rh-400-catalog-vertical-slice.md \
       projects/galaxy_game/tasks/active/2026-08-31-HIGH-FEATURE-ASSET-UI-C3-implement-rh-400-catalog-vertical-slice.md
```

Then open the moved file and change: `status: backlog → status: active`

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-31-HIGH-FEATURE-ASSET-UI-C3-implement-rh-400-catalog-vertical-slice.md"
```

**Paste the output in chat before proceeding.** Expected: exactly one result at `active/` path.

### Step 1 — Read B2 and C2

Use the approved contract and implemented data wiring. Confirm:
- What fields the RH-400 catalog view needs (per B2)
- How data is assembled (per C2)
- Which asset files to reference (catalog render + icon from A2/A3)

### Step 2 — Build the RH-400 view

Implement only the catalog detail presentation needed for the vertical slice. Specifically:
- Display the RH-400 catalog render image (not surface sprite)
- Display inventory icon where contract requires
- Show Blueprint, Operational Data, and Visual content per B2
- All text in HTML/UI — never baked into images

### Step 3 — Use static assets

Reference the catalog render and icon through the approved asset mechanism (from A2/A3). Do not move or copy generated assets.

### Step 4 — Verify

Run focused UI tests/checks and inspect the result. Specifically:
- Does the RH-400 catalog render display correctly?
- Is the inventory icon represented where required?
- Are Blueprint/Operational Data/Visual sections correct per B2?
- Is any text baked into images (should not be)?

### Step 5 — Stop

Do not generalize the component. Do not add Component support (that's C4). Do not expand beyond RH-400.

---

## Acceptance Criteria
- [ ] RH-400 catalog render is displayed (not surface sprite)
- [ ] Inventory icon is represented where contract requires
- [ ] Blueprint/Operational Data/Visual content follows B2
- [ ] No text is baked into generated images
- [ ] No surface sprite substituted for catalog imagery
- [ ] Focused verification passes
- [ ] Synthesis report posted to chat before any work began

---

## Stop Conditions — escalate to user immediately if:
- Existing frontend architecture cannot support the slice without a new architectural decision
- Asset paths require moving generated files (should not be necessary)
- Visual presentation requires changing canonical data

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add [specific files only — never git add .]
git commit -m "[type]: [spec/file name] — [brief description of root cause and fix]"
git push
```

---

## Documentation
- [x] No doc changes needed unless this task explicitly requests documentation output.

---

## Dependencies
**Blocked by**: C2
**Blocks**: C4, D2
**Related tasks**: A2, A3

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
HANDOFF SUMMARY: C3 | [result] | [next action]
