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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B2-catalog-presentation-contract.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B2-catalog-presentation-contract.md \
         projects/galaxy_game/tasks/active/2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B2-catalog-presentation-contract.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B2-catalog-presentation-contract.md"
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

# TASK: B2 — Define Catalog Presentation Contract
**Status**: BACKLOG
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-08-31
**Last Updated**: 2026-08-31

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: PASS
- **Docker Wrapper Check**: N/A — architecture task, no RSpec execution required.
- **MVP Alignment**: VALID — catalog presentation contract is prerequisite for all C-series implementation tasks.
- **MVP Impact Note**: Defines the minimal catalog presentation contract covering the Object Class split and RH-400 presentation assets.
- **Action Line**: NEEDS A2-A4-A6 EVIDENCE BEFORE DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Requires understanding of Object Class split and asset roles.
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. `/Users/tam0013/Documents/git/agent-tasks/README.md`
2. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. This task file
4. **A2 findings** — static asset storage and presentation paths
5. **A3 findings** — RH-400 asset family mapping
6. **A4 findings** — catalog data retrieval and Production/Presentation split
7. **A6 findings** — Icon Bible dependency status

---

## Context

Define a minimal catalog presentation contract covering the Object Class split and the RH-400 presentation assets. A2-A4 establish storage, asset-family, and data-retrieval facts. Catalog UI must work for Components as well as Units/Structures/Vehicles. Resources/Components use Blueprint + Visual; Units/Structures/Vehicles use Blueprint + Operational Data + Visual. RH-400 is the Unit case; the I-beam is the Component case. Production/Presentation boundary remains explicit — this contract defines what catalog presentation needs, not how production data becomes presentation data (that's an unresolved decision for later).

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions.
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules.
- `TASK_TEMPLATE.md` — task lifecycle and agent contract.
- Visual Definition Template v1.0 — existing visual definition schema.
- Asset-generation specifications in `docs/` are development-time source specifications, not Rails runtime data.
- Visual Profiles and Render Templates are locked/out-of-scope unless a task explicitly says otherwise.

---

## Critical Information for This Task

### Credentials
None required.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Resources/Components use Blueprint + Visual; Units/Structures/Vehicles use Blueprint + Operational Data + Visual.
- ❌ Wrong: Apply the same data contract to both object classes, or give Components fake Operational Data.
- ✅ Right: Components = Blueprint + Visual only (no Operational Data). Units/Structures/Vehicles = Blueprint + Operational Data + Visual.
- Why: Not every catalog entry has Operational Data. The I-beam (Component) case must not produce empty/fake Unit sections.

⚠️ **GOTCHA 2**: Keep catalog render/icon separate from surface assets.
- ❌ Wrong: Use the surface sprite in place of the catalog render, or bake UI text into generated images.
- ✅ Right: Catalog renders have backgrounds for documentation/UI; surface sprites are transparent gameplay assets. Text is in HTML/UI, never baked into generated images.
- Why: They serve fundamentally different consumers with different technical contracts.

⚠️ **GOTCHA 3**: Production/Presentation boundary remains explicit — do not silently decide it.
- ❌ Wrong: Assume catalog presentation consumes production data directly without documenting the boundary.
- ✅ Right: Document what is production data vs. what is presentation assembly; flag the split as an unresolved decision point.
- Why: This affects more than just catalog UI — it's a broader architectural question.

### Multi-Domain / Multi-Tenant Routing (if applicable)
Not applicable — this is an architecture design task.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: B2 — Define Catalog Presentation Contract
**Status**: backlog → active → completed
**Date**: YYYY-MM-DD

### What I'm About to Do
Define a minimal catalog presentation contract supporting both Components (I-beam case) and Units/Structures/Vehicles (RH-400 case). Specify required fields by object class, asset roles (catalog render vs. icon vs. surface), and the Production/Presentation boundary. Produce design only — no code changes.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| A2 findings | Catalog render/icon paths | reviewed |
| A3 findings | RH-400 asset family mapping | reviewed |
| A4 findings | Production/Presentation split | reviewed |
| A6 findings | Icon Bible status | reviewed |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Reviewed A2, A3, A4, A6 findings
- ✅ Understand architecture gotchas above

### Expected Outcomes
Both object classes (Component and Unit) represented; RH-400 and I-beam are valid validation cases; catalog render/icon distinct from surface assets; Production/Presentation boundary explicit; Icon Bible impact recorded; no code changes.

### Critical Gotchas I Will Avoid
- ❌ Applying same contract to Components and Units — instead ✅ Separating by Object Class (Blueprint+Visual vs Blueprint+OperationalData+Visual)
- ❌ Using surface sprite as catalog render — instead ✅ Keeping them separate with distinct paths and consumers
- ❌ Silently deciding the Production/Presentation boundary — instead ✅ Documenting it as explicit and unresolved

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1.
```

---

## Problem Statement

Define a minimal catalog presentation contract covering the Object Class split and the RH-400 presentation assets.

**Current behavior**: A2-A4 establish storage, asset-family, and data-retrieval facts.
**Expected behavior**: Produce a design-only contract that specifies required fields by object class, asset roles, and the Production/Presentation boundary — without modifying any code.

---

## Files Involved

### Primary Files — inspect or edit only as specified by this task
| File | Purpose | Key Method/Section |
|---|---|---|
| A2 findings | Catalog render/icon paths | N/A (input) |
| A3 findings | RH-400 asset family mapping | N/A (input) |
| A4 findings | Production/Presentation split | N/A (input) |
| A6 findings | Icon Bible status | N/A (input) |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `docs/new_agent/rules/DECISIONS.md` | Locked architectural decisions |
| `docs/new_agent/rules/GUARDRAILS.md` | Execution rules |
| Visual Definition Template v1.0 | Existing visual definition schema |

### Migration
- [x] No migration needed — design only, no code/schema changes

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT. Do not proceed to Step 1 until both are done and approved.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B2-catalog-presentation-contract.md \
       projects/galaxy_game/tasks/active/2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B2-catalog-presentation-contract.md
```

Then open the moved file and change: `status: backlog → status: active`

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-31-HIGH-ARCHITECTURE-ASSET-UI-B2-catalog-presentation-contract.md"
```

**Paste the output in chat before proceeding.** Expected: exactly one result at `active/` path.

### Step 1 — Review A2-A4 findings

Use only verified findings from A2, A3, A4, and A6. Confirm:
- Catalog render and icon paths (A2)
- RH-400 asset family representations (A3)
- Production/Presentation split options (A4)
- Icon Bible status (A6)

### Step 2 — Define presentation inputs by object class

Specify required fields for each object class:
- **Components** (I-beam case): Blueprint + Visual only
- **Units/Structures/Vehicles** (RH-400 case): Blueprint + Operational Data + Visual

For each, list the exact fields and their sources.

### Step 3 — Define asset roles

Specify catalog render and icon roles without including surface sprites:
- Which file is the catalog render?
- Which file is the inventory icon?
- What are their technical requirements (format, transparency, dimensions)?
- How do they differ from surface sprites?

### Step 4 — Define boundary

Document what remains production data versus presentation assembly. Specifically:
- What data comes from production runtime?
- What data is assembled specifically for catalog presentation?
- Where is the cleanest boundary between the two?

### Step 5 — Produce contract

No implementation. The report should answer:
1. What fields does each object class need in catalog presentation?
2. Which asset files serve which role (catalog render, icon, etc.)?
3. What is the Production/Presentation boundary?
4. How does the Icon Bible gap affect this contract?

---

## Acceptance Criteria
- [ ] Component and Unit object classes are both represented with distinct contracts
- [ ] RH-400 (Unit) and I-beam (Component) are valid validation cases
- [ ] Catalog render/icon are distinct from surface assets
- [ ] Production/Presentation boundary is explicit and documented as unresolved
- [ ] Icon Bible impact is recorded
- [ ] No code changes performed

---

## Stop Conditions — escalate to user immediately if:
- Contract requires a new shared architecture beyond catalog presentation
- Existing catalog architecture cannot support the contract without a broader decision
- Canonical data conflicts with the task assumptions

---

## Commit Instructions
No commit is authorized by this task unless explicitly stated in the task steps. Do not commit research or design work without separate approval.

---

## Documentation
- [x] No doc changes needed unless this task explicitly requests documentation output.

---

## Dependencies
**Blocked by**: A2, A3, A4, A6
**Blocks**: C2, C3, C4
**Related tasks**: Visual Definition Template

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
HANDOFF SUMMARY: B2 | [result] | [next action]
