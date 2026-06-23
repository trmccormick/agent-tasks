---
status: backlog
priority: MEDIUM
type: feature
system_domain: PRESSURIZATION | LIFE_SUPPORT
mvp_alignment: LUNA_SETTLEMENT_INFRASTRUCTURE_PHASE6
local_worker_safe: false
created_date: 2026-06-11
extracted_from: docs/agent/archive/backlog_april_2026/deprecated/2026-04-17-CRITICAL-ARCHITECTURE-ENCLOSED-ATMOSPHERE-FAILURE-PREDICTION-PLANNING.md
gate_condition: Phase 5 simulation running with observable pressurization events (Luna fuel loop validated)
---

# TASK: Implement TTR Metric and Failure Cascade Modeling in PressurizationService

**Status**: BACKLOG  
**Priority**: MEDIUM  
**Type**: feature  
**Created**: 2026-06-11  
**Last Updated**: 2026-06-11  

---

## Local Worker Triage Report
*Filled in during April 2026 backlog triage session (June 12, 2026)*

- **Template Conformance**: PASS — all required sections present  
- **Docker Wrapper Check**: N/A — feature task requiring implementation work  
- **MVP Alignment**: VALID for Phase 6+ — NOT needed for Luna simulation calibration per `research/LUNA-MVP-SIMULATION-DESIGN.md`
- **MVP Impact Note**: Pressurization infrastructure exists but TTR metric and failure cascade modeling not implemented; deferred to Phase 6+ as future scalability enhancement after fuel loop proven  
- **Action Line**: READY FOR CLOUD HANDOFF — task file created in phase6+/ backlog with gate condition

---

## Agent Assignment

**Assigned To**: GPT-5 mini 0x or Claude Sonnet 1x (requires architectural reasoning across pressurization services and life support systems)
**Why This Agent**: Requires deep understanding of existing PressurizationService infrastructure, TTR calculation patterns, failure propagation modeling  
**Supervision Level**: watched carefully

---

## Context

Enclosed atmospheric systems require robust failure prediction to model realistic habitat maintenance scenarios. The current codebase has pressurization infrastructure (`app/services/pressurization_service.rb` + specialized handlers) but lacks:
- **TTR (Time-to-Reversion)** metric — how long before atmosphere degrades below safe thresholds  
- **Failure cascade modeling** — propagation of atmospheric failures across interconnected habitats

This task was extracted from April 2026 backlog during June 12 triage audit. Original file archived at `docs/agent/archive/backlog_april_2026/deprecated/2026-04-17-CRITICAL-ARCHITECTURE-ENCLOSED-ATMOSPHERE-FAILURE-PREDICTION-PLANNING.md`. Per `research/LUNA-MVP-SIMULATION-DESIGN.md`, this is **NOT needed for Phase 5 Luna simulation calibration** (which focuses on fuel loop validation), making it a Phase 6+ future scalability enhancement.

### Gate Condition
This task should NOT be dispatched until:
✅ Phase 5 simulation running with observable pressurization events  
✅ Luna fuel loop validated (consumption modeling, precursor phase gating working)  

Once gate condition met, this work enables realistic multi-settlement atmospheric failure scenarios for L1 Depot infrastructure operations.

**Relevant Architecture Docs — read before starting:**
- `docs/new_agent/projects/galaxy_game/research/LUNA-MVP-SIMULATION-DESIGN.md` — Phase alignment reference (confirms NOT Phase 5 work)  
- `app/services/pressurization_service.rb` — existing pressurization infrastructure entry point  
- `app/models/structures/worldhouse.rb` — worldhouse model with enclosed_segments, coverage_percent tracking

---

## Problem Statement

**Current behavior**: PressurizationService handles gas calculations and retention multipliers based on magnetic field strength. Worldhouse model tracks construction progress (enclosed_segments vs total_segments). However:
- No TTR metric exists to predict when atmosphere will degrade below safe thresholds  
- No failure cascade modeling for interconnected habitats sharing life support infrastructure

**Expected behavior**: PressurizationService includes `time_to_reversion` method calculating hours until atmospheric degradation, plus basic failure propagation patterns for connected settlements. This enables realistic simulation of habitat maintenance scenarios where failures propagate through connected settlements (e.g., L1 Depot → Luna surface habitats).

---

## Current State Analysis

| Component | Status | Evidence |
|-----------|--------|----------|
| Pressurization infrastructure | ✅ EXISTS | `app/services/pressurization_service.rb` + specialized handlers (habitat, structure, unit, craft) |
| Worldhouse model with atmospheric tracking | ✅ EXISTS | `app/models/structures/worldhouse.rb` tracks enclosed_segments, coverage_percent, population_capacity |
| TTR metric calculation in PressurizationService | ❌ NOT IMPLEMENTED | grep search for "ttr\|time_to_reversion" returned 0 results in pressurization services |
| Failure cascade modeling in PressurizationService | ❌ NOT IMPLEMENTED | No event propagation patterns found across atmospheric systems |

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

## Technical Requirements

