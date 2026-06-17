---
id: "2026-06-17-HIGH-FEATURE-PHASE5-ATMOSPHERIC-SIMULATION-VALIDATION"
status: backlog
priority: HIGH
type: FEATURE/TESTING
created_date: 2026-06-17
system_domain: TERRAFORMING/SKIMMER_OPERATIONS
mvp_alignment: PHASE5_SIMULATION_VALIDATION
local_worker_safe: true
---

# Phase 5 Task — Validate Atmospheric Composition Tracking During Skimmer Operations

## Context & Strategic Purpose

**Why This Matters for Luna MVP:** Early skimmer operations on Venus and Titan **do affect atmospheric composition**, even if the effects are small. The game must track these changes correctly because:

1. **Venus CO2→O2 conversion efficiency** — affects long-term terraforming trajectory
2. **Titan CH4 harvesting with gas venting** — burning CH4/LOX leaves different gases in atmosphere  
3. **Cumulative effects over time** — repeated extraction cycles could slowly adjust composition if not tracked

Per your clarification: skimmers extract raw atmosphere, process desired components (CH4 on Titan), and may vent excess or byproduct gases back into the atmosphere. This creates a feedback loop that must be simulated correctly before we can trust any terraforming calculations in Phase 6+.

**Core Principle**: Atmospheric composition is **stateful simulation data**, not static configuration. We need to validate existing tracking mechanisms work correctly under skimmer operation scenarios.

---

## Problem Statement

### Current State — What Exists
- ✅ `TerraSim::AtmosphereSimulationService` tracks greenhouse gases (CO2, CH4, N2O, H2O) with mass calculations
- ✅ Atmosphere has gas composition data (`@celestial_body.atmosphere.gases`)  
- ⚠️ **Unknown**: Does skimmer extraction/venting actually modify this state in simulation?

### Current State — What We Need to Validate
```ruby
# Skimmer operation scenario (Titan example):
1. Extract 100 units of raw atmosphere → removes CH4, N2 from atmospheric mass
2. Process CH4 for fuel tanks  
3. Vent excess gases or byproducts back into atmosphere:
   - Burning CH4 + LOX leaves CO2 and H2O as exhaust products
   - Unprocessed components may be vented (N2 on Titan)
4. Net effect = extraction minus processing losses plus venting additions

# Question to answer in Phase 5 testing:
Does the current simulation track this correctly? Or does it just reduce atmospheric mass without composition changes?
```

### Expected Behavior After Validation
- ✅ If tracking exists and works → document scenarios, create regression tests  
- ⚠️ If partial implementation found → identify gaps for Phase 6 terraforming work
- ❌ If no tracking exists → this becomes a critical gap to address before any terraforming calculations can be trusted

---

## Implementation Scope (Phase 5 Testing Only)

### In Scope — Validation & Discovery Tasks

**1. Audit Existing Atmosphere Tracking Mechanisms**
   - Trace `AtmosphereSimulationService` gas mass updates during simulation cycles
   - Check if skimmer operations modify atmospheric composition in codebase
   - Review TerraSim service interactions: does extraction affect atmosphere state?

**2. Create Test Scenarios for Skimmer Operations**
   ```ruby
   # Scenario 1 — Titan CH4 harvesting with venting
   - Start: Titan has N2 (95%) + CH4 (5%) atmospheric composition  
   - Operation: Extract 10 units, process to fuel tanks, vent excess gases
   - Expected: Atmospheric mass decreases by extracted amount; composition shifts based on what's processed vs. vented
   
   # Scenario 2 — Venus CO2 skimming with O2 production
   - Start: Venus has ~96% CO2 atmosphere at 90 bar pressure
   - Operation: Extract CO2, convert to O2 via processing (terraforming goal)  
   - Expected: Atmospheric mass decreases; if venting occurs, what gases are released?

   # Scenario 3 — Cumulative effects over extended simulation
   - Run multiple extraction cycles on same world
   - Track whether composition changes accumulate or reset each cycle
   ```

**3. Identify Gaps for Phase 6+ Terraforming Work**
   - Document which atmospheric tracking features exist vs. missing  
   - Create task list of gaps that must be filled before terraforming calculations can proceed
   - Determine if hardcoded extraction limits (Venus/Titan special cases) are sufficient or need data-driven approach

### Out of Scope — Implementation Tasks (Phase 6+)
- Implementing new atmospheric composition tracking mechanisms ❌  
- Adding terraforming calculation logic for Venus CO2→O2 conversion ❌
- Creating multi-system Cycler route operations with bulk resource movement ❌
- Refactoring hardcoded world names to data-driven schema (unless discovered as critical gap)

