---
status: active
priority: HIGH
type: architecture
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
Agents receive this exact text as the startup contract. Every word matters.

You are Implementation Agent.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/economy/2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-EVALUATE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
```bash
git mv projects/galaxy_game/tasks/backlog/economy/2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-EVALUATE.md \
       projects/galaxy_game/tasks/active/2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-EVALUATE.md
```
Then open the moved file and change: status: backlog → status: active
Paste the output of both commands in chat before proceeding.
Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed

Tracked file: git mv (never cp or plain mv)

New/untracked file: mv then git add the final path

Never leave stale copies in the source folder

Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-EVALUATE.md"
Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
Chat is for questions only — never paste synthesis into chat (formatting breaks).


**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Add `.evaluate_strategy` to Existing `Market::NpcPriceCalculator` for AI Manager Acquisition
**Status**: BACKLOG
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-09-08
**Last Updated**: 2026-09-08

---

## Local Worker Triage Report (Optional — for backlog review only)
- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS
- **MVP Alignment**: VALID — Unblocks Grok's Phase 3 Acquisition logic for Earth and Luna nodes.
- **MVP Impact Note**: Adds location-aware strategy evaluation to the EXISTING `Market::NpcPriceCalculator` (14+ live callers). Does NOT create a new service. Refactors `cost_based_bid` to branch between EAP and extraction floor.
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Primary local worker with terminal access.
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

The AI Manager's acquisition system (Grok Phase 3) needs a stable, location-aware pricing interface to evaluate sourcing strategies (buy vs produce vs wait vs emergency). This task adds that interface to the **EXISTING** `Market::NpcPriceCalculator` — it does NOT create a new service.

### Why Refactor Existing, Not Create New

`Market::NpcPriceCalculator` already has **14+ live callers** across settlements, marketplaces, and AI acquisition lanes:
- `BaseSettlement#buy_resource` (line 90)
- `MegaProject` cost estimation (mega_project.rb:79)
- `Marketplace#buy_from_npc` / `sell_to_npc` (marketplace.rb:26,90)
- `Order#price_for` (order.rb:28,30)
- **`EscalationService`** (escalation_service.rb:21,522,538) — acquisition lane
- **`ResourceAcquisitionService`** (resource_acquisition_service.rb:80,140) — acquisition lane
- `DecisionTree#eap_ceiling` (decision_tree.rb:282)

Creating a duplicate `AIManager::NpcPriceCalculator` would cause naming collision and architectural fracture. The interface must live in the existing class under the `Market::` namespace.

### What This Task Does
1. Adds a new **public class method** `.evaluate_strategy(material:, location:, context:)` to `Market::NpcPriceCalculator`
2. Refactors `cost_based_bid` to branch between EAP (Earth/Luna) and extraction floor (distant bodies)
3. Keeps fee handling completely decoupled — fees are applied by callers, not baked into the calculator

### What This Task Does NOT Do
- ❌ Create a new service class
- ❌ Wire up `SettlementFees` or `TransactionFee` (neither is live on main; see fee history research)
- ❌ Implement temporal pricing or infrastructure state awareness (Phase 7+ scope)
- ❌ Change existing method signatures (`calculate_ask`, `calculate_bid`, `calculate_spread`)

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Do NOT create a new service class. Work on the EXISTING `Market::NpcPriceCalculator` at `galaxy_game/app/services/market/npc_price_calculator.rb`.

⚠️ **GOTCHA 2**: Do not couple pricing with transaction or broker fees.
- ❌ Wrong: Baking `SettlementFees` or `TransactionFee` checks into the calculator evaluation logic.
- ✅ Right: Return baseline reference costs; leave fee application to caller contexts.
- Why: Fee mechanisms are parked on `market-fee-hold` and are not live on main. See `summaries/2026-09-08-RESEARCH-FEE-MECHANISM-HISTORY.md`.

⚠️ **GOTCHA 3**: Do not mutate or hardcode global fallback values without location context.
- ❌ Wrong: Using Earth import cost as a blanket floor for all harvested raw resources across deep space nodes.
- ✅ Right: Respect location parameters (Earth vs Luna vs Mars) and support the trio of strategy types (`:eap`, `:capex_amortization`, `:extraction_floor`).

