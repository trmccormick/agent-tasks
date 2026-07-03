# Phase C: NpcPriceCalculator Deep Dive

**Date**: 2026-07-01  
**Purpose**: Examine `can_produce_locally?` and `calculate_earth_import_cost` methods in the service-based NpcPriceCalculator

---

## Lines 190–230: `can_produce_locally?` Method Body

```ruby
def can_produce_locally?(settlement, resource_name)
  return false unless settlement
  
  material_data = load_material_data(resource_name)
  return false unless material_data
  
  lunar_prod = material_data.dig('pricing', 'lunar_production')
  return false unless lunar_prod && lunar_prod['available']
  
  facility = lunar_prod['facility_required']
  return true if facility.blank?
  
  settlement.respond_to?(:has_facility?) && settlement.has_facility?(facility)
end
```

**Logic flow**:
1. Returns `false` if no settlement context
2. Loads material data via `MaterialGeneratorService.generate_material(resource_name)`
3. Checks `material_data['pricing']['lunar_production']['available']` — if false, local production not possible
4. If `facility_required` is blank, returns `true` (no facility needed)
5. Otherwise checks if settlement has the required facility

---

## `calculate_earth_import_cost` Method (Line 100–120)

```ruby
def calculate_earth_import_cost(settlement, resource_name)
  material_data = load_material_data(resource_name)
  return nil unless material_data
  
  # FIX: Fall back to 'luna' if location chain is incomplete for testing.
  destination = begin
    settlement&.location&.celestial_body&.name&.downcase
  rescue NoMethodError
    nil
  end
  destination ||= 'luna' # Use 'luna' as the hardcoded default fallback

  Tier1PriceModeler.new(
    material_data,
    destination: destination,
    source: 'earth'
  ).calculate_eap
rescue StandardError => e
  Rails.logger.error "Error calculating import cost for #{resource_name}: #{e.message}"
  nil
end
```

**Key observations**:
- Falls back to `'luna'` as destination if location chain is incomplete (testing artifact)
- Delegates to `Tier1PriceModeler.new(material_data, destination:, source: 'earth').calculate_eap`
- Returns `nil` on any error (graceful degradation)

---

## Tier1PriceModeler References in NpcPriceCalculator

```
113:        Tier1PriceModeler.new(
```

**Single reference at line 113** — the `calculate_earth_import_cost` method. The `Tier1PriceModeler` is the engine that computes EAP (Estimated Acquisition Price) from material data, destination, and source.

---

## Pricing Chain Summary

```
NpcPriceCalculator.calculate_bid(settlement, resource_name)
  → settlement_wants_resource?(settlement, resource_name)
    → storage capacity check
    → budget check
    → inventory excess check
  
  → cost_based_bid(settlement, resource_name)
    → calculate_import_cost(settlement, resource_name)
      → can_produce_locally?(settlement, resource_name)
        → load_material_data(resource_name)
          → MaterialGeneratorService.generate_material(resource_name)
        → material_data.dig('pricing', 'lunar_production', 'available')
      → calculate_earth_import_cost(settlement, resource_name)
        → Tier1PriceModeler.new(material_data, destination: 'luna', source: 'earth').calculate_eap
```

**Important**: The `cost_based_bid` applies a discount to the import cost (not markup). The actual bid price is typically **below** the full Earth import cost, making it more attractive for settlements to sell resources back.
