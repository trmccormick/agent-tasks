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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-RESEARCH-ASSET-UI-A3-rh-400-asset-family-mapping.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-RESEARCH-ASSET-UI-A3-rh-400-asset-family-mapping.md \
         projects/galaxy_game/tasks/active/2026-08-31-HIGH-RESEARCH-ASSET-UI-A3-rh-400-asset-family-mapping.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-31-HIGH-RESEARCH-ASSET-UI-A3-rh-400-asset-family-mapping.md"
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

# TASK: A3 — RH-400 Asset Family Mapping
**Status**: BACKLOG
**Priority**: HIGH
**Type**: research
**Created**: 2026-08-31
**Last Updated**: 2026-08-31

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: PASS
- **Docker Wrapper Check**: N/A — research task, no RSpec execution required.
- **MVP Alignment**: VALID — catalog presentation depends on knowing which asset in the RH-400 family to use.
- **MVP Impact Note**: Asset/UI foundation for the Luna + Earth-import MVP; distinguishing catalog from surface assets is critical.
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

Map each RH-400 asset-family representation to its intended consumer and distinguish catalog/presentation assets from surface-render assets. The RH-400 family includes: inventory icon, catalog render, encyclopedia render, blueprint, exploded view, surface sprite, animation frames, wrecked sprite, and thumbnail representations. Each has a different consumer and technical contract.

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

⚠️ **GOTCHA 1**: A catalog render is not a surface sprite.
- ❌ Wrong: Use the surface sprite in place of the catalog render, or vice versa.
- ✅ Right: Catalog renders have backgrounds and are for documentation/UI; surface sprites are transparent-background gameplay assets.
- Why: They serve fundamentally different consumers — catalog UI vs. game rendering engine.

⚠️ **GOTCHA 2**: Surface sprites must remain transparent-background gameplay assets.
- ❌ Wrong: Introduce baked terrain backgrounds into generated surface images.
- ✅ Right: Keep surface sprites as transparent PNGs; background/composition is the renderer's responsibility.
- Why: The surface renderer composes assets dynamically based on terrain, lighting, and game state.

### Multi-Domain / Multi-Tenant Routing (if applicable)
Not applicable — this is a repository inspection task.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: A3 — RH-400 Asset Family Mapping
**Status**: backlog → active → completed
**Date**: YYYY-MM-DD

### What I'm About to Do
Map each known RH-400 asset-family representation to its intended consumer and distinguish catalog/presentation assets from surface-render assets. Produce a compact mapping table with evidence.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| RH-400 asset family specification | Canonical definition | pending |
| Generated asset files | Actual file paths and naming | pending |
| Code/spec references | Consumer identification | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
All known RH-400 representations accounted for; catalog vs. surface consumers explicit; missing/ambiguous representations listed; no files modified.

### Critical Gotchas I Will Avoid
- ❌ Collapsing all RH-400 representations into one generic image field — instead ✅ Separately identifying each representation with its consumer
- ❌ Using surface sprite as catalog render — instead ✅ Keeping them separate with distinct paths and consumers

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1.
```

---

## Problem Statement

Map each RH-400 asset-family representation to its intended consumer and distinguish catalog/presentation assets from surface-render assets.

**Current behavior**: Repository state must be established by evidence; do not assume the planned architecture exists in code.
**Expected behavior**: Produce only the evidence/design result explicitly requested — a compact mapping table with file:line evidence for each known representation.

---

## Files Involved

### Primary Files — inspect or edit only as specified by this task
| File | Purpose | Key Method/Section |
|---|---|---|
| RH-400 asset family specification | Canonical definition | N/A (read-only) |
| Generated asset files | Actual file paths and naming | N/A (read-only) |
| Code/spec references | Consumer identification | N/A (read-only) |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| Existing asset-generation specifications in `docs/` | Establish canonical asset roles and naming conventions |
| Blueprint/Operational Data examples for RH-400 | Establish canonical game-data relationship |

### Migration
- [x] No migration needed

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT. Do not proceed to Step 1 until both are done and approved.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-RESEARCH-ASSET-UI-A3-rh-400-asset-family-mapping.md \
       projects/galaxy_game/tasks/active/2026-08-31-HIGH-RESEARCH-ASSET-UI-A3-rh-400-asset-family-mapping.md
```

Then open the moved file and change: `status: backlog → status: active`

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-31-HIGH-RESEARCH-ASSET-UI-A3-rh-400-asset-family-mapping.md"
```

**Paste the output in chat before proceeding.** Expected: exactly one result at `active/` path.

### Step 1 — Locate the RH-400 family specification

Find the canonical asset-family definition and generated files. Search for:
- "RH-400" in asset-generation documentation
- Generated asset directories containing RH-400 files
- Any schema or configuration defining the asset family

### Step 2 — Build the mapping

For each known representation, record intended consumer and current file/path. The known representations are:
1. Inventory icon — [consumer?] — [path?]
2. Catalog render — [consumer?] — [path?]
3. Encyclopedia render — [consumer?] — [path?]
4. Blueprint — [consumer?] — [path?]
5. Exploded view — [consumer?] — [path?]
6. Surface sprite — [consumer?] — [path?]
7. Animation frames (idle/moving/harvesting/damage) — [consumer?] — [path?]
8. Wrecked sprite — [consumer?] — [path?]
9. Thumbnail — [consumer?] — [path?]

### Step 3 — Identify gaps

Flag missing files, ambiguous roles, naming inconsistencies, or stale references. Specifically:
- Are any representations missing from the generated assets?
- Do any files have ambiguous names (could be catalog render OR surface sprite)?
- Are there naming inconsistencies across the family?

### Step 4 — Report

Produce a compact mapping table with evidence. Do not modify assets or code. The report should answer:
1. What representations exist and where?
2. What is each representation's intended consumer?
3. What gaps or ambiguities exist?
4. What is the smallest question B2/B3 must resolve?

---

## Acceptance Criteria
- [ ] All known RH-400 representations are accounted for with file paths
- [ ] Catalog vs. surface consumers are explicit and separated
- [ ] Missing/ambiguous representations are listed
- [ ] No files modified
- [ ] Synthesis report posted to chat before any work began

---

## Stop Conditions — escalate to user immediately if:
- Canonical sources disagree on an asset role requiring architectural approval
- A representation is used by multiple consumers in a way that needs architectural approval
- The canonical asset-family definition cannot be located

---

## Commit Instructions
No commit is authorized by this task unless explicitly stated in the task steps. Do not commit research or design work without separate approval.

---

## Documentation
- [x] No doc changes needed unless this task explicitly requests documentation output.

---

## Dependencies
**Blocked by**: A2
**Blocks**: A4, A5, B2, B3
**Related tasks**: RH-400 Run 03–06 evaluations

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
HANDOFF SUMMARY: A3 | [result] | [next action]
