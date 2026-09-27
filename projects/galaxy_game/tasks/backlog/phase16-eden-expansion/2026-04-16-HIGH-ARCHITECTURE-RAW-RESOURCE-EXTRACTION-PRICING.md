---
status: superseded
priority: HIGH
type: architecture
system_domain: ECONOMICS
mvp_alignment: ISRU_PRODUCTION
local_worker_safe: true
created: 2026-04-16
last_updated: 2026-07-29
superseded_by: 2026-06-21-HIGH-ARCHITECTURE-RAW-RESOURCE-EXTRACTION-PRICING.md
---

## ⚡ Minimal Handoff (Copy this to send to agent)
```text
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/review/2026-04-16-HIGH-ARCHITECTURE-RAW-RESOURCE-EXTRACTION-PRICING.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/review/2026-04-16-HIGH-ARCHITECTURE-RAW-RESOURCE-EXTRACTION-PRICING.md \
         projects/galaxy_game/tasks/active/2026-04-16-HIGH-ARCHITECTURE-RAW-RESOURCE-EXTRACTION-PRICING.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-04-16-HIGH-ARCHITECTURE-RAW-RESOURCE-EXTRACTION-PRICING.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-04-16-ARCHITECTURE-RAW-RESOURCE-EXTRACTION-PRICING.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Raw Resource Extraction Pricing — Break-Even Cost Model
**Status**: BACKLOG
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-04-16
**Last Updated**: 2026-07-29  

## Context

Harvested raw resources (mined gases, raw ore from asteroids/moons) have no material purchase cost — the resource itself is free. However the true cost of extraction includes craft fuel, depreciation, energy, and risk.

**Current state (2026-07-29)**: `NpcPriceCalculator` has evolved significantly since this task was created in 2026-04-16. It now has:
- ✅ Cost-based pricing (`cost_based_bid`/`cost_based_ask`) using Earth import costs
- ✅ Market-based pricing (`market_based_bid`/`market_based_ask`) for mature markets
- ✅ `can_produce_locally?` check that distinguishes local production from Earth import
- ✅ `settlement_has_extraction_equipment?` to verify settlement has units producing the resource
- ✅ `Tier1PriceModeler` at `app/services/tier1_price_modeler.rb` for Earth Anchor Price (EAP)

**The gap**: `cost_based_bid` uses **Earth import cost** as the floor price. For harvested raw resources, this is wrong — the resource was already acquired (free material), so the floor should be the **break-even extraction cost** (fuel + depreciation + energy + risk per kg extracted). Without this, NPCs will bid below what it actually costs to extract that resource locally, making local extraction economically nonsensical.

**Example**: If extracting helium-3 from lunar regolith costs 50 GCC/kg in fuel/energy/depreciation but Earth import costs 80 GCC/kg, the current code bids at ~80 GCC (import cost). The correct bid should be ~50 GCC (extraction cost), making local extraction economically viable and giving NPCs a reason to buy from players who extract locally.

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Do not replace `calculate_import_cost` globally — it is correct for Earth-imported goods.
- ❌ Wrong: Change `cost_based_bid` to use extraction cost everywhere, breaking import pricing
- ✅ Right: Add a separate `extraction_break_even_cost` method and use it ONLY when `can_produce_locally?` returns true AND the resource is a harvested raw material (not a manufactured good)
- Why: The current code correctly uses import cost as floor for goods that must be imported. Only harvested resources need extraction-cost floor.

⚠️ **GOTCHA 2**: `Tier1PriceModeler` moved from `app/models/` to `app/services/` — task file references old path.
- ❌ Wrong: Reference `app/models/tier1_price_modeler.rb`
- ✅ Right: Reference `app/services/tier1_price_modeler.rb`

⚠️ **GOTCHA 3**: The break-even formula must account for the fact that extraction is a MISSION cost, not a per-unit production cost.
- ❌ Wrong: Simple fuel-per-kg calculation
- ✅ Right: Round-trip fuel + craft depreciation amortized over mission yield + energy cost + risk factor, all divided by total kg extracted. This is fundamentally different from `lunar_production.cost_per_kg` which assumes continuous factory operation.

---

## Problem Statement

**File**: `app/services/market/npc_price_calculator.rb`, private method `cost_based_bid`

**Current behavior**: `cost_based_bid` uses Earth import cost as the floor price for ALL resources, including harvested raw materials. For a resource like helium-3 extracted from lunar regolith, this means NPCs bid ~80 GCC/kg (Earth import cost) instead of ~50 GCC/kg (actual extraction cost), making local extraction appear uneconomical when it is not.

**Expected behavior**: `cost_based_bid` should use the break-even extraction cost as the floor price for harvested raw resources, and only fall back to Earth import cost for goods that must be imported.

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Raw Resource Extraction Pricing — Break-Even Cost Model
**Status**: [backlog → active → completed]
**Date**: YYYY-MM-DD

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `app/services/market/npc_price_calculator.rb` | Add extraction break-even floor | [not started / pending / done] |
| `app/services/tier1_price_modeler.rb` | EAP ceiling comparison | [not started / pending / done] |

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
- ❌ Replace `calculate_import_cost` globally — instead ✅ Add extraction-specific method, only for harvested resources
- ❌ Simple fuel-per-kg calculation — instead ✅ Mission-cost model (fuel + depreciation + energy + risk) / total_kg

---

**SYNTHESIS COMPLETE.** Ready to proceed with [PRIORITY 1 / PRIORITY 2 / etc].
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Acceptance Criteria
- [ ] Break-even cost model computes minimum viable sell price for harvested raw resources (not manufactured goods)
- [ ] Formula: `(fuel_cost_round_trip + craft_depreciation_per_mission + energy_cost + risk_factor) / total_kg_extracted`
- [ ] `cost_based_bid` uses extraction break-even as floor when `can_produce_locally?` is true for a harvested resource
- [ ] Earth import cost remains the floor for goods that must be imported (no regression)
- [ ] If break-even > EAP from Tier1PriceModeler, resource marked "import viable" not "extract viable"
- [ ] Design document includes formula derivation and example calculations

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
(standard — see Minimal Handoff block above)

### Step 1 — Audit current `cost_based_bid` behavior
Read `app/services/market/npc_price_calculator.rb#cost_based_bid` and trace the full call chain:
- `calculate_import_cost` → `calculate_earth_import_cost` → `Tier1PriceModeler#calculate_eap`
- Identify exactly where the floor price is set and what resources pass through it
- Document which resource types currently use import-cost floor vs should use extraction-cost floor

