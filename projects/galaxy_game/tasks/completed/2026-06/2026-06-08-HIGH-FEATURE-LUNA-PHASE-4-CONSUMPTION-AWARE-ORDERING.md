---
status: completed
priority: HIGH
type: feature
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: false
completed_date: 2026-06-12
commit_hash: 01919af8
---

# TASK: Luna Phase 4 — Consumption-Aware Ordering + Precursor Phase Awareness
**Status**: COMPLETED ✅
**Priority**: HIGH
**Type**: feature
**Created**: 2026-06-08
**Last Updated**: 2026-06-12
**Completed**: 2026-06-12 (commit: 01919af8)

---

## Local Worker Triage Report
- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS
- **MVP Alignment**: VALID — directly extends Phase 3 import logic with transit-aware quantities
  and precursor phase gating
- **MVP Impact Note**: Phase 3 orders `deficit = need - have` only. On a 3-day Earth→Luna transit,
  the settlement consumes resources during transit and arrives short. Phase 4 fixes this.
- **Action Line**: READY FOR DISPATCH — blocked only by cleanup task
  (2026-06-08-HIGH-BUG-FIX-...REMOVE-PLURAL-API...)

---

## Agent Assignment

**Assigned To**: Qwen3.5-27B (M4 Copilot)
**Why This Agent**: Multi-file feature with spec writing — needs terminal access and deep reasoning
**Supervision Level**: Watched carefully

---

## Context

Phase 3 wired `ShortageDetector` and `ImportRequestGenerator` into `StrategySelector`. The
`execute_cost_reduction` method calculates `deficit = need - have` and orders exactly that
quantity. This is correct as a baseline but incomplete — a 3-day transit means the settlement
consumes resources during the flight and arrives in shortage again.

Phase 4 adds two capabilities:

**1. Transit-aware ordering** — import quantity = deficit + (transit_days × daily_consumption_rate)
× margin_factor. This ensures the settlement has enough stock to cover both the current deficit
AND consumption during the transit window.

**2. Precursor phase awareness** — Luna's precursor phase is fully robotic. No humans means no
life support consumption (food, water, oxygen). The AI Manager must not order survival consumables
until the human arrival gate is cleared. `current_population == 0` is the canonical signal —
the settlement has the `PopulationManagement` concern which validates this column. A
`precursor_phase?` helper using this check gates life-support ordering.

Both capabilities are documented as known limitations in the `KNOWN LIMITATIONS` comment block
at the bottom of `execute_cost_reduction` in `strategy_selector.rb`.

**Relevant Architecture Docs**:
- `docs/new_agent/rules/GUARDRAILS.md`
- `docs/new_agent/rules/DECISIONS.md`

---

## Key Constants (already defined — do not redefine)

