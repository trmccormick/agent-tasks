---
status: backlog
priority: HIGH
type: research
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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-RESEARCH-ASSET-UI-A5-surface-sprite-consumption-research.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-RESEARCH-ASSET-UI-A5-surface-sprite-consumption-research.md \
         projects/galaxy_game/tasks/active/2026-08-31-HIGH-RESEARCH-ASSET-UI-A5-surface-sprite-consumption-research.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-31-HIGH-RESEARCH-ASSET-UI-A5-surface-sprite-consumption-research.md"
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

# TASK: A5 — Surface Sprite Consumption Research
**Status**: BACKLOG
**Priority**: HIGH
**Type**: research
**Created**: 2026-08-31
**Last Updated**: 2026-08-31

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: PASS
- **Docker Wrapper Check**: N/A — research task, no RSpec execution required.
- **MVP Alignment**: VALID — surface asset integration (C5/D3) depends on knowing how sprites are currently consumed.
- **MVP Impact Note**: Surface representation is a separate consumer from catalog presentation; this task must stay precise about which RH-400 asset it means (surface sprite, not catalog render).
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Repository inspection and implementation access.
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. `/Users/tam0013/Documents/git/agent-tasks/README.md`
2. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. This task file

---

## Context

Determine how the surface renderer currently consumes transparent sprites and animation frames, specifically for the RH-400 surface representation. Surface assets have a different consumer and technical contract from catalog renders — they must eventually work in Civ4/FreeCiv-style layers and TerrainForge/SimCity-style views. Inspect the surface sprite and animation representations only. Do not design the catalog contract here.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions.
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules.
- `TASK_TEMPLATE.md` — task lifecycle and agent contract.
- Asset-generation specifications in `docs/` are development-time source specifications, not Rails runtime data.
- Visual Profiles and Render Templates are locked/out-of-scope unless a task explicitly says otherwise.

---

## Critical Information for This Task

### Credentials
None required.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Surface sprites are transparent-background gameplay assets.
- ❌ Wrong: Introduce baked terrain backgrounds into generated surface images or treat them as catalog renders.
- ✅ Right: Keep surface sprites as transparent PNGs; background/composition is the renderer's responsibility.
- Why: The surface renderer composes assets dynamically based on terrain, lighting, and game state.

⚠️ **GOTCHA 2**: Catalog renders and surface sprites have fundamentally different consumers.
- ❌ Wrong: Use catalog render as surface sprite or vice versa; assume they are interchangeable.
- ✅ Right: Treat them as separate representations with separate paths, formats, and consumers.
- Why: Catalog renders have backgrounds for documentation/UI; surface sprites are transparent for game rendering.

### Multi-Domain / Multi-Tenant Routing (if applicable)
Not applicable — this is a repository inspection task.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: A5 — Surface Sprite Consumption Research
**Status**: backlog → active → completed
**Date**: YYYY-MM-DD

### What I'm About to Do
Determine how the surface renderer currently consumes transparent sprites and animation frames for the RH-400 surface representation. Stay precise about surface sprite (not catalog render). Identify integration gaps without implementing them.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| Surface-layer/rendering services | Sprite consumer code | pending |
| RH-400 surface sprite file | Actual asset path and format | pending |
| Animation frame files (if any) | State representation evidence | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
Surface sprite consumer identified; sprite lookup path documented; animation-state handling documented; integration gaps identified without implementing them.

### Critical Gotchas I Will Avoid
- ❌ Using catalog render as surface sprite — instead ✅ Keeping them separate with distinct paths and consumers
- ❌ Introducing baked terrain backgrounds — instead ✅ Documenting existing renderer behavior
- ❌ Designing the catalog contract here — instead ✅ Focusing only on surface rendering

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1.
```

---

## Problem Statement

Determine how the surface renderer currently consumes transparent sprites and animation frames, specifically for the RH-400 surface representation.

**Current behavior**: Repository state must be established by evidence; do not assume the planned architecture exists in code.
**Expected behavior**: Produce only the evidence result explicitly requested — identify sprite consumer, lookup path, animation handling, and integration gaps without implementing anything.

---

## Files Involved

### Primary Files — inspect or edit only as specified by this task
| File | Purpose | Key Method/Section |
|---|---|---|
| Surface-layer/rendering services | Sprite consumer code | N/A (read-only) |
| RH-400 surface sprite file | Actual asset path and format | N/A (read-only) |
| Animation frame files (if any) | State representation evidence | N/A (read-only) |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| Existing asset-generation specifications in `docs/` | Establish canonical surface sprite format requirements |
| A3 (RH-400 asset family mapping) | Distinguish surface sprite from catalog render |

### Migration
- [x] No migration needed

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT. Do not proceed to Step 1 until both are done and approved.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-RESEARCH-ASSET-UI-A5-surface-sprite-consumption-research.md \
       projects/galaxy_game/tasks/active/2026-08-31-HIGH-RESEARCH-ASSET-UI-A5-surface-sprite-consumption-research.md
```

Then open the moved file and change: `status: backlog → status: active`

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-31-HIGH-RESEARCH-ASSET-UI-A5-surface-sprite-consumption-research.md"
```

**Paste the output in chat before proceeding.** Expected: exactly one result at `active/` path.

### Step 1 — Locate surface rendering code

Find the surface-layer/rendering services and sprite consumers. Search for:
- Surface rendering services or controllers
- Sprite loading/consumption logic
- Any Civ4/FreeCiv-style layer rendering code

### Step 2 — Trace asset lookup

Determine how a sprite path/identifier reaches the renderer. Specifically:
- What is the RH-400 surface sprite file path?
- How does the renderer resolve sprite paths?
- Is there an asset registry or direct path reference?

### Step 3 — Inspect animation handling

Determine whether idle/moving/harvesting/damage states have an existing representation. Specifically:
- Are animation frames generated for RH-400?
- How are they named/stored?
- Does the renderer support state-based sprite switching?

### Step 4 — Report

Identify the smallest missing integration contract without implementing it. The report should answer:
1. What surface sprite consumer exists?
2. How does sprite lookup work?
3. What animation states are supported (if any)?
4. What is the smallest gap C5 must address?

---

## Acceptance Criteria
- [ ] Surface sprite consumer identified with file:line evidence
- [ ] Sprite lookup path documented
- [ ] Animation-state handling documented (or confirmed absent)
- [ ] Integration gaps identified without implementing them
- [ ] No code/assets modified
- [ ] Synthesis report posted to chat before any work began

---

## Stop Conditions — escalate to user immediately if:
- Surface rendering depends on a shared asset system that does not yet exist
- Sprite transparency/format assumptions conflict with existing renderer behavior
- The RH-400 surface sprite cannot be located or identified

---

## Commit Instructions
No commit is authorized by this task unless explicitly stated in the task steps. Do not commit research or design work without separate approval.

---

## Documentation
- [x] No doc changes needed unless this task explicitly requests documentation output.

---

## Dependencies
**Blocked by**: A3
**Blocks**: B3, C5
**Related tasks**: Civ4/FreeCiv/TerrainForge surface layers

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: N/A for research/design tasks unless tests are explicitly required.

### What was changed
Research/design findings only unless implementation is explicitly authorized.

### Issues discovered
[Fill in]

### Follow-up tasks needed
[Fill in]

### Lessons learned
[Fill in]

---

## Handoff Summary
HANDOFF SUMMARY: A5 | [result] | [next action]