⚠️ **GOTCHA 4**: The existing `cost_based_bid` method currently enforces Earth import cost as a price floor for ALL resources. This is the primary target for refactoring. For distant bodies (Mars, Venus, outer stations) where physical import is economically unviable, local extraction break-even should be the floor instead.

### Existing Code Structure (Read Before Starting)

The existing `Market::NpcPriceCalculator` has two pricing modes:
- **Cost-based** (bootstrap markets): Uses `Tier1PriceModeler` for EAP + local production cost
- **Market-based** (mature markets): Uses price history averages

Key methods to understand:
- `calculate_import_cost(settlement, resource_name)` — checks local production first, then Earth import
- `calculate_earth_import_cost(settlement, resource_name)` — calls `Tier1PriceModeler` with destination
- `cost_based_bid(settlement, resource_name, context)` — **primary target for refactoring**
- `can_produce_locally?(settlement, resource_name)` — checks PrecursorCapabilityService

### Product Rules to Preserve (From Grok's AI Manager Handoff)

These rules govern the pricing strategy branching. The `.evaluate_strategy` method must respect them:

1. **EAP = Earth cost + transport** (expensive fallback); new local list seeds at **EAP × 0.9** without history
2. **Player-first market buy** when price sane **and** real **GCC** on hand
3. **Too expensive + harvestable → self-harvest**, keep need, list excess
4. **Normal shortage → wait for cycler/resupply** over feeding gouges (unless emergency)
5. **Preferred path:** real GCC + logistics corp as go-between
6. **Virtual ledger:** DC–DC / NPC–NPC; not default player market
7. **DC priority:** keep settlement running even at trade imbalance

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: 2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-EVALUATE.md
**Status**: backlog → active
**Date**: 2026-09-08

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `app/services/market/npc_price_calculator.rb` | Existing class — add `.evaluate_strategy` method | pending |
| `spec/services/market/npc_price_calculator_spec.rb` | Add tests for new method | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
A new `.evaluate_strategy(material:, location:, context:)` class method on the EXISTING `Market::NpcPriceCalculator` that returns a structured strategy evaluation object. No new service classes created. Existing methods unchanged.

### Critical Gotchas I Will Avoid
- ❌ Creating a new `AIManager::NpcPriceCalculator` or any duplicate service — instead ✅ Work on existing `Market::NpcPriceCalculator`
- ❌ Coupling fee logic into the calculator — instead ✅ Keep fees completely decoupled
- ❌ Hardcoding Earth import floors globally — instead ✅ Support location-aware strategy branching
- ❌ Changing existing method signatures (`calculate_ask`, `calculate_bid`) — instead ✅ Add new public method only

---

**SYNTHESIS COMPLETE.** Ready to proceed with implementation.
```

---

## Problem Statement
The AI Manager's acquisition system lacks a stable, location-aware pricing contract to evaluate sourcing strategies (buy vs produce vs wait vs emergency), blocking Phase 3 acquisition work.

**Current behavior**: `Market::NpcPriceCalculator.cost_based_bid` enforces Earth import cost as a blanket price floor for ALL resources across ALL locations — breaks down for distant bodies where physical import is economically unviable.

**Expected behavior**: The EXISTING `Market::NpcPriceCalculator` gains a new `.evaluate_strategy(material:, location:, context:)` class method that returns structured strategy metrics, and `cost_based_bid` branches between EAP (Earth/Luna) and extraction floor (distant bodies).

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Change |
|---|---|---|
| `app/services/market/npc_price_calculator.rb` | EXISTING class — add `.evaluate_strategy` method | New public class method; refactor `cost_based_bid` |
| `spec/services/market/npc_price_calculator_spec.rb` | EXISTING spec file — add tests for new method | Tests for `.evaluate_strategy` and refactored `cost_based_bid` |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/services/tier1_price_modeler.rb` | Understand EAP calculation (Earth cost + transport) |
| `app/services/ai_manager/escalation_service.rb` | See how acquisition lane currently uses pricing |
| `app/services/ai_manager/resource_acquisition_service.rb` | See how acquisition lane currently uses pricing |
| `summaries/2026-09-08-RESEARCH-FEE-MECHANISM-HISTORY.md` | Fee mechanism history — neither SettlementFees nor TransactionFee is live |
| `summaries/2026-09-08-COORDINATION-SUMMARY-MULTI-AGENT.md` | Multi-agent alignment notes, §8 Grok→Gemini dependency |

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/economy/2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-EVALUATE.md \
       projects/galaxy_game/tasks/active/2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-EVALUATE.md
