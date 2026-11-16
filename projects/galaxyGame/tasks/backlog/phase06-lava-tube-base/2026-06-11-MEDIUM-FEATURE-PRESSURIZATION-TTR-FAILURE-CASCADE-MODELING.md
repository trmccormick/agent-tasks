---
status: backlog
priority: MEDIUM
type: feature
system_domain: PRESSURIZATION | LIFE_SUPPORT
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT (Phase 6+ — lava-tube base construction)
local_worker_safe: true
gate_condition: Phase 5 simulation running with observable pressurization events (Luna fuel loop validated)
---

# TASK: Implement TTR Metric and Failure Cascade Modeling in PressurizationService

---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase06-lava-tube-base/2026-06-11-MEDIUM-FEATURE-PRESSURIZATION-TTR-FAILURE-CASCADE-MODELING.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/phase06-lava-tube-base/2026-06-11-MEDIUM-FEATURE-PRESSURIZATION-TTR-FAILURE-CASCADE-MODELING.md \
         projects/galaxy_game/tasks/active/2026-06-11-MEDIUM-FEATURE-PRESSURIZATION-TTR-FAILURE-CASCADE-MODELING.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-06-11-MEDIUM-FEATURE-PRESSURIZATION-TTR-FAILURE-CASCADE-MODELING.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

## Context

Enclosed atmospheric systems require robust failure prediction to model realistic habitat maintenance scenarios. The current codebase has pressurization infrastructure but lacks TTR (Time-to-Reversion) metric and failure cascade modeling.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/projects/galaxy_game/research/LUNA-MVP-SIMULATION-DESIGN.md` — Phase alignment reference (confirms NOT Phase 5 work)
- `galaxy_game/app/services/pressurization_service.rb` — existing pressurization infrastructure entry point
- `galaxy_game/app/models/structures/worldhouse.rb` — worldhouse model with enclosed_segments, coverage_percent tracking

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: Gate condition is real**
- ❌ Wrong: Implement this before Phase 5 simulation validates the fuel loop
- ✅ Right: Verify gate condition is met before starting — no observable pressurization events = no test data to validate against
- Why: This task depends on Phase 5 proving consumption modeling and precursor phase gating work. Without that foundation, TTR calculations have no baseline to compare against.

⚠️ **GOTCHA 2: Scope is PressurizationService only**
- ❌ Wrong: Build AI Manager integration hooks or player-facing UI during this task
- ✅ Right: Implement `time_to_reversion` and `simulate_atmospheric_failure` methods in PressurizationService only. Extract AI Manager integration as a separate task if needed later.
- Why: This is a focused infrastructure enhancement, not an AI Manager feature.

### Verified Current State (2026-08-09)

| Component | Status | Evidence |
|-----------|--------|----------|
| Pressurization infrastructure | ✅ EXISTS | `galaxy_game/app/services/pressurization_service.rb` line 1 |
| Worldhouse model with atmospheric tracking | ✅ EXISTS | `galaxy_game/app/models/structures/worldhouse.rb` line 2 |
| TTR metric calculation | ❌ NOT IMPLEMENTED | grep for `time_to_reversion\|ttr\|TTR` returned 0 results in codebase |
| Failure cascade modeling | ❌ NOT IMPLEMENTED | No event propagation patterns found across atmospheric systems |

---

## Problem Statement

**Current behavior**: PressurizationService handles gas calculations and retention multipliers based on magnetic field strength. Worldhouse model tracks construction progress (enclosed_segments vs total_segments). However:
- No TTR metric exists to predict when atmosphere will degrade below safe thresholds
- No failure cascade modeling for interconnected habitats sharing life support infrastructure

**Expected behavior**: PressurizationService includes `time_to_reversion` method calculating hours until atmospheric degradation, plus basic failure propagation patterns for connected settlements. This enables realistic simulation of habitat maintenance scenarios where failures propagate through connected settlements (e.g., L1 Depot → Luna surface habitats).

---

## Implementation Scope

### In Scope (Phase 6+ — Future Scalability)
1. **TTR Metric Design & Implementation in PressurizationService**
   - Define TTR calculation formula based on: current pressure, leak rate, atmospheric composition, magnetic field strength
   - Add `time_to_reversion` method to PressurizationService returning hours until atmosphere degrades below safe threshold (e.g., 0.5 atm for human habitation)

2. **Failure Cascade Modeling in PressurizationService**
   - Design event propagation patterns for interconnected habitats sharing life support infrastructure
   - Implement basic failure cascade logic that tracks dependencies between settlements (e.g., L1 Depot supplies Luna surface habitats with N2)

### Out of Scope (Not Phase 6+)
- AI Manager integration hooks — separate task if needed after TTR/failure modeling proven working
- Terraforming atmosphere stabilization tiers from original April task matrix — those are Act 3/4 work, move to phase9+/ if needed
- Player-facing UI for atmospheric failure warnings — that's a different system layer

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/app/services/pressurization_service.rb` | Add TTR metric and failure cascade methods | New public methods + private helpers |
| `galaxy_game/spec/services/pressurization_service_spec.rb` | Tests for new methods | Append to existing test suite |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/app/models/structures/worldhouse.rb` | Worldhouse model with enclosed_segments, coverage_percent tracking |
| `config/initializers/game_constants.rb` | Source of truth for boundary constants (MIN_SAFE_PRESSURE_KPA, etc.) |

### Migration (if needed)
- [ ] No migration needed

---

## Implementation Steps

### Step 1 — Add TTR Metric to PressurizationService

```ruby
# In app/services/pressurization_service.rb