```ruby
# On Logistics::Contract
Logistics::Contract::EARTH_LUNA_TRANSIT_DAYS = 3

# On GameConstants (confirm these exist before use — read the file)
GameConstants::FOOD_PER_PERSON      # 2 per person per day
GameConstants::WATER_PER_PERSON     # 1 per person per day
GameConstants::ENERGY_PER_PERSON    # 3 per person per day

# Margin factors (define as constants in StrategySelector or a config — do not hardcode)
LUNA_MARGIN_FACTOR = 1.15   # 15% NASA margin
```

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/ai_manager/strategy_selector.rb` | AI decision logic | `#execute_cost_reduction` (private) |
| `spec/services/ai_manager/strategy_selector_spec.rb` | Specs for StrategySelector | Phase 4 spec block |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/services/logistics/shortage_detector.rb` | Confirm shortage hash shape: `{ material:, need:, have:, priority: }` |
| `app/models/logistics/contract.rb` | Confirm `EARTH_LUNA_TRANSIT_DAYS` constant location |
| `app/models/game_constants.rb` (or equivalent) | Confirm GameConstants consumption values exist |
| `spec/factories/settlements.rb` | Factory structure for settlement with operational_data |

### Migration (if needed)
- [x] No migration needed

---

## Problem Statement

### Problem 1 — Transit Consumption Blindness

**Current behavior** in `execute_cost_reduction`:
```ruby
deficit = [0, shortage[:need] - shortage[:have]].max
# ... calls generate_import_request with shortage: { need: deficit }
```

**Expected behavior**:
```ruby
transit_days = Logistics::Contract::EARTH_LUNA_TRANSIT_DAYS  # 3
daily_rate = daily_consumption_rate_for(shortage[:material], settlement)
transit_buffer = (transit_days * daily_rate * LUNA_MARGIN_FACTOR).ceil
order_quantity = deficit + transit_buffer
```

For materials with no consumption rate (non-consumables like structural components), 
`daily_consumption_rate_for` returns 0 and the order quantity equals the deficit as before.

### Problem 2 — Precursor Phase Life Support Orders

**Current behavior**: ShortageDetector returns food/water/energy shortages even when the
settlement has no humans. `execute_cost_reduction` orders them anyway.

**Expected behavior**: If `settlement.precursor_phase?` returns true (or equivalent — see
Step 1 for how to detect this), skip ordering of life-support consumables
(food, water, oxygen, energy). Log a warning that life support ordering is gated until
human arrival.

Life-support material list to gate:
```ruby
LIFE_SUPPORT_MATERIALS = %w[Food Water Oxygen Energy].freeze
```

---

## Implementation Steps

> Read each reference file before writing any code. Do not assume APIs.

### Step 1 — Confirm current_population column exists

Run this grep to confirm the column and concern are present:

```bash
grep -rn "current_population" \
  ~/Documents/git/galaxyGame/galaxy_game/app/models/ --include="*.rb" | head -10
```

Expected: `PopulationManagement` concern validates `current_population` as a non-negative
integer. `SettlementCore` includes this concern. The column is on the settlements table.

Precursor phase detection uses this column directly — no new column or flag needed:

```ruby
def precursor_phase?(settlement)
  settlement.current_population.zero?
end
```

Add this as a private helper in `StrategySelector`. Do not add it to the model during this task.
Report the grep result in synthesis report.

### Step 2 — Confirm GameConstants consumption values

```bash
grep -rn "FOOD_PER_PERSON\|WATER_PER_PERSON\|ENERGY_PER_PERSON" \
  ~/Documents/git/galaxyGame/galaxy_game/app/ --include="*.rb"
```

If constants exist, use them. If not, define local constants at the top of the relevant
private section in `strategy_selector.rb`:

```ruby
# Temporary — replace with GameConstants references when available
DAILY_CONSUMPTION_RATES = {
  'Food'   => 2,
  'Water'  => 1,
  'Energy' => 3,
  'Oxygen' => 1
}.freeze
```

Report the grep result in synthesis report.

### Step 3 — Confirm EARTH_LUNA_TRANSIT_DAYS

```bash
grep -rn "EARTH_LUNA_TRANSIT_DAYS" \
  ~/Documents/git/galaxyGame/galaxy_game/app/ --include="*.rb"
```

Use the constant if it exists. If not, define locally in StrategySelector private section:
```ruby
LUNA_TRANSIT_DAYS = 3
```

### Step 4 — Add private helpers to StrategySelector

Add these private helpers to `strategy_selector.rb` below the existing private methods:

```ruby
# Returns daily consumption rate for a material at this settlement.
# Non-consumable materials return 0 (no transit buffer needed).
def daily_consumption_rate_for(material, settlement)
  DAILY_CONSUMPTION_RATES.fetch(material, 0)
end

# Returns true if settlement is in robotic precursor phase (no humans, no life support needed).
# current_population == 0 is the canonical signal — PopulationManagement concern validates this column.
def precursor_phase?(settlement)
  settlement.current_population.zero?
end
```

### Step 5 — Update execute_cost_reduction

Replace the shortage processing loop in `execute_cost_reduction`. The existing loop is:

```ruby
shortage_report[:survival_shortages].each do |shortage|
  deficit = [0, shortage[:need] - shortage[:have]].max
  next if deficit == 0
  begin
    import_req = Logistics::ImportRequestGenerator.generate_import_request(
      source: source,
      destination: settlement,
      shortage: { material: shortage[:material], need: deficit }
    )
    ...
  end
