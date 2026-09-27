---
status: backlog
priority: HIGH
type: architecture
system_domain: AI_MANAGER | MANUFACTURING | TERRA_SIM | CONTROLLERS | UNITS | OTHER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT | ISRU_PRODUCTION | SPEC_HEALTH | OTHER
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase9+/2026-06-21-HIGH-ARCHITECTURE-RAW-RESOURCE-EXTRACTION-PRICING.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/phase9+/2026-06-21-HIGH-ARCHITECTURE-RAW-RESOURCE-EXTRACTION-PRICING.md \
         projects/galaxy_game/tasks/active/2026-06-21-HIGH-ARCHITECTURE-RAW-RESOURCE-EXTRACTION-PRICING.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-06-21-HIGH-ARCHITECTURE-RAW-RESOURCE-EXTRACTION-PRICING.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Raw Resource Extraction Pricing — Break-Even Cost Model
**Status**: BACKLOG | ACTIVE | BLOCKED | COMPLETED
**Priority**: CRITICAL | HIGH | MEDIUM | LOW
**Type**: bug-fix | feature | refactor | architecture | data | documentation
**Created**: 2026-06-21
**Last Updated**: 2026-07-28

---

## Problem Statement

Harvested raw resources (mined gases, raw ore from asteroids/moons) have no material purchase cost — the resource itself is free. However the true cost of extraction includes craft fuel, depreciation, energy, and risk. Without a floor price model, `NpcPriceCalculator` has no basis for valuing these resources and cannot determine whether local extraction is economically viable vs Earth import.

**Current**: `NpcPriceCalculator` has no model for raw harvested resources; falls back to Earth import cost  
**Expected**: A break-even cost model that computes the minimum viable sell price for a harvested resource based on actual mission costs

## Evidence of Incompleteness

```bash
grep -n "extraction\|break_even\|floor_price" galaxy_game/app/services/market/npc_price_calculator.rb 2>/dev/null | head -5
# Returns no results — gap confirmed
```

## Files to Review/Create

| File | Purpose | Key Section |
|------|---------|-------------|
| `galaxy_game/app/services/market/npc_price_calculator.rb` | Price calculation service | Add extraction pricing method |
| `galaxy_game/app/services/tier1_price_modeler.rb` | Earth Anchor Price reference | Ceiling price logic |

## Acceptance Criteria

- [ ] Break-even cost model computes minimum viable sell price for harvested resources
- [ ] Formula includes: fuel_cost_round_trip + craft_depreciation_per_mission + energy_cost + risk_factor
- [ ] Floor price feeds into `cost_based_bid` as the floor price for NPC buyers
- [ ] If local extraction break-even exceeds Earth Anchor Price (EAP), importing from Earth is cheaper
- [ ] Design document includes formula derivation and example calculations

## Implementation Steps

1. Define break-even formula: `floor_price_per_kg = (fuel_cost_round_trip + craft_depreciation_per_mission + energy_cost + risk_factor) / total_kg_extracted`
2. Integrate with NpcPriceCalculator as floor price source for raw resources
3. Compare against EAP from Tier1PriceModeler — if break-even > EAP, mark resource as "import viable" not "extract viable"
4. Document design with example calculations

## Commit Instructions

```bash
git add galaxy_game/app/services/market/npc_price_calculator.rb docs/architecture/
git commit -m "feat: add raw resource extraction break-even pricing model"
```