def time_to_reversion(safe_threshold = GameConstants::MIN_SAFE_PRESSURE_KPA)
  return nil unless @atmosphere && current_pressure_kpa > safe_threshold
  
  pressure_loss_rate_per_hour = calculate_degradation_rate(
    magnetic_field_strength: retention_multiplier,
    atmospheric_composition: get_atmospheric_mix_percentages
  )
  
  remaining_safe_hours = (current_pressure_kpa - safe_threshold) / pressure_loss_rate_per_hour
  
  { hours_until_critical: remaining_safe_hours.to_f, degradation_rate_pph: pressure_loss_rate_perhour }
end

private

def calculate_degradation_rate(magnetic_field_strength:, atmospheric_composition:)
  # Base loss rate from Mars/Venus/Luna research data  
  base_loss = GameConstants::ATMOSPHERE_BASE_LOSS_RATE_KPA_PER_HOUR
  
  # Magnetic field reduces solar wind stripping (retention_multiplier already calculated)
  magnetic_protection = retention_multiplier * 0.5 # 50% effectiveness at full strength
  
  base_loss * (1 - magnetic_protection)
end

def get_atmospheric_mix_percentages
  return {} unless @atmosphere
  
  {
    nitrogen: (@atmosphere.nitrogen_percentage || 78).to_f,
    oxygen: (@atmosphere.oxygen_percentage || 21).to_f,  
    argon: (@atmosphere.argon_percentage || 0.93).to_f,
    co2: (@atmosphere.co2_percentage || 0.04).to_f
  }
end
```

### Step 2 — Add Failure Cascade Modeling to PressurizationService

```ruby
# In app/services/pressurization_service.rb (or separate module if too complex for single file)

def simulate_atmospheric_failure(failure_severity: :critical, dependent_settlements: [])
  # Calculate cascade impact based on severity and number of dependents  
  severity_multiplier = case failure_severity
                        when :warning then 1.0
                        when :critical then 2.5  
                        else 4.0 # emergency level
                        end
  
  affected_population = dependent_settlements.sum(&:population_capacity) * severity_multiplier / 10.0
  estimated_recovery_hours = (dependent_settlements.size + 24).to_f # Base recovery time scales with cascade depth
  
  { 
    failure_severity: failure_severity,
    affected_count: dependent_settlements.size, 
    total_population_at_risk: affected_population.to_i, 
    estimated_recovery_hours: estimated_recovery_hours 
  }
end

def find_dependent_settlements(settlement_id)
  # Find all settlements that depend on this one for life support materials  
  Settlement::BaseSettlement.where(id: settlement_id).joins(:life_support_supplies).distinct.pluck(:dependent_settlement_ids).flatten.uniq
rescue ActiveRecord::StatementInvalid
  [] # Return empty if association doesn't exist yet (graceful degradation)
end
```

### Step 3 — Write Tests

Append to `spec/services/pressurization_service_spec.rb`:

```ruby
describe '#time_to_reversion' do
  context 'when atmosphere is above safe threshold' do
    it 'returns hours until critical degradation based on magnetic field strength' do
      allow(service).to receive(:retention_multiplier).and_return(0.8) # Strong magnetic field
      
      result = service.time_to_reversion(GameConstants::MIN_SAFE_PRESSURE_KPA)
      
      expect(result[:hours_until_critical]).to be > 48.0 # Should have reasonable buffer with strong protection  
      expect(result[:degradation_rate_pph]).to be < GameConstants::ATMOSPHERE_BASE_LOSS_RATE_KPA_PER_HOUR
    end
    
    it 'returns shorter TTR for weaker magnetic field' do
      allow(service).to receive(:retention_multiplier).and_return(0.2) # Weak magnetic field
      
      result = service.time_to_reversion(GameConstants::MIN_SAFE_PRESSURE_KPA)
      
      expect(result[:hours_until_critical]).to be < 48.0 # Less protection means faster degradation  
    end
  end
  
  context 'when atmosphere is already below safe threshold' do
    it 'returns nil indicating immediate critical state' do
      allow(service).to receive(:current_pressure_kpa).and_return(30) # Below typical 50 kPa minimum
      
      result = service.time_to_reversion(GameConstants::MIN_SAFE_PRESSURE_KPA)
      
      expect(result).to be_nil  
    end
  end
