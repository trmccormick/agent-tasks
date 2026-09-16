---
status: active
priority: MEDIUM
type: architecture
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/ai-manager/2026-09-12-MEDIUM-ARCHITECTURE-PRE-PLAYER-ACQUISITION-DECISION-TREE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/ai-manager/2026-09-12-MEDIUM-ARCHITECTURE-PRE-PLAYER-ACQUISITION-DECISION-TREE.md \
         projects/galaxy_game/tasks/active/2026-09-12-MEDIUM-ARCHITECTURE-PRE-PLAYER-ACQUISITION-DECISION-TREE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-12-MEDIUM-ARCHITECTURE-PRE-PLAYER-ACQUISITION-DECISION-TREE.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-ARCHITECTURE-PRE-PLAYER-ACQUISITION-DECISION-TREE.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Pre-Player Acquisition Decision Tree (Architecture + Routing Contract)
**Status**: BACKLOG  
**Priority**: MEDIUM  
**Type**: architecture  
**Created**: 2026-09-12  
**Last Updated**: 2026-09-12  

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: To be confirmed by Qwen proof
- **Docker Wrapper Check**: N/A for pure architecture deliverable (any RSpec only if a minimal hook is approved)
- **MVP Alignment**: VALID — Early-game AI Manager must keep settlements alive with no players present
- **MVP Impact Note**: Defines system-side acquisition order for Luna training curriculum before AWS / players
- **Action Line**: NEEDS MANUAL REVIEW — proof paths and owner choice before dispatch

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Terminal access for path verification and read-only code inventory
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: agent-tasks README.md (EXECUTOR Role section)
2. **Project Guide**: projects/galaxy_game/README.md
3. **This Task File**: Everything below
4. **Prior art (read-only)**:
   - `summaries/2026-09-07-RESEARCH-AI-MANAGER-SERVICE-INVENTORY-AND-GAPS.md`
   - Wiring completion: three dead `calculate_eap_ceiling` calls replaced with `evaluate_strategy` (2026-09-11)
   - `Market::NpcPriceCalculator.evaluate_strategy` (commit `792e670b`) — locked pricing interface
   - Curriculum / coordination notes: early game = world-building + AI Manager training; players enter only after AWS links Sol–Eden–System B

> Agent MUST read in this order. Do not skip. Synthesis report goes to summaries/ BEFORE starting work.

---

## Context

Early game is **world-building and AI Manager training**, not a player economy.

- Players do **not** enter until the AWS network links Sol–Eden–(procedural) System B (post-crisis first AWS to Eden; second AWS toward System B while the natural wormhole is still open).
- Until then the AI Manager must keep settlements alive with **system-side tools only**.
- `evaluate_strategy` is live and is the **only** pricing source of truth for strategy cost.
- The 2026-09-11 wiring task removed dead `calculate_eap_ceiling` calls and attached `evaluate_strategy` at three sites. It did **not** implement a full decision tree.

This task defines the **pre-player acquisition decision tree** and the single-owner boundary so later implementation does not invent a parallel acquisition system or smuggle in player-first behavior too early.

**Out of scope for this task (explicit):**
- Player buy orders
- Player-offered missions as first escalation
- Full Material JSON migration
- Multi-system network coordinator
- Post-AWS player-first tree (separate later task)

**Related backlog that this supersedes in part:**
- `2026-09-03-MEDIUM-ARCHITECTURE-MATERIAL-SOURCING-AND-ACQUISITION-LOGIC.md` — still backlog; its decision tree and integration assumptions are stale relative to pre-player rules and `evaluate_strategy`. Do **not** dispatch that file as-is. This task is the correct next architecture step; Material Sourcing may be revised or superseded after this lands.

---

## Critical Information for This Task

### Pre-player decision tree (authoritative for this phase)

When a settlement needs material X and players are not yet in the economy:

1. **Inventory + intentional stockpile sufficient?** → done  
2. **Local production / harvest** if capability already exists (facility / ISRU / harvester + body resource)  
3. **Normal shortage** → prefer wait for scheduled cycler / resupply over forcing expensive build-out or import  
4. **Emergency** → time-to-critical shorter than time-to-next-resupply (or equivalent); may force local production if it can stand up in time, or emergency import  
5. **Last resort** → import using `Market::NpcPriceCalculator.evaluate_strategy` (`reference_cost` / `strategy_type`)

