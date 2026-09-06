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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-16-MEDIUM-INVESTIGATE-CNT-FABRICATOR-NAMING-COLLISION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-16-MEDIUM-INVESTIGATE-CNT-FABRICATOR-NAMING-COLLISION.md \
         projects/galaxy_game/tasks/active/2026-08-16-MEDIUM-INVESTIGATE-CNT-FABRICATOR-NAMING-COLLISION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks -name "2026-08-16-MEDIUM-INVESTIGATE-CNT-FABRICATOR-NAMING-COLLISION.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-08-16-RESEARCH-CNT-FABRICATOR-NAMING-COLLISION.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Investigate CNT Fabricator Naming Collision
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: research
**Created**: 2026-08-16
**Last Updated**: 2026-09-01

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*

- **Template Conformance**: PASS (updated 2026-09-01 to conform to TASK_TEMPLATE.md)
- **Docker Wrapper Check**: N/A — no RSpec commands in this task, research only
- **MVP Alignment**: VALID — data integrity issue in blueprint naming; affects any task that references CNT fabricator blueprints
- **MVP Impact Note**: Naming collision could cause wrong blueprint to be loaded in production if code references the wrong file path
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Read-only research task, well within local Qwen's terminal/grep access
**Local attempts before cloud**: N/A
**Supervision Level**: standard

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below
4. **NEEDS_REVIEW #5**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/NEEDS_REVIEW.md` — original flag that surfaced this collision

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context

The rename audit (v1→mk1 batch) surfaced two separate CNT fabricator blueprint families in different directories with near-identical names. This is a data-integrity investigation: determine whether they are duplicates, distinct deployment profiles, or a naming collision that needs disambiguation. No code changes — research and recommendation only.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Do not assume the two files are duplicates based on name similarity alone.
- ❌ Wrong: "Both say cnt_fabricator_mk1, must be the same thing"
- ✅ Right: Compare actual JSON content (materials, costs, capabilities, category) before concluding
- Why: The codebase has legitimate cases of same-unit-name with different deployment contexts (e.g., orbital vs surface variants)

⚠️ **GOTCHA 2**: Do not delete or rename any file during this task.
- ❌ Wrong: "They're identical, I'll remove the stale one"
- ✅ Right: Document the finding and recommendation; let a separate task execute the change
- Why: This is research-only. Deletion/renaming is a data-integrity operation that needs its own task with proper verification.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before running any commands or modifying any files, save a synthesis report as MD to the summaries folder covering:
- What files you found (exact paths)
- Your initial read on whether they're duplicates or distinct
- Your comparison plan (which fields to extract)
- Your verification plan (how you'll confirm the recommendation is safe)

---

## Problem Statement

**Current behavior**: Two CNT fabricator blueprint files exist with near-identical names in different directories:
- `data/json-data/blueprints/industrial/cnt_fabricator_unit_mk1_bp.json`
- `data/json-data/blueprints/production/fabricators/cnt_fabricator_mk1_bp.json` (plus mk2/mk3)

It is unclear whether these represent the same unit with two deployment profiles, true duplicates, or two genuinely distinct things that happen to share a name.

**Expected behavior**: A clear determination (duplicate / distinct / stale) with a recommendation (remove / rename / document) that prevents future confusion.

---

## Files Involved

### Primary Files — read/investigate, do not edit (this is research-only)
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/data/json-data/blueprints/industrial/cnt_fabricator_unit_mk1_bp.json` | CNT fabricator from v1→mk1 rename batch | Full JSON content |
| `galaxy_game/data/json-data/blueprints/production/fabricators/cnt_fabricator_mk1_bp.json` | Pre-existing CNT fabricator (mk1/mk2/mk3 family) | Full JSON content |
| `galaxy_game/data/json-data/blueprints/production/fabricators/cnt_fabricator_mk2_bp.json` | mk2 variant (if exists) | Full JSON content |
| `galaxy_game/data/json-data/blueprints/production/fabricators/cnt_fabricator_mk3_bp.json` | mk3 variant (if exists) | Full JSON content |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/app/` (grep for `cnt_fabricator`) | Determine which file(s) are actually referenced in code |
| `galaxy_game/spec/` (grep for `cnt_fabricator`) | Determine which file(s) are tested |
| `agent-tasks/projects/galaxy_game/NEEDS_REVIEW.md` | Original flag (#5) that surfaced this collision |

### Migration
- [x] No migration needed — research/read-only task

---

## Implementation Steps

> This is a research task — "implementation" here means investigation, not code changes.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
(See Agent Dispatch Interface above — same procedure applies.)

### Step 1 — Locate all CNT fabricator files
```bash
find galaxy_game/data/json-data/blueprints -name "*cnt*fabricator*" | sort
```
Document exact paths found.

### Step 2 — Side-by-side comparison
For each file, extract and compare:
- `display_name` / `designation`
- `required_materials` (quantities and types)
- `production_time` / `build_cost`
- `output_resources` / capabilities
- `category` / `subcategory`
- Any deployment-specific fields

Produce a comparison table.

### Step 3 — Cross-reference check
Search the codebase for references to each file's ID:
```bash
grep -r "cnt_fabricator" galaxy_game/app/ galaxy_game/spec/ --include="*.rb" | grep -v "_spec.rb:"
```
Determine: are both referenced? Is one dead code? Which one does production use?

### Step 4 — Decision and recommendation
Based on comparison:
- **If identical**: Recommend removing the stale one (likely the `industrial/` one from the rename batch)
- **If distinct but similar names**: Recommend renaming one for clarity (e.g., `cnt_fabricator_industrial_mk1_bp.json` vs `cnt_fabricator_production_mk1_bp.json`)
- **If intentionally distinct with clear purpose**: Document the distinction in a note

### Step 5 — Save findings
Save comparison table + recommendation to:
`/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-08-16-RESEARCH-CNT-FABRICATOR-NAMING-COLLISION.md`

---

## Acceptance Criteria
- [ ] Both files located and full JSON content extracted
- [ ] Side-by-side comparison table produced (all key fields)
- [ ] Codebase cross-reference documented (which file(s) are actually used)
- [ ] Clear recommendation made (remove / rename / document) with rationale
- [ ] Findings saved to summaries/ folder
- [ ] No files modified or deleted — research only

---

## Stop Conditions — escalate to user immediately if:
- More than 2 CNT fabricator files are found (scope expansion)
- Code references both files in conflicting ways (architectural decision needed)
- The "duplicate" is actually referenced in production code paths (removal would break things)
- Any architectural decision is required beyond simple rename/removal

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container.

**Task file move on completion:**
```bash
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy_game/tasks/active/2026-08-16-MEDIUM-INVESTIGATE-CNT-FABRICATOR-NAMING-COLLISION.md \
       projects/galaxy_game/tasks/completed/2026-08/2026-08-16-MEDIUM-INVESTIGATE-CNT-FABRICATOR-NAMING-COLLISION.md
git commit -m "chore: move CNT fabricator naming collision research to completed/"
```

---

## Documentation
- [x] No doc changes needed (research only)
- [ ] If rename recommended: flag in NEEDS_REVIEW.md for follow-up task creation

---

## Dependencies
**Blocked by**: none
**Blocks**: none currently known
**Related tasks**: NEEDS_REVIEW #5 (original flag); any future CNT fabricator blueprint work

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Final test result**: N/A (research only)

### What was changed
- Findings doc created in summaries/

### Issues discovered

### Follow-up tasks needed

### Lessons learned

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY:
