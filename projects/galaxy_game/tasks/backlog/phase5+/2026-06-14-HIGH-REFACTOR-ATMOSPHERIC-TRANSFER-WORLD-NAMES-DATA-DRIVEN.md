---
id: "2026-06-14-HIGH-REFACTOR-ATMOSPHERIC-TRANSFER-WORLD-NAMES-DATA-DRIVEN"
status: backlog
priority: HIGH
type: REFACTOR
created_date: 2026-06-14
system_domain: ISRU/ATMOSPHERIC_PROCESSING
mvp_alignment: PHASE5_CALIBRATION_PREP
local_worker_safe: true
---

# Refactor Atmospheric Transfer Service — Replace Hardcoded World Names with Data-Driven Extraction Limits

## Context & Strategic Purpose

**Why This Matters for Luna Simulation:** The `TerraSim::AtmosphericTransferService` currently uses hardcoded world name branching to determine atmospheric extraction limits. This directly impacts:

1. **Luna lava tube pressurization modeling** — Gas release over time calculations depend on accurate atmospheric properties
2. **Venus skimmer CO2→O2 conversion efficiency** — Affects multi-source supply chain for fuel loop validation  
3. **Titan CH4 harvesting rates** — Impacts inbound cargo awareness and resource coordination patterns

Per `research/LUNA-MVP-SIMULATION-DESIGN.md`, Phase 5 requires accurate atmospheric processing parameters derived from celestial body data, not name-based branching. This refactor is a prerequisite before Luna simulation can produce economically coherent behavior at real-world price anchors.

**Core Principle**: Services ask the celestial body what it is (via `properties` hash or attributes). They do NOT pattern-match on its name to decide extraction limits or atmospheric processing parameters.

---

## Problem Statement

### Current Behavior
```ruby
# app/services/terra_sim/atmospheric_transfer_service.rb:112-118
extraction_limit = case @source.name.downcase
  when 'venus'
    nil # No percentage limit - goal is to reduce from ~90 bar to ~1 bar (remove ~99%)
  when 'titan'
    0.05  # 5% limit to preserve Titan while extracting CH4 for early greenhouse warming
  else
    0.1   # Default 10% extraction cap
end
```

**Issues:**
- Hardcoded world names (`venus`, `titan`) in case statements  
- No way to add new worlds without modifying service code
- Extraction limits not tied to actual atmospheric composition data on celestial bodies
- Breaks scalability for multi-system expansion (Proxima Centauri, Trappist-1 via wormholes)

### Expected Behavior
```ruby
# Data-driven pattern:
extraction_limit = @source.celestial_body.properties&.dig('atmospheric_processing', 'max_extraction_fraction')
return extraction_limit || 0.1 # Default 10% if not specified in data
```

**Benefits:**
- Adding new worlds requires only JSON/data changes, no service code modifications  
- Extraction limits tied to actual atmospheric properties (pressure, composition, volume)
- Scales seamlessly for multi-system expansion via wormhole network
- Enables accurate simulation of Luna surface atmospheric buildup scenarios

---

## Current State Analysis

| Component | Status | Evidence |
|-----------|--------|----------|
| `DepotAdapter#calculate_orbital_altitude` | ✅ **REFACTORED** (April 14, 2026) | Uses `world.properties&.dig('standard_orbital_altitude_km')` — commit `1fc6bd42` |
| `AtmosphericTransferService` extraction limits | ⚠️ **HARDCODED PATTERNS REMAIN** | Lines 112-118, 167-173 use case statements on `@source.name.downcase` |
| Celestial body atmospheric data schema | 🤔 **NEEDS AUDIT** | Check if `properties['atmospheric_processing']` exists in Sol system JSON files |
| Related services with similar patterns | ⚠️ **PARTIAL REFACTOR ONLY** | ~20 hardcoded world name references remain across codebase (see audit below) |

