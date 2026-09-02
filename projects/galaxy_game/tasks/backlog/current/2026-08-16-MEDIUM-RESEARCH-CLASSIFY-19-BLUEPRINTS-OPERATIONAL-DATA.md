---
status: backlog
priority: MEDIUM
type: research
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
created: 2026-08-16
# DISPATCH ORDERING — do not dispatch a task whose depends_on is not yet completed.
depends_on: []
blocks: []
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [x] All Step 0-N instructions are clear and actionable (not vague)
- [x] Synthesis report template is provided (copy/paste ready, not as example) — adapted for a research task
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is READY FOR DISPATCH.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-16-MEDIUM-RESEARCH-CLASSIFY-19-BLUEPRINTS-OPERATIONAL-DATA.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-16-MEDIUM-RESEARCH-CLASSIFY-19-BLUEPRINTS-OPERATIONAL-DATA.md \
         projects/galaxy_game/tasks/active/2026-08-16-MEDIUM-RESEARCH-CLASSIFY-19-BLUEPRINTS-OPERATIONAL-DATA.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks -name "2026-08-16-MEDIUM-RESEARCH-CLASSIFY-19-BLUEPRINTS-OPERATIONAL-DATA.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-08-16-RESEARCH-CLASSIFY-19-BLUEPRINTS-OPERATIONAL-DATA.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Classify 19 Renamed Blueprints — Operational Data Requirement
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: research
**Created**: 2026-08-16
**Last Updated**: 2026-09-02

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*

- **Template Conformance**: PASS (updated 2026-09-02 to conform to TASK_TEMPLATE.md)
- **Docker Wrapper Check**: N/A — no RSpec commands in this task, research only
- **MVP Alignment**: VALID — operational data gaps affect spec reliability and any task that instantiates these blueprints
- **MVP Impact Note**: Missing operational data on active units will cause runtime failures or silent no-ops when the live game loop processes them
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Read-only research/classification task, well within local Qwen's terminal/grep access
**Local attempts before cloud**: N/A
**Supervision Level**: standard

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below
4. **NEEDS_REVIEW #4**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/NEEDS_REVIEW.md` — original flag that surfaced this gap

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context

The v1→mk1 rename audit found 19 blueprints across multiple categories (propulsion, sensors, electronics, specialized, storage, industrial, mechanical, life_support, infrastructure, power_generation) that have NO matching operational_data file. Per Tracy's rule: active/deployable units need operational data; components used in construction of other things don't. This task classifies each of the 19 to determine which need operational data written (follow-up task) and which are components that don't.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Do not write operational data in this task.
- ❌ Wrong: "This one's active, I'll just write its operational data now"
- ✅ Right: Classify it, document the decision, and flag it for a separate operational-data-writing task
- Why: This is a classification/research task. Writing operational data is a separate data task with its own verification requirements.

⚠️ **GOTCHA 2**: Do not assume "no operational_data file" means "needs one."
- ❌ Wrong: "No file → must be a gap → needs data"
- ✅ Right: Check whether the blueprint is a component/subunit (used in `required_materials` of other blueprints) — components legitimately don't have operational data
- Why: Tracy's rule explicitly distinguishes active deployable units from construction components. Many of the 19 are likely components.

