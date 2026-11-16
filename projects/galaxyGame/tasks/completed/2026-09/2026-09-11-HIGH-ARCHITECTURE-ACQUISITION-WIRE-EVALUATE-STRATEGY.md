---
status: active
priority: HIGH
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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/ai-manager/2026-09-11-HIGH-ARCHITECTURE-ACQUISITION-WIRE-EVALUATE-STRATEGY.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/ai-manager/2026-09-11-HIGH-ARCHITECTURE-ACQUISITION-WIRE-EVALUATE-STRATEGY.md \
         projects/galaxy_game/tasks/active/2026-09-11-HIGH-ARCHITECTURE-ACQUISITION-WIRE-EVALUATE-STRATEGY.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-11-HIGH-ARCHITECTURE-ACQUISITION-WIRE-EVALUATE-STRATEGY.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-ARCHITECTURE-ACQUISITION-WIRE-EVALUATE-STRATEGY.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Wire Acquisition Path to evaluate_strategy + Fix Dead EAP Calls
**Status**: BACKLOG  
**Priority**: HIGH  
**Type**: architecture  
**Created**: 2026-09-11  
**Last Updated**: 2026-09-11  

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: PASS (revised after Qwen proof 2026-09-11)
- **Docker Wrapper Check**: PASS — RSpec uses docker exec without `-it`
- **MVP Alignment**: VALID — Removes broken calculate_eap_ceiling calls; starts using locked evaluate_strategy
- **MVP Impact Note**: Unblocks safe runtime use of pricing interface on acquisition path
- **Action Line**: READY FOR LOCAL DISPATCH after human readiness-box sign-off

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Terminal access required for grep, path confirmation, and RSpec
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: agent-tasks README.md (EXECUTOR Role section)
2. **Project Guide**: projects/galaxy_game/README.md
3. **This Task File**: Everything below
4. **Prior art (read-only)**:
   - `summaries/2026-09-07-RESEARCH-AI-MANAGER-SERVICE-INVENTORY-AND-GAPS.md`
   - Verification that `evaluate_strategy` is live (galaxyGame commit `792e670b`)

> Agent MUST read in this order. Do not skip. Synthesis report goes to summaries/ BEFORE starting work.

---

## Context

`Market::NpcPriceCalculator.evaluate_strategy(material:, location:, context:)` is live on main (commit `792e670b`). It returns an OpenStruct with `strategy_type`, `reference_cost`, `breakdown`, `feasible?`, and `notes`. Fees are decoupled. Existing `calculate_ask` / `calculate_bid` signatures are unchanged.

**Verified dead calls** (all call a method that does not exist on the calculator):

| File | Approx line | Code pattern |
|------|-------------|--------------|
| `app/services/ai_manager/resource_acquisition_service.rb` | **140** | `NpcPriceCalculator.send(:calculate_eap_ceiling, settlement, material)` |
| `app/services/ai_manager/decision_tree.rb` | **282** | `NpcPriceCalculator.send(:calculate_eap_ceiling, @settlement, resource)` |
| `app/services/special_mission_service.rb` | **8** | `NpcPriceCalculator.send(:calculate_eap_ceiling, settlement, material)` |

Gemini confirmed the calculator API is locked: do **not** restore `calculate_eap_ceiling`. Callers must consume `evaluate_strategy` (or a thin local wrapper).

This task is the first wiring pass only:
- Remove all three dead calls.
- Replace them with `evaluate_strategy`-based checks that preserve intent (reference cost / EAP-style ceiling vs player offers).
- Do **not** implement the full player-first decision tree (buy orders, player missions, excess listing, cycler preference).

**Standing constraints:**
- No new parallel acquisition service.
- Extend existing EscalationService + Path B surface; do not invent a third owner.
- Do not change `NpcPriceCalculator` public API.
- Material JSON stays facility-based; no location-keyed sourcing.

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: Do not resurrect `calculate_eap_ceiling` on the calculator**
- ❌ Wrong: Add `calculate_eap_ceiling` back onto `Market::NpcPriceCalculator`
- ✅ Right: Change each caller to use `evaluate_strategy` (or a thin wrapper that reads `strategy_type` / `reference_cost` / `feasible?`)
- Why: Calculator API is locked by Gemini; acquisition owns decision-tree translation

