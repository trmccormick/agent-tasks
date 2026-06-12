---
status: backlog
priority: HIGH
type: feature
system_domain: PRESSURIZATION | LIFE_SUPPORT | SIMULATION
mvp_alignment: LUNA_SETTLEMENT_INFRASTRUCTURE
local_worker_safe: false
created_date: 2026-06-12
extracted_from: docs/agent/archive/backlog_april_2026/deprecated/2026-04-17-CRITICAL-ARCHITECTURE-ENCLOSED-ATMOSPHERE-FAILURE-PREDICTION-PLANNING.md
---

# TASK: Implement Time-to-Reversion (TTR) Metric and Failure Cascade Modeling for Enclosed Atmospheric Systems

**Status**: BACKLOG  
**Priority**: HIGH  
**Type**: feature  
**Created**: 2026-06-12  
**Last Updated**: 2026-06-12  

---

## Local Worker Triage Report
*Filled in during April 2026 backlog triage session (June 12, 2026)*

- **Template Conformance**: PASS — all required sections present  
- **Docker Wrapper Check**: N/A — architecture/feature task requiring implementation work  
- **MVP Alignment**: VALID but PHASE 6+ — not needed for Luna simulation calibration per `research/LUNA-MVP-SIMULATION-DESIGN.md`
- **MVP Impact Note**: Pressurization infrastructure exists (base_pressurization_service, habitat_pressurization_service, etc.) but TTR metric and failure cascade modeling NOT IMPLEMENTED; deferred to Phase 6+ as future scalability enhancement  
- **Action Line**: READY FOR CLOUD HANDOFF — task file created in phase6+/ backlog

---

## Agent Assignment

**Assigned To**: GPT-5 mini 0x or Claude Sonnet 1x (requires architectural reasoning across simulation, AI, and event systems)
**Why This Agent**: Requires deep understanding of pressurization services, life support ordering patterns, failure propagation modeling, and integration with existing infrastructure  
**Supervision Level**: watched carefully

---

## Context

Enclosed atmospheric systems (worldhouses, domes, stations, depots, asteroid/moon conversions) require robust failure prediction to model realistic habitat maintenance scenarios. The current codebase has pressurization infrastructure but lacks:
- **TTR (Time-to-Reversion)** metric — how long before atmosphere degrades below safe thresholds  
- **Failure cascade modeling** — propagation of atmospheric failures across interconnected habitats  
- **AI integration hooks** — automated response patterns for life support degradation events

This task was originally created April 17, 2026 as a CRITICAL architecture planning item. During June 12 backlog triage audit, it was discovered that pressurization services exist but the failure prediction layer described in the original task file was never implemented. Per `research/LUNA-MVP-SIMULATION-DESIGN.md`, this is **NOT needed for Phase 5 Luna simulation calibration** (which focuses on fuel loop validation), making it a Phase 6+ future scalability enhancement.

### Reference Appendix: Atmospheric Stabilization Matrix & Mars Science Context
*(From original April 2026 task file — preserved for historical context)*

**Four-Tier Atmospheric Stabilization Decision Matrix:**

| Tier | Logic | Cost | Retention | Implementation |
|------|-------|------|-----------|----------------|
| **1: Bulk Injection (Status Quo)** | "Cheaper to replace leaking gas than build shield" | Low tech, high fuel consumption | 40% baseline | Increase Venus/Saturn mass driver throughput |
| **2: Thermal Slat Arrays** | "15% solar flux reduction stabilizes cold trap" | Mid-tier (Ceres metals) | 70% | Orbital photovoltaic louvers at L1 |
| **3: Electrostatic Scrubbers** | "Dust density exceeds safety protocols" | High energy (H₂ fuel cells) | 85% | Ground-based ion towers |
| **4: Magnetic Dipole Shield** | "Total retention achieved - terminate emergency imports" | Extreme (Alpha Centauri-grade tech) | 98% | Superconducting magnet at L1 |

**Mars Science Context:** Recent Mars research shows dust storms heat the atmosphere by 15°C, pushing water vapor into UV breakdown zone. Terraformed worlds aren't naturally stable like Earth—they require ongoing technological maintenance.

This matrix and context inform all architectural planning for atmospheric retention, maintenance, and failure prediction in artificial environments.

**Relevant Architecture Docs — read before starting:**
- `docs/new_agent/projects/galaxy_game/research/LUNA-MVP-SIMULATION-DESIGN.md` — Phase alignment reference (confirms this is NOT Phase 5 work)  
- `app/services/pressurization_service.rb` — existing pressurization infrastructure entry point  
- `app/models/structures/worldhouse.rb` — worldhouse model with enclosed_segments, coverage_percent tracking