Cross-cutting:
- Unified affordability where it already exists; do not invent a second money model.
- DC continuity: keep the settlement running even at temporary trade imbalance.
- Material data stays facility-based + baseline economics only — **no location-keyed sourcing blocks**.
- Pricing truth = `evaluate_strategy` only (no hard-coded EAP multipliers in acquisition code; no resurrected `calculate_eap_ceiling`).

### Post-player tree (document only — do not implement)

After AWS network and players exist, preferred order becomes: stockpile → proactive buy orders → player-offered missions → local production → cycler wait → emergency → import. That is **follow-on work**, not this task.

### Architecture Gotchas

⚠️ **GOTCHA 1: Pre-player only — no player-first behavior in this task**
- ❌ Wrong: Design or implement buy orders / player missions as the default path
- ✅ Right: System-side tree only (stockpile → local → cycler → emergency → import)
- Why: Players are not in the economy until AWS links are up

⚠️ **GOTCHA 2: No parallel acquisition architecture**
- ❌ Wrong: New ProcurementService, AcquisitionOrchestrator, or second spine
- ✅ Right: Choose one owner among existing surface (EscalationService as decision owner vs Path B / ResourceAcquisitionService as execution owner) and extend it
- Why: Standing rule from 2026-09-07 inventory

⚠️ **GOTCHA 3: Pricing interface is locked**
- ❌ Wrong: Hard-code EAP × 0.8/0.9, restore `calculate_eap_ceiling`, or call private calculator methods
- ✅ Right: Call `Market::NpcPriceCalculator.evaluate_strategy(material:, location:, context:)` only
- Why: Gemini locked the public API; wiring task already depends on it

⚠️ **GOTCHA 4: Architecture + routing contract — not full live-loop wiring**
- ❌ Wrong: Implement every branch in production code and rewire the entire manager loop in one task
- ✅ Right: Written decision tree, owner boundary, integration points, ranked route shape, one test-case design; minimal hook only if needed to prove the contract
- Why: Keep dispatchable and reviewable

⚠️ **GOTCHA 5: Do not merge Foothold or multi-system scope**
- ❌ Wrong: Expand into foothold ranking or cross-settlement optimization
- ✅ Right: Per-settlement “need material X” only
- Why: Foothold is closed; multi-system task remains deferred

### Dual-path baseline (from inventory)

| Path | Entry | Role today |
|------|--------|------------|
| A | OperationalManager → ProcurementService | ISRU checks useful; market side was no-op / placeholder |
| B | ResourcePlanner → ResourceAcquisitionService → ResourceFulfillmentService | Real contracts / pricing path; now also consumes evaluate_strategy at former EAP sites |
| Spine | EscalationService | Shortage, strategy routing, expired orders, emergency vs normal |

This task must **name** which path is the canonical execution owner for pre-player acquisition and how EscalationService relates (decision owner vs collaborator). Do not leave dual ambiguous owners for the same need.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Save as MD file to summaries/ (do NOT paste full report in chat):

```markdown
## STATUS SYNTHESIS REPORT

**Task**: 2026-09-12-MEDIUM-ARCHITECTURE-PRE-PLAYER-ACQUISITION-DECISION-TREE
**Status**: backlog → active
**Date**: YYYY-MM-DD

### What I'm About to Do
Produce the authoritative pre-player acquisition decision tree, choose the single acquisition owner among existing services, define integration points on evaluate_strategy, and document non-goals (no player-first, no parallel service, no full live-loop rewrite).

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| app/services/market/npc_price_calculator.rb | evaluate_strategy (read-only) | pending |
| app/services/ai_manager/escalation_service.rb | Spine | pending |
| app/services/ai_manager/resource_acquisition_service.rb | Path B | pending |
| app/services/ai_manager/procurement_service.rb | Path A / ISRU checks | pending |
| summaries/2026-09-07 inventory synthesis | Dual-path baseline | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted)
- ✅ Step 0: YAML status updated backlog → active
- ✅ Read this task, inventory, and wiring completion context
- ✅ Understand pre-player vs post-player split

### Expected Outcomes
- Written pre-player decision tree
- Named single acquisition owner + EscalationService boundary
- evaluate_strategy as sole pricing source
- Ranked route option shape
- Explicit non-goals and follow-on (post-player tree, Material Sourcing revise)
- No parallel service; no player-first implementation

### Critical Gotchas I Will Avoid
- ❌ Player buy orders / missions as default — instead ✅ pre-player tree only
- ❌ New acquisition service — instead ✅ extend existing owner
- ❌ Hard-coded EAP multipliers or dead ceiling helpers — instead ✅ evaluate_strategy only

---
**SYNTHESIS COMPLETE.** Ready to proceed.
```

