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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-RESEARCH-ASSET-UI-A4-catalog-data-retrieval-research.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-RESEARCH-ASSET-UI-A4-catalog-data-retrieval-research.md \
         projects/galaxy_game/tasks/active/2026-08-31-HIGH-RESEARCH-ASSET-UI-A4-catalog-data-retrieval-research.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-31-HIGH-RESEARCH-ASSET-UI-A4-catalog-data-retrieval-research.md"
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

# TASK: A4 — Catalog Data Retrieval Research
**Status**: BACKLOG
**Priority**: HIGH
**Type**: research
**Created**: 2026-08-31
**Last Updated**: 2026-08-31

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: PASS
- **Docker Wrapper Check**: N/A — research task, no RSpec execution required.
- **MVP Alignment**: VALID — catalog presentation depends on knowing how to retrieve canonical data for Units.
- **MVP Impact Note**: Asset/UI foundation for the Luna + Earth-import MVP; unresolved Production/Presentation split must be reported, not silently decided.
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

Determine how catalog presentation can retrieve canonical Blueprint, Operational Data, and Visual Definition for a Unit such as RH-400, while identifying the unresolved Production/Presentation split. RH-400 is a Unit and therefore has Blueprint + Operational Data + Visual Definition. Catalog presentation is development/UI work; production runtime must continue using established game-data lookup architecture. The Icon Bible gap (from A6) should be flagged if this task touches icon handling.

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

⚠️ **GOTCHA 1**: Do not assume the catalog should consume raw documentation.
- ❌ Wrong: Treat docs/ content as data that the catalog UI directly consumes at runtime.
- ✅ Right: Catalog presentation assembles data from existing game-data lookup architecture; docs/ remain development-time source only.
- Why: The Production/Presentation split is intentional — production runtime and catalog UI have different data contracts.

⚠️ **GOTCHA 2**: Visual Profiles and Render Templates are locked source specifications.
- ❌ Wrong: Propose changing Visual Profiles or Render Templates to support catalog presentation.
- ✅ Right: Document what the catalog can consume from existing Visual Definition without modifying locked specs.
- Why: These are locked architectural decisions — any change requires separate approval.

⚠️ **GOTCHA 3**: The Production/Presentation split is intentionally unresolved.
- ❌ Wrong: Silently decide the architecture for how production data becomes presentation data.
- ✅ Right: Report concrete options/evidence without selecting an architecture. Flag this as a decision point for B2.
- Why: This is an architectural decision that affects more than just catalog UI.

### Multi-Domain / Multi-Tenant Routing (if applicable)
Not applicable — this is a repository inspection task.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: A4 — Catalog Data Retrieval Research
**Status**: backlog → active → completed
**Date**: YYYY-MM-DD

### What I'm About to Do
Determine how catalog presentation can retrieve canonical Blueprint, Operational Data, and Visual Definition for RH-400 (Unit case). Identify the unresolved Production/Presentation split without deciding it. Flag Icon Bible gap if relevant.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| Catalog controllers/services/models | Current retrieval mechanism | pending |
| Blueprint/Operational Data for RH-400 | Canonical data structure | pending |
| Visual Definition references | Asset relationship evidence | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
Blueprint retrieval path identified; Operational Data retrieval path identified; Visual Definition relationship identified; Production/Presentation split explicitly documented as unresolved; no implementation performed.

### Critical Gotchas I Will Avoid
- ❌ Deciding the Production/Presentation architecture — instead ✅ Reporting options/evidence for B2 to resolve
- ❌ Treating docs/ as runtime data — instead ✅ Documenting existing game-data lookup architecture
- ❌ Ignoring Icon Bible gap — instead ✅ Flagging if icon handling is affected

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1.
```

---

## Problem Statement

Determine how catalog presentation can retrieve canonical Blueprint, Operational Data, and Visual Definition for a Unit such as RH-400, while identifying the unresolved Production/Presentation split.

**Current behavior**: Repository state must be established by evidence; do not assume the planned architecture exists in code.
**Expected behavior**: Produce only the evidence result explicitly requested — identify retrieval paths, document the Production/Presentation split, and flag decision points for B2.

---

## Files Involved

### Primary Files — inspect or edit only as specified by this task
| File | Purpose | Key Method/Section |
|---|---|---|
| Catalog controllers/services/models | Current retrieval mechanism | N/A (read-only) |
| Blueprint/Operational Data for RH-400 | Canonical data structure | N/A (read-only) |
| Visual Definition references | Asset relationship evidence | N/A (read-only) |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| Existing asset-generation specifications in `docs/` | Establish canonical asset roles and naming conventions |
| Blueprint/Operational Data examples for RH-400 | Establish canonical game-data relationship |
| A6 (Icon Bible dependency research) | Flag if icon handling is affected |

### Migration
- [x] No migration needed

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT. Do not proceed to Step 1 until both are done and approved.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-RESEARCH-ASSET-UI-A4-catalog-data-retrieval-research.md \
       projects/galaxy_game/tasks/active/2026-08-31-HIGH-RESEARCH-ASSET-UI-A4-catalog-data-retrieval-research.md
```

Then open the moved file and change: `status: backlog → status: active`

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-31-HIGH-RESEARCH-ASSET-UI-A4-catalog-data-retrieval-research.md"
```

**Paste the output in chat before proceeding.** Expected: exactly one result at `active/` path.

### Step 1 — Trace current catalog retrieval

Find catalog controllers/services/models and how they retrieve asset/game data. Search for:
- CatalogService or similar catalog-related services
- Controllers that serve catalog/detail views
- Any existing catalog UI code

### Step 2 — Trace Unit data

Use RH-400 to document Blueprint, Operational Data, and Visual Definition relationships. Specifically:
- How is Blueprint data retrieved for a Unit?
- How is Operational Data retrieved for a Unit?
- How does Visual Definition relate to the Unit's visual assets?
- What file paths or model associations are used?

### Step 3 — Identify presentation boundary

Document where UI-facing presentation data is currently assembled and where it is absent. Specifically:
- What data is available in production runtime vs. catalog presentation?
- Where does the existing architecture end and catalog work begin?
- What would need to change to support catalog presentation?

### Step 4 — Report the unresolved split

State concrete options/evidence without selecting an architecture. The report should answer:
1. How is Blueprint data retrieved for RH-400?
2. How is Operational Data retrieved for RH-400?
3. How is Visual Definition related to RH-400?
4. What is the Production/Presentation split and what are the options?
5. Is the Icon Bible gap relevant to catalog presentation?

---

## Acceptance Criteria
- [ ] Blueprint retrieval path identified with file:line evidence
- [ ] Operational Data retrieval path identified with file:line evidence
- [ ] Visual Definition relationship identified with file:line evidence
- [ ] Production/Presentation split explicitly documented as unresolved (not decided)
- [ ] Icon Bible gap flagged if relevant to icon handling
- [ ] No implementation performed
- [ ] Synthesis report posted to chat before any work began

---

## Stop Conditions — escalate to user immediately if:
- Catalog architecture is absent or incompatible with canonical data
- Resolving the Production/Presentation split requires a new architectural decision beyond B2's scope
- Evidence is insufficient to distinguish specification from implementation

---

## Commit Instructions
No commit is authorized by this task unless explicitly stated in the task steps. Do not commit research or design work without separate approval.

---

## Documentation
- [x] No doc changes needed unless this task explicitly requests documentation output.

---

## Dependencies
**Blocked by**: A3
**Blocks**: B2
**Related tasks**: CatalogService and Lookup services

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
HANDOFF SUMMARY: A4 | [result] | [next action]
