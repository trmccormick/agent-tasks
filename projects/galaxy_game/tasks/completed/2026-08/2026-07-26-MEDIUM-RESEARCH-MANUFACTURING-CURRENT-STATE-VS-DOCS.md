---
status: backlog
priority: MEDIUM
type: architecture
system_domain: MANUFACTURING
mvp_alignment: ISRU_PRODUCTION
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent** — but this task is DIAGNOSIS ONLY, no code changes.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/2026-07/2026-07-26-MEDIUM-RESEARCH-MANUFACTURING-CURRENT-STATE-VS-DOCS.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/2026-07/2026-07-26-MEDIUM-RESEARCH-MANUFACTURING-CURRENT-STATE-VS-DOCS.md \
         projects/galaxy_game/tasks/active/2026-07-26-MEDIUM-RESEARCH-MANUFACTURING-CURRENT-STATE-VS-DOCS.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-26-MEDIUM-RESEARCH-MANUFACTURING-CURRENT-STATE-VS-DOCS.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Manufacturing System — Current State vs. Documented Intent
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: architecture (research, diagnosis-first)
**Created**: 2026-07-26
**Last Updated**: 2026-07-26

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: PASS
- **Docker Wrapper Check**: N/A — no specs run in this task
- **MVP Alignment**: VALID — clarifies what manufacturing/ISRU code actually supports the Luna MVP path today
- **MVP Impact Note**: Answers whether manufacturing chain code matches what Luna MVP actually needs, or carries stale architecture from before the pivot to RSpec stabilization + Luna MVP focus.
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Read-only call-chain tracing and doc comparison — within local worker capability, no architecture decisions required.
**Local attempts before cloud**: N/A
**Supervision Level**: standard

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

---

## Context
The manufacturing chain (Raw Materials → Processed Materials → Components → Blueprints → Assembly → Units/Craft) was originally architected before the project's current focus on RSpec stabilization and the Luna MVP. The AI Manager Service Inventory audit (2026-07-26) surfaced a live/dead duplicate pair (`ManufacturingService` vs `Manufacturing::Service` — see the related task) and an older analysis doc (`CORE_CONCEPT_MAP.md`, 2026-07-16) with at least one confirmed-stale claim about AI Manager service counts. This raises a broader question: is the rest of the documented manufacturing architecture still accurate, or has the actual code (and what the Luna MVP needs) diverged from what's written?

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `docs/architecture/manufacturing/MANUFACTURING_SYSTEM_OVERVIEW.md`
- `docs/architecture/isru/README.md`
- `docs/wiki_reorganization/analysis/CORE_CONCEPT_MAP.md` — treat as a possibly-stale lead, not ground truth

> If a doc doesn't exist for this area, do not create one during this task.
> Flag the gap in your completion report instead.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: This is diagnosis only — no code changes, no consolidation, no deletion.
- ❌ Wrong: Fixing or removing anything found to be stale/duplicate/orphaned during this pass.
- ✅ Right: List findings; downstream fix/cleanup tasks get filed separately based on what's found.
- Why: Keeps this task scoped and reviewable — mixing diagnosis and fixes is how the AI Manager service count spiraled across multiple sessions.

⚠️ **GOTCHA 2**: Don't re-derive numbers already locked from the Service Inventory audit.
- ❌ Wrong: Recounting manufacturing services from scratch.
- ✅ Right: Use the 121-service inventory (already finalized) as the service list; this task is about *call-chain reality and doc accuracy*, not counting.
- Why: Avoids repeating the exact reconciliation loop that already cost two sessions on the inventory task.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Post the standard synthesis report in chat before proceeding, covering: what you're about to trace, which docs you'll cross-check, and that the expected output is a findings summary, not a code change.

---

## Problem Statement
It's unclear whether the manufacturing chain's documented architecture still matches what's actually live and exercised for the Luna MVP, or whether parts of it (code and/or docs) are stale holdovers from before the project's focus shifted.

**Current behavior**: Unknown — not yet traced.
**Expected behavior after this task**: A findings doc listing what's live, what's stale, and what (if anything) needs a follow-up cleanup or doc-correction task.

---

## Files Involved

