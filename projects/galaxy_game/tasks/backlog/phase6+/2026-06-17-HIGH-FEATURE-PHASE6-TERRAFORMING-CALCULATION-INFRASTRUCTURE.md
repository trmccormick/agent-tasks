---
id: "2026-06-17-HIGH-FEATURE-PHASE6-TERRAFORMING-CALCULATION-INFRASTRUCTURE"
status: backlog  
priority: HIGH (blocked by Phase 5 validation)
type: FEATURE/IMPLEMENTATION
created_date: 2026-06-17
system_domain: TERRAFORMING/SKIMMER_OPERATIONS
mvp_alignment: PHASE6_TERRAFORMING_PREPARATION
local_worker_safe: true
blocked_by: "Phase 5 Atmospheric Simulation Validation (must complete first)"
---

# Phase 6 Task — Terraforming Calculation Infrastructure & Data-Driven Extraction Limits

## Context & Strategic Purpose

**Why This Matters for Luna MVP:** After Phase 5 validation confirms what atmospheric tracking exists, we need to implement missing infrastructure before any terraforming calculations can be trusted. This task focuses on:

1. **Data-driven extraction limits** — replace hardcoded world names with configurable properties
2. **Gas composition feedback loop** — track extraction → processing → venting byproducts correctly  
3. **Terraforming efficiency modeling** — calculate CO2→O2 conversion rates, CH4 harvesting impacts

Per your clarification: early skimmer operations do affect atmospheric composition through gas venting and processing byproducts. This task builds the infrastructure to model those effects accurately before we can simulate multi-system Cycler routes with bulk resource movement (Phase 7+).

**Core Principle**: Terraforming calculations require **validated simulation data**. Phase 5 tells us what exists; this phase fills gaps so terraforming math is trustworthy.

---

## Implementation Scope (Based on Phase 5 Validation Findings)

### Assumptions — To Be Confirmed During Phase 6 Planning
*These will be validated/adjusted after Phase 5 completion:*

**Assumption A**: Current atmosphere tracking has partial implementation:
- ✅ Tracks total atmospheric mass changes from extraction  
- ⚠️ May not track which specific gases are removed vs. vented back
- ❌ Likely missing processing byproduct gas composition (CO2 from CH4 combustion)

**Assumption B**: Hardcoded world names work for now but need data-driven approach:
```ruby
# Current hardcoded pattern (from Phase 5 audit):
extraction_limit = case @source.name.downcase
  when 'titan'  
    0.05  # 5% limit to preserve Titan while extracting CH4
  when 'venus'
    Float::INFINITY  # No extraction limit — terraforming goal is ~99% CO2 reduction
  else
    0.20  # Default 20% cap for other bodies (not 10% as task file originally said)
end

# Data-driven replacement pattern:
extraction_limit = @source.celestial_body.properties&.dig('atmospheric_processing', 'max_extraction_fraction') || DEFAULT_EXTRACTION_FRACTION
```

**Assumption C**: Processing byproducts need explicit tracking:
- Titan CH4 + LOX combustion → CO2 and H2O exhaust products (added back to atmosphere)  
- Venus CO2 extraction for O2 production → what's vented during processing?

---

## Technical Requirements

### Phase 1: Data Schema Design & Migration (Implementation — Day 1-3)
**Goal**: Add atmospheric processing configuration fields to Sol system JSON files.