⚠️ **GOTCHA 2: Three call sites, not one**
- ❌ Wrong: Fix only ResourceAcquisitionService and leave decision_tree.rb / special_mission_service.rb broken
- ✅ Right: Fix all three verified dead calls in this task
- Why: Acceptance requires no remaining `calculate_eap_ceiling` references in these paths

⚠️ **GOTCHA 3: No parallel acquisition architecture**
- ❌ Wrong: Create a new ProcurementService or AcquisitionOrchestrator
- ✅ Right: Wire existing services only
- Why: Standing AI Manager rule from 2026-09-07 inventory

⚠️ **GOTCHA 4: Scope is dead-call fix + minimal evaluate_strategy consumption, not full decision tree**
- ❌ Wrong: Implement proactive buy orders, player-offered missions, excess listing, or cycler preference
- ✅ Right: Stop the crashes; use evaluate_strategy for the old EAP-ceiling intent; leave full tree for follow-on
- Why: Keep the change reviewable

⚠️ **GOTCHA 5: No existing ResourceAcquisitionService spec**
- ❌ Wrong: Assume `spec/services/ai_manager/resource_acquisition_service_spec.rb` exists
- ✅ Right: It does **not** exist — create focused examples from scratch for the changed behavior only
- Why: Confirmed missing in 2026-09-11 proof

### Locked decision tree (context only — not implemented here)

1. Inventory + intentional stockpile sufficient? → done  
2. Proactive buy orders (player-first)  
3. Player-offered missions as first escalation  
4. Local production only when market + missions fail  
5. Cycler/resupply wait on normal shortage  
6. Hard emergency when time-to-critical demands it  
7. Last-resort import via `evaluate_strategy` reference_cost  

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Save as MD file to summaries/ (do NOT paste full report in chat):

```markdown
## STATUS SYNTHESIS REPORT

**Task**: 2026-09-11-HIGH-ARCHITECTURE-ACQUISITION-WIRE-EVALUATE-STRATEGY
**Status**: backlog → active
**Date**: YYYY-MM-DD

### What I'm About to Do
Remove all three dead calculate_eap_ceiling calls (ResourceAcquisitionService ~140, decision_tree ~282, special_mission_service ~8). Replace each with Market::NpcPriceCalculator.evaluate_strategy consumption. Create focused specs from scratch where none exist. Do not implement the full player-first decision tree. Do not modify the calculator API.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| app/services/ai_manager/resource_acquisition_service.rb | Dead call ~140 | pending |
| app/services/ai_manager/decision_tree.rb | Dead call ~282 | pending |
| app/services/special_mission_service.rb | Dead call ~8 | pending |
| app/services/market/npc_price_calculator.rb | evaluate_strategy (read-only) | pending |
| app/services/ai_manager/escalation_service.rb | Spine; legacy bid/ask — note only | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted)
- ✅ Step 0: YAML status updated backlog → active
- ✅ Read this task file and gotchas
- ✅ Confirmed evaluate_strategy is live (commit 792e670b)

### Expected Outcomes
- Zero remaining calculate_eap_ceiling references in the three files
- Strategy cost checks use evaluate_strategy
- No new service class; calculator API unchanged
- Focused specs green; calculator suite still green
- Full decision tree still deferred

### Critical Gotchas I Will Avoid
- ❌ Resurrect calculate_eap_ceiling on calculator — instead ✅ consume evaluate_strategy
- ❌ Fix only one of three call sites — instead ✅ fix all three
- ❌ Full buy-order / mission / cycler implementation — instead ✅ wiring + crash fix only

---
**SYNTHESIS COMPLETE.** Ready to proceed.
```

---

## Problem Statement

Three call sites invoke `NpcPriceCalculator.send(:calculate_eap_ceiling, ...)`, but that method does not exist. Runtime will raise `NoMethodError` when those paths execute. Acquisition also does not yet use the locked `evaluate_strategy` interface for strategy cost.