⚠️ **GOTCHA 3**: The `find` command in Step 1 will return ALL `*_mk1_bp.json` files, not just the 19 from the rename audit.
- ❌ Wrong: Use the full find output as the classification list
- ✅ Right: Cross-reference against the 19 specific files listed in NEEDS_REVIEW #4 (or the rename audit log) to scope the classification
- Why: The codebase has many mk1 blueprints that already have operational data. Only the 19 from the audit are in scope.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before running any commands or modifying any files, save a synthesis report as MD to the summaries folder covering:
- The 19 blueprints you've identified (exact paths)
- Your initial read on which are likely active vs component
- Your classification criteria (what makes something "active deployable" vs "component")
- Your verification plan (how you'll confirm each classification)

---

## Problem Statement

**Current behavior**: 19 blueprints from the v1→mk1 rename audit have no matching operational_data file. It is unclear which of these are active deployable units (needing operational data) vs components/subunits (legitimately without operational data).

**Expected behavior**: A clear classification of all 19 with rationale, a list of those needing operational data (for follow-up task), and documentation of those that don't.

---

## Files Involved

### Primary Files — read/investigate, do not edit (this is research-only)
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/data/json-data/blueprints/` (19 specific files from rename audit) | Blueprints to classify | Full JSON content (category, display_name, capabilities) |
| `galaxy_game/data/json-data/operational_data/` | Existing operational data files | Check which of the 19 have a match |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/app/` (grep for blueprint IDs) | Determine which blueprints are instantiated in code (active) vs only referenced as materials (component) |
| `galaxy_game/spec/` (grep for blueprint IDs) | Determine which are tested as active units |
| `agent-tasks/projects/galaxy_game/NEEDS_REVIEW.md` | Original flag (#4) with the list of 19 |
| `galaxy_game/data/json-data/blueprints/` (other blueprints' `required_materials`) | Check if the 19 appear as components in other blueprints |

### Migration
- [x] No migration needed — research/read-only task

---

## Implementation Steps

> This is a research/classification task — "implementation" here means investigation, not code changes.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
(See Agent Dispatch Interface above — same procedure applies.)

### Step 1 — Inventory the 19 blueprints
```bash
find galaxy_game/data/json-data/blueprints -name "*_mk1_bp.json" | sort
```
Cross-reference against NEEDS_REVIEW #4 to identify the specific 19 in scope. List each with its category and display name.

### Step 2 — Classification pass
For each of the 19 blueprints, determine:
- Is this an active deployable unit (harvester, transport, habitat, power plant, etc.)?
- Or is this a component/subunit (clamp, connector, module, processor)?
- Does it appear in any `required_materials` or `component` references of other blueprints?
- Is it instantiated in code (grep `app/` for the blueprint ID)?

### Step 3 — Document decisions
Create a classification table:

| Blueprint ID | Category | Display Name | Classification | Rationale |
|-------------|----------|--------------|----------------|-----------|
| ... | ... | ... | active/component | ... |

### Step 4 — File follow-up tasks (if any)
For blueprints classified as "active" — list them for a separate operational-data-writing task. Do NOT write operational data in this task; just identify what's needed.

### Step 5 — Save findings
Save classification table + follow-up list to:
`/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-08-16-RESEARCH-CLASSIFY-19-BLUEPRINTS-OPERATIONAL-DATA.md`

---

## Acceptance Criteria
- [ ] All 19 blueprints identified with exact paths
- [ ] Each classified as active/component with rationale
- [ ] Codebase cross-reference documented (which are instantiated in code)
- [ ] List of blueprints needing operational data produced (for follow-up task)
- [ ] Findings saved to summaries/ folder
- [ ] No operational data written — research only
- [ ] No files modified or deleted

---

## Stop Conditions — escalate to user immediately if:
- More than 19 blueprints are in scope (scope expansion — confirm with user)
- A blueprint's classification is ambiguous (neither clearly active nor component)
- Code references a "component" blueprint as an active unit (contradiction needs resolution)
- Any architectural decision is required about what constitutes "active" vs "component"

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container.

**Task file move on completion:**
```bash
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy_game/tasks/active/2026-08-16-MEDIUM-RESEARCH-CLASSIFY-19-BLUEPRINTS-OPERATIONAL-DATA.md \
       projects/galaxy_game/tasks/completed/2026-08/2026-08-16-MEDIUM-RESEARCH-CLASSIFY-19-BLUEPRINTS-OPERATIONAL-DATA.md
git commit -m "chore: move 19-blueprint operational data classification to completed/"
```

---

## Documentation
- [x] No doc changes needed (research only)
- [ ] If active units found: flag in NEEDS_REVIEW.md for follow-up operational-data task creation

---

## Dependencies
**Blocked by**: none
**Blocks**: Operational data writing task (to be filed after this classification is complete)
**Related tasks**: NEEDS_REVIEW #4 (original flag); CNT Fabricator naming collision (same rename audit batch)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Final test result**: N/A (research only)

### What was changed
- Classification table created in summaries/

### Issues discovered

### Follow-up tasks needed

### Lessons learned

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY:
