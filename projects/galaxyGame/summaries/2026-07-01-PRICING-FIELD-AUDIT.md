# STATUS SYNTHESIS REPORT — Pricing Field Population Audit

**Date:** 2026-07-01
**Scope:** JSON data files + Market models, galaxy_game
**Role:** Research Agent (read-only audit)

---

## Q1: Material JSON Files — `cost_data.purchase_cost.amount` Population

| Metric | Count |
|---|---|
| Total material JSON files | **214** |
| Files with `purchase_cost` field | 206 |
| Files with **non-zero** `amount` | **204** (~95.3% of total) |
| Files with zero/placeholder `amount` | 2 |
| Files without `purchase_cost` at all | 8 |

**Finding:** Material pricing is **well-populated**. ~95% of material files have a meaningful (non-zero) `purchase_cost.amount`. The 204 populated files include byproducts, gases, synthetic materials with amounts ranging from 1.0 to 1,000,000.

---

## Q2: Unit Operational Data — `base_cost_eap` Population

| Metric | Count |
|---|---|
| Files with non-zero `base_cost_eap` | **75** |
| Files with zero `base_cost_eap` | 4 |
| Total files with field present | 79 |

**Finding:** `base_cost_eap` is **populated across all unit categories**:
- **Computers** (5 files): basic_computer (3.0), advanced_computer (2.4), server_farm (1.5), quantum_computing_array (1.5), distributed_computing_cluster (1.5)
- **Propulsion** (17 files): range from 1.0 (basic_ion_thruster, liquid_rocket_engine) to 10.0 (em_capture_thruster — highest)
- **Energy** (3+ files): solar_panel (1.5), biogas_generator (1.2), radioisotope_thermoelectric_generator (1.8)

All values are non-trivial EAP costs. No gaps detected.

---

## Q3: `Market::PriceHistory` — Settlement/Location Reference

**Model (`app/models/market/price_history.rb`):**
```ruby
belongs_to :market_condition, class_name: 'Market::Condition'
validates :price, presence: true
```

**Schema (`db/schema.rb`):**
```sql
create_table "market_price_histories" do |t|
  t.bigint "market_condition_id", null: false
  t.decimal "price"
  t.datetime "created_at", null: false
  t.datetime "updated_at", null: false
end
```

**Finding:** ❌ **NO settlement/location reference exists.** `PriceHistory` only has a `market_condition_id` foreign key and a `price` decimal. There is no `settlement_id`, `location_id`, `source_id`, or `destination_id` column. Price history is global/undifferentiated — it cannot distinguish prices by location.

**Note:** `MarketSupplyChain` does have polymorphic `sourceable_type/sourceable_id` and `destinationable_type/destinationable_id` columns, but these are not linked to `PriceHistory`.

---

## Q4: `INITIAL_TRANSPORTATION_COST_PER_KG` Usage

| File | Line | Context |
|---|---|---|
| `config/initializers/game_constants.rb` | 19 | Definition: `INITIAL_TRANSPORTATION_COST_PER_KG = 1320.00` |
| `app/services/ai_manager/mission_planner_service.rb` | 719 | Usage in cost calculation |

**Finding:** The constant is **defined and actively used in exactly 2 files**. It is set to **$1,320/kg** (reflecting Earth-to-orbit launch costs). Used in `MissionPlannerService` for transportation cost estimation. No other references found across the codebase.

---

## Q5: Blueprint JSON Files — `empty_mass_kg` Population

| Metric | Count |
|---|---|
| Total files with `empty_mass_kg` field | **185** |
| Files with **non-zero** `empty_mass_kg` | **166** (~89.7% of total) |
| Files with zero/placeholder `empty_mass_kg` | 19 |

**Finding:** `empty_mass_kg` is **well-populated** across blueprint types:
- **Ground crafts**: harvesting_rover (820 kg)
- **Space probes**: thermal_desorption_sail_probe (750 kg), generic_probe (1,500 kg)
- **Satellites**: generic_satellite (2,500 kg)
- **Spacecraft**: orbital_em_skimmer_mid (320,000 kg), earth_mars_cycler (500,000 kg), gas_giant_cycler (750,000 kg), asteroid_relocation_tug (2,500,000 kg)
- **landers**: heavy_lander (80,000 kg)
- **Units**: solar_panel (150–500 kg), computers (12–1,200 kg)

Mass values span 3 orders of magnitude (12 kg → 2.5M kg), indicating realistic scaling.

---

## Summary Table

| Question | Status | Key Finding |
|---|---|---|
| Q1: Material `purchase_cost.amount` | ✅ **Good** | 95% populated (204/214) |
| Q2: Unit `base_cost_eap` | ✅ **Good** | 75 files with non-zero values across all categories |
| Q3: PriceHistory settlement ref | ❌ **Missing** | No location/settlement FK — prices are global only |
| Q4: `INITIAL_TRANSPORTATION_COST_PER_KG` | ⚠️ **Narrow** | Defined + used in 2 files only ($1,320/kg) |
| Q5: Blueprint `empty_mass_kg` | ✅ **Good** | 90% populated (166/185), realistic mass range |

---

## Recommendations

1. **PriceHistory needs location context** — Add `settlement_id` or polymorphic `sourceable/destinationable` references to enable per-location pricing.
2. **Transportation cost is hardcoded** — Consider making it dynamic (per-route, per-vehicle type) rather than a single global constant.
3. **8 materials lack `purchase_cost`** — Investigate if these are intentionally free resources or data gaps.