### Step 2 — Design break-even model
Create a new private method `extraction_break_even_cost(settlement, resource_name)` that:
1. Loads material data for the resource
2. Checks if it's a harvested raw resource (not manufactured)
3. Computes: `(fuel_cost_round_trip + craft_depreciation_per_mission + energy_cost + risk_factor) / total_kg_extracted`
4. Returns nil if no extraction data available (falls back to import cost)

### Step 3 — Integrate with `cost_based_bid`
Modify `cost_based_bid` to:
1. Check `can_produce_locally?(settlement, resource_name)` first
2. If true AND resource is harvested raw → use `extraction_break_even_cost` as floor
3. Otherwise → keep existing import-cost floor (no regression)

### Step 4 — Compare against EAP ceiling
In the same method or a helper:
1. Get EAP from `Tier1PriceModeler`
2. If break-even > EAP → mark resource as "import viable" (return nil or flag)
3. This prevents NPCs from bidding above what Earth charges for the same resource

### Step 5 — Verify
Run targeted specs:
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/market/npc_price_calculator_spec.rb 2>&1 | tail -40'
```
Also grep for any other code that calls `cost_based_bid` directly to check for regressions.

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add galaxy_game/app/services/market/npc_price_calculator.rb docs/architecture/raw_resource_extraction_pricing.md
git commit -m "feat: add raw resource extraction break-even cost model to NpcPriceCalculator"
```

**Task file move on completion:**
```bash
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy_game/tasks/review/2026-04-16-HIGH-ARCHITECTURE-RAW-RESOURCE-EXTRACTION-PRICING.md \
     projects/galaxy_game/tasks/completed/2026-07/2026-04-16-HIGH-ARCHITECTURE-RAW-RESOURCE-EXTRACTION-PRICING.md
git commit -m "chore: move 2026-04-16-HIGH-ARCHITECTURE-RAW-RESOURCE-EXTRACTION-PRICING.md to completed/"
```

---

## Stop Conditions — escalate to user immediately if:
- Root cause turns out to be a deliberate workaround for a missing factory/fixture system that itself needs design work
- `can_produce_locally?` logic is too fragile or incomplete to rely on as a gate
- Material data schema lacks extraction-specific fields (fuel, yield, risk) — would need schema change first
- Any architectural decision about pricing model hierarchy is required (e.g., should market-based pricing also use extraction floor?)

---

## Dependencies
**Blocked by**: none
**Blocks**: Phase 9+ Mars/Venus settlement economics (extraction pricing must exist before multi-world cost comparison)
**Related tasks**: None currently known

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

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
