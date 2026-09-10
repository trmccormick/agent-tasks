---
status: active
priority: HIGH
type: research
system_domain: OTHER
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIBLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-31-HIGH-RESEARCH-ASSET-UI-A1-asset-registry-reality-check.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-31-HIGH-RESEARCH-ASSET-UI-A1-asset-registry-reality-check.md \
         projects/galaxy_game/tasks/active/2026-08-31-HIGH-RESEARCH-ASSET-UI-A1-asset-registry-reality-check.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-31-HIGH-RESEARCH-ASSET-UI-A1-asset-registry-reality-check.md"
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

# TASK: A1 — Asset Registry Reality Check
**Status**: BACKLOG
**Priority**: HIGH
**Type**: research
**Created**: 2026-08-31
**Last Updated**: 2026-08-31

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*

- **Template Conformance**: PASS — includes YAML frontmatter and the required Agent Dispatch Interface immediately afterward.
- **Docker Wrapper Check**: N/A — research task, no RSpec execution required.
- **MVP Alignment**: VALID — asset registry evidence is prerequisite for all B/C/D tasks in this workstream.
- **MVP Impact Note**: Asset/UI foundation for the Luna + Earth-import MVP; catalog presentation depends on knowing what exists.
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Repository inspection and implementation access.
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context

The asset-generation architecture already defines Asset Registry as a concept. We need repository evidence before mapping catalog assets onto it. This task determines what specification and/or implementation actually exists, where it lives, and how it currently relates to Visual Definition. Do not design or implement a new registry.

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

⚠️ **GOTCHA 1**: Do not invent an asset-to-visual relationship.
- ❌ Wrong: Propose a new registry model that duplicates Visual Definition's responsibilities.
- ✅ Right: Record what exists in the repository and identify the smallest mapping question B1 must resolve.
- Why: Visual Definition already owns `asset_id`, `asset_family`, and visual attributes. The registry must not duplicate their responsibilities.

⚠️ **GOTCHA 2**: Do not treat docs/ as Rails runtime data.
- ❌ Wrong: Add Docker mounts for docs/ or treat documentation as application data.
- ✅ Right: Treat all docs/ content as development-time source material only.
- Why: Asset-generation documentation is source specification, not runtime dependency.

### Multi-Domain / Multi-Tenant Routing (if applicable)
Not applicable — this is a repository inspection task.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: A1 — Asset Registry Reality Check
**Status**: backlog → active → completed
**Date**: YYYY-MM-DD

### What I'm About to Do
Determine what Asset Registry specification and/or implementation exists and how it relates to the existing Visual Definition Template. Do not design or implement a new registry.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| Repository files identified during execution | Evidence gathering | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
A concise evidence-based report identifying registry locations, documenting its role, and mapping its relationship to Visual Definition. No source files modified.

### Critical Gotchas I Will Avoid
- ❌ Inventing a new registry model — instead ✅ Recording existing specification/implementation with file:line evidence
- ❌ Treating docs/ as runtime data — instead ✅ Treating all docs/ as development-time source only
- ❌ Modifying Visual Definition, Visual Profiles, or Render Templates — instead ✅ Documenting overlap/gaps for B1 to resolve

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement

Determine what Asset Registry specification and/or implementation actually exists, where it lives, and how it currently relates to Visual Definition. Do not design or implement a new registry.

**Current behavior**: Repository state must be established by evidence; do not assume the planned architecture exists in code.
**Expected behavior**: Produce only the evidence/design result explicitly requested — identify what exists, document its role, flag overlap/gaps with Visual Definition.

---

## Files Involved

### Primary Files — inspect or edit only as specified by this task
| File | Purpose | Key Method/Section |
|---|---|---|
| Repository files identified during execution | Evidence gathering | N/A (read-only) |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| Existing asset-generation specifications in `docs/` | Establish canonical asset roles and locked generation architecture |
| Blueprint/Operational Data examples | Establish canonical game-data relationship |
| Visual Definition Template v1.0 (if exists) | Compare registry concepts against existing visual definition fields |

### Migration
- [x] No migration needed

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT. Do not proceed to Step 1 until both are done and approved.

All agents: follow these steps exactly in order. Do not skip steps or reorder them.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

This must be done before reading the task content, before synthesis, before any other action.

```bash
# From inside agent-tasks repo root:
git mv projects/galaxy_game/tasks/backlog/current/2026-08-31-HIGH-RESEARCH-ASSET-UI-A1-asset-registry-reality-check.md \
       projects/galaxy_game/tasks/active/2026-08-31-HIGH-RESEARCH-ASSET-UI-A1-asset-registry-reality-check.md
```

Then open the moved file and change the YAML status field:
```
status: backlog  →  status: active
```

Then verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-31-HIGH-RESEARCH-ASSET-UI-A1-asset-registry-reality-check.md"
```

**Paste the output of the find command in chat before proceeding.**
Expected: exactly one result, at the `active/` path.

> ❌ Do NOT proceed if two results appear — a stale copy exists and must be removed first.
> ❌ Do NOT use cp or plain mv — always git mv for tracked files.

### Step 1 — Locate the registry

Search the repository for Asset Registry specifications, classes, tables, services, schemas, and references. Use grep/search to find any file mentioning "asset_registry", "AssetRegistry", "asset.registry", or related terms. Record exact file paths and line numbers.

### Step 2 — Map its current role

Record file:line evidence showing what the registry currently stores or is intended to store. Distinguish between:
- Specification (docs/design documents)
- Implementation (models, services, database tables)
- Neither (planned but not yet built)

### Step 3 — Compare with Visual Definition

Identify overlap, gaps, and the smallest mapping question that B1 must resolve. Specifically check:
- Does the registry store `asset_id`, `asset_family`, or visual attributes?
- Does it reference Visual Definition fields?
- What would a minimal mapping look like without duplicating responsibilities?

### Step 4 — Report

Write a synthesis report with file:line evidence. Do not modify source files. The report should answer:
1. What exists (specification, implementation, or neither)?
2. Where does it live (file paths)?
3. How does it relate to Visual Definition (overlap/gaps)?
4. What is the smallest question B1 must resolve?

---

## Acceptance Criteria
- [ ] Existing registry specification/code locations identified with file:line evidence
- [ ] Relationship to Visual Definition documented (overlap, gaps, mapping questions)
- [ ] No new registry model proposed or designed
- [ ] No source files modified
- [ ] Synthesis report posted to chat before any work began

---

## Stop Conditions — escalate to user immediately if:
- Any registry implementation exists but conflicts with locked architecture
- A database schema change appears necessary (not just documentation)
- Evidence is insufficient to distinguish specification from implementation
- Canonical sources disagree on an asset role requiring architectural approval

---

## Commit Instructions
No commit is authorized by this task unless explicitly stated in the task steps. Do not commit research or design work without separate approval.

---

## Documentation
- [x] No doc changes needed unless this task explicitly requests documentation output.
- [ ] Flag doc gap if a required specification is missing; do not create unrelated documentation.

---

## Dependencies
**Blocked by**: none
**Blocks**: A2, B1
**Related tasks**: ASSET_GENERATION_PIPELINE_VALIDATION.md

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
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: A1 | [result] | [next action]
