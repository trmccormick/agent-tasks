---
status: backlog
priority: HIGH
type: feature
system_domain: AI_MANAGER
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
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/ai-manager/2026-09-17-HIGH-FEATURE-PRE-PLAYER-ACQUISITION-TREE-WIRING.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/ai-manager/2026-09-17-HIGH-FEATURE-PRE-PLAYER-ACQUISITION-TREE-WIRING.md \
         projects/galaxy_game/tasks/active/2026-09-17-HIGH-FEATURE-PRE-PLAYER-ACQUISITION-TREE-WIRING.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-17-HIGH-FEATURE-PRE-PLAYER-ACQUISITION-TREE-WIRING.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-FEATURE-PRE-PLAYER-ACQUISITION-TREE-WIRING.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Wire Pre-Player Acquisition Decision Tree (EscalationService → ResourceAcquisitionService)
**Status**: BACKLOG  
**Priority**: HIGH  
**Type**: feature  
**Created**: 2026-09-17  
**Last Updated**: 2026-09-17 (post-Qwen proof: stub gotchas + preserve handle_resource_shortage)  

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: To be confirmed by Qwen proof
- **Docker Wrapper Check**: PASS expected — use `docker exec web` without `-it`
- **MVP Alignment**: VALID — Early-game AI Manager must acquire materials with no players present
- **MVP Impact Note**: Implements locked pre-player tree on existing spine; unblocks Luna training acquisition behavior
- **Action Line**: NEEDS MANUAL REVIEW — proof paths and entry-point method names before dispatch

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Terminal access for grep, path verification, and RSpec
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: agent-tasks README.md (EXECUTOR Role section)
2. **Project Guide**: projects/galaxy_game/README.md
3. **This Task File**: Everything below
4. **Prior art (read-only)**:
   - `summaries/2026-09-16-ARCHITECTURE-PRE-PLAYER-ACQUISITION-DECISION-TREE.md` — **authoritative** owner + tree
   - `summaries/2026-09-07-RESEARCH-AI-MANAGER-SERVICE-INVENTORY-AND-GAPS.md`
   - Wiring completion (2026-09-11): dead `calculate_eap_ceiling` removed; `evaluate_strategy` at ResourceAcquisitionService and related sites
   - `Market::NpcPriceCalculator.evaluate_strategy` (commit `792e670b`)

> Agent MUST read in this order. Do not skip. Synthesis report goes to summaries/ BEFORE starting work.

---

## Context

Architecture (2026-09-16 synthesis) locked:

| Role | Service |
|------|---------|
| **Decision spine** | `EscalationService` — shortage / emergency vs normal; owns the tree |
| **Execution owner** | `ResourceAcquisitionService` — executes local / import / related acquisition actions |
| **Not canonical** | `ProcurementService` — do not make it the owner; market path still stub |

**Pre-player decision tree (implement this only):**

1. Inventory + intentional stockpile sufficient? → done  
2. Local production / harvest capability exists? → produce / harvest locally  
3. Normal shortage? → prefer wait for cycler / resupply  
4. Emergency — can local stand up in time? → force local if possible, else continue  
5. Last resort → import via `Market::NpcPriceCalculator.evaluate_strategy` (`reference_cost` / `strategy_type`)

**Explicitly out of scope:**
- Player buy orders  
- Player-offered missions  
- New acquisition service  
- Full multi-system coordination  
- Revising Material Sourcing JSON / 2026-09-03 task (follow-on)  
- Changing `NpcPriceCalculator` public API  

Early game = world-building + AI Manager training. Players enter only after AWS links Sol–Eden–System B. This wiring must not assume players exist.

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: EscalationService decides; ResourceAcquisitionService executes**
- ❌ Wrong: Put the full tree only in ResourceAcquisitionService and bypass EscalationService
- ✅ Right: EscalationService runs the ordered checks and delegates execution steps to ResourceAcquisitionService (and existing local-production / import helpers)
- Why: Locked 2026-09-16 owner split

⚠️ **GOTCHA 2: Pre-player only**
- ❌ Wrong: Add buy-order posting or player-mission offers as default branches
- ✅ Right: System-side branches only (stockpile, local, cycler wait, emergency, import)
- Why: Players are not in the economy yet

⚠️ **GOTCHA 3: Pricing = evaluate_strategy only**
- ❌ Wrong: Hard-code EAP multipliers or call missing private helpers
- ✅ Right: `Market::NpcPriceCalculator.evaluate_strategy(material:, location:, context:)`
- Why: Calculator API locked; prior wiring already depends on it

⚠️ **GOTCHA 4: No parallel acquisition architecture**
- ❌ Wrong: New service or third owner
- ✅ Right: Extend EscalationService + ResourceAcquisitionService only
- Why: Standing AI Manager constraint