### Audit Results from Legacy Task File #8
```bash
# Total remaining hardcoded patterns: 20 instances
$ grep -rn "case.*\.name\|when /Mars" galaxy_game/app/services/ --include="*.rb" | wc -l  
20 matches found

# Claude's Phase Classification (June 14, 2026):
Group 1 — PHASE5+ (needs review before Luna simulation) ✅ THIS TASK:
- atmospheric_transfer_service.rb — extraction limits by world name

Group 2 — PHASE6+ (after Luna simulation validated):  
- escalation_service.rb — cargo routing logic, defer until after single-system fuel loop proven

Group 3 — ACCEPTABLE/NO ACTION:
- system_generator_service.rb — procedural generation patterns expected here
- fitting_service.rb — string construction identifiers (Type C per task criteria)
```

---

## Implementation Scope

### In Scope (This Task)
1. **Refactor `AtmosphericTransferService` extraction limits** to use data-driven pattern:
   - Replace case statements on `@source.name.downcase` with celestial body property lookups  
   - Add fallback defaults for worlds without atmospheric processing data specified
2. **Audit Sol system JSON files** (`data/json-data/star_systems/sol-complete.json`, `sol.json`) to determine if atmospheric properties exist:
   - Check for `atmospheric_processing.max_extraction_fraction` or similar schema fields
   - Document gaps where new attributes need to be added
3. **Update celestial body data schema** (if needed):
   - Add `properties.atmospheric_processing.max_extraction_fraction` field definition  
   - Populate values for Venus, Titan, Luna based on current hardcoded logic:
     - Venus: `null` (no limit — terraforming goal)
     - Titan: `0.05` (5% preservation cap)
     - Luna/Mars/Earth: `0.1` (default 10%)
4. **Add RSpec coverage** for data-driven extraction limits:
   - Test that service reads from celestial body properties correctly  
   - Verify fallback defaults work when property missing
   - Ensure no regression in existing atmospheric transfer behavior

### Out of Scope (Defer to Future Tasks)
- Refactoring `EscalationService` cargo routing logic → Phase 6+ task (not blocking Luna MVP)  
- Procedural generation patterns in `SystemGeneratorService` → Acceptable as-is per legacy task criteria  
- String construction identifiers in `FittingService` → Type C acceptable, no action needed
- Full codebase audit of remaining ~17 hardcoded world name references → Separate Phase 6+ refactor after Luna simulation validated

---

## Technical Requirements

### Phase 1: Audit & Schema Design (Research)
**Goal**: Understand current data structure and design schema for atmospheric processing properties.

```bash
# Step 1 — Check existing celestial body property schemas:
grep -rn "atmospheric\|extraction.*limit\|max_extraction" ~/Documents/git/galaxyGame/data/json-data/star_systems/sol*.json | head -20  

# Step 2 — Review current hardcoded values to extract into data:
cat ~/Documents/git/galaxyGame/galaxy_game/app/services/terra_sim/atmospheric_transfer_service.rb | grep -A 10 "extraction_limit = case"

# Step 3 — Design schema structure (example):
{
  "venus": {
    "properties": {
      "atmospheric_processing": {
        "max_extraction_fraction": null,  # No limit — terraforming goal is ~99% reduction  
        "primary_gases": ["CO2", "N2"],
        "surface_pressure_bar": 90.0,
        "target_surface_pressure_bar": 1.0
      }
    }
  },
  "titan": {
    "properties": {
      "atmospheric_processing": {
        "max_extraction_fraction": 0.05,  # Preserve 95% of atmosphere  
        "primary_gases": ["N2", "CH4"],
        "surface_pressure_bar": 1.45,
        "extraction_purpose": "methane_harvesting_for_greenhouse_warming"
      }
    }
  },
  "luna": {
    "properties": {
      "atmospheric_processing": {
        "max_extraction_fraction": 0.1,  # Default conservative cap  
        "primary_gases": ["trace"],
        "surface_pressure_bar": 0.0001,  # Near-vacuum — lava tube pressurization scenario
        "special_case": "lava_tube_gas_release_over_time"
      }
    }
  }
}
```

**Deliverable**: Schema design document with proposed JSON structure and field definitions for all Sol system bodies requiring atmospheric processing.

### Phase 2: Data Migration (Implementation)
**Goal**: Add atmospheric processing properties to celestial body data files.

1. Update `sol-complete.json` and/or `sol.json`:
   - Add `properties.atmospheric_processing` fields for Venus, Titan, Luna  
   - Populate values based on current hardcoded logic in service code
2. Verify JSON schema validity:
   ```bash
   cd ~/Documents/git/galaxyGame && bundle exec rake json_validate RAILS_ENV=test
   ```