end

describe '#simulate_atmospheric_failure' do
  let(:dependent_settlements) { [create(:base_settlement, population_capacity: 500), create(:base_settlement, population_capacity: 300)] }
  
  it 'calculates affected population based on severity multiplier' do
    result = service.simulate_atmospheric_failure(failure_severity: :critical, dependent_settlements: dependent_settlements)
    
    expect(result[:affected_count]).to eq(2)
    expect(result[:total_population_at_risk]).to be > 0  
    expect(result[:estimated_recovery_hours]).to be_between(48.0, 72.0)
  end
  
  it 'returns higher population at risk for emergency severity' do
    critical_result = service.simulate_atmospheric_failure(failure_severity: :critical, dependent_settlements: dependent_settlements)  
    emergency_result = service.simulate_atmospheric_failure(failure_severity: :emergency, dependent_settlements: dependent_settlements)
    
    expect(emergency_result[:total_population_at_risk]).to be > critical_result[:total_population_at_risk]
  end
end
```

### Step 4 — Verify

> CRITICAL EXECUTION MANDATE: All RSpec commands must use the Docker wrapper below.
> The container working directory is already /home/galaxy_game — do NOT add cd /home/galaxy_game.

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/pressurization_service_spec.rb --format progress' 2>&1 | tail -5
```

Expected result: All examples pass with 0 failures (confirm total count after adding new tests).

---

## Acceptance Criteria
- [ ] `PressurizationService#time_to_reversion` returns accurate hours until atmosphere degrades below safe threshold for various test scenarios (Luna, Mars, Venus habitats)
- [ ] `PressurizationService#simulate_atmospheric_failure` correctly calculates affected settlements and population at risk metrics
- [ ] All new tests added to pressurization_service_spec.rb pass with 0 failures (TTR calculation + failure cascade scenarios)
- [ ] Integration test demonstrates correct multi-settlement propagation behavior
- [ ] Documentation updated: `app/services/pressurization_service.rb` includes inline comments explaining TTR formula and failure severity multiplier logic

---

## Stop Conditions — escalate to user immediately if:
- Gate condition not met (Phase 5 simulation not running with observable pressurization events) — report and stop
- PressurizationService API differs from expected — do not guess, flag it
- Fix requires changes to shared inventory models beyond pressurization service — extract as separate task
- Any architectural decision is required beyond what's specified here

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add galaxy_game/app/services/pressurization_service.rb galaxy_game/spec/services/pressurization_service_spec.rb
git commit -m "feature: pressurization_service — add TTR metric and failure cascade modeling

- time_to_reversion: calculates hours until atmosphere degrades below safe threshold
- simulate_atmospheric_failure: models cascade impact across connected settlements
- Tests: TTR calculation with various magnetic field strengths, failure severity levels"
git push
```

**Task file move on completion:**
```bash
mv projects/galaxy_game/tasks/active/2026-06-11-MEDIUM-FEATURE-PRESSURIZATION-TTR-FAILURE-CASCADE-MODELING.md \
   projects/galaxy_game/tasks/completed/2026-08/2026-06-11-MEDIUM-FEATURE-PRESSURIZATION-TTR-FAILURE-CASCADE-MODELING.md
git add projects/galaxy_game/tasks/completed/2026-08/2026-06-11-MEDIUM-FEATURE-PRESSURIZATION-TTR-FAILURE-CASCADE-MODELING.md
git commit -m "chore: move 2026-06-11-MEDIUM-FEATURE-PRESSURIZATION-TTR-FAILURE-CASCADE-MODELING.md to completed/"
```

---

## Documentation
- [ ] Update `app/services/pressurization_service.rb` — inline comments explaining TTR formula and failure severity multiplier logic

---

## Dependencies
**Blocked by**: Phase 5 simulation running with observable pressurization events (Luna fuel loop validated)
**Blocks**: AI Manager integration hooks for automated atmospheric failure response (if ever implemented as separate task)
**Related tasks**: none identified

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Final test result**: X examples, Y failures

### What was changed
-

### Issues discovered
-

### Follow-up tasks needed
-

### Lessons learned
-

---

## Handoff Summary
HANDOFF SUMMARY: [files updated] | TTR metric + failure cascade implemented | next: AI Manager integration (if needed)