```json
// Update sol-complete.json and/or sol.json with new schema:

{
  "venus": {
    // ... existing properties ...
    "properties": {
      "standard_orbital_altitude_km": 15000,
      "atmospheric_processing": {
        "max_extraction_fraction": null,  // No limit — terraforming goal is ~99% CO2 reduction to ~1 bar
        "primary_gases": ["CO2", "N2"],
        "surface_pressure_bar": 90.0,
        "target_surface_pressure_bar": 1.0,
        "processing_byproducts": {
          // When extracting CO2 for O2 production via electrolysis:
          "vented_composition": {
            "CO2_percent_remaining": 5,  // Final target composition after terraforming complete
            "N2_percent_increased": true,  // N2 becomes dominant as CO2 removed
            "O2_production_rate_per_day": 10.0  // kg/day of O2 produced from extracted CO2
          }
        },
        "special_case": "terraforming_goal_co2_to_o2"  
      }
    }
  },
  "titan": {
    // ... existing properties ...
    "properties": {
      "standard_orbital_altitude_km": 5000,
      "major_moon": true,
      "atmospheric_processing": {
        "max_extraction_fraction": 0.05,  // Preserve 95% of atmosphere while extracting CH4 for greenhouse warming
        "primary_gases": ["N2", "CH4"],
        "surface_pressure_bar": 1.45,
        "extraction_purpose": "methane_harvesting_for_greenhouse_warming_and_fuel_production",
        "processing_byproducts": {
          // When extracting CH4 for fuel tanks (burns with LOX):
          "vented_composition": {
            "CO2_from_combustion_percent_added": 0.5,  // Small amount added per extraction cycle  
            "H2O_vapor_from_combustion_percent_added": 0.3,
            "N2_venting_percent_of_extracted_mass": 90  // Most extracted N2 is vented back after CH4 separation
          },
          "processing_efficiency_factor": 0.85  // 15% loss during extraction/separation process
        }
      }
    }  
  },
  "luna": {
    // ... existing properties ...
    "properties": {
      "atmospheric_processing": {
        "max_extraction_fraction": 0.20,  // Default conservative cap for near-vacuum lava tube pressurization scenario
        "primary_gases": ["trace"],  
        "surface_pressure_bar": 0.0001,
        "special_case": "lava_tube_gas_release_over_time",
        "note": "Atmospheric extraction primarily for terraforming infrastructure testing; actual Luna atmosphere is near-vacuum"
      }
    }
  },
  // Generic fallback for worlds without atmospheric_processing config:
  "default_atmospheric_config": {
    "max_extraction_fraction": 0.20,  // Match current hardcoded default behavior (not original task file's 10%)
    "primary_gases": ["unknown"],  
    "surface_pressure_bar": null,
    "note": "Fallback value — worlds without explicit config use this conservative cap"
  }
}
```

**Deliverable**: Updated Sol system data files with atmospheric processing properties for Venus, Titan, Luna; validated JSON schema.

### Phase 2: Service Refactor (Implementation — Day 4-7)  
**Goal**: Replace hardcoded case statements with data-driven property lookups in `AtmosphericTransferService`.

```ruby
# Before (current implementation from Phase 5 audit):
extraction_limit = case @source.name.downcase
  when 'titan'  
    0.05  # Preserve atmosphere while extracting CH4 for greenhouse warming
  when 'venus'
    Float::INFINITY  # No limit — terraforming goal is ~99% CO2 reduction
  else
    0.20  # Default cap (not original task file's 10%)
end

# After (data-driven pattern with byproduct tracking):
def extraction_limit_for_source(source)
  atmospheric_config = source.celestial_body.properties&.dig('atmospheric_processing')
  
  return nil if atmospheric_config['max_extraction_fraction'].nil? # No limit for Venus terraforming
  
  atmospheric_config['max_extraction_fraction'] || DEFAULT_EXTRACTION_FRACTION
end

def process_atmospheric_extraction(source, extracted_mass:, processed_component:)
  extraction_limit = extraction_limit_for_source(source)
  
  return false if exceeds_extraction_limit?(source, extracted_mass, extraction_limit)
  
  # Extract gases from atmosphere (proportional to composition)
  atmospheric_changes = extract_gases_from_atmosphere(
    source.celestial_body.atmosphere, 
    components: processed_component, 
    mass: extracted_mass
  )
  
  # Calculate processing byproducts and venting effects
  if has_processing_byproducts?(source.celestial_body)
    byproduct_composition = get_vented_gases(source.celestial_body, processed_component)
    atmospheric_changes.add_vented_gases(byproduct_composition)
  end
  
  # Update celestial body atmosphere with net changes  
  source.celestial_body.atmosphere.update_from_gas_changes(atmospheric_changes)
  
  { 
    success: true, 
    extracted_mass:, 
    byproducts_added: atmospheric_changes.vented_gases,
    final_atmospheric_composition: calculate_new_composition(source.celestial_body)
  }
end

private

def has_processing_byproducts?(celestial_body)
  celestial_body.properties&.dig('atmospheric_processing', 'processing_byproducts').present?
end

def get_vented_gases(celestial_body, processed_component)
  config = celestial_body.properties['atmospheric_processing']
  
  case [processed_component, celestial_body.name.downcase]
  when ['CH4', 'titan']
    # Titan: CH4 extraction with LOX combustion → CO2 + H2O exhaust products added back to atmosphere
    { 
      "CO2_percent_added": config['processing_byproducts']['vented_composition']['CO2_from_combustion_percent_added'],
      "H2O_vapor_percent_added": config['processing_byproducts']['vented_composition']['H2O_vapor_from_combustion_percent_added']  
    }
  when ['CO2', 'venus']
    # Venus: CO2 extraction for O2 production — what's vented during processing?
    { 
      "N2_percent_increased": config['processing_byproducts']['vented_composition']['N2_percent_increased'],
      "O2_production_rate_per_day": config['processing_byproducts']['vented_composition']['O2_production_rate_per_day']  
    }
  else
    # Generic fallback: assume no byproduct gases added back (just mass reduction)
    {}
  end
end

def calculate_new_composition(celestial_body)
  total_mass = celestial_body.atmosphere.total_atmospheric_mass
  
  return {} if total_mass.zero?
  
  # Calculate percentage composition of each gas after extraction/venting operations
  celestial_body.atmosphere.gases.map do |gas|
    { 
      name: gas.name, 
      mass: gas.mass, 
      percent_of_total: (gas.mass / total_mass * 100).round(2)  
    }
  end
end

DEFAULT_EXTRACTION_FRACTION = 0.20 # Match current hardcoded default behavior; document rationale for future adjustment if MVP design requires different value
```