**Deliverable**: Updated Sol system data files with atmospheric processing properties for all relevant celestial bodies.

### Phase 3: Service Refactor (Implementation)  
**Goal**: Replace hardcoded case statements with data-driven property lookups in `AtmosphericTransferService`.

```ruby
# Before (current implementation):
extraction_limit = case @source.name.downcase
  when 'venus'
    nil # No limit — terraforming goal
  when 'titan'  
    0.05 # Preserve atmosphere while extracting CH4
  else
    0.1 # Default cap
end

# After (data-driven pattern):
def extraction_limit_for_source(source)
  atmospheric_config = source.celestial_body.properties&.dig('atmospheric_processing')
  
  return atmospheric_config['max_extraction_fraction'] if atmospheric_config.present?
  
  DEFAULT_EXTRACTION_FRACTION # Fallback to 0.1 (10%) if not specified in data
end

# Usage:
extraction_limit = extraction_limit_for_source(@source)
```

**Key Implementation Notes:**
- Add helper method `extraction_limit_for_source` for testability and reuse  
- Use safe navigation (`&.`) to handle celestial bodies without atmospheric processing config  
- Define constant `DEFAULT_EXTRACTION_FRACTION = 0.1` at class level (documented fallback behavior)

**Deliverable**: Refactored service code with data-driven extraction limits, no hardcoded world name branching.

### Phase 4: Test Coverage (Validation)
**Goal**: Add RSpec tests ensuring correct behavior and preventing regression.

```ruby
# spec/services/terra_sim/atmospheric_transfer_service_spec.rb

RSpec.describe TerraSim::AtmosphericTransferService do
  describe '#extraction_limit_for_source' do
    context 'when celestial body has atmospheric_processing config' do
      it 'returns max_extraction_fraction from properties' do
        venus = create(:celestial_body, name: 'Venus', properties: { 
          atmospheric_processing: { max_extraction_fraction: nil } # No limit for terraforming  
        })
        source = build(:atmospheric_source, celestial_body: venus)
        
        expect(subject.extraction_limit_for_source(source)).to be_nil
      end
      
      it 'respects 5% cap for Titan' do
        titan = create(:celestial_body, name: 'Titan', properties: { 
          atmospheric_processing: { max_extraction_fraction: 0.05 }  
        })
        source = build(:atmospheric_source, celestial_body: titan)
        
        expect(subject.extraction_limit_for_source(source)).to eq(0.05)
      end
    end
    
    context 'when celestial body lacks atmospheric_processing config' do
      it 'returns default 10% extraction cap' do
        generic_world = create(:celestial_body, name: 'GenericWorld', properties: {})  
        source = build(:atmospheric_source, celestial_body: generic_world)
        
        expect(subject.extraction_limit_for_source(source)).to eq(0.1) # Default fallback
      end
      
      it 'handles nil properties gracefully' do
        minimal_world = create(:celestial_body, name: 'MinimalWorld', properties: nil)  
        source = build(:atmospheric_source, celestial_body: minimal_world)
        
        expect(subject.extraction_limit_for_source(source)).to eq(0.1) # Safe fallback
      end
    end
    
    context 'Luna lava tube pressurization scenario' do
      it 'supports special_case flag for gas release over time modeling' do
        luna = create(:celestial_body, name: 'Luna', properties: { 
          atmospheric_processing: { 
            max_extraction_fraction: 0.1,
            special_case: 'lava_tube_gas_release_over_time'  
          }
        })
        source = build(:atmospheric_source, celestial_body: luna)
        
        expect(subject.extraction_limit_for_source(source)).to eq(0.1)
        # Additional logic can check for special_case flag to apply time-based release curves
      end
    end
  end
  
  describe '#execute_transfer' do
    it 'uses data-driven extraction limits instead of hardcoded world names' do
      # Regression test ensuring no case statements on source.name remain in execution path  
      venus = create(:celestial_body, name: 'Venus', properties: { 
        atmospheric_processing: { max_extraction_fraction: nil }  
      })
      source = build(:atmospheric_source, celestial_body: venus)
      
      # Execute transfer and verify no hardcoded branching occurred
      expect(subject).to receive(:extraction_limit_for_source).with(source).and_return(nil)
      subject.execute_transfer(source, target_resource:, amount:)
    end
  end
end
```

