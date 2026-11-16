---
status: backlog
priority: MEDIUM
type: documentation
system_domain: ECONOMY
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [x] All Step 0-N instructions are clear and actionable (not vague)
- [x] Synthesis report template is provided (copy/paste ready, not as example)
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as their startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/economy/2026-09-13-MEDIUM-DOCS-WIKI-SYNC-AND-CLEANUP.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/economy/2026-09-13-MEDIUM-DOCS-WIKI-SYNC-AND-CLEANUP.md \
         projects/galaxy_game/tasks/active/2026-09-13-MEDIUM-DOCS-WIKI-SYNC-AND-CLEANUP.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-13-MEDIUM-DOCS-WIKI-SYNC-AND-CLEANUP.md"
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

# TASK: Relocate Misplaced Economic Docs and Sync to Wiki Structure
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: documentation
**Created**: 2026-09-13
**Last Updated**: 2026-09-13

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*
*This section is NOT sent to agents — it's for human task management only*

- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS
- **MVP Alignment**: VALID — Keeps task directories clean of non-task documentation and preserves knowledge in the wiki.
- **MVP Impact Note**: Ensures reference guides (`npc_economy_lifecycle.md`, `economy_models.md`) are preserved in `docs/` rather than cluttering `agent-tasks/`.
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Primary local worker with terminal/git access.
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

Several economic reference documents and planning files are currently misplaced inside the `agent-tasks/` directory structure:
1. Misplaced planning overview file inside `agent-tasks/projects/galaxy_game/tasks/backlog/economy/`
2. `npc_economy_lifecycle.md` inside `agent-tasks/projects/galaxy_game/economy/` (20KB)
3. `economy_models.md` inside `agent-tasks/projects/galaxy_game/economy/`

These files contain valuable documentation that should be preserved and integrated into the canonical documentation tree (`docs/wiki_reorganization/economy/`), while removing clutter from the task management folders.

**Verified**: All source files exist on disk and are real documentation (not fabricated paths). These two files are preserved-but-unverified source material — not confirmed-redundant leftovers. This task relocates them for safekeeping; it does not close the question of whether `07-npc-economy-lifecycle.md` fully captures their content.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Use `git mv` when relocating tracked documentation files to preserve their git history.
- ❌ Wrong: `mv` or `rm` on tracked files
- ✅ Right: `git mv <source> <destination>`
- Why: Plain `mv` breaks git's file tracking; `rm` loses history

⚠️ **GOTCHA 2**: Do not delete documentation content without verifying that it has been safely moved or synced to the canonical `docs/` directory structure.
- ❌ Wrong: Delete before confirming destination has the files
- ✅ Right: Move first, verify, then confirm source is empty

### Multi-Domain / Multi-Tenant Routing (if applicable)

| Domain/Route | Purpose | What Features Available | What NOT Available |
|---|---|---|---|
| Admin domain | System config, tenant creation | User management, settings | NO feature work, NO content repos |
| Tenant domain | Repository operations | Works, collections, batch edit | NO system config |

> If confused, ask: "Which domain am I supposed to be testing on?" If you're getting 404 or permission errors, you may be on the wrong domain.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: 2026-09-13-MEDIUM-DOCS-WIKI-SYNC-AND-CLEANUP.md
**Status**: backlog → active
**Date**: 2026-09-13

