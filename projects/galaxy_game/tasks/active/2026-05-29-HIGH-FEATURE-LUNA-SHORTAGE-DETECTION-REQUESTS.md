---
status: in-progress
priority: HIGH
type: feature
system_domain: AI_MANAGER
mvp_alignment: LUNA_SETTLEMENT_AUTONOMOUS_TESTING
local_worker_safe: true
---

# TASK: Luna Phase 3 — Shortage Detection & Tiered Import Request Generation

**Status**: READY FOR IMPLEMENTATION  
**Priority**: HIGH  
**Type**: feature  
**Created**: 2026-05-29  
**Last Updated**: 2026-05-31  
**Assigned To**: Qwen3.5-9B (local) — Ready to implement  
**Blockers Cleared**: 2026-05-31 by Haiku  
**Est. Time (Local Agent)**: 2-3 hours (modular, well-spec'd)

---

## Agent Assignment

**Primary**: Qwen3.5-9B (local) — Full implementation  
**Why This Agent**: All blockers cleared, spec complete, modular services, straightforward RSpec integration  
**Supervision Level**: standard (verify test suite before merge)

---

## Strategic Context

Luna settlement must survive first, then expand. Shortage detection is tiered:

**TIER 1 (CRITICAL): Survival**
- Consumables (oxygen, water, food, power)
- Basic ISRU capability (must have at least O2 extraction or site is non-viable)
- Always imported if short (crew survival > costs)

**TIER 2 (EXPANSION): Growth**
- Production equipment (solar panels, power units, construction tools)
- Imported only after Tier 1 is secured
- ROI-driven (prefer units that enable local production)

**ISRU Viability Gate**: 
- If settlement lacks basic O2 extraction → flag as non-viable
- Prevents resource waste on expansion in unsurvivable locations
- Gate check happens during shortage detection

**Default Strategy**: Always prefer ISRU over imports (cost/sustainability), but critical consumables override this.

---

## Problem Statement

**Gap**: Settlements don't detect shortages with strategic priority. Current system would import expansion equipment before securing survival resources.

**Current state**: No distinction between critical consumables and nice-to-have equipment. No ISRU capability assessment.

**Missing pieces**:
1. **Tiered ShortageDetector**: Identify which shortages are survival-critical vs expansion
2. **ISRU Viability Check**: Assess settlement's basic production capability
3. **Tiered ImportRequestGenerator**: Create requests with proper priority (Tier 1 always, Tier 2 conditional)
4. **AI Manager Integration**: Enforce tier strategy in orchestration

---

## Specification

### Service 1: `Logistics::ShortageDetector` (REFACTORED)

**File**: `app/services/logistics/shortage_detector.rb`

#### Method: `self.detect_shortages(settlement, threshold_percent = 20)`

**Input**:
- `settlement` (Settlement::BaseSettlement)
- `threshold_percent` (Integer): Threshold for shortage detection (default 20%)

**Output**: Hash with structure:
```ruby
{
  survival_shortages: [
    { resource: "oxygen", current: 10, target: 100, amount: 90, critical: true, tier: :survival, category: :consumable },
    { resource: "water", current: 5, target: 50, amount: 45, critical: true, tier: :survival, category: :consumable }
  ],
  expansion_shortages: [
    { resource: "solar_panel", current: 2, target: 10, amount: 8, critical: false, tier: :expansion, category: :equipment }
  ],
  viable: true,        # false if missing basic ISRU
  viability_reason: "O2 extraction confirmed" # explanation if not viable
}
```

**Logic**:
- Scan inventory for all resources
- Categorize each as `:survival` (consumable) or `:expansion` (equipment)
- Group by tier
- **Call `assess_isru_capability()` to check viability**
- If not viable, flag and return with explanation
- Return both tiers separately (caller decides action)

#### Method: `self.assess_isru_capability(settlement)`

**Purpose**: Determine if settlement can support human life via ISRU

**Output**: Hash
```ruby
{
  viable: true/false,                    # false if missing critical capability
  o2_extraction: true,                   # has oxygen production unit
  water_extraction: true,                # has water/ice extraction
  power_source: true,                    # has RTG, solar, or reactor
  basic_capability: "O2 extraction confirmed",  # human-readable
  missing: ["water extraction"]          # what's needed for viability
}
```

**Logic**:
- Check settlement inventory for ISRU equipment:
  - O2 extraction (e.g., "Planetary Volatiles Extractor")
  - Water extraction (e.g., "Volatiles Extractor with ice processing")
  - Power source (RTG, solar panel, reactor)
- **O2 extraction is mandatory** for viability
- Mark as viable only if O2 + power present
- Suggest missing critical equipment in `missing` array

#### Method: `self.categorize_resource(resource_name)`

**Purpose**: Determine if resource is survival or expansion tier

**Output**: `:survival` or `:expansion`

**Logic**:
- **Survival tier**: oxygen, water, food, critical_consumable, life_support_fluid
- **Expansion tier**: everything else (equipment, components, construction materials)
- Configurable via settlement.operational_data['resource_categories'] if present

#### Method: `self.calculate_target_inventory(settlement, resource, tier = :survival)`

**Purpose**: Determine healthy inventory level by tier

**Input**:
- `settlement`, `resource`, `tier` (:survival or :expansion)

**Output**: Integer (target quantity) or nil if not determinable

**Logic**:
- Check settlement.operational_data for tier-specific targets:
  - `survival_targets`: { resource => qty } for critical items
  - `expansion_targets`: { resource => qty } for growth equipment
- Fall back to consumption_rates for consumables
- **Survival items always have stricter targets** (30-60 days stock)
- Expansion items can have lower targets (7-14 days)
- Return nil if no data available

---

### Service 2: `Logistics::ImportRequestGenerator` (UPDATED)

**File**: `app/services/logistics/import_request_generator.rb`

#### Method: `self.generate_import_requests(settlement, shortage_report)`

**Input**:
- `settlement` (Settlement::BaseSettlement)
- `shortage_report` (Hash from ShortageDetector.detect_shortages)

**Output**: Array of ImportRequest objects (created & persisted)

**Logic**:
1. If `shortage_report[:viable]` is false:
   - Log settlement as non-viable
   - Create ImportRequest for "Critical: ISRU capability missing"
   - Return with only survival tier processing
2. Process `survival_shortages` first:
   - Create ImportRequest for each
   - Set priority: `:critical` for shortages < 5% of target
3. Process `expansion_shortages` only if all survival shortages covered:
   - Create ImportRequest for each
   - Set priority: `:expansion`
4. For each request:
   - Use Phase 1 (CostAnalyzer) to get cost data
   - Use Phase 2 (ManifestGenerator) to create cargo manifest
   - Store reference to manifest
5. Return array of created requests

#### Model: `Logistics::ImportRequest` (ENHANCED)

**Attributes** (addition to existing):
```ruby
- tier (enum): :survival, :expansion
- priority (enum): :critical, :high, :normal, :low
- viability_gap (boolean): true if settlement is non-viable
- category (string): consumable, equipment, component
```

---

### Service 3: `Logistics::ISRUCapabilityManager` (NEW)

**File**: `app/services/logistics/isru_capability_manager.rb`

#### Method: `self.has_basic_isru?(settlement)`

**Purpose**: Quick check for survival capability

**Output**: Boolean

**Logic**:
- True if settlement has O2 extraction + power source
- False otherwise

#### Method: `self.missing_for_survival(settlement)`

**Purpose**: List what's needed to make site viable

**Output**: Array of strings (equipment names/types needed)

**Logic**:
- Check against assessment results
- Return missing critical equipment

---

### Integration: AI Manager

**Modify**: `app/services/ai_manager/service_coordinator.rb`

**Add Method**: `detect_and_request_imports(settlement)`

**Logic**:
1. Call `ShortageDetector.detect_shortages(settlement)`
2. If not viable:
   - Log: "Settlement non-viable: {reason}"
   - Create emergency ImportRequest for missing ISRU
   - Alert strategist (emit event or set flag)
   - Return early
3. Process survival_shortages first
4. If all survival_shortages handled, process expansion_shortages
5. Create manifests for all approved imports
6. Return summary

---

## Test Cases Required

**ShortageDetector**:
1. ✅ Detect survival shortage (consumable low)
2. ✅ Detect expansion shortage (equipment low)
3. ✅ Separate shortages by tier in output
4. ✅ Mark settlement as non-viable (missing O2)
5. ✅ Mark settlement as viable (has O2 + power)
6. ✅ Categorize resource correctly (survival vs expansion)
7. ✅ Calculate target from operational_data by tier

**ImportRequestGenerator**:
1. ✅ Create requests for survival shortages first
2. ✅ Create requests for expansion shortages only if survival secured
3. ✅ Set priority=critical for survival shortages < 5%
4. ✅ Flag non-viable settlement and create ISRU request
5. ✅ Generate manifest for each request
6. ✅ Handle tier prioritization

**ISRUCapabilityManager**:
1. ✅ Confirm basic ISRU present
2. ✅ Flag missing O2 extraction
3. ✅ Return missing equipment list

**Service Coordinator Integration**:
1. ✅ Process shortages in tier order
2. ✅ Stop early if non-viable
3. ✅ Return summary with tier breakdown

---

## Success Criteria

✅ ShortageDetector returns tiered structure (survival vs expansion)  
✅ ISRU capability assessment integrated into detection  
✅ ImportRequestGenerator respects tier priority  
✅ Non-viable settlements flagged early  
✅ Test suite: 10+ cases, all passing  
✅ No hardcoded thresholds (use operational_data)  
✅ Manifest creation tied to tier priority  

---

## Stop Conditions

✅ **RESOLVED (May 31, 2026)**:
- ✅ ISRU capability check logic: `planetary_volatiles_extractor` & `thermal_extraction_unit` are Tier 1 O2 units
- ✅ Settlement inventory: Uses base_units table with unit_type filtering
- ✅ operational_data structure: JSONB field on settlements; add nested survival_targets & expansion_targets (no migration needed)
- ✅ Phase 1/2 integration: CostAnalyzer & ManifestGenerator confirmed ready

### Previous Blockers (Now Resolved)
- **Blocker #1 — O2 Equipment Names**: Confirmed `planetary_volatiles_extractor` (PVE) and `thermal_extraction_unit` (TEU). Read from `Lookup::UnitLookupService`, don't hardcode.
- **Blocker #2 — Viability Gate Policy**: Option B implemented — import survival consumables only, deny expansion when ISRU missing, flag HIGH priority for O2 equipment.
- **Blocker #3 — operational_data Schema**: Confirmed JSONB structure; add `survival_targets` & `expansion_targets` as nested JSON (existing migration 20260113040336).  

---

## Dependencies

- ✅ Phase 1: `Settlements::CostAnalyzer` (ready)
- ✅ Phase 2: `Logistics::ManifestGenerator` (ready)
- Existing: Settlement.operational_data, Settlement.inventory
- Existing: Equipment/consumable categorization (may need alignment)

---

## EXECUTOR INSTRUCTIONS (QWEN3.5-9B - ALL BLOCKERS CLEARED)

**Your Role**: Implement the tier-based shortage detection system. All blockers cleared; you have canonical equipment names, viability policy, and data structures.

**What to do**:
1. **Review the Specification section above** (Services 1-3, Integration)
2. **Read blockers resolution** (see "Stop Conditions" section above)
3. **Implement in this order**:
   - Service 1: `Logistics::ShortageDetector` — detect_shortages, assess_isru_capability, categorize_resource, calculate_target_inventory
   - Service 2: `Logistics::ImportRequestGenerator` — generate_import_requests
   - Service 3: `Logistics::ISRUCapabilityManager` — has_basic_isru?, missing_for_survival
   - Integration: Add detect_and_request_imports to ServiceCoordinator
4. **Key Facts**:
   - O2 equipment: `planetary_volatiles_extractor`, `thermal_extraction_unit`, `lunar_oxygen_extractor`
   - Read from: `Lookup::UnitLookupService.find_unit(unit_type)`
   - Viability requires: O2 extraction + power source
   - When non-viable: Allow survival imports only, deny expansion, flag HIGH priority for O2 equipment
   - Tier targets stored in: `settlement.operational_data['survival_targets']` & `['expansion_targets']`
5. **Write RSpec tests** — 10+ cases per service
6. **Run full test suite** before marking complete

**Estimated Implementation Time**: 2-3 hours
- Service 1 (ShortageDetector): 45 min + 30 min tests
- Service 2 (ImportRequestGenerator): 40 min + 30 min tests  
- Service 3 (ISRUCapabilityManager): 20 min + 15 min tests
- Integration: 15 min + 20 min tests

**Do NOT**:
- ❌ Hardcode equipment names (read from Lookup service)
- ❌ Guess at operational_data schema (use nested JSON as documented)
- ❌ Skip RSpec test coverage

**CRITICAL RULES FOR QWEN3.5**

⚠️ **RULE 1: Use Lookup Service, Not Hardcoded Names**
- Equipment names live in JSON/database, retrieved via Lookup::UnitLookupService
- Code example: `Lookup::UnitLookupService.new.find_unit('planetary_volatiles_extractor')`

⚠️ **RULE 2: operational_data is JSONB, No Migration Needed**
- Read/write nested JSON directly: `settlement.update(operational_data: {...})`
- Migration already exists (20260113040336)

⚠️ **RULE 3: Viability Gate is Non-Negotiable**
- If O2 extraction missing: Return viable=false immediately
- Process survival tiers first; expansion only if survival secured
- Enforce at generation time (ImportRequestGenerator)

⚠️ **RULE 4: Test Coverage Required**
- 10+ RSpec cases minimum (see Test Cases Required section)
- All passing before handoff to verification

---

## ACCEPTANCE CRITERIA (How STRATEGIST Will Know It's Done)

✅ **All services implemented** and tested:
- `Logistics::ShortageDetector` with all 4 methods
- `Logistics::ImportRequestGenerator` with manifest creation
- `Logistics::ISRUCapabilityManager` with viability checks
- `ServiceCoordinator#detect_and_request_imports` integration

✅ **RSpec suite passes**:
- 10+ test cases across all services
- Tier separation verified (survival vs expansion)
- Viability gate prevents non-viable expansion
- Equipment lookup uses Lookup service (not hardcoded)
- operational_data schema respected

✅ **No hardcoded values**:
- Equipment names read from Lookup service
- Thresholds/targets read from operational_data
- No magic numbers in service logic

---

## REPORTING FORMAT (When You're Done)

Reply with:

```
## Implementation Report: Luna Shortage Detection & Tiered Imports

### Services Implemented
- ✅ ShortageDetector (4 methods, X test cases)
- ✅ ImportRequestGenerator (1 method, X test cases)
- ✅ ISRUCapabilityManager (2 methods, X test cases)
- ✅ ServiceCoordinator integration (detect_and_request_imports)

### RSpec Test Results
All X tests passing:
- ShortageDetector: [X/X]
- ImportRequestGenerator: [X/X]
- ISRUCapabilityManager: [X/X]
- Integration: [X/X]

### Key Implementation Decisions
- Viability gate enforced at detection time (fail fast)
- Equipment lookup via Lookup::UnitLookupService
- operational_data structure: nested JSON, no migration
- Import priority: survival always > expansion if non-viable

### Files Modified
- app/services/logistics/shortage_detector.rb (new)
- app/services/logistics/import_request_generator.rb (updated)
- app/services/logistics/isru_capability_manager.rb (new)
- app/services/ai_manager/service_coordinator.rb (updated)
- spec/services/logistics/shortage_detector_spec.rb (new)
- spec/services/logistics/import_request_generator_spec.rb (updated)
- spec/services/logistics/isru_capability_manager_spec.rb (new)

### Any Issues Encountered
[Document any surprises, workarounds, or deviations from spec]

### Ready for Verification
Yes / No — [note if anything needs review before full testing]
```

---

## Notes

- **Viability is non-negotiable**: O2 extraction is the gate. No site without it should receive expansion equipment.
- **Tier separation is intentional**: Allows AI Manager to pause expansion during survival crises.
- **Strategic default**: ISRU-first, but consumables override (crew alive = priority).
- **Future integration**: This feeds into precursor mission planning (craft fitting & inventory allocation).



#### Method: `self.detect_shortages(settlement, threshold_percent = 20)`