```

Update YAML status to `active`, then run verification:

```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-EVALUATE.md"
```

**Paste the output of the find command in chat before proceeding.**
Expected: exactly one result, at the `active/` path.

### Step 1 — Add `.evaluate_strategy` to Existing `Market::NpcPriceCalculator`

Add a new **public class method** to the EXISTING `Market::NpcPriceCalculator` (do NOT create a new class):

```ruby
# app/services/market/npc_price_calculator.rb

module Market
  class NpcPriceCalculator
    class << self
      
      # ... existing methods (calculate_ask, calculate_bid, etc.) remain unchanged ...
      
      # Evaluate pricing strategies for AI Manager acquisition decisions.
      # Returns a structured evaluation object suitable for AI decision tree consumption.
      #
      # @param material [String, Hash] Resource name or material data hash
      # @param location [String, Settlement] Location identifier or settlement object
      # @param context [Hash] Optional: { quantity:, urgency:, player_gcc: }
      # @return [OpenStruct] Structured strategy evaluation with keys:
      #   - strategy_type [:eap, :capex_amortization, :extraction_floor]
      #   - reference_cost [Float] Baseline cost per unit (GCC)
      #   - breakdown [Hash] Component costs for logging/transparency
      #   - feasible? [Boolean] Whether this strategy is viable at current location
      #   - notes [String] Human-readable explanation
      def evaluate_strategy(material:, location:, context: {})
        new(material: material, location: location, context: context).evaluate_strategy
      end
      
      # ... rest of existing class ...
    end
  end
end
```

The instance method `#evaluate_strategy` should implement the following strategy branching logic:

1. **Determine location type**: Earth → EAP; Luna → EAP (viable); Mars/Venus/outer → extraction floor or CapEx amortization
2. **For EAP locations** (Earth, Luna): Use existing `calculate_earth_import_cost` via `Tier1PriceModeler`
3. **For distant locations** (Mars, Venus, outer stations): 
   - Check if local production is possible (`can_produce_locally?`)
   - If yes → extraction floor = local production cost
   - If no → CapEx amortization estimate (equipment import ÷ operational lifespan)
4. **Return structured object** with all three strategies evaluated (even if some are infeasible)

### Step 2 — Refactor `cost_based_bid` to Branch by Location

The existing `cost_based_bid` method currently uses Earth import cost as a blanket floor. Refactor it to branch:

```ruby
def cost_based_bid(settlement, resource_name, context)
  import_cost = calculate_import_cost(settlement, resource_name)
  return nil unless import_cost && import_cost > 0
  
  # For distant bodies where import is economically unviable, use extraction floor
  celestial_body = settlement&.location&.celestial_body
  is_deep_space = deep_space_location?(celestial_body)
  
  base_cost = is_deep_space ? calculate_extraction_floor(settlement, resource_name) : import_cost
  
  discount = context[:discount] || EconomicConfig.npc_buy_discount(market_exists: false)
  adjusted_discount = apply_inventory_adjustments(settlement, resource_name, discount, context)
  
  (base_cost * adjusted_discount).round(2)
end
```

Add helper method `deep_space_location?(celestial_body)` that returns true for bodies where Earth import is economically unviable (Mars, Venus, outer stations).

### Step 3 — Create RSpec Test Suite

Add tests to the EXISTING spec file at `spec/services/market/npc_price_calculator_spec.rb`:

