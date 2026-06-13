# Return Cargo Profit Optimization for Earth-Luna Trade Balance

```yaml
id: 2026-06-08-MEDIUM-FEATURE-RETURN-CARGO-PROFIT-OPTIMIZATION
status: active
priority: HIGH
type: FEATURE
created: 2026-06-08
last_updated: 2026-06-13
system_domain: AI_MANAGER | LOGISTICS | ECONOMICS
mvp_alignment: PHASE_4_LUNA_MVP
local_worker_safe: true
```

---

## Context & Strategic Purpose

**Extracted from:** `docs/agent/archive/backlog_april_2026/deprecated/implement_luna_base_buildup_test_case.md` (Task 1.4 — "Return Cargo Profit Optimization")

After Luna Phases complete the import dependency loop (Earth→Moon), logistics economics require
return cargo to balance trade ledger and prevent empty AstroLift HLT flights back to Earth. This
task implements outbound manifest generation for Luna exports, market price integration for
real-time valuation, and profit optimization algorithms that maximize revenue while maintaining
settlement operational needs.

**Why Phase 4 (Active Backlog)?** As soon as HLT tankers begin cycling Earth→Luna→Earth, the
return leg carries cargo. Optimizing that return revenue directly affects LDC's GCC balance
during the CH4 bridge period — before Titan arrives, every GCC earned on return cargo offsets
Earth methane imports. This is not a future enhancement, it is an immediate operational concern
once Luna operations begin.

**Phase placement note (updated 2026-06-09):** Originally tagged PHASE_5_AUTONOMOUS_EXPANSION
and MEDIUM priority. Corrected to PHASE_4_LUNA_MVP and HIGH priority. Return cargo optimization
is active from the first HLT rotation, not a post-simulation feature. Priority raised because
LDC GCC balance during bridge period depends on it.

**Critical Economics Note:** One-way GCC flow breaks logistics realism. If Luna only imports and
never exports, AstroLift HLTs fly back empty — inefficient use of cargo capacity that should
carry valuable return goods (He-3, regolith-derived materials in excess, scientific samples).
AI Manager must know what Luna can export to balance trade ledger.

---

## Market Infrastructure — Already Exists (Read Before Starting)

**Important:** The market layer is not greenfield. Significant infrastructure was built in
February 2026 and is live in the current schema. Do not recreate what already exists.

| Table | Purpose | Key Columns |
|-------|---------|-------------|
| `market_price_histories` | Price tracking over time | `market_condition_id`, `price` |
| `market_settings` | Global market configuration | `transportation_cost_per_kg` ← use this |
| `market_supply_chains` | Polymorphic source/destination trade routes | `sourceable`, `destinationable` |

Also read before starting:
- `docs/market/economic_baseline.md` — NPC economic behavior patterns and trade mechanics
- Existing market-related services/models in `app/services/economics/` or `app/models/market/`

**Verify `market_settings.transportation_cost_per_kg` is seeded before running any tests.**
If not seeded, create a seed entry — this is the transport cost baseline for Luna pricing.

---

## Market Price Bootstrap

**Do not block on live simulation data.** No running simulation exists yet — pricing must be
bootstrapped from static sources. Use this hierarchy:

1. **Blueprint EAP data** — load as price ceiling per resource type (already defined in
   blueprint/recipe data)
2. **Earth spot price + `market_settings.transportation_cost_per_kg`** — calculable now,
   use as Luna market floor
3. **Supply/demand modifiers** — emergent from simulation once running; add as future seam

`MarketPriceService` must function correctly against layers 1 and 2 without layer 3.
Layer 3 is a TODO comment only — do not implement.

---

## Problem Statement

Current AI Manager logistics handles:
- Earth→Luna import manifests for survival shortages ✅ (Phase 2/3)
- Single-direction resource flow from parent settlements ✅ (Cape Canaveral → Lunar Gateway)