### Primary Files — read only, do not edit
| File | Purpose |
|---|---|
| `app/services/manufacturing_service.rb` | Confirmed live entry point |
| `app/services/market_stabilization_service.rb` | Confirmed caller of ManufacturingService — trace forward/backward from here |
| `app/services/resource_planner.rb` | Confirmed caller of ManufacturingService — trace forward/backward from here |
| `app/services/manufacturing/` (17 services per inventory) | Full manufacturing service directory |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `docs/architecture/manufacturing/MANUFACTURING_SYSTEM_OVERVIEW.md` | Compare against actual call chain |
| `docs/architecture/isru/README.md` | Compare against actual call chain |
| `docs/wiki_reorganization/analysis/CORE_CONCEPT_MAP.md` | Possibly-stale lead, cross-check only |
| Finalized AI Manager Service Inventory (`ai_manager_service_inventory.md`) | Source of the 121-service list and the 6 flagged incomplete/stub services — do not recount |

### Migration (if needed)
- [x] No migration needed — research/diagnosis task only

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
Follow the standard Step 0 procedure from TASK_TEMPLATE.md.

### Step 1 — Trace the live Luna MVP manufacturing call chain
Starting from `market_stabilization_service.rb` and `resource_planner.rb` (confirmed live callers of `ManufacturingService`), trace forward and backward through the manufacturing chain. Document which of the 121 inventoried services are actually reached by this chain.

### Step 2 — Compare against documented architecture
Read `MANUFACTURING_SYSTEM_OVERVIEW.md` and `docs/architecture/isru/README.md`. For each major claim in these docs, note: does it match the traced call chain, or does it describe something not currently reached/exercised?

### Step 3 — Check for further duplicate/orphaned patterns
Using the finalized 121-service inventory (already audited — do not recount), scan for any other services that look duplicated or orphaned in the same way as `Manufacturing::Service` (i.e., not the 6 already-flagged incomplete stubs, which is a separate category — this is specifically "superseded/duplicate," not "unfinished").

### Step 4 — Report findings
No code changes. Write findings to the completion report below — what's live, what's stale, what needs follow-up.

---

## Acceptance Criteria
- [ ] Live call chain for manufacturing (starting from the two confirmed callers) documented
- [ ] MANUFACTURING_SYSTEM_OVERVIEW.md and isru/README.md checked against traced call chain, discrepancies listed
- [ ] Any additional duplicate/orphaned services (beyond Manufacturing::Service) identified, if present
- [ ] No files modified
- [ ] Findings summarized in a way that supports filing follow-up cleanup tasks, without filing them in this task

---

## Stop Conditions — escalate to user immediately if:
- The traced call chain reveals a service central to Luna MVP that has no spec coverage (separate risk from the InfrastructureCostCalculator/Manufacturing::Service findings, but same category)
- Docs and code disagree on something that looks like an active design decision rather than staleness (i.e., this isn't drift, it's a live disagreement worth a human call)

---

## Commit Instructions
No commits expected beyond a findings doc, if one is created:
```bash
git add [findings file only]
git commit -m "docs: manufacturing current-state-vs-docs investigation findings"
git push
```

**Task file move on completion:**
```bash
git mv projects/galaxy_game/tasks/active/2026-07-26-MEDIUM-RESEARCH-MANUFACTURING-CURRENT-STATE-VS-DOCS.md \
       projects/galaxy_game/tasks/completed/2026-07/2026-07-26-MEDIUM-RESEARCH-MANUFACTURING-CURRENT-STATE-VS-DOCS.md
git commit -m "chore: move manufacturing-current-state-vs-docs task to completed/"
```

---

## Documentation
- [ ] No doc changes needed
- [ ] Flag doc gap: if MANUFACTURING_SYSTEM_OVERVIEW.md or isru/README.md are found stale, note in completion report — do not edit them in this task.

---

## Dependencies
**Blocked by**: none
**Blocks**: none directly, but findings will likely spawn follow-up cleanup/doc-correction tasks
**Related tasks**: 2026-07-26-LOW-REFACTOR-MANUFACTURING-SERVICE-DUPLICATE.md, 2026-07-24-CRITICAL-DOCUMENTATION-AI-MANAGER-SERVICE-INVENTORY.md

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was found
[live call chain summary, doc-vs-code discrepancies, any additional duplicate/orphaned services found]

### Follow-up tasks needed
[list any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[what worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [findings] | [structural changes] | [next action needed]