⚠️ **GOTCHA 5: Prefer minimal surface area**
- ❌ Wrong: Rewrite OperationalManager, ResourcePlanner, and ProcurementService in one pass
- ✅ Right: One clear entry on EscalationService (or the existing shortage entry it already owns) that runs the tree; leave Path A call sites alone unless a one-line safe redirect is obvious and approved
- Why: Keep the change reviewable

⚠️ **GOTCHA 6: `player_sell_orders_exceed_eap?` is a stub returning false**
- ❌ Wrong: Assume this method provides real EAP / player-order checking
- ✅ Right: It is a placeholder — pre-player tree must not depend on it
- Why: Pre-player phase has no player sell orders; do not block the tree on this stub

⚠️ **GOTCHA 7: `time_to_critical` / `time_to_next_resupply` are hardcoded stubs**
- ❌ Wrong: Assume real cycler/resupply ETA scheduling exists
- ✅ Right: Today `time_to_critical` ≈ 72h and `time_to_next_resupply` ≈ 7 days (conservative defaults). Pre-player “wait” may use `emergency_required?` with those defaults or a simple defer flag — do not invent a full cycler scheduler in this task
- Why: Real scheduling is out of scope; stop and escalate only if even a conservative wait branch cannot be expressed

### Existing hooks (confirm exact names in Step 1)

- `EscalationService` — `handle_resource_shortage` **already has real implementation** (normalize material, `calculate_bid`, funding check → emergency mission **or** resupply manifest). New tree must **wrap or extend, not replace**.
- `EscalationService` — `emergency_required?` depends on stub `time_to_critical` / `time_to_next_resupply` (hardcoded defaults)
- `ResourceAcquisitionService` — already calls `evaluate_strategy` at former EAP site; local/GCC vs USD fork. Note: some paths may call `ContractCreationService.create_player_contract` — prefer import/`evaluate_strategy` or non-player local helpers for pre-player branches
- `Market::NpcPriceCalculator.evaluate_strategy` — class method, returns OpenStruct with `strategy_type`, `reference_cost`, `breakdown`, `feasible?`, `notes`
- `player_sell_orders_exceed_eap?` — **stub always false**; do not depend on it

**Confirm in Step 1** current method names and that inventory/stockpile checks are obtainable without inventing new subsystems.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Save as MD file to summaries/ (do NOT paste full report in chat):

```markdown
## STATUS SYNTHESIS REPORT

**Task**: 2026-09-17-HIGH-FEATURE-PRE-PLAYER-ACQUISITION-TREE-WIRING
**Status**: backlog → active
**Date**: YYYY-MM-DD

### What I'm About to Do
Wire the locked 5-step pre-player acquisition tree onto EscalationService as decision spine, delegating execution to ResourceAcquisitionService. Use evaluate_strategy for import cost only. No player-first branches. No new service.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| galaxy_game/app/services/ai_manager/escalation_service.rb | Decision spine | pending |
| galaxy_game/app/services/ai_manager/resource_acquisition_service.rb | Execution owner | pending |
| galaxy_game/app/services/market/npc_price_calculator.rb | evaluate_strategy (read-only API) | pending |
| summaries/2026-09-16-ARCHITECTURE-PRE-PLAYER-ACQUISITION-DECISION-TREE.md | Locked design | pending |
| Specs for EscalationService / ResourceAcquisitionService | Regression + new examples | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted)
- ✅ Step 0: YAML status updated backlog → active
- ✅ Read architecture synthesis and this task
- ✅ Understand pre-player vs post-player split

### Expected Outcomes
- EscalationService entry runs ordered pre-player checks
- Execution delegated to ResourceAcquisitionService where appropriate
- Import path uses evaluate_strategy
- Specs cover tree branches (or focused examples)
- No player buy-order / mission code
- No new service class

### Critical Gotchas I Will Avoid
- ❌ Player-first default — instead ✅ pre-player tree only
- ❌ New acquisition service — instead ✅ EscalationService + ResourceAcquisitionService
- ❌ Bypass EscalationService — instead ✅ spine decides, Path B executes

---
**SYNTHESIS COMPLETE.** Ready to proceed.
```

---

## Problem Statement

The pre-player acquisition decision tree is designed but not wired. EscalationService and ResourceAcquisitionService exist and already use `evaluate_strategy` at former EAP sites, but there is no single ordered path that implements:

stockpile → local production → cycler wait → emergency → import.

**Current behavior**: Fragmented shortage / acquisition calls; no complete pre-player tree on the locked owners.  
**Expected behavior**: One EscalationService-driven flow that walks the five steps and delegates execution to ResourceAcquisitionService; import costs from `evaluate_strategy` only.