**Missing:** Return cargo optimization that creates profitable outbound shipments:
- No export manifest generation logic exists yet — Luna only receives, never sends goods back
- Market price integration missing for real-time valuation of Luna products
- Cargo load optimization absent for maximizing revenue per AstroLift flight
- Trade balance monitoring not implemented (import costs vs export revenues)

**Result:** Empty return flights waste cargo capacity and break economic simulation realism.
GCC flows one direction without circulation through NPC-to-NPC trade settlements.

---

## Current State Analysis

### Existing Code (Read Before Starting)
| File | Purpose | Relevance to This Task |
|------|---------|------------------------|
| `galaxy_game/app/services/logistics/manifest_generator.rb` | Creates Earth→Luna import manifests | Foundation for return cargo — needs extension to handle outbound Luna→Earth shipments with different optimization criteria (profit vs survival) |
| `galaxy_game/app/services/settlements/cost_analyzer.rb` | Compares local production vs import costs | Provides cost data but doesn't include market prices or export revenue calculations yet |
| `galaxy_game/app/models/logistics/contract.rb` | Tracks transport contracts between settlements | May need extension to track return cargo profitability metrics and trade balance per contract pair |

### Reference Documentation — Read First
- `docs/market/economic_baseline.md` — NPC economic behavior and trade mechanics baseline
- Original source: `docs/agent/archive/backlog_april_2026/deprecated/implement_luna_base_buildup_test_case.md#task-14-return-cargo-profit-optimization`

---

## Implementation Scope

### In Scope

**Sub-Task A: Export Manifest Generation Service**
Create outbound manifests from Luna to Earth carrying excess production:
```ruby
class Logistics::ExportManifestGenerator

  def self.generate_return_manifest(source_settlement, destination_settlement)
    # Identify surplus resources at source settlement beyond local consumption needs
    # Calculate optimal cargo mix based on market prices and available capacity
    # Create manifest with profit-maximizing resource allocation per AstroLift flight constraints
  end

  def self.identify_exportable_surplus(settlement, current_manifest_capacity)
    # Query operational_data for production rates vs local consumption targets
    # Filter resources where (current_stock - target_reserve) > minimum_safety_buffer
    # Return ranked list by profit potential per kg transported via HLT
  end
end
```

**Sub-Task B: Market Price Service**
Real-time valuation of Luna products using existing market infrastructure:
```ruby
class Economics::MarketPriceService

  def self.get_current_market_price(resource_type, settlement_context)
    # Load EAP from blueprint data as price ceiling
    # Calculate Earth spot price + transportation_cost_per_kg from market_settings as floor
    # Return price within that range (no supply/demand modifiers yet — future seam)
  end

  def self.calculate_trade_balance(import_manifests, export_manifests)
    # Sum total import costs paid to AstroLift/logistics providers
    # Subtract total export revenues received from Earth buyers/consortiums
    # Return net GCC flow direction (positive = settlement earning more than spending)
  end
end
```

**Sub-Task C: Cargo Load Optimization Algorithm**
Maximize revenue per flight while respecting physical constraints and operational needs:
```ruby
def self.optimize_cargo_load(available_resources, manifest_capacity_kg, manifest_volume_m3)
  # Greedy algorithm to maximize total_value = Σ(quantity_i × market_price_i)
  # Subject to constraints:
  #   - Total weight ≤ AstroLift cargo capacity (50 tons per HLT flight)
  #   - Total volume ≤ available container space
  #   - Each resource quantity ≥ minimum_shipment_threshold
  #   - Settlement retains safety buffer above target reserves after export
end
```

### Out of Scope
- TerraGen consortium formation detection (Phase 6+)
- Multi-system trade routing via wormhole network — Luna→Earth only
- Player mission offer generation based on export opportunities (Act 2+)
- Supply/demand modifier implementation — future seam, TODO comment only

---

## Return Cargo Architecture