end
```

Replace with:

```ruby
transit_days = defined?(Logistics::Contract::EARTH_LUNA_TRANSIT_DAYS) ?
  Logistics::Contract::EARTH_LUNA_TRANSIT_DAYS : LUNA_TRANSIT_DAYS

shortage_report[:survival_shortages].each do |shortage|
  # Gate life support ordering during precursor (robotic) phase
  if precursor_phase?(settlement) && LIFE_SUPPORT_MATERIALS.include?(shortage[:material])
    Rails.logger.warn "[StrategySelector] Skipping #{shortage[:material]} order — " \
                      "#{settlement.name} in precursor phase, no humans present"
    next
  end

  deficit = [0, shortage[:need] - shortage[:have]].max
  next if deficit == 0

  # Add transit buffer — covers consumption during Earth→Luna transit window
  daily_rate = daily_consumption_rate_for(shortage[:material], settlement)
  transit_buffer = (transit_days * daily_rate * LUNA_MARGIN_FACTOR).ceil
  order_quantity = deficit + transit_buffer

  begin
    import_req = Logistics::ImportRequestGenerator.generate_import_request(
      source: source,
      destination: settlement,
      shortage: { material: shortage[:material], need: order_quantity }
    )
    success_count += 1 if import_req
    Rails.logger.info "[StrategySelector] Import request created for #{shortage[:material]} " \
                      "(deficit: #{deficit}, transit_buffer: #{transit_buffer}, " \
                      "total: #{order_quantity} units)"
  rescue Logistics::ImportRequestGenerator::ImportRequestError => e
    Rails.logger.warn "[StrategySelector] Failed to create import for #{shortage[:material]}: #{e.message}"
  end
end
```

Also add these constants to the private section of StrategySelector (before the helpers):

```ruby
LUNA_MARGIN_FACTOR = 1.15
LIFE_SUPPORT_MATERIALS = %w[Food Water Oxygen Energy].freeze
LUNA_TRANSIT_DAYS = 3  # fallback if Logistics::Contract::EARTH_LUNA_TRANSIT_DAYS unavailable
DAILY_CONSUMPTION_RATES = {
  'Food'   => 2,
  'Water'  => 1,
  'Energy' => 3,
  'Oxygen' => 1
}.freeze
```

### Step 6 — Update KNOWN LIMITATIONS comment block

In `strategy_selector.rb`, update the `KNOWN LIMITATIONS` block to reflect Phase 4 completion:

```ruby
# === KNOWN LIMITATIONS (Phase 4) ===
# - Transit Consumption: RESOLVED Phase 4 — order_quantity = deficit + (transit_days × daily_rate × margin)
# - Precursor Phase Gating: RESOLVED Phase 4 — life support orders skipped until human arrival gate
# - Single-Source Routing: All imports default to Cape Canaveral Spaceport via AstroLift.
#   LEO depot routing and multi-source selection are Phase 5+ concerns.
# - Skimmer Supply Chain Not Yet Available: Gas shortages (Methane, Hydrogen) routed to
#   Earth imports via AstroLift. Correct for early game. Phase 5+ optimization.
# - HLT Fuel Consumption Uncertainty: LOX/Methane requirements not yet calibrated.
#   ISRU LOX production comes online first; methane via resupply until validated.
# - Consumption rates hardcoded — replace with GameConstants references when available.
# - DAILY_CONSUMPTION_RATES do not account for population size yet — Phase 5 concern.
# === END LIMITATIONS ===
```

### Step 7 — Write specs

Add a Phase 4 spec block to `spec/services/ai_manager/strategy_selector_spec.rb`.

Minimum 6 specs covering:

1. **Transit buffer added** — consumable material (Food): order_quantity = deficit +
   (3 days × 2/day × 1.15 margin).ceil = deficit + 7
2. **Non-consumable no buffer** — structural material (Iron or similar): order_quantity = deficit
   (transit_buffer = 0)
3. **Precursor phase gates food** — settlement with `current_population: 0`,
   Food shortage present → `generate_import_request` NOT called for Food
4. **Precursor phase gates water** — settlement with `current_population: 0`,
   Water shortage → not ordered
5. **Precursor phase allows non-life-support** — settlement with `current_population: 0`,
   Iron shortage present → `generate_import_request` IS called for Iron
6. **Post-precursor orders life support** — settlement with `current_population: 10`,
   Food shortage → `generate_import_request` called with transit-buffered quantity

Use the existing Phase 3 spec pattern for stubbing ShortageDetector and
ImportRequestGenerator.

### Step 8 — Run targeted spec suite

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/strategy_selector_spec.rb 2>&1 | tail -20'
```