---

## Problem Statement

**Current behavior**: PressurizationService handles gas calculations and retention multipliers based on magnetic field strength. Worldhouse model tracks construction progress (enclosed_segments vs total_segments). However:
- No TTR metric exists to predict when atmosphere will degrade below safe thresholds  
- No failure cascade modeling for interconnected habitats sharing life support infrastructure  
- AI Manager has no hooks for automated response to atmospheric degradation events

**Expected behavior**: Clear architecture and implementation for TTR calculation, failure propagation patterns, and AI/maintenance integration across all enclosed atmospheric systems. This enables realistic simulation of habitat maintenance scenarios where failures propagate through connected settlements (e.g., L1 Depot → Luna surface habitats).

---

## Current State Analysis

| Component | Status | Evidence |
|-----------|--------|----------|
| Pressurization infrastructure | ✅ EXISTS | `app/services/pressurization_service.rb` + specialized handlers (habitat, structure, unit, craft) |
| Worldhouse model with atmospheric tracking | ✅ EXISTS | `app/models/structures/worldhouse.rb` tracks enclosed_segments, coverage_percent, population_capacity |
| TTR metric calculation | ❌ NOT IMPLEMENTED | grep search for "ttr\|time_to_reversion" returned 0 results in pressurization services |
| Failure cascade modeling | ❌ NOT IMPLEMENTED | No event propagation patterns found across atmospheric systems |
| AI Manager integration hooks | ❌ NOT IMPLEMENTED | No failure prediction callbacks or automated response logic exists |

---

## Implementation Scope

### In Scope (Phase 6+ — Future Scalability)
1. **TTR Metric Design & Implementation**
   - Define TTR calculation formula based on: current pressure, leak rate, atmospheric composition, magnetic field strength  
   - Add `time_to_reversion` method to PressurizationService returning hours until atmosphere degrades below safe threshold (e.g., 0.5 atm for human habitation)  
   - Store TTR as computed attribute in BaseStructure or separate AtmosphericStatus model

2. **Failure Cascade Modeling Architecture**
   - Design event propagation patterns for interconnected habitats sharing life support infrastructure  
   - Implement failure cascade service that tracks dependencies between settlements (e.g., L1 Depot supplies Luna surface habitats with N2)  
   - Add cascade simulation hooks to test multi-settlement atmospheric failures

3. **AI Manager Integration Hooks**
   - Create automated response patterns when TTR falls below configurable thresholds (warning, critical, emergency levels)  
   - Integrate failure prediction into AI Manager's life support ordering logic for proactive maintenance scheduling  
   - Add event system callbacks for pressurization degradation events

4. **Testing & Validation**
   - Unit tests for TTR calculation accuracy across different atmospheric compositions and leak rates  
   - Integration tests for failure cascade propagation through connected settlements  
   - System-level simulation scenarios demonstrating realistic habitat maintenance challenges

### Out of Scope (Not Phase 6+)
- Terraforming atmosphere stabilization tiers from original April task matrix — those are Act 3/4 work, move to phase9+/ if needed  
- Venus terraforming-specific logic — separate domain entirely  
- Player-facing UI for atmospheric failure warnings — that's a different system layer

---

## Technical Requirements

### Phase A: TTR Metric Implementation
1. **Define Atmospheric Degradation Model**
   - Research real-world atmosphere loss rates (Venus, Mars, Luna) to establish baseline leak rate constants  
   - Design formula incorporating: current_pressure_kpa, target_safe_threshold_kpa, magnetic_field_strength_tesla, atmospheric_composition_percentages

2. **Add TTR Calculation to PressurizationService**
```ruby
def time_to_reversion(safe_threshold = GameConstants::MIN_SAFE_PRESSURE_KPA)
  return nil unless @atmosphere && current_pressure_kpa > safe_threshold
  
  pressure_loss_rate_per_hour = calculate_degradation_rate(
    magnetic_field_strength: retention_multiplier,
    atmospheric_composition: get_atmospheric_mix_percentages,
    structural_integrity_factor: enclosure_seal_quality
  )
  
  remaining_safe_hours = (current_pressure_kpa - safe_threshold) / pressure_loss_rate_per_hour
  
  { hours_until_critical: remaining_safe_hours.to_f, degradation_rate_pph: pressure_loss_rate_perhour }
end

private

def calculate_degradation_rate(magnetic_field_strength:, atmospheric_composition:, structural_integrity_factor:)
  # Base loss rate from Mars/Venus/Luna research data
  base_loss = GameConstants::ATMOSPHERE_BASE_LOSS_RATE_KPA_PER_HOUR
  
  # Magnetic field reduces solar wind stripping (retention_multiplier already calculated)
  magnetic_protection = retention_multiplier * 0.5 # 50% effectiveness at full strength
  
  # Structural integrity affects seal quality (from worldhouse_segments or structure operational_data)  
  structural_factor = [structural_integrity_factor, 1.0].min
  
  base_loss * (1 - magnetic_protection) / structural_factor
end
```