**Deliverable**: Full RSpec coverage for data-driven extraction limits with regression tests preventing hardcoded patterns.

---

## Testing Requirements

### Unit Tests (RSpec — Service Layer)
- ✅ `extraction_limit_for_source` returns correct values from celestial body properties  
- ✅ Fallback defaults work when property missing or nil  
- ✅ No case statements on world names remain in execution path  
- ✅ Luna lava tube special_case flag handled correctly  

### Integration Tests (Service + Data Layer)
- ✅ Service reads updated Sol system JSON files with atmospheric processing config  
- ✅ Venus terraforming scenario works without extraction cap  
- ✅ Titan CH4 harvesting respects 5% preservation limit  
- ✅ Generic worlds fall back to 10% default safely

### System Tests (End-to-End Simulation Scenarios)
- ⚠️ **Luna simulation calibration** — Verify atmospheric processing produces economically coherent behavior at real-world price anchors (Phase 5 acceptance criteria per `research/LUNA-MVP-SIMULATION-DESIGN.md`)  
- ⚠️ **Multi-source supply chain validation** — Venus CO2→O2 and Titan CH4 extraction rates feed into fuel loop calculations correctly

---

## Dependencies & Blockers

### Prerequisites (Must Complete First)
- ✅ Luna Phase 1–3 complete (`Settlements::CostAnalyzer`, `Logistics::ManifestGenerator`, `ShortageDetector` + `ImportRequestGenerator`)  
- ✅ Luna Phase 4 complete (consumption-aware ordering, precursor phase gating — commit `5a2af17a`)

### Blocked By
- **None** — This task is ready to dispatch immediately after archival of legacy file #8.

### Blocks (Downstream Dependencies)
- ⏳ **Phase 5 simulation calibration tasks**:
  - Skimmer processing efficiency modeling  
  - Multi-source supply chain validation  
  - Tank farm architecture design  
  - Inbound cargo awareness implementation  
  - CH4 arbitration logic  

---

## Success Criteria & Acceptance Tests

### Code Quality Checks
- ✅ No case statements on `source.name` or `celestial_body.name` remain in `AtmosphericTransferService`  
- ✅ All extraction limits derived from celestial body properties with safe fallback defaults  
- ✅ RSpec suite passes: 0 failures, no pending tests related to atmospheric transfer

### Data Schema Validation
- ✅ Sol system JSON files include `properties.atmospheric_processing` for Venus, Titan, Luna  
- ✅ Values match current hardcoded logic (Venus=null/Titan=0.05/Luna=0.1) with documented rationale  
- ✅ JSON schema validation passes without errors or warnings

### Simulation Readiness
- ⚠️ **Phase 5 gate check**: After this refactor completes, atmospheric processing parameters are data-driven and Luna simulation can produce accurate fuel loop behavior (validated in subsequent Phase 5 calibration tasks).

---

## Notes for Implementation Agent

1. **Read legacy task file #8 first** — Contains full audit methodology and completion report format from original April 2026 request  
2. **Review commit `1fc6bd42`** — Shows how DepotAdapter was refactored to data-driven pattern (reference implementation)
3. **Check existing Sol system JSON structure** before designing schema — May already have partial atmospheric properties defined  
4. **Prioritize Venus/Titan/Luna scenarios** — These are the critical paths for Luna simulation calibration; other worlds can be added later with same pattern  
5. **Document special_case flags carefully** — Luna lava tube pressurization may need time-based release curves beyond simple extraction limits (scope this in Phase 1 audit)

---

## References
- Legacy task file: `docs/agent/archive/backlog_april_2026/deprecated/2026-04-10-MEDIUM-REFACTOR-HARDCODED-SOL-WORLD-NAMES-DATA-DRIVEN.md`  
- Phase alignment guide: `research/LUNA-MVP-SIMULATION-DESIGN.md` (Phase 5 calibration requirements)
- Reference refactor commit: `1fc6bd42` — "refactor: replace hardcoded Sol world names with data-driven orbital altitude and atmosphere checks — Phase 1"  
- Current service code: `app/services/terra_sim/atmospheric_transfer_service.rb` lines 108–173
