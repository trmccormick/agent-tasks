---
status: backlog
priority: LOW
type: refactor
system_domain: MANUFACTURING
mvp_alignment: OTHER
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent** — but this task is DIAGNOSIS ONLY, no code changes.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/2026-07/2026-07-26-LOW-REFACTOR-MANUFACTURING-SERVICE-DUPLICATE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/2026-07/2026-07-26-LOW-REFACTOR-MANUFACTURING-SERVICE-DUPLICATE.md \
         projects/galaxy_game/tasks/active/2026-07-26-LOW-REFACTOR-MANUFACTURING-SERVICE-DUPLICATE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-26-LOW-REFACTOR-MANUFACTURING-SERVICE-DUPLICATE.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Manufacturing::Service — likely dead duplicate of ManufacturingService
**Status**: BACKLOG
**Priority**: LOW
**Type**: refactor
**Created**: 2026-07-26
**Last Updated**: 2026-07-26

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: PASS
- **Docker Wrapper Check**: N/A — no specs run in this task
- **MVP Alignment**: VALID — low-priority cleanup, not blocking Luna MVP
- **MVP Impact Note**: No direct MVP impact; resolves a duplicate-service question surfaced during the AI Manager Service Inventory audit.
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Read-only investigation (git blame, grep, call-chain tracing) — well within local worker capability.
**Local attempts before cloud**: N/A
**Supervision Level**: standard

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

---

## Context
During the AI Manager Service Inventory audit (2026-07-26), two services were found doing the same job: `ManufacturingService` (`app/services/manufacturing_service.rb`) validates blueprints and creates unit assembly jobs, and is confirmed live in production (called from `market_stabilization_service.rb` and `resource_planner.rb`). `Manufacturing::Service` (`app/services/manufacturing/service.rb`) does the same thing but has zero production callers — only its own spec references it.

