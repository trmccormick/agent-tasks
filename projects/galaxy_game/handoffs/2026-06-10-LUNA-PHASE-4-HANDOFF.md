# HANDOFF — Luna Phase 4 Implementation (Consumption-Aware Ordering + Precursor Gating)

**TO**: Qwen3.5-27B (Implementation Agent)  
**FROM**: Strategist  
**DATE**: June 10, 2026  
**TASK FILE PATH**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/2026-06/2026-06-08-HIGH-FEATURE-LUNA-PHASE-4-CONSUMPTION-AWARE-ORDERING.md`

---

## CRITICAL FIRST STEP — Move Task to Active Before Starting Work

**The task file is currently in `tasks/backlog/`. You MUST move it before writing any code:**

```bash
cd /Users/tam0013/Documents/git/agent-tasks
git mv 'projects/galaxy_game/tasks/backlog/2026-06/2026-06-08-HIGH-FEATURE-LUNA-PHASE-4-CONSUMPTION-AWARE-ORDERING.md' \
       'projects/galaxy_game/tasks/active/2026-06-08-HIGH-FEATURE-LUNA-PHASE-4-CONSUMPTION-AWARE-ORDERING.md'

# Update YAML header: status: backlog → status: active
# Commit the move with message "Start task: 2026-06-08-HIGH-FEATURE-LUNA-PHASE-4..."
```

**Do not skip this step.** This signals work has begun.

---

## Context & Background

Phase 3 wired `ShortageDetector` and `ImportRequestGenerator` into `StrategySelector`. The current implementation orders exactly the deficit (`need - have`). However, on a **3-day Earth→Luna transit**, settlements consume resources during flight and arrive short again.

**Phase 4 adds two capabilities:**
1. **Transit-aware ordering**: order_quantity = deficit + (transit_days × daily_consumption_rate × margin_factor)
2. **Precursor phase gating**: Skip life-support orders when `current_population == 0` (robotic-only settlement, no humans present yet)

Both are documented as known limitations in the current codebase's KNOWN LIMITATIONS block at bottom of `execute_cost_reduction`.

---

## Target Files

### Primary files you will edit:
- `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/services/ai_manager/strategy_selector.rb` — Add transit buffer logic + precursor gating to `#execute_cost_reduction` (private method)
- `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/spec/services/ai_manager/strategy_selector_spec.rb` — Add 6 Phase 4 specs

### Reference files you must read first:
- `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/services/logistics/shortage_detector.rb` — Confirm shortage hash shape
- `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/models/logistics/contract.rb` — Check for `EARTH_LUNA_TRANSIT_DAYS` constant
- `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/models/game_constants.rb` (or equivalent) — Check consumption rate constants

---

## Implementation Scope

### Step 1: Verify prerequisites via grep (inside Docker)
```bash
# Confirm current_population column exists in models
grep -rn "current_population" app/models/ --include="*.rb" | head -10

# Confirm GameConstants consumption values exist  
grep -rn "FOOD_PER_PERSON\|WATER_PER_PERSON\|ENERGY_PER_PERSON" app/ --include="*.rb"

# Confirm EARTH_LUNA_TRANSIT_DAYS constant
grep -rn "EARTH_LUNA_TRANSIT_DAYS" app/ --include="*.rb"
```

Report grep results in synthesis report. Use local fallbacks if constants don't exist (see task file for values).

### Step 2: Add private helpers to StrategySelector
Add below existing private methods:
- `daily_consumption_rate_for(material, settlement)` — returns rate from DAILY_CONSUMPTION_RATES hash or 0
- `precursor_phase?(settlement)` — returns true if `settlement.current_population.zero?`

### Step 3: Update execute_cost_reduction method
Replace the shortage processing loop with transit-aware logic:
1. Gate life-support materials (Food, Water, Oxygen, Energy) during precursor phase → skip + log warning
2. Calculate deficit as before: `[0, need - have].max`
3. Add transit buffer for consumables: `(transit_days × daily_rate × 1.15_margin).ceil`
4. Order quantity = `deficit + transit_buffer` (non-consumables get no buffer)

