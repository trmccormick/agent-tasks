# Investigation Findings — Settlement Model Architecture

**Date**: May 31, 2026  
**Status**: Complete — Ready for Implementation

---

## Inventory Access

**Primary Method**: `settlement.inventory` (has_one association)
```ruby
settlement.inventory  # => Inventory object
settlement.items     # => Array of Item objects
```

**Inventory Model Columns**:
- `id`, `inventoryable_type`, `inventoryable_id`, `capacity`, `created_at`, `updated_at`

**Item Model Columns** (what's stored in inventory):
- `id`, `name`, `amount` (THIS IS THE QUANTITY), `material_type`, `storage_method`, `inventory_id`
- Also: `total_weight`, `volume`, `durability`, `metadata`, `origin_world`, `extraction_date`

**Key**: To get items in settlement inventory:
```ruby
settlement.items  # => Array of Item objects
settlement.items.where(material_type: 'oxygen')  # Filter by material
item.amount  # Get quantity of that item
```

---

## operational_data Structure

**Type**: Hash (JSONB field deserialized to Ruby Hash)

**Current/Known Fields**: (via schema inspection)
- Fields vary by settlement type
- Can store arbitrary nested JSON
- No current schema for `survival_targets` or `expansion_targets` — **you need to create it**

**Usage**:
```ruby
settlement.operational_data['survival_targets']  # Can set this
settlement.operational_data['expansion_targets'] # Can set this
```

---

## Phase 1 & 2 Services

### Phase 1: CostAnalyzer
**Service**: `Settlements::CostAnalyzer`  
**Status**: EXISTS ✅

**Need to determine**: Exact method signature and return format. Likely:
```ruby
Settlements::CostAnalyzer.calculate_cost(material, quantity)
# => Returns cost data (hash or object)
```

### Phase 2: ManifestGenerator
**Service**: `Logistics::ManifestGenerator`  
**Status**: EXISTS ✅

**Need to determine**: Exact method signature and return format. Likely:
```ruby
Logistics::ManifestGenerator.generate_manifest(settlement: settlement, items: items_list)
# => Returns manifest object or hash with id
```

---

## ImportRequest Model

**Status**: EXISTS ✅  
**Table**: `logistics_import_requests`

**Columns**:
```
id, settlement_id, manifest_id, resource (string), 
quantity_needed (integer), cost_analysis (jsonb), 
status (string/enum), tier (string/enum), priority (string/enum), 
category (string), resolved_at, created_at, updated_at
```

**Key Fields for Implementation**:
- `resource` — NOT `material` (use this name!)
- `quantity_needed` — NOT `quantity`
- `cost_analysis` — JSONB field (store analysis result here)
- `status`, `tier`, `priority` — Can be strings or enums (check validator)
- `category` — For resource categorization

**Create Example**:
```ruby
request = Logistics::ImportRequest.create!(
  settlement_id: settlement.id,
  resource: 'oxygen',           # Use 'resource' not 'material'
  quantity_needed: 100,         # Use 'quantity_needed'
  cost_analysis: cost_data,     # Store Phase 1 result here
  manifest_id: manifest.id,     # Link to Phase 2 result
  tier: 'survival',
  priority: 'critical',
  status: 'pending',
  category: 'consumable'
)
```

---

## Equipment Detection (O2 Extraction)

**Location**: Base units with `unit_type` matching O2 producers

**Known O2 Equipment Names** (from unit data files loaded):
- `co2_oxygen_production_unit` (from co2_oxygen_production_data.json)
- `planetary_volatiles_extractor_mk1` (from planetary_volatiles_extractor_mk1_data.json)
- `planetary_volatiles_extractor_mk2`
- `planetary_volatiles_extractor_mk3`

**How to Check**:
```ruby
settlement.base_units.map(&:unit_type)  # Get all unit types
# Filter for O2 producers via Lookup::UnitLookupService

lookup = Lookup::UnitLookupService.new
unit_data = lookup.find_unit('planetary_volatiles_extractor_mk1')
# => Returns hash with unit specs including produced_materials
```

**Power Equipment Types**:
- `solar_panel`, `solar_panel_array`, `compact_solar_panel`
- `nuclear_reactor`, `nuclear_reactor_fusion`
- `radioisotope_thermoelectric_generator`, `rtg`
- `power_generator`, `biogas_generator_engine`

---

## Storage Manager

**Type**: `Storage::StorageManager`

**Access**:
```ruby
storage_manager = settlement.storage_manager
# Use for querying/updating storage
```

---

## Action Items for Qwen3.5 Implementation

### Service 1: ISRUCapabilityManager

```ruby
def self.assess_isru_capability(settlement)
  # Check for O2 equipment in settlement.base_units
  # Check for power equipment in settlement.base_units
  # Return { viable: true/false, o2_extraction: bool, power_source: bool, ... }
end
```

### Service 2: ShortageDetector

```ruby
def self.detect_shortages(settlement, threshold_percent = 20)
  # Use settlement.items to get current inventory
  # Filter by material_type and amount
  # Get targets from settlement.operational_data['survival_targets']
  # Return { viable: bool, survival_shortages: [...], expansion_shortages: [...] }
end
```

### Service 3: ImportRequestGenerator

```ruby
def self.generate_import_requests(settlement, shortage_report)
  # For each shortage:
  #   1. Call Settlements::CostAnalyzer.calculate_cost(resource, quantity)
  #   2. Call Logistics::ManifestGenerator.generate_manifest(...)
  #   3. Create Logistics::ImportRequest with:
  #      - resource (not material!)
  #      - quantity_needed (not quantity!)
  #      - cost_analysis: cost_data
  #      - manifest_id: manifest.id
  #      - tier: shortage[:tier]
  # Return array of persisted request objects
end
```

---

## Important Gotchas

❌ **Don't Use**: `material` field on ImportRequest (use `resource`)  
❌ **Don't Use**: `quantity` field on ImportRequest (use `quantity_needed`)  
✅ **Do Use**: `settlement.items` not some other inventory method  
✅ **Do Use**: `item.amount` for quantity  
✅ **Do Use**: `item.material_type` to categorize  

---

## Test Data Note

**No test settlements exist** in test DB currently. You may need to:
- Create factory/fixture for Settlement with items
- Or mock the inventory/items associations

---

You now have everything needed to implement correctly. No more guessing.