**Key Implementation Notes:**
- Add helper methods `process_atmospheric_extraction`, `get_vented_gases` for testability and reuse  
- Use safe navigation (`&.`) to handle celestial bodies without atmospheric processing config (fallback to defaults)
- Define constant `DEFAULT_EXTRACTION_FRACTION = 0.20` at class level (documented fallback behavior — matches current hardcoded default, not original task file's 10%)
- **Critical**: Byproduct tracking must be configurable per world/processing type via JSON schema

**Deliverable**: Refactored service code with data-driven extraction limits and processing byproduct tracking.

### Phase 3: Test Coverage (Validation — Day 8-10)  
**Goal**: Add RSpec tests ensuring correct behavior and preventing regression.

```ruby
# spec/services/terra_sim/atmospheric_transfer_service_spec.rb

RSpec.describe TerraSim::AtmosphericTransferService do
  
  describe '#extraction_limit_for_source' do
    
    context 'when celestial body has atmospheric_processing config with null max_extraction_fraction (Venus terraforming)' do
      it 'returns nil to indicate no extraction limit — cycler capacity is only constraint' do
        venus = create(:celestial_body, name: 'Venus', properties: { 
          atmospheric_processing: { 
            max_extraction_fraction: nil,  # No limit for terraforming goal of ~99% CO2 reduction  
            primary_gases: ["CO2", "N2"],
            surface_pressure_bar: 90.0,
            target_surface_pressure_bar: 1.0
          } 
        })
        
        expect(subject.extraction_limit_for_source(venus)).to be_nil
        
        # Verify this allows extraction up to cycler capacity (not atmospheric constraint)  
        allow_any_instance_of(TerraSim::AtmosphericTransferService).to receive(:cycler_capacity_constraint).and_return(100.0)
        
        result = subject.process_atmospheric_extraction(venus, extracted_mass: 50.0, processed_component: 'CO2')
        expect(result[:success]).to be true # No extraction limit blocking operation
      end
      
      it 'handles Float::INFINITY as legacy representation of no limit' do
        # Current code uses Float::INFINITY for Venus — ensure backward compatibility during transition period  
        venus_legacy = create(:celestial_body, name: 'Venus', properties: { 
          atmospheric_processing: { max_extraction_fraction: Float::INFINITY }
        })
        
        expect(subject.extraction_limit_for_source(venus_legacy)).to be_nil # Normalize to nil for consistency
      end
    end
    
    context 'Titan CH4 harvesting with gas venting' do
      
      it 'respects 5% preservation cap while extracting methane' do
        titan = create(:celestial_body, name: 'Titan', properties: { 
          atmospheric_processing: { 
            max_extraction_fraction: 0.05,  
            primary_gases: ["N2", "CH4"],
            surface_pressure_bar: 1.45,
            extraction_purpose: "methane_harvesting_for_greenhouse_warming_and_fuel_production"
          } 
        })
        
        expect(subject.extraction_limit_for_source(titan)).to eq(0.05)
      end
      
      it 'tracks processing byproducts from CH4 + LOX combustion' do
        titan = create(:celestial_body, name: 'Titan', properties: { 
          atmospheric_processing: { 
            max_extraction_fraction: 0.05,  
            primary_gases: ["N2", "CH4"],
            surface_pressure_bar: 1.45,
            processing_byproducts: {
              vented_composition: {
                CO2_from_combustion_percent_added: 0.5,
                H2O_vapor_from_combustion_percent_added: 0.3,
                N2_venting_percent_of_extracted_mass: 90  
              }
            },
            processing_efficiency_factor: 0.85
          } 
        })
        
        # Simulate extraction of CH4 for fuel tanks (burns with LOX)
        result = subject.process_atmospheric_extraction(
          titan, 
          extracted_mass: 10.0,  
          processed_component: 'CH4'
        )
        
        expect(result[:success]).to be true
        
        # Verify byproducts added back to atmosphere (CO2 and H2O from combustion)
        expect(result[:byproducts_added]['CO2_percent_added']).to eq(0.5)
        expect(result[:byproducts_added]['H2O_vapor_percent_added']).to eq(0.3)
        
        # Verify N2 venting after CH4 separation (most extracted mass is N2, not CH4)  
        expect(result[:byproducts_added]['N2_venting_percent_of_extracted_mass']).to eq(90)
      end
      
      it 'accumulates composition changes over multiple extraction cycles' do
        # Run simulation for 10 days with repeated skimmer operations
        
        titan = create(:celestial_body, name: 'Titan', properties: { 
          atmospheric_processing: { max_extraction_fraction: 0.05 }  
        })
        
        initial_composition = calculate_new_composition(titan)
        
        # Simulate multiple extraction cycles with byproduct venting
        10.times do |day|
          subject.process_atmospheric_extraction(
            titan, 
            extracted_mass: (2.0 + day * 0.5),  
            processed_component: 'CH4'
          )
          
          # Verify composition shifts gradually based on net effect of all extractions/ventings
          expect(calculate_new_composition(titan)['CO2_percent_added']).to be > initial_composition['CO2_percent'] + (day * 0.5)
        end
        
        final_composition = calculate_new_composition(titan)
        
        # Verify cumulative effect: CH4 percentage decreased, CO2 and H2O increased from combustion byproducts  
        expect(final_composition['CH4_percent']).to be < initial_composition['CH4_percent'] - 0.5
      end
      
    end
    
    context 'Venus CO2 extraction for O2 production' do
      
      it 'tracks atmospheric mass reduction without extraction limit (terraforming goal)' do
        venus = create(:celestial_body, name: 'Venus', properties: { 
          atmospheric_processing: { 
            max_extraction_fraction: nil,  # No limit — terraforming goal is ~99% CO2 reduction to ~1 bar  
            primary_gases: ["CO2", "N2"],
            surface_pressure_bar: 90.0,
            target_surface_pressure_bar: 1.0,
            processing_byproducts: {
              vented_composition: {
                N2_percent_increased: true,
                O2_production_rate_per_day: 10.0  
              }
            },
            special_case: "terraforming_goal_co2_to_o2"
          } 
        })
        
        # Simulate CO2 extraction for electrolysis (CO2 → O2 + C)
        result = subject.process_atmospheric_extraction(
          venus, 
          extracted_mass: 5.0,  
          processed_component: 'CO2'
        )
        
        expect(result[:success]).to be true
        
        # Verify atmospheric pressure decreased (mass removed from atmosphere)
        expect(result[:final_pressure_bar]).to be < initial_pressure_bar(venus) - 0.1
        
        # O2 production rate tracked separately for terraforming calculations  
        expect(subject.oxygen_production_rate_for(venus)).to eq(10.0) # kg/day based on processing config
      end
      
    end
    
    context 'Generic worlds without atmospheric_processing config' do
      
      it 'returns default 20% extraction cap (matches current hardcoded behavior)' do
        generic_world = create(:celestial_body, name: 'GenericWorld', properties: {})  
        
        expect(subject.extraction_limit_for_source(generic_world)).to eq(0.20) # Default fallback matches current codebase
        
        # Verify no regression in existing extraction limit logic for worlds without special cases
      end
      
    end
    
  end
  
  describe '#process_atmospheric_extraction' do
    
    it 'handles case where processing byproducts are not configured (fallback to mass reduction only)' do
      minimal_world = create(:celestial_body, name: 'MinimalWorld', properties: { 
        atmospheric_processing: {} # No vented_composition or other details  
      })
      
      result = subject.process_atmospheric_extraction(
        minimal_world, 
        extracted_mass: 5.0,  
        processed_component: 'CO2'
      )
      
      expect(result[:success]).to be true
      
      # Verify atmospheric mass decreased by extracted amount (no additional gas additions)
      expect(result[:byproducts_added]).to eq({}) # No venting effects when not configured
    end
    
  end
  
end
```

**Deliverable**: Full RSpec coverage for data-driven extraction limits, processing byproduct tracking, and regression tests preventing hardcoded patterns.

---

## Testing Requirements

### Unit Tests (RSpec — Service Layer)
- ✅ `extraction_limit_for_source` returns correct values from celestial body properties  
- ✅ Fallback defaults work when property missing or nil (20% default for generic worlds)
- ⚠️ **Critical**: Processing byproduct tracking works correctly per world/processing type configuration
- ✅ No case statements on world names remain in execution path

### Integration Tests (Service + Data Layer)  
- ✅ Service reads updated Sol system JSON files with atmospheric processing config
- ✅ Venus terraforming scenario allows unlimited extraction up to cycler capacity constraint
- ⚠️ **Critical**: Titan CH4 harvesting correctly tracks CO2 and H2O byproducts from combustion venting back into atmosphere
- ✅ Generic worlds fall back to 20% default safely (matches current hardcoded behavior)

### System Tests (End-to-End Simulation Scenarios)  
- ⚠️ **Phase 6 gate check**: After this refactor completes, atmospheric processing parameters are data-driven and simulation correctly tracks gas composition changes during skimmer operations
- ⚠️ **Critical dependency for Phase 7+**: Must validate that cumulative extraction effects over multiple cycles produce coherent results before implementing multi-system Cycler routes with bulk resource movement

---

## Dependencies & Blockers

### Prerequisites (Must Complete First)  
- ✅ Luna Phase 1–4 complete (`Settlements::CostAnalyzer`, `Logistics` services, AI Manager integration)
- ⚠️ **Blocked by**: Phase 5 Atmospheric Simulation Validation must confirm what tracking exists before implementing gaps

### Blocked By
- **Phase 5 validation completion** — cannot create accurate implementation plan until discovered gaps are known  
- May need to adjust scope based on whether current atmosphere tracking is partial or non-existent

### Blocks (Downstream Dependencies)
- ⏳ **Phase 7+ multi-system Cycler operations**: Requires validated single-world extraction mechanics + bulk resource movement infrastructure first
- ⏳ **Advanced terraforming calculations**: Cannot trust CO2→O2 conversion efficiency modeling until Phase 6 confirms atmospheric tracking is accurate

---

## Success Criteria & Acceptance Tests

### Code Quality Checks  
- ✅ No case statements on `source.name` or `celestial_body.name` remain in `AtmosphericTransferService`
- ✅ All extraction limits derived from celestial body properties with safe fallback defaults (20% for generic worlds)
- ⚠️ **Critical**: Processing byproduct tracking correctly models gas composition changes during skimmer operations per world/processing type configuration

### Data Schema Validation  
- ✅ Sol system JSON files include `properties.atmospheric_processing` for Venus, Titan, Luna with documented rationale  
- ✅ Values match current hardcoded logic (Venus=null/Titan=0.05/Luna=0.20) plus new processing byproduct fields
- ⚠️ **Critical**: Byproduct composition configuration allows per-world customization of venting effects

### Simulation Validity (Gate Check for Phase 7+)  
- ✅ After this refactor completes: Can confidently answer "Does the simulation correctly track atmospheric composition changes during skimmer operations?" with YES + specific validation results
- ⚠️ **Phase 6 gate**: If tracking is incomplete after implementation, must have prioritized gap-filling tasks ready before proceeding to Phase 7+ Cycler route work

---

## Notes for Implementation Agent

1. **This task depends on Phase 5 findings** — read the validation report first to understand what gaps exist vs. current partial implementations
2. **Default value is 20% not 10%** — matches current hardcoded behavior; document rationale if MVP design requires different value later  
3. **Processing byproducts are critical** — this is where most simulation accuracy depends on correct modeling of gas composition changes during skimmer operations
4. **Venus special case uses `nil` for no limit** (not original task file's Float::INFINITY) — normalize to nil for consistency; handle legacy Float::INFINITY as transitional support if needed
5. **Document all assumptions made about current tracking state** — Phase 6 implementation may need adjustments based on discovered gaps

---

## References
- Current service code: `app/services/terra_sim/atmospheric_transfer_service.rb` (lines 108–173 show hardcoded case statements)  
- Related services to refactor: atmosphere simulation, biosphere/geosphere interface for gas mass calculations
- Phase alignment guide: This task is **Phase 6 terraforming infrastructure** — must complete after Phase 5 validation and before any multi-system Cycler route work (Phase 7+)