---

## Problem Statement

The AI Manager lacks a single, authoritative **pre-player** acquisition decision tree that matches the training-curriculum phase of the game. Legacy Material Sourcing drafts still describe player-first defaults and pre-`evaluate_strategy` integration points. Dual acquisition paths remain without a named canonical owner for “settlement needs material X.”

**Current behavior**: Partial wiring to `evaluate_strategy` at former EAP crash sites; no complete pre-player tree; Material Sourcing task stale.  
**Expected behavior**: Documented pre-player tree, single owner boundary, integration contract on existing services + `evaluate_strategy`, ready for a later implementation task.

---

## Files Involved

### Primary (architecture deliverable)
| Deliverable | Purpose |
|---|---|
| Synthesis / design note under `summaries/` (required) | Decision tree, owner choice, integration points, non-goals |
| Optional short note under `docs/architecture/ai_manager/` | Only if human asks; do not create docs sprawl by default |

### Reference — read; edit only if a minimal proven hook is explicitly required and approved mid-task
| File | Why |
|---|---|
| `galaxy_game/app/services/market/npc_price_calculator.rb` | Locked `evaluate_strategy` API |
| `galaxy_game/app/services/ai_manager/escalation_service.rb` | Shortage / emergency spine |
| `galaxy_game/app/services/ai_manager/resource_acquisition_service.rb` | Path B execution |
| `galaxy_game/app/services/ai_manager/resource_fulfillment_service.rb` | Thin Path B wrapper |
| `galaxy_game/app/services/ai_manager/procurement_service.rb` | Path A; ISRU prior art |
| `galaxy_game/app/services/ai_manager/operational_manager.rb` | Path A entry |
| `galaxy_game/app/services/ai_manager/resource_planner.rb` | Path B entry |
| `summaries/2026-09-07-RESEARCH-AI-MANAGER-SERVICE-INVENTORY-AND-GAPS.md` | Baseline |

### Migration
- [x] No migration needed

**Qwen proof:** Confirm these paths still exist; note any renames.

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0. Write STATUS SYNTHESIS REPORT to summaries/. Do not proceed until both are done.

### Step 0 — Move task file to active/ and update status (MANDATORY)

```bash
git mv projects/galaxy_game/tasks/backlog/ai-manager/2026-09-12-MEDIUM-ARCHITECTURE-PRE-PLAYER-ACQUISITION-DECISION-TREE.md \
       projects/galaxy_game/tasks/active/2026-09-12-MEDIUM-ARCHITECTURE-PRE-PLAYER-ACQUISITION-DECISION-TREE.md
```

Set YAML `status: active`. Verify one copy via find; paste output in chat.

### Step 1 — Read-only inventory refresh

Confirm current entry points and that `evaluate_strategy` remains the public API:

```bash
grep -n "def evaluate_strategy" galaxy_game/app/services/market/npc_price_calculator.rb
grep -n "evaluate_strategy\|ProcurementService\|ResourceAcquisitionService\|handle_resource" \
  galaxy_game/app/services/ai_manager/operational_manager.rb \
  galaxy_game/app/services/ai_manager/resource_planner.rb \
  galaxy_game/app/services/ai_manager/escalation_service.rb \
  galaxy_game/app/services/ai_manager/resource_acquisition_service.rb \
  galaxy_game/app/services/ai_manager/procurement_service.rb 2>/dev/null | head -80
```

Record which path is dominant for live manager-loop acquisition calls.

### Step 2 — Write the pre-player decision tree

In the synthesis report, write the authoritative tree (sections matching Context above). For each step state:
- Trigger condition
- Data needed (inventory, capability, cycler ETA, `evaluate_strategy` result)
- Success / fall-through

Explicitly mark player buy orders and player missions as **out of scope / post-AWS**.

### Step 3 — Choose single acquisition owner

Recommend **one** of:
- **Path B execution owner**: ResourceAcquisitionService (and fulfillment) executes; EscalationService remains shortage/emergency spine, or  
- **EscalationService decision owner**: EscalationService owns the tree and calls Path B machinery for execution  

State the boundary in one short paragraph. Reject any third service.

### Step 4 — Integration contract