### Phase A: TTR Metric Implementation in PressurizationService
1. **Define Atmospheric Degradation Model**
   - Research real-world atmosphere loss rates (Venus, Mars, Luna) to establish baseline leak rate constants  
   - Design formula incorporating: current_pressure_kpa, target_safe_threshold_kpa, magnetic_field_strength_tesla

2. **Add TTR Calculation Method to PressurizationService**
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

### Phase B: Failure Cascade Modeling in PressurizationService
1. **Design Settlement Dependency Tracking**
   - Model which settlements supply life support materials to others (L1 Depot → Luna habitats, Earth imports → all settlements)  
   - Add dependency tracking logic within PressurizationService or create helper module

2. **Implement Basic Failure Cascade Logic in PressurizationService**
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

---

## Testing Requirements

### Unit Tests (Isolation)
- `spec/services/pressurization_service_spec.rb` — add TTR calculation tests with various atmospheric compositions and magnetic field strengths  
- Verify time_to_reversion returns nil when atmosphere already below safe threshold  
- Test failure cascade simulation with different severity levels and dependent settlement counts

```ruby
# spec/services/pressurization_service_spec.rb (add to existing test suite)
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

### Integration Tests (Multi-Settlement Cascades)
```ruby
# spec/services/pressurization_service_spec.rb (integration section)
describe 'failure cascade across connected settlements' do
  let(:l1_depot) { create(:orbital_settlement, settlement_type: 'depot') }  
  let(:luna_habitat_1) { create(:base_settlement, population_capacity: 500) }
  let(:luna_habitat_2) { create(:base_settlement, population_capacity: 300) }

  it 'identifies dependent settlements and calculates cascade impact' do
    service = PressurizationService.new(l1_depot)
    
    result = service.simulate_atmospheric_failure(
      failure_severity: :critical, 
      dependent_settlements: [luna_habitat_1, luna_habitat_2]
    )
    
    expect(result[:affected_count]).to eq(2)  
    expect(result[:total_population_at_risk]).to be_between(80, 350) # Based on severity multiplier logic
  end
end
```

---

## Dependencies & Blockers

### Prerequisite Tasks (Must Complete First)
- None — pressurization infrastructure already exists and functional per audit findings  
- **Gate Condition**: Phase 5 simulation running with observable pressurization events (Luna fuel loop validated)

### Blocks These Future Tasks  
- AI Manager integration hooks for automated atmospheric failure response (if ever implemented as separate task)  
- Player-facing atmospheric failure warning UI systems  
- Advanced multi-settlement cascade modeling beyond basic dependency tracking

---

## Success Criteria & Acceptance Tests Checklist

- [ ] **TTR Metric Implemented**: `PressurizationService#time_to_reversion` returns accurate hours until atmosphere degrades below safe threshold for various test scenarios (Luna, Mars, Venus habitats)  
- [ ] **Failure Cascade Logic Built**: `PressurizationService#simulate_atmospheric_failure` correctly calculates affected settlements and population at risk metrics  
- [ ] **Unit Tests Passing**: All new tests added to pressurization_service_spec.rb pass with 0 failures (TTR calculation + failure cascade scenarios)  
- [ ] **Integration Tests Passing**: Failure cascade spec demonstrates correct multi-settlement propagation behavior  
- [ ] **Documentation Updated**: `app/services/pressurization_service.rb` includes inline comments explaining TTR formula and failure severity multiplier logic

---

## Completion Report Template
*To be filled in by implementing agent after completion*

**Completed by**:  
**Completion date**:  
**Final test result**: X examples, Y failures  

### What was changed
- [List files modified with brief description of changes]

### Issues discovered
- [Note any architectural gaps or unexpected dependencies found during implementation]

### Follow-up tasks needed
- [Identify any related work that should be extracted as separate task files (e.g., AI Manager integration)]

### Lessons learned  
- [Document insights gained about pressurization systems, failure modeling patterns, etc.]

---

## Notes for Future Reviewers

This task was **extracted from April 2026 backlog during June 12 triage session**. Original file: `docs/agent/archive/backlog_april_2026/deprecated/2026-04-17-CRITICAL-ARCHITECTURE-ENCLOSED-ATMOSPHERE-FAILURE-PREDICTION-PLANNING.md`

**Why Phase 6+ and not Phase 5?**: Per `research/LUNA-MVP-SIMULATION-DESIGN.md`, Luna simulation calibration (Phase 5) focuses on proving the fuel loop closes — consumption modeling, precursor phase gating, life support ordering. TTR/failure prediction is NOT listed as a Phase 5 acceptance criterion; it's future scalability work for multi-settlement infrastructure after L1 Depot operational.

**Gate Condition**: This task should only be dispatched when Phase 5 simulation is running with observable pressurization events (Luna fuel loop validated). Do not attempt implementation before gate condition met — ensures proper context and test data availability.

**Original April task was CRITICAL priority**: Adjusted to MEDIUM since this is deferred work, not immediate Phase 5 blocker. Scope narrowed from full architecture planning to focused PressurizationService implementation only (AI Manager integration moved out of scope for future consideration).