**Current behavior**: Dead EAP calls in ResourceAcquisitionService, DecisionTree, and SpecialMissionService; evaluate_strategy unused by acquisition surface.  
**Expected behavior**: All three dead calls removed; strategy cost reads go through `evaluate_strategy`; no new services; full decision tree left for follow-on work.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key change |
|---|---|---|
| `app/services/ai_manager/resource_acquisition_service.rb` | Path B; dead call ~line 140 | Replace with evaluate_strategy |
| `app/services/ai_manager/decision_tree.rb` | Dead call ~line 282 | Replace with evaluate_strategy |
| `app/services/special_mission_service.rb` | Dead call ~line 8 | Replace with evaluate_strategy |
| New focused spec(s) under `spec/services/ai_manager/` (and/or special_mission) | Coverage for changed behavior | **Create from scratch** — no ResourceAcquisitionService spec exists today |

### Reference Files — read but do not edit
| File | Why |
|---|---|
| `app/services/market/npc_price_calculator.rb` | `evaluate_strategy` API (locked) |
| `app/services/ai_manager/escalation_service.rb` | Spine; still on calculate_bid/ask — leave unchanged unless a one-line safe switch is obvious |
| `spec/services/market/npc_price_calculator_spec.rb` | Must remain green |
| `summaries/2026-09-07-RESEARCH-AI-MANAGER-SERVICE-INVENTORY-AND-GAPS.md` | Dual-path baseline |

### Migration
- [x] No migration needed

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then write the STATUS SYNTHESIS REPORT to summaries/.  
> Do not proceed to Step 1 until both are done.

### Step 0 — Move task file to active/ and update status (MANDATORY)

```bash
git mv projects/galaxy_game/tasks/backlog/ai-manager/2026-09-11-HIGH-ARCHITECTURE-ACQUISITION-WIRE-EVALUATE-STRATEGY.md \
       projects/galaxy_game/tasks/active/2026-09-11-HIGH-ARCHITECTURE-ACQUISITION-WIRE-EVALUATE-STRATEGY.md
```

Change YAML `status: backlog` → `status: active`.

Verify only one copy:

```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-09-11-HIGH-ARCHITECTURE-ACQUISITION-WIRE-EVALUATE-STRATEGY.md"
```

Paste find output in chat before proceeding.

### Step 1 — Confirm live API and all three dead calls (read-only)

From the app root (paths relative to project `app/` as used in-repo):

```bash
grep -n "def evaluate_strategy" app/services/market/npc_price_calculator.rb
grep -rn "calculate_eap_ceiling" app/services/ai_manager/ app/services/special_mission_service.rb
```

Expected: evaluate_strategy present; three calculate_eap_ceiling hits at the lines noted above (or updated lines if code shifted). Record exact lines in notes. If any hit is already gone, do not reintroduce it.

### Step 2 — Replace each dead call

For each of the three sites, replace:

```ruby
Market::NpcPriceCalculator.send(:calculate_eap_ceiling, settlement, material)
```

with a call of the form:

```ruby
result = Market::NpcPriceCalculator.evaluate_strategy(
  material: material,   # or resource — match local variable names
  location: settlement, # or @settlement / resolved location per existing pattern
  context: {}
)
# Use result.reference_cost / result.strategy_type / result.feasible? to preserve
# the previous intent (ceiling vs player offers / critical-need checks).
```

Do **not** add methods to the calculator. Prefer the smallest change that removes the crash and uses the structured result.

### Step 3 — EscalationService

Leave EscalationService unchanged unless a single, obvious, safe switch from `calculate_bid`/`calculate_ask` to `evaluate_strategy` is clearly correct for a strategy-cost read. If not obvious, document as follow-on and do not expand scope.

### Step 4 — Specs (create focused examples; no existing ResourceAcquisitionService spec)

Create minimal specs that cover:
- The changed ResourceAcquisitionService behavior (EAP/strategy check no longer calls missing method; uses evaluate_strategy).
- The changed DecisionTree and SpecialMissionService behavior (same).

Suggested locations (adjust if project conventions differ):
- `spec/services/ai_manager/resource_acquisition_service_spec.rb` (new)
- Focused examples in existing DecisionTree / SpecialMissionService specs if they exist; otherwise small new files