### Step 4: Update KNOWN LIMITATIONS comment block
Mark "Transit Consumption" and "Precursor Phase Gating" as RESOLVED in the limitations list at bottom of file.

### Step 5: Write 6 new specs covering:
1. Transit buffer added for Food (deficit=10 → order_quantity = deficit + ceil(3×2×1.15) = 17)
2. Non-consumable gets no transit buffer (Iron orders exactly deficit)
3. Precursor phase gates Food ordering (`current_population: 0` → generate_import_request NOT called for Food)
4. Precursor phase gates Water ordering  
5. Precursor phase allows non-life-support materials (Iron still ordered with buffer)
6. Post-precursor settlement (`current_population: 10`) orders life support normally with transit buffer

### Step 6: Run targeted specs and report results
```bash
# Targeted spec
docker-compose -f docker-compose.dev.yml exec web bundle exec rspec \
  spec/services/ai_manager/strategy_selector_spec.rb --format documentation | tail -20

# Full AI Manager suite baseline check  
docker-compose -f docker-compose.dev.yml exec web bundle exec rspec \
  spec/services/ai_manager/ --format progress | grep "examples\|failures"
```

Expected: **746+ examples** (740 + 6 new), **3 pre-existing failures**, **4 pending**. No NEW failures.

---

## Constants to Use (with fallbacks)

From task file — use existing constants if found, otherwise define locally in StrategySelector private section:
```ruby
LUNA_MARGIN_FACTOR = 1.15   # 15% NASA margin
LIFE_SUPPORT_MATERIALS = %w[Food Water Oxygen Energy].freeze
LUNA_TRANSIT_DAYS = 3       # fallback for Logistics::Contract::EARTH_LUNA_TRANSIT_DAYS

DAILY_CONSUMPTION_RATES = {
  'Food'   => 2,  # per person per day
  'Water'  => 1,
  'Energy' => 3,
  'Oxygen' => 1
}.freeze
```

---

## Stop Conditions — Report Immediately If:
- `current_population` column doesn't exist (would need migration)
- Existing constants have different values than task file specifies  
- Any Phase 3 spec breaks after your changes
- AI Manager suite shows NEW failures outside strategy_selector_spec.rb

---

## Output Required Before Applying Changes

Produce synthesis report with:
1. **Grep verification results** (constants found/not found, using which fallbacks)
2. **Transit buffer calculation example**: Food deficit=10 → show daily_rate, transit_buffer, order_quantity breakdown
3. **Precursor gate example**: settlement.current_population = 0 + what happens to Food vs Iron shortages
4. **Spec results**: strategy_selector_spec.rb summary line (X examples, Y failures)
5. **AI Manager suite baseline check** (746+ examples expected, confirm no new failures introduced)

**STOP and wait for approval before committing changes.** Do not apply until explicitly told to proceed.

---

## After Approval — Commit Instructions

```bash
cd /Users/tam0013/Documents/git/galaxyGame
git add galaxy_game/app/services/ai_manager/strategy_selector.rb
git add galaxy_game/spec/services/ai_manager/strategy_selector_spec.rb  
git commit -m "feature: luna phase 4 — consumption-aware ordering with transit buffer and precursor gating"

# Then move task to completed in agent-tasks repo (update YAML status + git mv)
```

---

## Dependencies & Blockers

**Blocked by**: ✅ COMPLETED — `2026-06-08-HIGH-BUG-FIX-IMPORT-REQUEST-GENERATOR-REMOVE-PLURAL-API...`  
**Blocks**: Phase 5 (multi-source routing, population-aware consumption rates)

---

## Notes
- This is additive-only work to existing execute_cost_reduction method. No changes needed to ShortageDetector or ImportRequestGenerator.
- All command execution must stay inside Docker container (`docker-compose -f docker-compose.dev.yml exec web ...`)
- Keep spec output minimal — report only summary lines and failure snippets, not full test runs

**Ready for implementation.** Move task to active first, then proceed with Step 1 verification greps.