**Step 1: Extend Manifest Generator to Handle Outbound Shipments**
Current `ManifestGenerator.create_manifest(source, destination, items)` assumes Earth→Luna
imports. Add export-specific logic:
```ruby
def self.create_export_manifest(source_settlement, destination_settlement, available_capacity)
  items = ExportManifestGenerator.optimize_cargo_load(available_resources, available_capacity)

  Logistics::Manifest.create!(
    source_settlement: source_settlement,
    destination_settlement: destination_settlement,
    manifest_type: :export,
    total_weight_kg: items.sum { |item| item[:weight] },
    estimated_revenue_gcc: calculate_total_value(items),
    status: :pending_approval
  )
end
```

**Step 2: Implement Market Price Service Methods**
- Base prices per resource type using EAP ceiling + transport floor
- He-3 premium, regolith-derived materials at commodity rates
- Strategic value multipliers for rare/mission-critical items
- TODO comment for supply/demand modifiers — do not implement

**Step 3: Trade Balance Monitoring**
```ruby
def self.get_trade_balance_report(settlement, time_window_days = 90)
  {
    settlement_name: settlement.name,
    period_start_date:,
    period_end_date:,
    total_import_costs_gcc:,
    total_export_revenues_gcc:,
    net_trade_balance_gcc:,
    trade_ratio: (export_revenues / import_costs),
    top_export_resources: [...],
    recommendations: [...]
  }
end
```

---

## Files to Create/Modify

### Primary Files
| File | Purpose | Key Changes |
|------|---------|-------------|
| `galaxy_game/app/services/logistics/export_manifest_generator.rb` (new) | Outbound manifest generation | Surplus identification, cargo optimization, profit-maximizing allocation |
| `galaxy_game/app/services/economics/market_price_service.rb` (new) | Pricing and trade balance | EAP ceiling + transport floor pricing, revenue projections per resource type |
| `galaxy_game/app/models/logistics/manifest.rb` (edit if needed) | Add manifest_type enum + revenue tracking | Distinguish :import vs :export; add `estimated_revenue_gcc` field |
| `galaxy_game/spec/services/logistics/export_manifest_generator_spec.rb` (new) | Test new behavior | Surplus identification, cargo optimization under constraints |

### Reference Files (Read Only)
| File | Why You Need It |
|------|-----------------|
| `galaxy_game/app/services/logistics/manifest_generator.rb` | Existing import manifest pattern to extend |
| `docs/market/economic_baseline.md` | Trade mechanics and NPC economic behavior baseline |
| `galaxy_game/db/schema.rb` | Confirm market table columns before writing queries |

### Migration (Conditional)
Only if `manifest_type` enum and `estimated_revenue_gcc` not already on `logistics_manifests`.
Check schema first:
```ruby
class AddExportTrackingToLogisticsManifests < ActiveRecord::Migration[7.0]
  def change
    add_column :logistics_manifests, :manifest_type, :enum, values: [:import, :export], default: :import, null: false
    add_column :logistics_manifests, :estimated_revenue_gcc, :decimal, precision: 12, scale: 2
    add_index :logistics_manifests, :manifest_type
  end
end
```

---

## Acceptance Criteria

- [ ] `ExportManifestGenerator.generate_return_manifest` creates valid outbound Luna→Earth
      shipments with profit-maximizing cargo allocation
- [ ] `MarketPriceService` returns EAP-capped, transport-floored valuations per resource type
      without requiring live simulation data
- [ ] `market_settings.transportation_cost_per_kg` is read correctly and applied to Luna pricing
- [ ] Cargo load optimization respects weight/volume constraints (≤50 tons per HLT) while
      maximizing total revenue
- [ ] Trade balance monitoring correctly calculates net GCC flow direction and magnitude
- [ ] All new behavior covered by focused RSpec specs
- [ ] No regressions in existing Luna Phase 3 Earth→Moon import request functionality

