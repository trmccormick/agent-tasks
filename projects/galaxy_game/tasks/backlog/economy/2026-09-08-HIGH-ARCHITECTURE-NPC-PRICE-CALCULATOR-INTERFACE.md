Markdown
---
status: backlog
priority: HIGH
type: architecture
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

You are Implementation Agent.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/economy/2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-INTERFACE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
```bash
git mv projects/galaxy_game/tasks/backlog/economy/2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-INTERFACE.md \
       projects/galaxy_game/tasks/active/2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-INTERFACE.md
```
Then open the moved file and change: status: backlog → status: active
Paste the output of both commands in chat before proceeding.
Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed

Tracked file: git mv (never cp or plain mv)

New/untracked file: mv then git add the final path

Never leave stale copies in the source folder

Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-INTERFACE.md"
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

# TASK: Implement Stable NpcPriceCalculator Interface for AI Manager Acquisition
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
- **MVP Impact Note**: Establishes the core triple-pricing service contract (`Consumable EAP` vs `Hardware CapEx Amortization` vs `Extraction-Floor`) while decoupling fee mechanics.
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
The AI Manager requires a stable location-aware pricing and strategy-selection interface (`AIManager::NpcPriceCalculator`) to unblock acquisition logic (Phase 3). This task establishes the core service contract supporting Earth and Luna contexts, categorizing strategies into consumable EAP, hardware CapEx amortization, and local extraction floors, while cleanly separating fee handling.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `summaries/2026-09-08-RESEARCH-FEE-MECHANISM-HISTORY.md` — background on unmerged fee history
- `summaries/2026-09-08-COORDINATION-SUMMARY-MULTI-AGENT.md` — multi-agent alignment notes

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Do not couple pricing with transaction or broker fees.
- ❌ Wrong: Baking `SettlementFees` or `TransactionFee` checks directly into the calculator evaluation logic.
- ✅ Right: Treat fees as completely decoupled; return baseline reference costs, leaving fee application and sanity checks to caller contexts.
- Why: Fee mechanisms are parked on `market-fee-hold` and are not live on main.

⚠️ **GOTCHA 2**: Do not mutate or hardcode global fallback values without location context.
- ❌ Wrong: Using Earth import cost as a blanket floor for all harvested raw resources across deep space nodes.
- ✅ Right: Respect location parameters (Earth vs Luna) and support the trio of strategy types (`:eap`, `:capex_amortization`, `:extraction_floor`).

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: 2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-INTERFACE.md
**Status**: backlog → active
**Date**: 2026-09-08

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `app/services/ai_manager/npc_price_calculator.rb` | Core pricing interface service | pending |
| `spec/services/ai_manager/npc_price_calculator_spec.rb` | RSpec test coverage | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
A fully tested `AIManager::NpcPriceCalculator` service supporting `.evaluate(material:, location:, context: {})` returning structured strategy metrics.

### Critical Gotchas I Will Avoid
- ❌ Coupling fee logic into main pricing calculator — instead ✅ Keep fees completely decoupled.
- ❌ Hardcoding Earth import floors globally — instead ✅ Support location-aware strategy branching.

---

**SYNTHESIS COMPLETE.** Ready to proceed with implementation.
```

---

## Problem Statement
The AI Manager's acquisition system lacks a stable, location-aware pricing contract to evaluate sourcing strategies (buy vs produce vs wait vs emergency), blocking Phase 3 acquisition work.

**Current behavior**: No centralized service exposes triple-pricing strategy evaluation (`:eap`, `:capex_amortization`, `:extraction_floor`) for AI acquisition routines.
**Expected behavior**: `AIManager::NpcPriceCalculator.evaluate(material:, location:, context: {})` is implemented, tested, and callable by Grok's acquisition logic.

---

## Files Involved

### Primary Files — you will edit/create these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/ai_manager/npc_price_calculator.rb` | Implements pricing entry point and strategy evaluation | `#evaluate` |
| `spec/services/ai_manager/npc_price_calculator_spec.rb` | Unit tests for pricing contract and strategy routing | RSpec suite |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `spec/factories/` | Factory structure for material/location fixtures |

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/economy/2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-INTERFACE.md \
       projects/galaxy_game/tasks/active/2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-INTERFACE.md
```

Update YAML status to `active`, then run verification:

```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-INTERFACE.md"
```

**Paste the output of the find command in chat before proceeding.**
Expected: exactly one result, at the `active/` path.

### Step 1 — Create `app/services/ai_manager/npc_price_calculator.rb`
Implement the service wrapping the agreed method signature and structured return object:

```ruby
module AIManager
  class NpcPriceCalculator
    def self.evaluate(material:, location:, context: {})
      new(material: material, location: location, context: context).evaluate
    end

    def initialize(material:, location:, context: {})
      @material = material
      @location = location
      @context = context
    end

    def evaluate
      strategy = determine_optimal_strategy
      
      OpenStruct.new(
        material_sku: @material.respond_to?(:sku) ? @material.sku : @material.to_s,
        location_id: @location.respond_to?(:id) ? @location.id : @location.to_s,
        strategy_type: strategy[:type],          # :eap, :capex_amortization, or :extraction_floor
        reference_cost: strategy[:cost],         # Calculated numerical baseline cost per unit
        breakdown: strategy[:breakdown],         # Transparent component costs for logging
        feasible?: strategy[:feasible]           # Boolean flag for AI decision tree
      )
    end

    private

    def determine_optimal_strategy
      # Phase 5 baseline implementation supporting Earth and Luna contexts
      # Stubbing default fallback or dispatching based on location/context parameters
      {
        type: :eap,
        cost: 100.0,
        breakdown: { base_import: 100.0 },
        feasible: true
      }
    end
  end
end
```

### Step 2 — Create RSpec test suite at `spec/services/ai_manager/npc_price_calculator_spec.rb`
Write comprehensive tests covering parameter validation, structured return keys, and strategy types (`:eap`, `:capex_amortization`, `:extraction_floor`).

### Step 3 — Verify via Docker RSpec

> CRITICAL EXECUTION MANDATE: All RSpec commands must use the Docker wrapper below.
> The container working directory is already /home/galaxy_game — do NOT add cd /home/galaxy_game.
> Never run bare local test commands. Never fabricate test results. Actually run the specs.

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/npc_price_calculator_spec.rb 2>&1 | tail -20'
```

Expected result: X examples, 0 failures

---

## Acceptance Criteria
- [ ] `AIManager::NpcPriceCalculator.evaluate(material:, location:, context: {})` successfully executes and returns an OpenStruct with `strategy_type`, `reference_cost`, `breakdown`, and `feasible?`.
- [ ] Isolation specs pass with 0 failures.
- [ ] No regression introduced across main test suite.

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
git add app/services/ai_manager/npc_price_calculator.rb spec/services/ai_manager/npc_price_calculator_spec.rb
git commit -m "architecture: implement AIManager::NpcPriceCalculator service for Phase 3 acquisition unblock"
git push
```

**Task file move on completion:**

```bash
# Tracked file (already committed): use git mv
git mv projects/galaxy_game/tasks/active/2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-INTERFACE.md \
       projects/galaxy_game/tasks/completed/2026-09/2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-INTERFACE.md
git commit -m "chore: move 2026-09-08-HIGH-ARCHITECTURE-NPC-PRICE-CALCULATOR-INTERFACE.md to completed/"
```

---

## Documentation
- [ ] No doc changes needed

---

## Dependencies
**Blocked by**: none
**Blocks**: none
**Related tasks**: none