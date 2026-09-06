---
status: backlog
priority: MEDIUM
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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-31-MEDIUM-RESEARCH-ASSET-UI-A6-icon-bible-dependency-research.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-31-MEDIUM-RESEARCH-ASSET-UI-A6-icon-bible-dependency-research.md \
         projects/galaxy_game/tasks/active/2026-08-31-MEDIUM-RESEARCH-ASSET-UI-A6-icon-bible-dependency-research.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-31-MEDIUM-RESEARCH-ASSET-UI-A6-icon-bible-dependency-research.md"
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

# TASK: A6 — Icon Bible Dependency Research
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: research
**Created**: 2026-08-31
**Last Updated**: 2026-08-31

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: PASS
- **Docker Wrapper Check**: N/A — research task, no RSpec execution required.
- **MVP Alignment**: VALID — Icon Bible status affects B2's catalog presentation contract and C3/C4's UI implementation.
- **MVP Impact Note**: If the Icon Bible is a blocking dependency, it must be flagged before catalog icon work proceeds.
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Repository inspection to locate references and assess blocking status.
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. `/Users/tam0013/Documents/git/agent-tasks/README.md`
2. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. This task file

---

## Context

Determine whether the referenced Icon Bible is a blocking dependency for catalog/inventory icon work or a documentation gap that can remain unresolved. Several canonical documents reference an Icon Bible, but its absence has been reported. We need evidence before allowing it to shape B2. Do not create the missing Icon Bible. Do not invent icon rules. If the catalog can proceed from existing canonical fields, say so with evidence.

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

⚠️ **GOTCHA 1**: Do not create the missing Icon Bible.
- ❌ Wrong: Generate icon rules or create an Icon Bible document as part of this task.
- ✅ Right: Document what exists, flag the gap, and assess whether existing canonical fields are sufficient for catalog work.
- Why: This is a research/assessment task, not a documentation creation task.

⚠️ **GOTCHA 2**: Do not invent icon rules from thin air.
- ❌ Wrong: Propose icon design rules or standards without evidence from existing specifications.
- ✅ Right: Search for any existing icon rules in canonical documents; if none exist, report that the gap is unresolvable without a product decision.
- Why: Icon rules are a product/design decision, not an implementation detail.

### Multi-Domain / Multi-Tenant Routing (if applicable)
Not applicable — this is a repository inspection task.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: A6 — Icon Bible Dependency Research
**Status**: backlog → active → completed
**Date**: YYYY-MM-DD

### What I'm About to Do
Determine whether the Icon Bible is a blocking dependency for catalog/inventory icon work or a documentation gap. Classify as blocking, non-blocking, or stale-reference documentation gap.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| Documents referencing "Icon Bible" | Reference locations | pending |
| Existing icon/asset specifications | Substitute rules evidence | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
Icon Bible references identified; existing substitute rules checked; blocking status explicitly classified (blocking/non-blocking/stale-reference); no documentation created.

### Critical Gotchas I Will Avoid
- ❌ Creating the Icon Bible or inventing icon rules — instead ✅ Documenting what exists and flagging the gap
- ❌ Allowing the Icon Bible to block B2 without evidence — instead ✅ Assessing whether existing canonical fields suffice

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1.
```

---

## Problem Statement

Determine whether the referenced Icon Bible is a blocking dependency for catalog/inventory icon work or a documentation gap that can remain unresolved.

**Current behavior**: Several canonical documents reference an Icon Bible, but its absence has been reported. We need evidence before allowing it to shape B2.
**Expected behavior**: Classify the Icon Bible as blocking, non-blocking, or stale-reference documentation gap with evidence. Do not create documentation.

---

## Files Involved

### Primary Files — inspect or edit only as specified by this task
| File | Purpose | Key Method/Section |
|---|---|---|
| Documents referencing "Icon Bible" | Reference locations | N/A (read-only) |
| Existing icon/asset specifications | Substitute rules evidence | N/A (read-only) |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| A2 (Static asset storage research) | Catalog render/icon paths |
| A3 (RH-400 asset family mapping) | Icon representation in asset family |

### Migration
- [x] No migration needed

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT. Do not proceed to Step 1 until both are done and approved.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/current/2026-08-31-MEDIUM-RESEARCH-ASSET-UI-A6-icon-bible-dependency-research.md \
       projects/galaxy_game/tasks/active/2026-08-31-MEDIUM-RESEARCH-ASSET-UI-A6-icon-bible-dependency-research.md
```

Then open the moved file and change: `status: backlog → status: active`

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-31-MEDIUM-RESEARCH-ASSET-UI-A6-icon-bible-dependency-research.md"
```

**Paste the output in chat before proceeding.** Expected: exactly one result at `active/` path.

### Step 1 — Search references

Find every current reference to the Icon Bible and the expected location/name. Search for:
- "Icon Bible" or "icon_bible" or "icon-bible" in all docs and code
- Any document that specifies icon design rules or standards
- The expected file path or format of the Icon Bible

### Step 2 — Search for replacement rules

Determine whether icon rules exist elsewhere. Specifically:
- Do Visual Profiles contain icon specifications?
- Do asset-generation specs define icon rules?
- Are there any existing icon images with implicit rules?

### Step 3 — Assess blocking status

Identify exactly which planned catalog behavior would depend on the missing document. Classify as:
- **Blocking**: Catalog icon work cannot proceed without Icon Bible rules
- **Non-blocking**: Existing canonical fields are sufficient for catalog icon work
- **Stale-reference documentation gap**: The Icon Bible was planned but is no longer relevant

### Step 4 — Report

Classify the Icon Bible status with evidence. The report should answer:
1. Where is the Icon Bible referenced (file:line)?
2. What icon rules exist elsewhere (if any)?
3. Is it blocking, non-blocking, or a stale reference?
4. What does B2 need to know about this gap?

---

## Acceptance Criteria
- [ ] Icon Bible references identified with file:line evidence
- [ ] Existing substitute rules checked and documented
- [ ] Blocking status explicitly classified (blocking/non-blocking/stale-reference)
- [ ] No documentation created (no Icon Bible generated)
- [ ] Synthesis report posted to chat before any work began

---

## Stop Conditions — escalate to user immediately if:
- References conflict materially with existing icon behavior
- A product decision is required to define icon rules (beyond research scope)
- The Icon Bible reference cannot be located anywhere in the repository

---

## Commit Instructions
No commit is authorized by this task unless explicitly stated in the task steps. Do not commit research or design work without separate approval.

---

## Documentation
- [x] No doc changes needed unless this task explicitly requests documentation output.

---

## Dependencies
**Blocked by**: none
**Blocks**: B2
**Related tasks**: A2 (static asset storage — icon paths)

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
HANDOFF SUMMARY: A6 | [result] | [next action]