3. **Store TTR as Computed Attribute**
   - Add `time_to_reversion_hours` computed field to BaseStructure or create separate AtmosphericStatus model  
   - Cache calculation with refresh interval (e.g., recalculate every game hour during simulation advance)

### Phase B: Failure Cascade Modeling
1. **Design Settlement Dependency Graph**
   - Model which settlements supply life support materials to others (L1 Depot → Luna habitats, Earth imports → all settlements)  
   - Add dependency tracking to BaseSettlement or create separate SupplyChainDependency model

2. **Implement Cascade Propagation Service**
```ruby
class FailureCascadeService < ApplicationService
  def initialize(settlements:)
    @settlements = settlements
    build_dependency_graph!
  end
  
  def simulate_atmospheric_failure(origin_settlement_id, failure_severity: :critical)
    # Find all downstream dependencies affected by this settlement's atmospheric failure  
    affected_chain = find_affected_dependencies(origin_settlement_id)
    
    # Calculate cascade impact based on severity and dependency strength
    cascade_impact = calculate_cascade_metrics(affected_chain, failure_severity)
    
    { origin: origin_settlement_id, affected_count: affected_chain.size, total_population_at_risk: cascade_impact[:population], estimated_recovery_hours: cascade_impact[:recovery_time] }
  end
  
  private
  
  def build_dependency_graph!
    # Map settlement → [dependent settlements that rely on this one for life support materials]  
    @dependency_graph = {}
    
    @settlements.each do |sett|
      next unless sett.respond_to?(:life_support_supplies) && sett.life_support_supplies
      
      sett.life_support_supplies.each do |material, dependent_settlement_ids|
        (@dependency_graph[sett.id] ||= []) += dependent_settlement_ids
      end
    end
  end
  
  def find_affected_dependencies(origin_id)
    # BFS/DFS traversal of dependency graph starting from origin settlement  
    affected = []
    queue = [origin_id]
    
    while queue.any?
      current = queue.shift
      next unless @dependency_graph[current]
      
      dependents = @dependency_graph[current]
      affected += dependents - affected # Avoid duplicates
      queue.concat(dependents)
    end
    
    affected.uniq
  end
  
  def calculate_cascade_metrics(affected_chain, failure_severity)
    severity_multiplier = case failure_severity
                          when :warning then 1.0
                          when :critical then 2.5  
                          else 4.0 # emergency level
                          end
    
    affected_settlements = @settlements.select { |s| affected_chain.include?(s.id) }
    
    total_population_at_risk = affected_settlements.sum(&:population_capacity) * severity_multiplier / 10.0
    estimated_recovery_hours = (affected_chain.size + 24).to_f # Base recovery time scales with cascade depth
    
    { population: total_population_at_risk.to_i, recovery_time: estimated_recovery_hours }
  end
end
```

### Phase C: AI Manager Integration Hooks
1. **Create Automated Response Patterns**
   - Define response thresholds (TTR > 72h = warning, TTR < 48h = critical, TTR < 24h = emergency)  
   - Implement automated import request generation when TTR falls below threshold for life support materials

2. **Add Event System Callbacks**
```ruby
# In PressurizationService after calculating time_to_reversion:
ttr_data = time_to_reversion(safe_threshold)

if ttr_data && ttr_data[:hours_until_critical] < GameConstants::TTR_WARNING_THRESHOLD_HOURS
  Events::AtmosphericDegradation.publish(
    settlement_id: @environment.settlement.id,
    current_ttr_hours: ttr_data[:hours_until_critical],
    severity_level: determine_severity(ttr_data[:hours_until_critical]),
    recommended_action: generate_recommended_action(ttr_data)
  )
end

private

def determine_severity(hours_until_critical)
  if hours_until_critical < GameConstants::TTR_EMERGENCY_THRESHOLD_HOURS
    :emergency
  elsif hours_until_critical < GameConstants::TTR_CRITICAL_THRESHOLD_HOURS  
    :critical
  else
    :warning
  end
end

def generate_recommended_action(ttr_data)
  # Generate actionable recommendations based on TTR and degradation rate  
  { action: "initiate_emergency_import", material: "N2", quantity_kg: calculate_required_materials(ttr_data[:degradation_rate_pph]) }
end
```

---

## Testing Requirements

