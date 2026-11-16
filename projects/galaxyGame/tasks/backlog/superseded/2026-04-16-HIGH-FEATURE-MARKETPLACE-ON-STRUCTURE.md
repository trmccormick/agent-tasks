---
status: deprecated
priority: HIGH
type: feature
system_domain: ECONOMICS
mvp_alignment: ISRU_PRODUCTION
local_worker_safe: true
created: 2026-04-16
last_updated: 2026-07-29
deprecated: 2026-07-29
---

## ⚡ Minimal Handoff (Copy this to send to agent)
```text
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/review/2026-04-16-HIGH-FEATURE-MARKETPLACE-ON-STRUCTURE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/review/2026-04-16-HIGH-FEATURE-MARKETPLACE-ON-STRUCTURE.md \
         projects/galaxy_game/tasks/active/2026-04-16-HIGH-FEATURE-MARKETPLACE-ON-STRUCTURE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-04-16-HIGH-FEATURE-MARKETPLACE-ON-STRUCTURE.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-04-16-FEATURE-MARKETPLACE-ON-STRUCTURE.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Add Marketplace Association to BaseStructure
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-04-16
**Last Updated**: 2026-07-29  

## Context

> **DEPRECATED 2026-07-29**: Superseded by scope discussion — the "marketplace on structure" approach (polymorphic Marketplace ownership) is no longer the right fix. Orbital settlements already inherit a marketplace via BaseSettlement. The real gap is buy-vs-physical-transfer and cross-structure cargo logistics within a single orbital settlement, which needs a research pass before any implementation task is written. See replacement task: `2026-07-29-HIGH-RESEARCH-ORBITAL-CARGO-LOGISTICS-AND-MARKET-LOCATION.md` (pending).

`Market::Marketplace` currently belongs_to `Settlement::BaseSettlement` only. Orbital structures (depots, stations) are not settlements — they are `Structures::BaseStructure` instances owned by a player, corporation, or AI Manager. Without a marketplace on the structure, craft docking at an orbital structure have no local order book to transact against.

**Current state (2026-07-29)**: The gap still exists — `Marketplace` has only `belongs_to :settlement`, no polymorphic or optional association for structures. No migration has been added for this.

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: `Marketplace` is referenced by `Market::Condition` and `Market::PriceHistory` via the marketplace record. Any change to how Marketplace identifies its owner must not break existing settlement-based marketplaces.
- ❌ Wrong: Replace `belongs_to :settlement` with a new association, breaking all existing records
- ✅ Right: Add an optional polymorphic association (`owner_type`, `owner_id`) while keeping the existing `settlement` column for backward compatibility
- Why: Existing data has settlement-based marketplaces; they must continue to work

⚠️ **GOTCHA 2**: The `Marketplace.get_price` class method resolves a settlement from its caller (`seller`). If the seller is a structure, it may not have a `.settlement` or `.base_settlement` method.
- ❌ Wrong: Assume all sellers have a settlement
- ✅ Right: Handle structures that don't have settlements — either skip price lookup or use the structure's owner settlement if available
- Why: Orbital depots/stations are owned by corporations or AI Manager, not settlements

⚠️ **GOTCHA 3**: Database migration must be additive-only (no column drops) to avoid data loss.
- ❌ Wrong: Drop `settlement_id` and replace with polymorphic columns
- ✅ Right: Add `owner_type` + `owner_id` columns, keep `settlement_id` for backward compatibility; update code to prefer polymorphic when present

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Add Marketplace Association to BaseStructure
**Status**: [backlog → active → completed]
**Date**: YYYY-MM-DD

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `app/models/market/marketplace.rb` | Add polymorphic owner association | [not started / pending / done] |
| `app/models/structures/base_structure.rb` | Add has_one :marketplace | [not started / pending / done] |
| Migration file | Add owner_type/owner_id columns | [not started / pending / done] |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above
- ✅ Know which domain/credentials to use

### Expected Outcomes
[Exact description of what "done" looks like]

### Critical Gotchas I Will Avoid
- ❌ Replace belongs_to :settlement — instead ✅ Add polymorphic owner_type/owner_id alongside existing settlement column
- ❌ Drop settlement_id column — instead ✅ Keep for backward compatibility, add new columns
- ❌ Assume all sellers have a settlement — instead ✅ Handle structures without settlements in get_price

---

**SYNTHESIS COMPLETE.** Ready to proceed with [PRIORITY 1 / PRIORITY 2 / etc].
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement

`Market::Marketplace` currently belongs_to `Settlement::BaseSettlement` only. Orbital structures (depots, stations) are not settlements — they are `Structures::BaseStructure` instances owned by a player, corporation, or AI Manager. Without a marketplace on the structure, craft docking at an orbital structure have no local order book to transact against.

**Current**: Marketplace only belongs_to settlement  
**Expected**: Marketplace can belong to either a settlement OR a structure; Structure gets `has_one :marketplace`

## Acceptance Criteria

- [ ] `Market::Marketplace` supports both settlement and structure as owners (polymorphic or optional association)
- [ ] `BaseStructure` has `has_one :marketplace` association
- [ ] Marketplace can be created on orbital structures without errors
- [ ] Existing marketplace functionality on settlements continues to work
- [ ] Tests verify marketplace creation on both settlement and structure

## Implementation Steps

1. Update `Market::Marketplace` to support polymorphic ownership (settlement or structure)
2. Add `has_one :marketplace` to `BaseStructure`
3. Create migration if needed for polymorphic association columns
4. Update tests to verify marketplace on structures works correctly

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add galaxy_game/app/models/market/marketplace.rb \
       galaxy_game/app/models/structures/base_structure.rb \
       db/migrate/[timestamp]_add_owner_to_marketplaces.rb
git commit -m "feat: add polymorphic owner association to Marketplace for orbital structures"
```

**Task file move on completion:**
```bash
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy_game/tasks/review/2026-04-16-HIGH-FEATURE-MARKETPLACE-ON-STRUCTURE.md \
     projects/galaxy_game/tasks/completed/2026-07/2026-04-16-HIGH-FEATURE-MARKETPLACE-ON-STRUCTURE.md
git commit -m "chore: move 2026-04-16-HIGH-FEATURE-MARKETPLACE-ON-STRUCTURE.md to completed/"
```

---

## Stop Conditions — escalate to user immediately if:
- `Market::Condition` or `Market::PriceHistory` have hard dependencies on `settlement_id` that can't be handled via polymorphic association
- Existing marketplace data is in production and migration must include data migration (not just schema change)
- Any architectural decision about whether structures should have their own marketplace vs sharing settlement marketplaces is required

---

## Dependencies
**Blocked by**: none
**Blocks**: Phase 7+ orbital infrastructure (depots need marketplaces for trade), Phase 9+ Mars/Venus surface settlement economics
**Related tasks**: Raw resource extraction pricing (companion task — both needed for full marketplace functionality)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