---

## Files Involved

### Primary — you will edit these
| File | Purpose | Key change |
|---|---|---|
| `galaxy_game/app/services/ai_manager/escalation_service.rb` | Decision spine | Add or extend entry that runs the 5-step pre-player tree |
| `galaxy_game/app/services/ai_manager/resource_acquisition_service.rb` | Execution owner | Ensure local / import execution methods are callable from the spine (minimal adapters if needed) |
| Specs under `galaxy_game/spec/services/ai_manager/` | Coverage | Extend escalation_service_spec (and resource_acquisition if needed) with focused tree examples |

### Reference — read; do not broaden scope into full rewrites
| File | Why |
|---|---|
| `galaxy_game/app/services/market/npc_price_calculator.rb` | `evaluate_strategy` API |
| `galaxy_game/app/services/ai_manager/procurement_service.rb` | Path A — do not make canonical |
| `galaxy_game/app/services/ai_manager/operational_manager.rb` | May still call Path A — leave unless one-line safe redirect approved |
| `galaxy_game/app/services/ai_manager/resource_planner.rb` | Path B entry — note only |
| `summaries/2026-09-16-ARCHITECTURE-PRE-PLAYER-ACQUISITION-DECISION-TREE.md` | Locked design |

### Migration
- [x] No migration needed

**Qwen proof:** Verify paths exist; confirm exact public methods for shortage handling, local capability check, and import/order creation.

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0. Write STATUS SYNTHESIS REPORT to summaries/. Do not proceed until both are done.

### Step 0 — Move task file to active/ and update status (MANDATORY)

```bash
git mv projects/galaxy_game/tasks/backlog/ai-manager/2026-09-17-HIGH-FEATURE-PRE-PLAYER-ACQUISITION-TREE-WIRING.md \
       projects/galaxy_game/tasks/active/2026-09-17-HIGH-FEATURE-PRE-PLAYER-ACQUISITION-TREE-WIRING.md
```

Set YAML `status: active`. Verify one copy via find; paste output in chat.

### Step 1 — Read-only mapping

```bash
grep -n "def handle_resource_shortage\|def emergency_required\|def evaluate_strategy\|def can_harvest\|def procure\|def order_acquisition\|def time_to_critical\|def time_to_next_resupply\|def player_sell_orders_exceed_eap" \
  galaxy_game/app/services/ai_manager/escalation_service.rb \
  galaxy_game/app/services/ai_manager/resource_acquisition_service.rb \
  galaxy_game/app/services/market/npc_price_calculator.rb 2>/dev/null | head -80
```

Document in notes:
- Best EscalationService entry to own the tree. **⚠️ `handle_resource_shortage` already has real implementation** (emergency mission / resupply manifest) — extend or wrap; do **not** delete that behavior without an explicit migration path in the completion report.
- How to read inventory / stockpile sufficiency without new subsystems
- How to detect local production capability (existing helpers only)
- How cycler / resupply wait is represented today: **`time_to_critical` and `time_to_next_resupply` are hardcoded stubs (72h / 7 days)**. Wait step may use `emergency_required?` with those defaults or a simple defer flag — do not build a real scheduler here.
- Import execution path that can use `evaluate_strategy`
- Confirm `player_sell_orders_exceed_eap?` is still a false stub — do not depend on it for pre-player logic

If a required input truly does not exist beyond these documented stubs, **stop and escalate** — do not invent a parallel logistics engine.

### Step 2 — Implement tree on EscalationService

Add or extend a single entry (prefer extending `handle_resource_shortage` or a clearly named pre-player method called from it). **Preserve existing emergency-mission / resupply-manifest behavior** unless the tree cleanly subsumes it with equivalent outcomes.

Ordered steps:

1. Returns early if inventory + stockpile covers need  
2. Attempts local production / harvest via existing capability checks + ResourceAcquisitionService (or existing local helpers that do not require player contracts)  
3. On normal shortage, prefers wait / defer over import (align with `emergency_required?` and its stub ETAs)  
4. On emergency, attempts fast local stand-up if possible, else continues to import  
5. Last resort: obtain strategy cost via `evaluate_strategy`, then execute import / order through ResourceAcquisitionService  

Keep methods small. Prefer private helpers on EscalationService for each step over one giant method.

### Step 3 — ResourceAcquisitionService adapters only as needed

Only add thin public/private methods required for the spine to request:
- local fulfillment attempt  
- import / order using strategy cost from `evaluate_strategy`  

⚠️ Prefer non-player local helpers; avoid `ContractCreationService.create_player_contract` for pre-player branches.

Do not reimplement the full tree inside ResourceAcquisitionService.

### Step 4 — Specs

