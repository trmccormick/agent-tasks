---
title: "NPC economy lifecycle documentation — AI Manager → player opportunity flow"
priority: HIGH
status: backlog
owner: Implementation Agent (Qwen)
type: documentation
system_domain: AI_MANAGER / ECONOMY
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
created: 2026-07-24
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/drafts/2026-07-24/2026-07-24-HIGH-DOCUMENTATION-NPC-ECONOMY-LIFECYCLE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/drafts/2026-07-24/2026-07-24-HIGH-DOCUMENTATION-NPC-ECONOMY-LIFECYCLE.md \
         projects/galaxy_game/tasks/active/2026-07-24-HIGH-DOCUMENTATION-NPC-ECONOMY-LIFECYCLE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-24-HIGH-DOCUMENTATION-NPC-ECONOMY-LIFECYCLE.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-DOCUMENTATION-NPC-ECONOMY-LIFECYCLE.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: NPC economy lifecycle documentation

**Status**: BACKLOG
**Priority**: HIGH
**Type**: documentation
**Created**: 2026-07-24
**Last Updated**: 2026-07-24

---

## Context

The NPC economy is a core gameplay loop where AI-managed NPCs initialize pricing, create orders, and players can accept contracts to keep the economy moving. This flow exists in code but is undocumented, making it impossible for new contributors (or agents) to understand without reading through multiple service files. The goal is to produce clear documentation that explains the NPC-to-player opportunity flow without requiring a code review.

---

## Problem Statement

- NPC economy lifecycle is implemented across multiple services but has no unified documentation
- The flow from AI initialization → pricing → orders → player acceptance → fallback is undocumented
- Contributors cannot understand the economic simulation without reading code
- No diagram or step-by-step explanation exists

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: NPC economy spans multiple service namespaces**
- ❌ Wrong: Only look at `app/services/npc_economy/`
- ✅ Right: Also check `app/services/ai_manager/` for pricing initialization, order creation, and contract management services
- Why: The AI Manager drives the economy; NPC economy services are consumers of AI decisions

⚠️ **GOTCHA 2: Fallback mechanisms are scattered**
- ❌ Wrong: Assume a single "fallback service" handles economy recovery
- ✅ Right: Fallback logic is distributed across multiple services (price correction, order expiration, market stabilization)
- Why: The fallback is an emergent property of multiple independent safety nets

### Files to Audit (Read-Only)

| File/Directory | Purpose |
|---|---|
| `app/services/npc_economy/` | NPC economy core services |
| `app/services/ai_manager/` | AI-driven pricing and order creation |
| `app/models/` | Economy-related models (orders, contracts, prices) |
| `spec/services/npc_economy/` | Tests that reveal expected behavior |
| `docs/new_agent/projects/galaxy_game/services/ai_manager_service_inventory.md` | Service inventory (if already created — reference only) |

---

## Implementation Steps

### Step 1 — Trace the NPC economy lifecycle

Read through relevant services and tests to map the complete lifecycle:

```bash
# List all NPC economy related files
find /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/services -path "*npc*" -o -path "*economy*" | sort
find /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/models -name "*order*" -o -name "*contract*" -o -name "*price*" | sort
```

Trace each phase:
1. **Initialization**: How NPCs are created and assigned economic roles
2. **Pricing**: How AI Manager sets initial prices for goods/resources
3. **Order creation**: How NPCs create buy/sell orders
4. **Player acceptance**: How players can accept contracts/orders
5. **Fallback**: What happens when NPCs fail, prices diverge, or orders expire

### Step 2 — Create NPC economy lifecycle doc

Create: `docs/new_agent/projects/galaxy_game/economy/npc_economy_lifecycle.md`

Structure:
```markdown
# NPC Economy Lifecycle

## Overview
[1-2 paragraph summary]

## Lifecycle Phases
### 1. NPC Initialization
[How NPCs enter the economy, their roles, capabilities]

### 2. Price Setting (AI Manager)
[How AI determines initial prices, what factors influence them]

### 3. Order Creation
[How NPCs create buy/sell orders, order lifecycle]

### 4. Player Contract Acceptance
[How players interact with NPC orders, contract mechanics]

### 5. Fallback Mechanisms
[Price correction, order expiration, market stabilization]

## Key Services
| Service | Role in Lifecycle |
|---|---|
| ... | ... |

## Data Flow Diagram
[text-based diagram showing flow between systems]

## Edge Cases
- What happens when an NPC dies/leaves?
- What happens when prices diverge from market?
- What happens when no players accept orders?
```

### Step 3 — Create economy model documentation

Create: `docs/new_agent/projects/galaxy_game/economy/economy_models.md`

Document the key models:
- Order/Contract models (fields, states, associations)
- Price tracking models
- NPC agent models
- Market state models

### Step 4 — Verify

- [ ] Lifecycle doc covers all 5 phases with clear explanations
- [ ] Data flow diagram accurately reflects code behavior
- [ ] Edge cases documented for known failure modes
- [ ] Model docs match actual schema (verify against migrations/factories)
- [ ] No speculative claims — every statement backed by code evidence

---

## Acceptance Criteria
- [ ] NPC economy lifecycle doc exists and is readable without code review
- [ ] All 5 lifecycle phases documented with service references
- [ ] Economy models documented with field descriptions
- [ ] Data flow diagram matches actual implementation
- [ ] Edge cases and fallback mechanisms explained

---

## Stop Conditions — escalate to user immediately if:
- Cannot trace a lifecycle phase through code (gap in implementation?)
- Model fields don't match what tests expect
- Fallback logic is so scattered it can't be coherently documented
- A migration is needed (unlikely for docs-only task)

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add docs/new_agent/projects/galaxy_game/economy/npc_economy_lifecycle.md
git add docs/new_agent/projects/galaxy_game/economy/economy_models.md
git commit -m "docs: NPC economy lifecycle documentation — pricing, orders, player contracts, fallback mechanisms"
```

---

## Documentation
- [x] New docs created (lifecycle + models)
- [ ] Update existing architecture doc — [path TBD]
- [ ] Flag doc gap: [description if needed]

---

## Dependencies
**Blocked by**: AI Manager service inventory docs (task #1) — for service references
**Blocks**: none directly
**Related tasks**: 2026-07-24-CRITICAL-DOCUMENTATION-AI-MANAGER-SERVICE-INVENTORY.md, 2026-07-24-HIGH-DOCUMENTATION-MANUFACTURING-CHAIN-OVERVIEW.md

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation]

### Follow-up tasks needed
[Any new backlog items identified]

### Lessons learned
[What worked, what didn't]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