### Unit Tests (Isolation)
- `spec/services/pressurization_service_spec.rb` — add TTR calculation tests with various atmospheric compositions and magnetic field strengths  
- `spec/models/structures/worldhouse_spec.rb` — verify time_to_reversion_hours computed attribute updates correctly during simulation advance

### Integration Tests (Multi-Settlement Cascades)
```ruby
# spec/services/failure_cascade_service_spec.rb
RSpec.describe FailureCascadeService, type: :service do
  let(:l1_depot) { create(:orbital_settlement, settlement_type: 'depot', location: l1_orbit_location) }  
  let(:luna_habitat_1) { create(:base_settlement, population_capacity: 500, life_support_supplies: { N2 => [luna_depot.id] }) }
  let(:luna_habitat_2) { create(:base_settlement, population_capacity: 300, life_support_supplies: { O2 => [l1_depot_id] }) }

  describe '#simulate_atmospheric_failure' do
    context 'when L1 Depot atmospheric failure cascades to Luna habitats' do
      it 'identifies all affected settlements and calculates population at risk' do
        cascade = service.simulate_atmospheric_failure(l1_depot.id, failure_severity: :critical)
        
        expect(cascade[:affected_count]).to eq(2) # Both Luna habitats  
        expect(cascade[:total_population_at_risk]).to be > 0
        expect(cascade[:estimated_recovery_hours]).to be_between(48.0, 72.0)
      end
      
      it 'triggers automated import requests for affected settlements' do
        allow_any_instance_of(AIManager::ImportRequestGenerator).to receive(:generate_emergency_request)
        
        service.simulate_atmospheric_failure(l1_depot.id, failure_severity: :emergency)
        
        expect(AIManager::ImportRequestGenerator).to have_received(:generate_emergency_request).exactly(2).times
      end
    end
  end
end
```

### System-Level Simulation Scenarios (Acceptance Tests)
- Scenario A: Luna habitat loses N2 supply from L1 Depot → verify TTR drops below critical threshold within expected timeframe  
- Scenario B: Multi-settlement cascade where Earth import disruption affects all settlements simultaneously → verify AI Manager prioritizes life support ordering correctly

---

## Dependencies & Blockers

### Prerequisite Tasks (Must Complete First)
- None — pressurization infrastructure already exists and functional per audit findings

### Blocks These Future Tasks  
- Phase 7+ terraforming atmosphere stabilization tiers (if ever implemented)  
- Player-facing atmospheric failure warning UI systems  
- Advanced AI Manager life support optimization algorithms that factor in TTR predictions

---

## Success Criteria & Acceptance Tests Checklist

- [ ] **TTR Metric Implemented**: `PressurizationService#time_to_reversion` returns accurate hours until atmosphere degrades below safe threshold for various test scenarios (Luna, Mars, Venus habitats)  
- [ ] **Failure Cascade Service Built**: `FailureCascadeService` correctly identifies all affected settlements in dependency graph and calculates population at risk metrics  
- [ ] **AI Manager Integration Complete**: Automated import requests generated when TTR falls below configurable thresholds; event system callbacks fire for atmospheric degradation events  
- [ ] **Unit Tests Passing**: All new tests added to pressurization_service_spec.rb, worldhouse_spec.rb pass with 0 failures  
- [ ] **Integration Tests Passing**: Failure cascade service spec demonstrates correct multi-settlement propagation behavior  
- [ ] **Documentation Updated**: `docs/new_agent/projects/galaxy_game/research/LUNA-MVP-SIMULATION-DESIGN.md` updated to note this is Phase 6+ work (not needed for Luna simulation calibration)

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
- [Identify any related work that should be extracted as separate task files]

### Lessons learned  
- [Document insights gained about pressurization systems, failure modeling patterns, etc.]

---

## Notes for Future Reviewers

This task was **extracted from April 2026 backlog during June 12 triage session**. Original file: `docs/agent/archive/backlog_april_2026/deprecated/2026-04-17-CRITICAL-ARCHITECTURE-ENCLOSED-ATMOSPHERE-FAILURE-PREDICTION-PLANNING.md`

**Why Phase 6+ and not Phase 5?**: Per `research/LUNA-MVP-SIMULATION-DESIGN.md`, Luna simulation calibration (Phase 5) focuses on proving the fuel loop closes — consumption modeling, precursor phase gating, life support ordering. TTR/failure prediction is NOT listed as a Phase 5 acceptance criterion; it's future scalability work for multi-settlement infrastructure after L1 Depot operational.

**Original April task was CRITICAL priority**: This reflects its importance in the original architecture vision but doesn't mean it blocks Luna simulation calibration. Priority adjusted to HIGH (not CRITICAL) since this is deferred work, not immediate Phase 5 blocker.