An older analysis document, `CORE_CONCEPT_MAP.md` (`docs/wiki_reorganization/analysis/`, created 2026-07-16), lists `Manufacturing::Service` as a joint "likely owner" of the manufacturing chain. Treat that as weak, unverified evidence, not a documented decision — the same file carries other known-stale claims (e.g. an outdated "89→8" AI Manager service count that's since been corrected to 121), and uses hedged "likely owner" language throughout rather than confirmed fact.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `docs/architecture/manufacturing/MANUFACTURING_SYSTEM_OVERVIEW.md` — check whether this doc agrees with the code or is also stale
- `docs/architecture/isru/README.md` — same check

> If a doc doesn't exist for this area, do not create one during this task.
> Flag the gap in your completion report instead.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Do not delete or consolidate either file in this task.
- ❌ Wrong: Removing `Manufacturing::Service` because the caller-grep looks conclusive.
- ✅ Right: This task only gathers evidence (git history, doc cross-check). A separate task will make the removal/consolidation decision.
- Why: The CORE_CONCEPT_MAP.md mention means this could be an intended-but-unfinished migration rather than simple duplication — worth confirming before anything is deleted.

⚠️ **GOTCHA 2**: Don't trust CORE_CONCEPT_MAP.md's "Likely owner" claims at face value.
- ❌ Wrong: Treating the doc's ownership claim as equal weight to the caller-grep evidence.
- ✅ Right: Use it only as a lead to investigate (was there ever a migration attempt?), not as a fact to reconcile against.
- Why: The same document contains other confirmed-stale numbers from before the project's focus shifted to RSpec stabilization and the Luna MVP.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Post the standard synthesis report (see TASK_TEMPLATE.md) in chat before proceeding, covering: what you're about to investigate, files you'll reference, and expected outcome (a findings summary, not a code change).

---

## Problem Statement
Two services in the manufacturing domain do the same job. One is live, one appears dead. It's unclear whether the "dead" one is genuinely abandoned duplicate code, or an incomplete migration that was intended to replace the live one.

**Current behavior**: Both files exist; `ManufacturingService` is called in production, `Manufacturing::Service` is not.
**Expected behavior after this task**: A clear, evidence-based answer on which scenario is true, documented for a follow-up decision task.

---

## Files Involved

### Primary Files — read only, do not edit
| File | Purpose |
|---|---|
| `app/services/manufacturing_service.rb` | Live version — called from market_stabilization_service.rb, resource_planner.rb |
| `app/services/manufacturing/service.rb` | Suspected dead duplicate — only referenced by its own spec |
| `spec/services/manufacturing/service_spec.rb` | Only known reference to Manufacturing::Service |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `docs/wiki_reorganization/analysis/CORE_CONCEPT_MAP.md` | Source of the "likely owner" claim — check its own last-modified/commit date |
| `docs/architecture/manufacturing/MANUFACTURING_SYSTEM_OVERVIEW.md` | Check whether this agrees with current code |
| `docs/architecture/isru/README.md` | Same check |

### Migration (if needed)
- [x] No migration needed — this is a research/diagnosis task only

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
Follow the standard Step 0 procedure from TASK_TEMPLATE.md.

### Step 1 — Git history check
```bash
git log --follow --format="%h %ad %s" --date=short -- app/services/manufacturing_service.rb
git log --follow --format="%h %ad %s" --date=short -- app/services/manufacturing/service.rb
```
Determine: which file was created first? Do they look like independent same-era work, or does one look like an abandoned attempt to replace the other?

### Step 2 — Doc staleness check
```bash
git log -1 --format="%ad" --date=short -- docs/wiki_reorganization/analysis/CORE_CONCEPT_MAP.md
git log -1 --format="%ad" --date=short -- docs/architecture/manufacturing/MANUFACTURING_SYSTEM_OVERVIEW.md
git log -1 --format="%ad" --date=short -- docs/architecture/isru/README.md
```
Read `MANUFACTURING_SYSTEM_OVERVIEW.md` and `docs/architecture/isru/README.md` — do they mention `Manufacturing::Service` specifically, and does the description match current code?

### Step 3 — Report findings
No code changes. Write findings to the completion report below.

---

## Acceptance Criteria
- [ ] Git history for both files documented (creation date, commit messages)
- [ ] Doc staleness check completed for CORE_CONCEPT_MAP.md, MANUFACTURING_SYSTEM_OVERVIEW.md, isru/README.md
- [ ] Clear conclusion stated: duplicate, abandoned migration, or still-unclear (with reasoning either way)
- [ ] No files modified

---

## Stop Conditions — escalate to user immediately if:
- Git history suggests `Manufacturing::Service` was recently touched (i.e. this isn't old abandoned work but active in-progress development)
- Any other manufacturing service shows the same live/dead duplicate pattern (flag, don't fix)

---

## Commit Instructions
No commits expected — this is a read-only investigation. If a findings doc is created, add and commit only that file:
```bash
git add [findings file only]
git commit -m "docs: manufacturing service duplicate investigation findings"
git push
```

**Task file move on completion:**
```bash
git mv projects/galaxy_game/tasks/active/2026-07-26-LOW-REFACTOR-MANUFACTURING-SERVICE-DUPLICATE.md \
       projects/galaxy_game/tasks/completed/2026-07/2026-07-26-LOW-REFACTOR-MANUFACTURING-SERVICE-DUPLICATE.md
git commit -m "chore: move manufacturing-service-duplicate task to completed/"
```

---

## Documentation
- [ ] No doc changes needed
- [ ] Flag doc gap: if CORE_CONCEPT_MAP.md, MANUFACTURING_SYSTEM_OVERVIEW.md, or isru/README.md are found stale, note this in the completion report — do not edit them in this task.

---

## Dependencies
**Blocked by**: none
**Blocks**: any future decision task to consolidate/remove Manufacturing::Service
**Related tasks**: 2026-07-24-CRITICAL-DOCUMENTATION-AI-MANAGER-SERVICE-INVENTORY.md

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was found
[git history summary, doc staleness summary, conclusion]

### Follow-up tasks needed
[e.g. a decision task to consolidate/remove, if warranted]

### Lessons learned
[anything worth carrying forward]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [findings] | [conclusion] | [next action needed]