Specs are **app-level** under the Rails app root inside Docker (`web` container; typical app root `/home/galaxy_game`). Use paths relative to that root (often `spec/services/ai_manager/...` without a second `galaxy_game/` prefix). Confirm with `find` if unsure.

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/escalation_service_spec.rb 2>&1 | tail -40'
```

Add focused examples for:
- Sufficient stockpile → no acquisition side effects  
- Local capability → local path attempted  
- Normal shortage → no forced import when emergency is false  
- Emergency + import last resort uses `evaluate_strategy` (stub calculator)  

Also keep calculator suite green if touched only via stubs:

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/market/npc_price_calculator_spec.rb 2>&1 | tail -20'
```

### Step 5 — Guardrails grep

```bash
grep -rn "calculate_eap_ceiling\|player_sell_orders_exceed_eap" galaxy_game/app/services/ai_manager/ || true
```

Expected: `calculate_eap_ceiling` returns no results (already removed); `player_sell_orders_exceed_eap?` may appear in ResourceAcquisitionService as a stub. No resurrected dead helpers. No new player-order posting methods in this task.

### Step 6 — Completion report

Document entry method name, delegation points, and any gaps (e.g. cycler ETA approximate). List follow-on: post-player tree; Material Sourcing revise; deeper OperationalManager redirect if still on Path A.

Do not commit until human approval.

---

## Acceptance Criteria

- [ ] EscalationService owns an entry that runs the ordered 5-step pre-player tree  
- [ ] Execution of local / import actions delegated to ResourceAcquisitionService (or existing helpers it already uses)  
- [ ] Import / last-resort cost path uses `Market::NpcPriceCalculator.evaluate_strategy`  
- [ ] No new acquisition service class  
- [ ] No player buy-order or player-mission implementation  
- [ ] No changes to `NpcPriceCalculator` public API  
- [ ] Focused specs pass for the new/changed behavior  
- [ ] Existing escalation / calculator specs remain green  
- [ ] Completion report names follow-on work (post-player tree, Material Sourcing revise)

---

## Stop Conditions — escalate to user immediately if:

- Required inventory / cycler / local-capability API does not exist and would need a new subsystem  
- Fix causes failures in unrelated specs after two attempts  
- Pressure to implement player-first branches  
- Pressure to create a new acquisition service  
- Change set spreads into OperationalManager / ResourcePlanner / ProcurementService beyond a single approved one-line redirect  
- Architectural conflict with the 2026-09-16 synthesis owner split

---

## Commit Instructions

Host only — never inside Docker:

```bash
git add galaxy_game/app/services/ai_manager/escalation_service.rb \
        galaxy_game/app/services/ai_manager/resource_acquisition_service.rb \
        galaxy_game/spec/services/ai_manager/
# adjust paths to match actual repo layout after proof
git commit -m "feature(ai-manager): wire pre-player acquisition tree on EscalationService → ResourceAcquisitionService"
```

Task file move on completion:

```bash
git mv projects/galaxy_game/tasks/active/2026-09-17-HIGH-FEATURE-PRE-PLAYER-ACQUISITION-TREE-WIRING.md \
       projects/galaxy_game/tasks/completed/2026-09/2026-09-17-HIGH-FEATURE-PRE-PLAYER-ACQUISITION-TREE-WIRING.md
git commit -m "chore: move 2026-09-17-HIGH-FEATURE-PRE-PLAYER-ACQUISITION-TREE-WIRING.md to completed/"
```

---

## Documentation

- [ ] No new architecture doc required if synthesis already records the tree  
- [ ] Flag in completion report if status.md should note pre-player tree is live on EscalationService

---

## Dependencies

**Blocked by**:  
- `2026-09-16` pre-player architecture synthesis (completed)  
- `evaluate_strategy` live + dead EAP wiring (completed)

**Blocks**:  
- Reliable system-side acquisition during Luna training curriculum  

**Related**:  
- `2026-09-03-MEDIUM-ARCHITECTURE-MATERIAL-SOURCING-AND-ACQUISITION-LOGIC.md` (stale — do not dispatch)  
- Post-player acquisition tree (future)  
- Multi-system coordination (still deferred)

---

## Completion Report

*Filled in by the implementing agent after completion*

**Completed by**:  
**Completion date**:  
**Final test result**:  

### What was changed
- 

### Entry method / delegation points
- 

### Issues discovered
- 

### Follow-up tasks needed
- Post-player tree (buy orders + player missions)  
- Revise/supersede 2026-09-03 Material Sourcing  
- Optional OperationalManager Path A redirect  

### Lessons learned
- 

---

## Handoff Summary

*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [pre-player tree wired on EscalationService] | [execution via ResourceAcquisitionService] | [next: post-player tree or Material Sourcing revise]
```
