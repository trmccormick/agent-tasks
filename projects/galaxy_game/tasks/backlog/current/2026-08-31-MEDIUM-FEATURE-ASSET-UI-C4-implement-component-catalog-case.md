---
status: backlog
priority: MEDIUM
type: feature
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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-31-MEDIUM-FEATURE-ASSET-UI-C4-implement-component-catalog-case.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-31-MEDIUM-FEATURE-ASSET-UI-C4-implement-component-catalog-case.md \
         projects/galaxy_game/tasks/active/2026-08-31-MEDIUM-FEATURE-ASSET-UI-C4-implement-component-catalog-case.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-31-MEDIUM-FEATURE-ASSET-UI-C4-implement-component-catalog-case.md"
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

# TASK: C4 — Implement Component Catalog Case
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: feature
**Created**: 2026-08-31
**Last Updated**: 2026-08-31

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS — all RSpec commands use the Docker wrapper.
- **MVP Alignment**: VALID — validates catalog contract works for Components (not just Units).
- **MVP Impact Note**: Extends the reviewed catalog pattern to the Component object class using the I-beam as the validation case.
- **Action Line**: NEEDS C3 COMPLETED BEFORE DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Requires extending existing catalog pattern to a new object class.
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. `/Users/tam0013/Documents/git/agent-tasks/README.md`
2. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. This task file
4. **C3 completed** — RH-400 catalog vertical slice must be working first
5. **B2 approved design** — Component object class contract

---

## Context

Extend the reviewed catalog pattern to the Component object class using the I-beam as the validation case. B2 explicitly separates Components from Units. The I-beam demonstrates that not every catalog entry has Operational Data. Do not copy RH-400-specific fields into the Component presentation. Do not create fake operational data.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions.
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules.
- `TASK_TEMPLATE.md` — task lifecycle and agent contract.
- B2 approved design document — Component object class contract.

---

## Critical Information for This Task

### Credentials
None required.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Do not copy RH-400-specific fields into the Component presentation.
- ❌ Wrong: Apply the same data sections to Components as Units, producing empty/fake Operational Data sections.
- ✅ Right: Components = Blueprint + Visual only; omit Operational Data entirely (not empty, not null — absent).
- Why: Not every catalog entry has Operational Data. The I-beam case must not produce fake Unit sections.

⚠️ **GOTCHA 2**: Do not redesign the catalog globally.
- ❌ Wrong: Refactor the entire catalog component to be "more generic" as part of this task.
- ✅ Right: Extend only what's needed for the I-beam case; keep the shared presentation compatible with RH-400.
- Why: This is a narrow extension task, not a global refactoring opportunity.

### Multi-Domain / Multi-Tenant Routing (if applicable)
Not applicable — this is an implementation task.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: C4 — Implement Component Catalog Case
**Status**: backlog → active → completed
**Date**: YYYY-MM-DD

### What I'm About to Do
Extend the reviewed catalog pattern (from C3) to the Component object class using the I-beam as the validation case. Components = Blueprint + Visual only; no fake Operational Data. Keep shared presentation compatible with RH-400. No global refactoring.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| C3 implementation | Existing RH-400 catalog view | reviewed |
| B2 approved design | Component contract | reviewed |
| [UI files to modify] | Extend pattern for Components | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Reviewed C3 implementation and B2 design
- ✅ Understand architecture gotchas above

### Expected Outcomes
I-beam renders through the catalog pattern; Component has no fake Operational Data; shared presentation remains compatible with RH-400; focused checks pass.

### Critical Gotchas I Will Avoid
- ❌ Copying RH-400 fields to Components — instead ✅ Using Blueprint + Visual only for Components
- ❌ Creating fake Operational Data — instead ✅ Omitting Operational Data section entirely for Components
- ❌ Global catalog refactoring — instead ✅ Narrow extension for I-beam case only

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1.
```

---

## Problem Statement

Extend the reviewed catalog pattern to the Component object class using the I-beam as the validation case.

**Current behavior**: C3 provides a working RH-400 (Unit) catalog view.
**Expected behavior**: Extend only what's needed for the I-beam (Component) case; no fake Operational Data; shared presentation remains compatible with RH-400.

---

## Files Involved

### Primary Files — inspect or edit only as specified by this task
| File | Purpose | Key Method/Section |
|---|---|---|
| [UI files from C3] | Extend pattern for Components | As needed |
| [Controller/service if needed] | Component data assembly | As specified by B2 |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| C3 implementation | Existing RH-400 catalog view (pattern source) |
| B2 approved design | Component contract |
| A6 findings | Icon Bible status (if relevant to icons) |

### Migration
- [x] No migration needed — UI extension only

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT. Do not proceed to Step 1 until both are done and approved.

All agents: follow these steps exactly in order. Do not skip steps or reorder them. Debug prints OK for complex callbacks — add temporary `puts` statements, remove after verification.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/current/2026-08-31-MEDIUM-FEATURE-ASSET-UI-C4-implement-component-catalog-case.md \
       projects/galaxy_game/tasks/active/2026-08-31-MEDIUM-FEATURE-ASSET-UI-C4-implement-component-catalog-case.md
```

Then open the moved file and change: `status: backlog → status: active`

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-31-MEDIUM-FEATURE-ASSET-UI-C4-implement-component-catalog-case.md"
```

**Paste the output in chat before proceeding.** Expected: exactly one result at `active/` path.

### Step 1 — Review C3 and B2

Identify reusable presentation structure and the Component contract. Specifically:
- What parts of C3's pattern are reusable for Components?
- What parts must differ (no Operational Data)?
- What is the I-beam's Blueprint + Visual data?

### Step 2 — Implement the I-beam case

Use existing Blueprint + Visual inputs only. Specifically:
- Display I-beam catalog render/icon (from A2/A3)
- Show Blueprint content per B2
- Show Visual content per B2
- **Do not** show Operational Data section (it does not exist for Components)

### Step 3 — Add focused verification

Ensure absent Operational Data does not produce empty/fake Unit sections. Specifically:
- Does the I-beam view omit Operational Data entirely (not show "N/A" or empty)?
- Does the shared presentation remain compatible with RH-400?
- Do both views render correctly side by side?

### Step 4 — Stop

Do not redesign the catalog globally. Do not add support for other object classes beyond Components and Units.

---

## Acceptance Criteria
- [ ] I-beam renders through the catalog pattern
- [ ] Component has no fake Operational Data (section is absent, not empty)
- [ ] Shared presentation remains compatible with RH-400
- [ ] Focused checks pass
- [ ] Synthesis report posted to chat before any work began

---

## Stop Conditions — escalate to user immediately if:
- C3 architecture cannot support Components without redesign
- Component presentation requires changing B2
- The I-beam's Blueprint/Visual data cannot be located

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
**Blocked by**: C3
**Blocks**: D2
**Related tasks**: B2

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
HANDOFF SUMMARY: C4 | [result] | [next action]