Define:
- Input: settlement + material id (+ optional urgency/context)
- Call: `Market::NpcPriceCalculator.evaluate_strategy(material:, location:, context:)`
- Output shape for ranked options (cost, time, source type: local / cycler_wait / emergency / import) — even if only documented, not fully coded
- Where the tree should live (method/service) on the chosen owner

### Step 5 — Non-goals and follow-on list

Document non-goals (player-first, multi-system coordinator, full JSON audit, foothold, live tick wiring of every branch).  
List follow-on tasks: post-player tree; revise or supersede `2026-09-03` Material Sourcing; optional EscalationService deeper evaluate_strategy adoption.

### Step 6 — Optional minimal hook (only if needed to prove contract)

Do **not** implement the full tree in code in this task. If a single, tiny hook is required to demonstrate the contract and is approved in chat, keep it under the stop conditions. Default is architecture-only deliverable in summaries/.

### Step 7 — Completion

Fill Completion Report. Move task to completed/ only after human approval of the synthesis.

---

## Acceptance Criteria

- [ ] Synthesis report exists under summaries/ with the required filename pattern
- [ ] Pre-player decision tree is fully written (stockpile → local → cycler → emergency → import via evaluate_strategy)
- [ ] Player buy orders and player-offered missions are explicitly excluded for this phase
- [ ] Single acquisition owner is named; dual-path ambiguity resolved in the design
- [ ] No new acquisition service proposed
- [ ] `evaluate_strategy` is the only pricing API referenced for strategy cost
- [ ] Ranked route option shape is defined (even if documentation-only)
- [ ] Non-goals and follow-on tasks listed
- [ ] No location-keyed material sourcing in the design
- [ ] Material Sourcing 2026-09-03 task is noted as stale / blocked until revise or supersede

---

## Stop Conditions — escalate to user immediately if:

- Design pressure to implement player-first behavior in this task
- Proposal to create a new parallel acquisition service
- Need to change `NpcPriceCalculator` public API
- Scope expands into multi-system coordination or Foothold Planner
- Full live-loop wiring of every branch is demanded mid-task without a new task file
- Same design conflict persists after two clarification attempts

---

## Commit Instructions

Host only — never inside Docker:

```bash
# Architecture-only: commit synthesis under summaries/ as directed by project norms
# If any minimal code hook was explicitly approved:
# git add [specific files only]
git commit -m "architecture(ai-manager): pre-player acquisition decision tree (evaluate_strategy owner boundary)"
```

Task file move on completion:

```bash
git mv projects/galaxy_game/tasks/active/2026-09-12-MEDIUM-ARCHITECTURE-PRE-PLAYER-ACQUISITION-DECISION-TREE.md \
       projects/galaxy_game/tasks/completed/2026-09/2026-09-12-MEDIUM-ARCHITECTURE-PRE-PLAYER-ACQUISITION-DECISION-TREE.md
git commit -m "chore: move 2026-09-12-MEDIUM-ARCHITECTURE-PRE-PLAYER-ACQUISITION-DECISION-TREE.md to completed/"
```

---

## Documentation

- [ ] Primary deliverable is the summaries/ synthesis report
- [ ] Optional docs/architecture note only if human requests
- [ ] Flag that 2026-09-03 Material Sourcing task should be revised or marked superseded

---

## Dependencies

**Blocked by**:  
- `evaluate_strategy` live (done — `792e670b`)  
- Dead EAP call wiring (done — 2026-09-11 completion)

**Blocks**:  
- Honest implementation of system-side acquisition for Luna training  
- Clean revise of Material Sourcing architecture

**Related**:  
- `2026-09-03-MEDIUM-ARCHITECTURE-MATERIAL-SOURCING-AND-ACQUISITION-LOGIC.md` (stale — do not dispatch as-is)  
- `2026-06-07-MEDIUM-FEATURE-MULTI-SYSTEM-RESOURCE-COORDINATION.md` (still deferred)  
- Curriculum coordination: pre-player until AWS Sol–Eden–System B

---

## Completion Report

*Filled in by the implementing agent after completion*

**Completed by**:  
**Completion date**:  
**Final test result**: N/A or minimal hook results  

### What was delivered
- 

### Owner decision recorded
- 

### Issues discovered
- 

### Follow-up tasks needed
- Post-player acquisition tree (buy orders + player missions)
- Revise or supersede 2026-09-03 Material Sourcing task
- Implementation task for the pre-player tree on the chosen owner

### Lessons learned
- 

---

## Handoff Summary

*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [pre-player tree written] | [owner = …] | [next: implement on owner / revise Material Sourcing]
```
