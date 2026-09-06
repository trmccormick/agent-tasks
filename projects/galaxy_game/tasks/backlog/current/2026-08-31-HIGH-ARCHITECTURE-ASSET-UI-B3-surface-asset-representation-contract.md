---
status: backlog
priority: HIGH
type: architecture
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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B3-surface-asset-representation-contract.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B3-surface-asset-representation-contract.md \
         projects/galaxy_game/tasks/active/2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B3-surface-asset-representation-contract.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B3-surface-asset-representation-contract.md"
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

# TASK: B3 — Define Surface Asset Representation Contract
**Status**: BACKLOG
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-08-31
**Last Updated**: 2026-08-31

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: PASS
- **Docker Wrapper Check**: N/A — architecture task, no RSpec execution required.
- **MVP Alignment**: VALID — surface asset integration (C5/D3) depends on this contract.
- **MVP Impact Note**: Defines how an asset family exposes a surface sprite and animation states to the surface renderer without coupling that renderer to catalog presentation.
- **Action Line**: NEEDS A3-A5 EVIDENCE BEFORE DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Requires understanding of both asset family structure and surface rendering.
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. `/Users/tam0013/Documents/git/agent-tasks/README.md`
2. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. This task file
4. **A3 findings** — RH-400 asset family mapping (surface sprite identification)
5. **A5 findings** — surface renderer and sprite-consumption behavior

---

## Context

Define how an asset family exposes a surface sprite and animation states to the surface renderer without coupling that renderer to catalog presentation. A5 establishes the current surface renderer and sprite-consumption behavior. Surface sprites are transparent gameplay assets. Catalog renders, encyclopedia renders, blueprints, and thumbnails are not interchangeable with surface sprites. This contract is intentionally separate from B2 (catalog presentation contract) — they serve different consumers and must remain decoupled.

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

⚠️ **GOTCHA 1**: Surface sprites are transparent gameplay assets, not catalog renders.
- ❌ Wrong: Use catalog render as surface sprite or introduce baked terrain backgrounds into generated surface images.
- ✅ Right: Keep surface sprites as transparent PNGs; background/composition is the renderer's responsibility.
- Why: The surface renderer composes assets dynamically based on terrain, lighting, and game state.

⚠️ **GOTCHA 2**: This contract is intentionally separate from B2 (catalog presentation contract).
- ❌ Wrong: Mix catalog presentation concerns into the surface asset contract, or assume they share the same data flow.
- ✅ Right: Define surface asset identity and states independently; document how they relate to `asset_id`/`asset_family` without referencing catalog UI.
- Why: Surface rendering and catalog presentation have different consumers, different technical contracts, and different update cycles.

### Multi-Domain / Multi-Tenant Routing (if applicable)
Not applicable — this is an architecture design task.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: B3 — Define Surface Asset Representation Contract
**Status**: backlog → active → completed
**Date**: YYYY-MM-DD

### What I'm About to Do
Define how an asset family exposes a surface sprite and animation states to the surface renderer without coupling that renderer to catalog presentation. Specify surface representation identity, animation-state handling grounded in existing renderer behavior, and the boundary between surface assets and catalog assets. Produce design only — no code changes.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| A3 findings | RH-400 surface sprite identification | reviewed |
| A5 findings | Surface renderer behavior | reviewed |
| B2 findings (for boundary) | Catalog vs. surface separation | reviewed |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Reviewed A3, A5 findings and B2 boundary
- ✅ Understand architecture gotchas above

### Expected Outcomes
Surface representation distinct from catalog presentation; asset identity linkage defined; animation-state handling grounded in existing renderer behavior; no catalog UI dependencies introduced; no code changes.

### Critical Gotchas I Will Avoid
- ❌ Mixing catalog concerns into surface contract — instead ✅ Keeping B2 and B3 as separate, decoupled contracts
- ❌ Introducing baked terrain backgrounds — instead ✅ Documenting transparent sprite requirements
- ❌ Designing the catalog contract here — instead ✅ Focusing only on surface rendering needs

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1.
```

---

## Problem Statement

Define how an asset family exposes a surface sprite and animation states to the surface renderer without coupling that renderer to catalog presentation.

**Current behavior**: A5 establishes the current surface renderer and sprite-consumption behavior.
**Expected behavior**: Produce a design-only contract that specifies surface representation identity, animation-state handling, and the boundary between surface and catalog assets — without modifying any code.

---

## Files Involved

### Primary Files — inspect or edit only as specified by this task
| File | Purpose | Key Method/Section |
|---|---|---|
| A3 findings | RH-400 surface sprite identification | N/A (input) |
| A5 findings | Surface renderer behavior | N/A (input) |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `docs/new_agent/rules/DECISIONS.md` | Locked architectural decisions |
| `docs/new_agent/rules/GUARDRAILS.md` | Execution rules |
| B2 findings (for boundary) | Catalog vs. surface separation evidence |

### Migration
- [x] No migration needed — design only, no code/schema changes

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT. Do not proceed to Step 1 until both are done and approved.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/current/2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B3-surface-asset-representation-contract.md \
       projects/galaxy_game/tasks/active/2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B3-surface-asset-representation-contract.md
```

Then open the moved file and change: `status: backlog → status: active`

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B3-surface-asset-representation-contract.md"
```

**Paste the output in chat before proceeding.** Expected: exactly one result at `active/` path.

### Step 1 — Review A3 and A5 findings

Use verified asset-family and renderer findings. Confirm:
- Which RH-400 file is the surface sprite (A3)
- How the surface renderer currently consumes sprites (A5)
- What animation states exist or are supported (A5)

### Step 2 — Define identity

Specify how surface representation relates to `asset_id`/`asset_family`. Specifically:
- How does the surface renderer identify which sprite to load for a given asset?
- What is the naming/path convention for surface sprites within an asset family?
- How do animation states (idle/moving/harvesting/damage) relate to the base surface sprite?

### Step 3 — Define states

Document idle/moving/harvesting/damage representation only to the extent supported by existing architecture. Specifically:
- What animation states currently exist (if any)?
- How are they stored/named?
- Does the renderer support state-based switching?

### Step 4 — Produce contract

No code or schema changes. The report should answer:
1. How does surface representation relate to `asset_id`/`asset_family`?
2. What animation states are supported and how?
3. Where is the cleanest boundary between surface assets and catalog assets?
4. What is the smallest gap C5 must address?

---

## Acceptance Criteria
- [ ] Surface representation is distinct from catalog presentation (B2)
- [ ] Asset identity linkage is defined (asset_id/asset_family relationship)
- [ ] Animation-state handling is grounded in existing renderer behavior (not invented)
- [ ] No catalog UI dependencies introduced
- [ ] No code changes performed

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
**Blocked by**: A3, A5
**Blocks**: C5
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
HANDOFF SUMMARY: B3 | [result] | [next action]