Run (no `-it`):

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/resource_acquisition_service_spec.rb 2>&1 | tail -30'
```

Also confirm calculator suite still green:

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/market/npc_price_calculator_spec.rb 2>&1 | tail -20'
```

Expected: 0 failures on new/updated specs; calculator suite remains green.

### Step 5 — Final grep and completion report

```bash
grep -rn "calculate_eap_ceiling" app/services/
```

Expected: **zero** hits in the three primary files (ideally zero across app/services for this method name).

Document follow-on: full decision-tree wiring (buy orders, player-offered missions, excess listing, cycler preference).

Do not commit until human approval.

---

## Acceptance Criteria

- [ ] Zero remaining references to `calculate_eap_ceiling` in:
  - `app/services/ai_manager/resource_acquisition_service.rb`
  - `app/services/ai_manager/decision_tree.rb`
  - `app/services/special_mission_service.rb`
- [ ] Each former call site uses `Market::NpcPriceCalculator.evaluate_strategy` (or a thin local wrapper over it)
- [ ] No new service class created
- [ ] No changes to `NpcPriceCalculator` public API
- [ ] Focused specs for the changed behavior exist and pass
- [ ] `spec/services/market/npc_price_calculator_spec.rb` still green
- [ ] No code added that implements proactive buy orders, player-offered missions, cycler preference, or excess listing
- [ ] Completion report lists follow-on work for the full decision tree

---

## Stop Conditions — escalate to user immediately if:

- Fix causes failures in specs you did not touch
- Same failure persists after two attempts
- Root cause is in a shared concern used across many services
- Architectural decision required beyond “use evaluate_strategy instead of dead method”
- Change set grows beyond the three primary files + focused specs (+ optional one-line EscalationService)
- Temptation to implement full buy-order or mission logic in this task

---

## Commit Instructions

Host only — never inside Docker:

```bash
git add app/services/ai_manager/resource_acquisition_service.rb \
        app/services/ai_manager/decision_tree.rb \
        app/services/special_mission_service.rb \
        spec/services/ai_manager/
# adjust spec paths to match what was actually created
git commit -m "architecture(ai-manager): replace dead calculate_eap_ceiling calls with evaluate_strategy"
```

Task file move on completion:

```bash
git mv projects/galaxy_game/tasks/active/2026-09-11-HIGH-ARCHITECTURE-ACQUISITION-WIRE-EVALUATE-STRATEGY.md \
       projects/galaxy_game/tasks/completed/2026-09/2026-09-11-HIGH-ARCHITECTURE-ACQUISITION-WIRE-EVALUATE-STRATEGY.md
git commit -m "chore: move 2026-09-11-HIGH-ARCHITECTURE-ACQUISITION-WIRE-EVALUATE-STRATEGY.md to completed/"
```

---

## Documentation

- [ ] No doc changes required for this wiring pass
- [ ] Flag in completion report if architecture docs should later state evaluate_strategy as the pricing source of truth for acquisition

---

## Dependencies

**Blocked by**: `2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-EVALUATE` (completed — commit `792e670b`)  
**Blocks**: Full acquisition decision-tree implementation (buy orders, player-offered missions, excess listing, cycler preference)  
**Related**:
- `2026-09-01-LOW-RESEARCH-AI-MANAGER-SERVICE-INVENTORY-AND-GAPS` (completed)
- `2026-09-03-MEDIUM-ARCHITECTURE-MATERIAL-SOURCING-AND-ACQUISITION-LOGIC` (still backlog)
- Gemini confirmation: calculator API locked; do not restore calculate_eap_ceiling

---

## Completion Report

*Filled in by the implementing agent after completion*

**Completed by**:  
**Completion date**:  
**Final test result**:  

### What was changed
- 

### Issues discovered
- 

### Follow-up tasks needed
- Full decision-tree wiring (player-first buy orders, player-offered missions, local production fallback, cycler preference, excess listing)
- Optional EscalationService switch to evaluate_strategy if not done here
- Broader integration specs for acquisition consuming evaluate_strategy

### Lessons learned
- 

---

## Handoff Summary

*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [three dead EAP calls removed] | [evaluate_strategy wired at those sites] | [next: full decision-tree task]
```