---

## Technical Requirements

### Phase 1: Codebase Audit (Research — Day 1-2)
**Goal**: Understand what atmospheric tracking already exists.

```bash
# Step 1 — Trace atmosphere state modifications during skimmer operations:
grep -rn "atmosphere.*gas\|extract.*atmosphere" galaxy_game/app/services/terra_sim/ --include="*.rb" | head -30  

# Step 2 — Check if extraction modifies atmospheric composition:
find galaxy_game/app/models -name "*atmosphere*" -o -name "*celestial_body*" 
grep -rn "extract\|vent\|process" galaxy_game/app/services/skimmer/ --include="*.rb" | head -30

# Step 3 — Review simulation service interactions:
cat galaxy_game/app/services/terra_sim/atmosphere_simulation_service.rb
```

**Deliverable**: Audit report documenting which atmospheric tracking features exist, how they work, and what gaps were found.

### Phase 2: Test Scenario Development (Testing — Day 3-4)  
**Goal**: Create focused RSpec tests to validate existing behavior under skimmer operation scenarios.

```ruby
# spec/services/terra_sim/atmosphere_simulation_service_spec.rb

RSpec.describe TerraSim::AtmosphereSimulationService do
  describe 'skimmer operations impact on atmospheric composition' do
    
    context 'Titan CH4 harvesting with gas venting' do
      it 'tracks extraction of raw atmosphere components correctly' do
        # Setup: Titan has N2 (95%) + CH4 (5%) 
        titan = create(:celestial_body, name: 'Titan', atmosphere_attributes: { ... })
        
        # Operation: Extract 10 units, process CH4 for fuel tanks  
        skimmer_operation = build(:skimmer_extraction, celestial_body: titan)
        result = TerraSim::AtmosphereSimulationService.new(titan).simulate_skimmer_op(skimmer_operation)
        
        # Verify: Atmospheric mass decreased by extracted amount; composition shifted appropriately
        expect(result.atmospheric_mass_change).to eq(-10.0)
        expect(result.ch4_extracted).to be > 0
      end
      
      it 'accounts for venting of excess gases back into atmosphere' do
        # Setup: Skimmer extracts more than needed, vents remainder  
        
        # Operation: Extract with partial processing and gas venting
        
        # Expected: Venting adds specific gases back (not just mass reduction)
        expect(result.vented_gases).to include('N2')  # Example expected byproduct
      end
      
      it 'accumulates composition changes over multiple extraction cycles' do
        # Run simulation for N days with repeated skimmer operations
        
        # Expected: Composition shifts gradually based on net effect of all extractions/ventings
        expect(final_composition.ch4_percentage).to be < initial_composition.ch4_percentage
      end
    end
    
    context 'Venus CO2 extraction and O2 production' do
      it 'tracks atmospheric mass reduction from skimmer operations' do
        # Setup: Venus 96% CO2 atmosphere at ~90 bar
        
        # Operation: Extract CO2 for terraforming processing
        
        # Expected: Atmospheric pressure decreases; if venting occurs, what gases released?
      end
      
      it 'handles special case of no extraction limit (Float::INFINITY)' do
        # Current code uses Float::INFINITY for Venus — verify this works correctly in simulation
        expect(subject.extraction_limit_for_source(venus)).to eq(Float::INFINITY)
      end
    end
    
  end
  
  describe 'atmospheric composition tracking gaps' do
    it 'identifies missing features needed for terraforming calculations' do
      # This test will document what's NOT implemented yet
      pending "Document gap: Does simulation track gas byproducts from CH4/LOX combustion?"
      
      # Example finding to validate during Phase 5 testing:
      # Current code may just reduce atmospheric mass without tracking which gases are removed vs. vented back
    end
  end
end
```

**Deliverable**: RSpec test suite that validates existing behavior and documents gaps via pending tests with clear descriptions of missing features.

### Phase 3: Gap Analysis & Task Creation (Synthesis — Day 5)  
**Goal**: Create actionable task list for Phase 6+ terraforming work based on discovered gaps.