1. **`.evaluate_strategy` integration specs**:
   - Returns OpenStruct with all required keys (`strategy_type`, `reference_cost`, `breakdown`, `feasible?`, `notes`)
   - EAP strategy for Earth/Luna locations
   - Extraction floor strategy for Mars/Venus locations
   - Infeasible flag when no local production possible and import cost is prohibitive

2. **Refactored `cost_based_bid` specs**:
   - Existing behavior preserved for Earth/Luna (no regression)
   - New deep-space branching behavior tested

3. **Fee decoupling verification**:
   - No references to `SettlementFees`, `TransactionFee`, or fee calculations in the calculator

### Step 4 — Verify via Docker RSpec

> CRITICAL EXECUTION MANDATE: All RSpec commands must use the Docker wrapper below.
> The container working directory is already /home/galaxy_game — do NOT add cd /home/galaxy_game.
> Never run bare local test commands. Never fabricate test results. Actually run the specs.

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/market/npc_price_calculator_spec.rb 2>&1 | tail -20'
```

Expected result: X examples, 0 failures (including both existing and new tests)

---

## Acceptance Criteria
- [ ] `.evaluate_strategy(material:, location:, context:)` successfully executes and returns an OpenStruct with `strategy_type`, `reference_cost`, `breakdown`, `feasible?`, and `notes`
- [ ] EAP strategy returned for Earth/Luna locations; extraction floor or CapEx for distant bodies
- [ ] Refactored `cost_based_bid` preserves existing behavior for Earth/Luna (no regression)
- [ ] Deep-space branching tested with Mars/Venus location context
- [ ] No references to fee mechanisms (`SettlementFees`, `TransactionFee`) in the calculator
- [ ] All existing tests pass (no regression across the full spec file)
- [ ] No new service classes created — all changes are additions/refactors to existing `Market::NpcPriceCalculator`

---

## Stop Conditions — escalate to user immediately if:
- Fix causes new failures in specs you did not touch
- Same failure persists after two attempts
- Root cause is in a shared concern, base class, or factory used across many specs
- A database migration is needed that wasn't anticipated
- Any architectural decision is required (e.g., "should deep_space_location? include Phobos/Deimos?")
- Fix requires changing more files than the task specifies

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:

```bash
git add app/services/market/npc_price_calculator.rb spec/services/market/npc_price_calculator_spec.rb
git commit -m "architecture: add evaluate_strategy to Market::NpcPriceCalculator for Phase 3 acquisition unblock"
git push
```

**Task file move on completion:**

```bash
# Tracked file (already committed): use git mv
git mv projects/galaxy_game/tasks/active/2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-EVALUATE.md \
       projects/galaxy_game/tasks/completed/2026-09/2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-EVALUATE.md
git commit -m "chore: move 2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-EVALUATE.md to completed/"
```

---

## Documentation
- [ ] No doc changes needed

---

## Dependencies
**Blocked by**: none
**Blocks**: Grok Phase 3 (Acquisition Logic) — acquisition waits for this interface to be stable
**Related tasks**: 
- `2026-09-01-LOW-RESEARCH-AI-MANAGER-SERVICE-INVENTORY-AND-GAPS.md` (completed, synthesis in `summaries/`)
- Gemini Phase 2 (temporal pricing, infrastructure state awareness) — out of scope for this task

---

## Design Intent for AI Manager (From Grok's Handoff)

Once this interface is stable, acquisition will wire to it as follows:

1. **Player-first proactive buy orders** as the default
2. **Player-offered missions** as the first escalation
3. **Local production / harvest** only when market + player-mission paths fail or are clearly inferior
4. **Cycler/resupply preference** on normal shortages
5. **Hard emergency** only when time-to-critical demands it
6. **System as backstop** when players are absent

The interface returns structured strategy metrics; acquisition evaluates them against the decision tree above. Fees are an input to the price, not part of the evaluation logic.

---

**Status**: Ready for dispatch. This task unblocks Grok Phase 3 by providing the stable pricing interface that acquisition depends on.
