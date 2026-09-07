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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-RESEARCH-ASSET-UI-A2-static-asset-storage-research.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-RESEARCH-ASSET-UI-A2-static-asset-storage-research.md \
         projects/galaxy_game/tasks/active/2026-08-31-HIGH-RESEARCH-ASSET-UI-A2-static-asset-storage-research.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-31-HIGH-RESEARCH-ASSET-UI-A2-static-asset-storage-research.md"
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

# TASK: A2 — Static Asset Storage and Presentation Research
**Status**: BACKLOG
**Priority**: HIGH
**Type**: research
**Created**: 2026-08-31
**Last Updated**: 2026-08-31

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: PASS
- **Docker Wrapper Check**: N/A — research task, no RSpec execution required.
- **MVP Alignment**: VALID — catalog presentation depends on knowing how assets are stored and referenced.
- **MVP Impact Note**: Asset/UI foundation for the Luna + Earth-import MVP; catalog work needs presentation assets, not surface sprites.
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

Determine how generated static assets are currently stored, named, referenced, and served, focusing specifically on the RH-400 catalog render and inventory icon. The RH-400 family contains multiple representations — catalog work needs the presentation assets, not the surface sprite. Catalog presentation is development/UI work; production runtime must continue using established game-data lookup architecture.

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

⚠️ **GOTCHA 1**: Do not treat the RH-400 family as one image.
- ❌ Wrong: Treat "RH-400's asset" as a single generic image field.
- ✅ Right: Distinguish catalog render, inventory icon, encyclopedia render, blueprint, exploded view, surface sprite, animation frames, wrecked sprite, and thumbnail as separate representations with separate consumers.
- Why: Each representation has a different consumer and technical contract. Collapsing them loses critical information.

⚠️ **GOTCHA 2**: Do not move generated assets into runtime data merely to make them discoverable.
- ❌ Wrong: Add Docker mounts for docs/ or treat documentation as application data.
- ✅ Right: Asset-generation documentation remains development-time source material; catalog presentation uses existing game-data lookup architecture.
- Why: The Production/Presentation split is intentional — production runtime and catalog UI have different data contracts.

### Multi-Domain / Multi-Tenant Routing (if applicable)
Not applicable — this is a repository inspection task.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: A2 — Static Asset Storage and Presentation Research
**Status**: backlog → active → completed
**Date**: YYYY-MM-DD

### What I'm About to Do
Determine how generated static assets are stored, named, referenced, and served, focusing on the RH-400 catalog render and inventory icon. Distinguish these from surface sprites and other RH-400 representations.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| Repository asset/image directories | Storage location evidence | pending |
| Code/spec references to RH-400 assets | Consumption path evidence | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
Exact paths for catalog render and inventory icon identified; existing storage/reference mechanism documented; no runtime Docker/docs dependency introduced.

### Critical Gotchas I Will Avoid
- ❌ Treating RH-400 as one image — instead ✅ Separately identifying each representation (catalog render, icon, surface sprite, etc.)
- ❌ Adding docs/ as runtime data — instead ✅ Documenting existing storage mechanism without modifying it

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1.
```

---

## Problem Statement

Determine how generated static assets are currently stored, named, referenced, and served, focusing specifically on the RH-400 catalog render and inventory icon.

**Current behavior**: Repository state must be established by evidence; do not assume the planned architecture exists in code.
**Expected behavior**: Produce only the evidence result explicitly requested — identify exact paths, naming conventions, and reference mechanisms for catalog presentation assets.

---

## Files Involved

### Primary Files — inspect or edit only as specified by this task
| File | Purpose | Key Method/Section |
|---|---|---|
| Repository asset/image directories | Storage location evidence | N/A (read-only) |
| Code/spec references to RH-400 assets | Consumption path evidence | N/A (read-only) |

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
git mv projects/galaxy_game/tasks/backlog/asset-ui/2026-08-31-HIGH-RESEARCH-ASSET-UI-A2-static-asset-storage-research.md \
       projects/galaxy_game/tasks/active/2026-08-31-HIGH-RESEARCH-ASSET-UI-A2-static-asset-storage-research.md
```

Then open the moved file and change: `status: backlog → status: active`

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-31-HIGH-RESEARCH-ASSET-UI-A2-static-asset-storage-research.md"
```

**Paste the output in chat before proceeding.** Expected: exactly one result at `active/` path.

### Step 1 — Locate asset roots

Find the existing asset/image directories and relevant configuration. Search for:
- `public/assets/`, `app/assets/images/`, or similar Rails asset paths
- Any custom asset storage directories
- Configuration files that reference asset paths

### Step 2 — Inspect RH-400 presentation assets

Identify actual catalog-render and inventory-icon files and their naming/path conventions. Specifically:
- What is the exact file path for the RH-400 catalog render?
- What is the exact file path for the RH-400 inventory icon?
- Are these separate files or derived from a single source?

### Step 3 — Trace references

Find existing code/spec references that consume or identify these files. Search for:
- References to "RH-400" in controllers, services, views
- Asset path resolution logic
- Any catalog-related code that currently displays RH-400 imagery

### Step 4 — Report

Record exact paths and file:line evidence. Do not alter files. The report should answer:
1. Where are catalog render and inventory icon stored?
2. How are they named (naming convention)?
3. How does existing code reference them?
4. What is the smallest next design question for B2 to resolve?

---

## Acceptance Criteria
- [ ] Catalog render and inventory icon are separately identified with exact file paths
- [ ] Existing storage/reference mechanism is documented with file:line evidence
- [ ] No runtime Docker/docs dependency introduced
- [ ] Findings identify the smallest next design question for B2
- [ ] Synthesis report posted to chat before any work began

---

## Stop Conditions — escalate to user immediately if:
- Asset files are missing or ambiguous (cannot determine which file is which representation)
- Existing serving architecture requires an architectural decision beyond this task's scope
- Proposed change would affect shared asset infrastructure

---

## Commit Instructions
No commit is authorized by this task unless explicitly stated in the task steps. Do not commit research or design work without separate approval.

---

## Documentation
- [x] No doc changes needed unless this task explicitly requests documentation output.

---

## Dependencies
**Blocked by**: A1
**Blocks**: A3, A4, B2
**Related tasks**: RH-400 Run 06 asset family

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
HANDOFF SUMMARY: A2 | [result] | [next action]