```markdown
# Terraforming Implementation Gaps Discovered in Phase 5 Testing

## Critical Gaps (Must Have Before Any Terraforming Calculations):
1. ❌ Gas byproduct tracking — simulation doesn't track what gases are produced during processing operations  
2. ⚠️ Venting mechanics exist but incomplete — can vent excess mass, not specific gas compositions
3. ❌ Cumulative composition changes over time — no mechanism to accumulate small extraction effects

## Nice-to-Have Gaps (Can Be Added Later):
4. ⚪ Data-driven extraction limits vs. hardcoded world names (currently works with special cases)  
5. ⚪ Multi-world atmospheric interaction via Cycler routes (requires bulk resource movement first)
```

**Deliverable**: Task files for Phase 6+ terraforming work, prioritized by criticality to MVP simulation validity.

---

## Testing Requirements

### Unit Tests (RSpec — Service Layer)
- ✅ `AtmosphereSimulationService` correctly tracks gas mass changes during skimmer operations  
- ✅ Extraction reduces atmospheric composition of targeted gases proportionally
- ⚠️ **Gap discovery**: Does venting add specific gases back or just reduce total mass?
- ⚠️ **Gap discovery**: Are processing byproducts (CO2 from CH4 combustion) tracked and added to atmosphere?

### Integration Tests (Simulation + Data Layer)  
- ✅ Extended simulation runs show cumulative composition changes over multiple extraction cycles
- ✅ Venus special case (`Float::INFINITY` extraction limit) works correctly in simulation context
- ⚠️ **Gap discovery**: Does current implementation support the full skimmer operation feedback loop?

### System Tests (End-to-End Simulation Scenarios)  
- ⚠️ **Phase 5 gate check**: After validation completes, we must have clear understanding of what tracking exists vs. missing before any terraforming calculations can be trusted
- ⚠️ **Critical dependency**: Phase 6+ terraforming tasks cannot proceed until this validation confirms whether existing mechanisms are sufficient or need replacement

---

## Dependencies & Blockers

### Prerequisites (Must Complete First)
- ✅ Luna Phase 1–4 complete (`Settlements::CostAnalyzer`, `Logistics` services, AI Manager integration)  
- ✅ RSpec baseline clean: 746 examples, 0 failures as of June 15, 2026

### Blocked By
- **None** — this task is ready to dispatch immediately after archival of legacy file #8.

### Blocks (Downstream Dependencies)
- ⏳ **Phase 6+ terraforming tasks**: Cannot create accurate implementation plans until Phase 5 validation identifies specific gaps in atmospheric tracking mechanisms  
- ⏳ **Multi-system Cycler operations**: Requires bulk resource movement infrastructure + validated single-world extraction mechanics first

---

## Success Criteria & Acceptance Tests

### Code Quality Checks
- ✅ Audit report documents all existing atmosphere state modification code paths
- ✅ RSpec test suite covers skimmer operation scenarios for Venus, Titan, and generic worlds  
- ⚠️ **Gap discovery**: Clear identification of what tracking exists vs. missing features needed for terraforming calculations

### Simulation Validity (Critical Gate Check)
- ✅ After Phase 5 testing completes: Can confidently answer "Does the simulation correctly track atmospheric composition changes during skimmer operations?" with YES or NO + specific gap list if NO  
- ⚠️ **Phase 6 gate**: If tracking is incomplete, must have prioritized task list of gaps that block terraforming calculations

### Task Creation Quality
- ✅ Phase 6+ terraforming tasks created based on discovered gaps (not assumptions)
- ✅ Each Phase 6+ task has clear acceptance criteria tied to specific missing features identified in validation testing  
- ⚠️ **Critical**: If no tracking exists at all, must have high-priority gap-filling tasks ready for immediate implementation

---

## Notes for Implementation Agent

1. **This is a VALIDATION TASK, not an implementation task** — your job is to discover what exists and document gaps, NOT to implement new features
2. **Read existing code carefully before creating tests** — understand current atmosphere state modification mechanisms first (Phase 1 audit)
3. **Use pending specs strategically** — mark missing features as `pending` with clear descriptions of what's needed rather than trying to force-fit incomplete implementations  
4. **Focus on skimmer operation feedback loop**: extraction → processing → venting byproducts back into atmosphere
5. **Document the "current behavior" even if it seems wrong** — sometimes simulation has partial tracking that works for some scenarios but not others

---

## References
- Current service code: `app/services/terra_sim/atmosphere_simulation_service.rb` (lines 1–80 show gas mass calculations)  
- Related services to audit: `atmospheric_transfer_service.rb`, biosphere simulation, geosphere initializer
- Phase alignment guide: This task is **Phase 5 validation** — must complete before any Phase 6+ terraforming implementation can be accurately scoped