### What I'm About to Do
[2-3 sentences: the goal, the migration method, the verification criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `projects/galaxy_game/economy/npc_economy_lifecycle.md` | Source lifecycle doc (20KB) | pending |
| `projects/galaxy_game/economy/economy_models.md` | Source models doc | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
Misplaced documentation files successfully moved to `docs/wiki_reorganization/economy/source-archive/`, task folders cleaned, and git history preserved.

### Critical Gotchas I Will Avoid
- ❌ Using plain rm or mv on tracked files — instead ✅ Use `git mv` to preserve git history
- ❌ Deleting before verifying destination — instead ✅ Move first, verify second

---

**SYNTHESIS COMPLETE.** Ready to proceed with file migration.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement

The `agent-tasks/` folder contains non-task documentation files (`npc_economy_lifecycle.md`, `economy_models.md`, and backlog planning notes) which violate the repository housekeeping mandate separating actionable tasks from general documentation.

**Current behavior**: Documentation files clutter task and backlog folders.  
**Expected behavior**: All economic documentation is consolidated under `docs/wiki_reorganization/economy/` and `agent-tasks/` contains strictly executable tasks.

---

## Files Involved

### Source Files — relocate these via `git mv`
| File | Purpose | Verified Size |
|---|---|---|
| `projects/galaxy_game/economy/npc_economy_lifecycle.md` | NPC economy lifecycle reference doc (20KB) — raw source, not verified against 07-npc-economy-lifecycle.md | ✅ Exists |
| `projects/galaxy_game/economy/economy_models.md` | Economy models data model inventory — raw source, not verified against 07-npc-economy-lifecycle.md | ✅ Exists |

### Destination
| Path | Purpose |
|---|---|
| `docs/wiki_reorganization/economy/source-archive/` | Raw source material archive (not part of canonical 01-07 structure) |

### Stray Planning Files in Backlog Economy — move or archive
| File | Status |
|---|---|
| `projects_galaxy_game_backlog_economy_2026-09-07-PLANNING-OVERVIEW-ECONOMIC-SUBSYSTEM.md` | Planning doc — top level is fine for planning material |
| `2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-EVALUATE.md` | Task file — keep in backlog/economy/ (valid task) |

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

All agents: follow these steps exactly in order.
- Do not skip steps or reorder them.
- Do not proceed to the next step if the current step has not produced a clean result.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

This must be done before reading the task content, before synthesis, before any other action.

```bash
# From inside agent-tasks repo root:
git mv projects/galaxy_game/tasks/backlog/economy/2026-09-13-MEDIUM-DOCS-WIKI-SYNC-AND-CLEANUP.md \
       projects/galaxy_game/tasks/active/2026-09-13-MEDIUM-DOCS-WIKI-SYNC-AND-CLEANUP.md
```

Then open the moved file and change the YAML status field:
```
status: backlog  →  status: active
```

Then verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-09-13-MEDIUM-DOCS-WIKI-SYNC-AND-CLEANUP.md"
```

**Paste the output of the find command in chat before proceeding.**
Expected: exactly one result, at the `active/` path.

> ❌ Do NOT proceed if two results appear — a stale copy exists and must be removed first.
> ❌ Do NOT use cp or plain mv — always git mv for tracked files.

### Step 1 — Ensure Destination Directories Exist

```bash
mkdir -p docs/wiki_reorganization/economy/source-archive/
```

### Step 2 — Relocate Raw Source Files to source-archive/ via git mv

```bash
git mv projects/galaxy_game/economy/npc_economy_lifecycle.md docs/wiki_reorganization/economy/source-archive/
git mv projects/galaxy_game/economy/economy_models.md docs/wiki_reorganization/economy/source-archive/
```

### Step 3 — Move Stray Planning File from Backlog Economy

```bash
git mv projects/galaxy_game/tasks/backlog/economy/projects_galaxy_game_backlog_economy_2026-09-07-PLANNING-OVERVIEW-ECONOMIC-SUBSYSTEM.md docs/wiki_reorganization/economy/
```

**Note**: `2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-EVALUATE.md` is a valid task file — keep it in `backlog/economy/`.

### Step 4 — Verify Repository Tree

```bash
# Confirm source directory is empty
echo "=== Source economy/ (should be empty) ==="
ls projects/galaxy_game/economy/ 2>&1

# Confirm destination has the files
echo "=== Destination source-archive/ ==="
ls docs/wiki_reorganization/economy/source-archive/

# Confirm stray planning file moved
echo "=== Stray backlog economy (should only have valid task) ==="
find projects/galaxy_game/tasks/backlog/economy -name "*.md" | sort
```

---

## Acceptance Criteria
- [ ] Misplaced documentation files successfully moved to `docs/wiki_reorganization/economy/source-archive/` using `git mv`
- [ ] Stray planning file moved from backlog economy to `docs/wiki_reorganization/economy/`
- [ ] Valid task file (`2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-EVALUATE.md`) remains in `backlog/economy/`
- [ ] Changes committed cleanly to git

---

## Stop Conditions — escalate to user immediately if:
- Fix causes new failures in specs you did not touch
- Same failure persists after two attempts
- Root cause is in a shared concern, base class, or factory used across many specs
- A database migration is needed that wasn't anticipated
- Any architectural decision is required
- Fix requires changing more files than the task specifies

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add docs/wiki_reorganization/economy/
git commit -m "docs: relocate misplaced economy lifecycle and model docs to wiki source-archive"
git push
```

**Task file move on completion:**
```bash
# Tracked file (already committed): use git mv
git mv projects/galaxy_game/tasks/active/2026-09-13-MEDIUM-DOCS-WIKI-SYNC-AND-CLEANUP.md projects/galaxy_game/tasks/completed/2026-09/2026-09-13-MEDIUM-DOCS-WIKI-SYNC-AND-CLEANUP.md

# New/untracked file (just created this session): move with filesystem, then add the final path
mv projects/galaxy_game/tasks/active/2026-09-13-MEDIUM-DOCS-WIKI-SYNC-AND-CLEANUP.md projects/galaxy_game/tasks/completed/2026-09/2026-09-13-MEDIUM-DOCS-WIKI-SYNC-AND-CLEANUP.md
git add projects/galaxy_game/tasks/completed/2026-09/2026-09-13-MEDIUM-DOCS-WIKI-SYNC-AND-CLEANUP.md

git commit -m "chore: move [FILENAME] to completed/"
```

---

## Documentation
- [ ] Economy documentation properly indexed in `docs/wiki_reorganization/economy/source-archive/`

---

## Dependencies
**Blocked by**: none  
**Blocks**: none  
**Related tasks**: none

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

**Required follow-up task**: Create a new task to diff `docs/wiki_reorganization/economy/07-npc-economy-lifecycle.md` line-by-line against the archived source files (`npc_economy_lifecycle.md`, `economy_models.md`) and confirm whether 07 fully captures their content. This is the open question that this relocation task does not resolve.

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