---

## Testing Requirements

### RSpec (Unit Behavior Only)
1. `generate_return_manifest` correctly identifies surplus beyond safety buffer
2. Cargo optimization respects weight limits and maximizes revenue
3. `MarketPriceService` returns EAP-capped price correctly
4. `MarketPriceService` applies `transportation_cost_per_kg` from `market_settings`
5. Trade balance report correctly sums import costs minus export revenues
6. Export manifest creation does not break existing Phase 3 import flow

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/logistics/export_manifest_generator_spec.rb 2>&1 | tail -30'
```

### Production Validation (Required — RSpec Is Not Sufficient)
RSpec confirms unit behavior only. Market pricing correctness requires simulation integration
testing:

- Seed `market_settings.transportation_cost_per_kg` before any test run
- Run simulation loop with market service active
- Inspect `market_price_histories` records after settlement ticks — confirm prices are
  populating within expected EAP ceiling / transport floor range
- Confirm `market_supply_chains` records are created for Luna→Earth trade routes
- Flag pricing anomalies (zero prices, negative trade balance on He-3, etc.) for review

**Document production test results in completion report.** Do not mark task complete on
RSpec passing alone.

---

## Stop Conditions — Escalate Immediately If:

- Existing market services found that already partially implement `MarketPriceService` —
  extend don't duplicate
- `market_settings` table is empty (not seeded) — note in report, create seed, continue
- Fix requires changing more files than specified above
- Architectural decision needed about cargo optimization algorithm or market price weights
- Blueprint EAP data not accessible from service layer — flag location and stop

---

## Future Seams (Document in Code, Don't Implement)

```ruby
# TODO PHASE 6+: Supply/demand modifiers based on destination settlement stock levels
# TODO PHASE 6+: TerraGen consortium formation detection — shared resource pools change
#   optimal export allocation when corporations collaborate across systems
# TODO POST-MVP: Multi-system trade routing via wormhole network (Luna→Earth only for now)
# TODO FUTURE: Player mission offer generation based on export opportunities — Act 2+
```

---

## Dependencies

**Blocked by:**
- Luna Phases 1–4 complete ✅ (01919af8)
- `market_settings.transportation_cost_per_kg` seeded

**Blocks:**
- Multi-system trade routing via wormhole network
- TerraGen consortium formation detection

---

## Completion Report Template

*Fill in after implementation:*

**Completed by:** [agent name]
**Completion date:** YYYY-MM-DD
**Final RSpec result:** X examples, Y failures
**Production validation run:** yes/no

### What was changed
- `[file]` — [description]

### Market infrastructure findings
[What existing market services/models were found. What was reused vs created new.]

### Production test results
[market_price_histories populated correctly? transportation_cost_per_kg applied?
Any pricing anomalies observed?]

### Issues discovered
[Problems found during implementation not anticipated in task file]

### Follow-up tasks needed
[New backlog items identified — list only, do not create files]

### Lessons learned
[What worked, what didn't, recommendations for future trade/logistics tasks]

---

## Notes for Future Reviewers

This task extracted from April 2026 backlog item `implement_luna_base_buildup_test_case.md`
which was over-scoped (mixed Luna MVP work with Phase 5+ export economics). The original
document correctly identified return cargo optimization as critical to logistics realism —
one-way GCC flow and empty AstroLift HLT flights back from Moon break economic simulation
credibility.

**Extraction sequence from original April 2026 backlog item:**
1. ✅ Import Request System (Task 1.2) → Implemented in Luna Phase 3
2. ✅ Manifest Generation for Earth→Luna imports (Task 1.3) → Completed in Luna Phase 2
3. ⏸️ Return Cargo Profit Optimization (this task — Task 1.4)

**Remaining from original April 2026 document:** Infrastructure Expansion Triggers (Task 1.6)
already covered by Foothold Establishment Service Design — no additional extraction needed.