Then full ai_manager suite:

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/ 2>&1 | tail -10'
```

Expected: 746+ examples (740 + 6 new), 0 failures, 4 pending.

### Step 9 — Synthesis Report (before commit, wait for approval)

```
SYNTHESIS REPORT
Phase 4 — Consumption-Aware Ordering + Precursor Phase Awareness

CONSTANTS CONFIRMED
EARTH_LUNA_TRANSIT_DAYS: [found at X / not found, using local LUNA_TRANSIT_DAYS = 3]
GameConstants consumption values: [found / not found, using local DAILY_CONSUMPTION_RATES]
precursor_phase? detection: current_population.zero? — confirmed via PopulationManagement concern

TRANSIT BUFFER EXAMPLE (Food, deficit=10)
daily_rate: 2
transit_days: 3
transit_buffer: ceil(3 × 2 × 1.15) = 7
order_quantity: 10 + 7 = 17

PRECURSOR GATE EXAMPLE
settlement.current_population = 0
Food shortage present → skipped with warning log
Iron shortage present → ordered with transit buffer

SPEC RESULTS
strategy_selector_spec.rb: X examples, 0 failures
ai_manager suite: X examples, 0 failures, 4 pending

RISK
Low — additive changes to execute_cost_reduction only. No changes to ShortageDetector,
ImportRequestGenerator, or ManifestGenerator.

READY TO APPLY? — waiting for approval
```

---

## Acceptance Criteria
- [ ] `execute_cost_reduction` orders `deficit + transit_buffer` for consumable materials
- [ ] `transit_buffer = (transit_days × daily_rate × LUNA_MARGIN_FACTOR).ceil`
- [ ] Non-consumable materials (no entry in DAILY_CONSUMPTION_RATES) get no transit buffer
- [ ] Precursor phase settlements skip life support material orders with warning log
- [ ] Post-precursor settlements order life support normally with transit buffer
- [ ] 6 new Phase 4 specs passing
- [ ] ai_manager suite: 746+ examples, 0 failures, 4 pending
- [ ] KNOWN LIMITATIONS block updated

---

## Stop Conditions — escalate to user immediately if:
- `precursor_phase?` detection requires a model migration or new column
- GameConstants consumption values exist but differ from values in this task file
- `EARTH_LUNA_TRANSIT_DAYS` exists but with a different value than 3
- Any existing Phase 3 spec breaks
- ai_manager suite shows new failures outside strategy_selector_spec.rb

---

## Commit Instructions
Run on **host**, not inside container:
```bash
cd ~/Documents/git/galaxyGame/galaxy_game
git add app/services/ai_manager/strategy_selector.rb
git add spec/services/ai_manager/strategy_selector_spec.rb
git commit -m "feature: luna phase 4 — consumption-aware ordering with transit buffer and precursor phase gating"
git push
```

---

## Dependencies
**Blocked by**: 2026-06-08-HIGH-BUG-FIX-...-REMOVE-PLURAL-API-AND-LOGISTICS-SERVICE-COORDINATOR.md
**Blocks**: Phase 5 (multi-source routing, population-aware consumption rates)
**Related tasks**: none

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: —
**Completion date**: —
**Final test result**: —

### What was changed
### Issues discovered
### Follow-up tasks needed
### Lessons learned

---

## Handoff Summary
HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
