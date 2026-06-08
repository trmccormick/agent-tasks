# Return Cargo Profit Optimization for Earth-Luna Trade Balance

```yaml
id: 2026-06-08-MEDIUM-FEATURE-RETURN-CARGO-PROFIT-OPTIMIZATION  
status: backlog
priority: MEDIUM
type: FEATURE
created: 2026-06-08
last_updated: 2026-06-08
system_domain: AI_MANAGER | LOGISTICS | ECONOMICS
mvp_alignment: PHASE_5_AUTONOMOUS_EXPANSION  
local_worker_safe: true
```

---

## Context & Strategic Purpose

**Extracted from:** `docs/agent/archive/backlog_april_2026/deprecated/implement_luna_base_buildup_test_case.md` (Task 1.4 — "Return Cargo Profit Optimization")

After Luna Phases complete the import dependency loop (Earth→Moon), logistics economics require return cargo to balance trade ledger and prevent empty AstroLift HLT flights back to Earth. This task implements outbound manifest generation for Luna exports, market price integration for real-time valuation, and profit optimization algorithms that maximize revenue while maintaining settlement operational needs.

**Why Phase 5+?** Only becomes relevant after:
- ✅ Single-system supply chain proven reliable (Luna Phases 1-3 complete)  
- ⏸️ Import dependency loop working reliably via ShortageDetector + ImportRequestGenerator integration
- 🔄 Settlement has excess production capacity beyond local consumption needs

**Critical Economics Note:** One-way GCC flow breaks logistics realism. If Luna only imports and never exports, AstroLift HLTs fly back empty — inefficient use of cargo capacity that should carry valuable return goods (He-3, regolith-derived materials in excess, scientific samples). AI Manager must know what Luna can export to balance trade ledger.

---

## Problem Statement

Current AI Manager logistics handles:
- Earth→Luna import manifests for survival shortages ✅ (Phase 2/3)  
- Single-direction resource flow from parent settlements ✅ (Cape Canaveral → Lunar Gateway)

**Missing:** Return cargo optimization that creates profitable outbound shipments:
- No export manifest generation logic exists yet — Luna only receives, never sends goods back to Earth
- Market price integration missing for real-time valuation of Luna products  
- Cargo load optimization absent for maximizing revenue per AstroLift flight
- Trade balance monitoring not implemented (import costs vs export revenues)

**Result:** Empty return flights waste cargo capacity and break economic simulation realism. GCC flows one direction without circulation through NPC-to-NPC trade settlements.

---

## Current State Analysis

### Existing Code (Read Before Starting)
| File | Purpose | Relevance to This Task |
|------|---------|------------------------|  
| `galaxy_game/app/services/logistics/manifest_generator.rb` | Creates Earth→Luna import manifests | Foundation for return cargo — needs extension to handle outbound Luna→Earth shipments with different optimization criteria (profit vs survival) |
| `galaxy_game/app/services/settlements/cost_analyzer.rb` | Compares local production vs import costs | Provides cost data but doesn't include market prices or export revenue calculations yet  
| `galaxy_game/app/models/logistics/contract.rb` | Tracks transport contracts between settlements | May need extension to track return cargo profitability metrics and trade balance per contract pair

### Reference Documentation — Read First
- Original source: `docs/agent/archive/backlog_april_2026/deprecated/implement_luna_base_buildup_test_case.md#task-14-return-cargo-profit-optimization`  
- Market economics baseline: `docs/market/economic_baseline.md` (if exists) — NPC economic behavior patterns and trade mechanics
- Active Luna Phase 3 task for context on current import flow implementation

---

## Implementation Scope  

### In Scope (Phase 5+)

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
    # Return ranked list by profit potential per kg transported via wormhole/HLT  
  end
end
```

**Sub-Task B: Market Price Integration System**  
Real-time valuation of Luna products for trade balance calculations:
```ruby
class Economics::MarketPriceService
  
  def self.get_current_market_price(resource_type, settlement_context)
    # Return base price adjusted by supply/demand factors at destination market (Earth vs other settlements)
    # Factor in resource rarity and strategic value modifiers  
  end
  
  def self.calculate_trade_balance(import_manifests, export_manifests)
    # Sum total import costs paid to AstroLift/logistics providers
    # Subtract total export revenues received from Earth buyers/consortiums
    # Return net GCC flow direction (positive = settlement earning more than spending on logistics)  
  end
end
```

**Sub-Task C: Cargo Load Optimization Algorithm**  
Maximize revenue per flight while respecting physical constraints and operational needs:
```ruby
# Optimize cargo allocation considering weight limits, volume capacity, resource priorities
def self.optimize_cargo_load(available_resources, manifest_capacity_kg, manifest_volume_m3)
  # Linear programming or greedy algorithm to maximize total_value = Σ(quantity_i × market_price_i)  
  # Subject to constraints:
  #   - Total weight ≤ AstroLift cargo capacity (e.g., 50 tons per flight for HLT class vessels)
  #   - Total volume ≤ available container space in manifest bay area
  #   - Each resource quantity ≥ minimum_shipment_threshold (avoid tiny shipments that waste slot allocation)  
  #   - Settlement retains safety buffer above target reserves after export shipment dispatched
end
```

### Out of Scope (Future Tasks or Different Domains)  
- TerraGen consortium formation detection — shared resource pools change optimal production distribution when corporations collaborate across systems (Phase 6+)
- Multi-system trade routing via wormhole network — Luna→Earth only for now, not Venus/Titan skimmer logistics yet
- Player mission offer generation based on export opportunities detected by this service (Act 2+ player-facing feature)

---

## Technical Requirements  

### Return Cargo Profit Optimization Architecture

**Step 1: Extend Manifest Generator to Handle Outbound Shipments**  
Current `ManifestGenerator.create_manifest(source, destination, items)` assumes Earth→Luna imports. Add export-specific logic:
```ruby
# New method alongside existing import manifest generation
def self.create_export_manifest(source_settlement, destination_settlement, available_capacity)
  # Identify surplus resources at source settlement (beyond local consumption needs + safety buffer)  
  # Rank by profit potential per kg transported via AstroLift HLT class vessels
  # Allocate cargo slots to maximize total revenue while respecting weight/volume constraints
  
  items = ExportManifestGenerator.optimize_cargo_load(available_resources, available_capacity)
  
  Logistics::Manifest.create!(
    source_settlement: source_settlement,
    destination_settlement: destination_settlement, 
    manifest_type: :export, # Distinguish from import manifests for tracking purposes
    total_weight_kg: items.sum { |item| item[:weight] },
    estimated_revenue_gcc: calculate_total_value(items),
    status: :pending_approval # May need human review before dispatch in early game phases  
  )
end
```

**Step 2: Implement Market Price Service Methods**  
System-wide pricing to enable trade balance calculations and profit optimization decisions:
- Base prices per resource type (He-3 premium, regolith-derived materials commodity rates)
- Supply/demand modifiers based on destination settlement current stock levels vs targets
- Strategic value multipliers for rare resources or mission-critical items

**Step 3: Create Trade Balance Monitoring Dashboard Data Service**  
Track import costs vs export revenues to inform AI Manager strategic decisions about production scaling:
```ruby
def self.get_trade_balance_report(settlement, time_window_days = 90)
  # Query Logistics::Manifest records filtered by settlement as source or destination
  # Sum total GCC spent on imports (paid to AstroLift/logistics providers for inbound cargo)  
  # Subtract total GCC earned from exports (received from Earth buyers/consortiums for outbound goods)
  # Return net flow direction and magnitude with trend analysis over time window
  
  {
    settlement_name: settlement.name,
    period_start_date:, 
    period_end_date:,
    total_import_costs_gcc:,  
    total_export_revenues_gcc:,
    net_trade_balance_gcc:, # Positive = profitable exports exceed import costs; negative = deficit requiring GCC injection from LDC anchor activities or other revenue sources
    trade_ratio: (export_revenues / import_costs), # >1.0 means settlement earning more than spending on logistics  
    top_export_resources: [...], # Ranked by total value shipped during period
    recommendations: [...] # AI Manager decision support for production scaling adjustments based on profitability trends
  }
end
```

---

## Files to Create/Modify  

### Primary Files (Create New / Edit Existing)  
| File | Purpose | Key Changes |
|------|---------|-------------|
| `galaxy_game/app/services/logistics/export_manifest_generator.rb` (new) | Outbound manifest generation logic for Luna→Earth shipments | Surplus identification, cargo optimization algorithms, profit-maximizing allocation methods |
| `galaxy_game/app/services/economics/market_price_service.rb` (new) | Real-time pricing and trade balance calculations | Market price lookups with supply/demand modifiers, revenue projections per resource type  
| `galaxy_game/spec/services/logistics/export_manifest_generator_spec.rb` (new) | Test new behavior | Specs for surplus identification accuracy, cargo optimization correctness under constraints
| `galaxy_game/app/models/logistics/manifest.rb` (edit if needed) | Add manifest_type enum and revenue tracking fields | Distinguish :import vs :export manifests; add estimated_revenue_gcc field for outbound shipments

### Reference Files (Read Only)  
| File | Why You Need It |
|------|-----------------|
| `galaxy_game/app/services/logistics/manifest_generator.rb` | Current import manifest pattern to extend for export logic — understand existing constraints and validation rules before adding new methods  
| Original source: `docs/agent/archive/backlog_april_2026/deprecated/implement_luna_base_buildup_test_case.md#task-14-return-cargo-profit-optimization` | Historical context on April 2026 architectural thinking about Luna supply chain economics

### Migration Needed?  
- [ ] **Conditional** — Only if adding new fields to `logistics_manifests` table (manifest_type enum, estimated_revenue_gcc decimal). Check existing schema first; may already have these from Phase 2/3 work. If not present:
```ruby
# Example migration structure if needed
class AddExportTrackingToLogisticsManifests < ActiveRecord::Migration[7.0]
  def change  
    add_column :logistics_manifests, :manifest_type, :enum, values: [:import, :export], default: :import, null: false
    add_column :logistics_manifests, :estimated_revenue_gcc, :decimal, precision: 12, scale: 2 # For export manifests only  
    add_index :logistics_manifests, :manifest_type # Query optimization for trade balance reports filtering by direction
  end
end
```

---

## Acceptance Criteria  

- [ ] `ExportManifestGenerator.generate_return_manifest` creates valid outbound Luna→Earth shipments with profit-maximizing cargo allocation  
- [ ] Market price service returns realistic valuations per resource type (He-3 premium, regolith commodities at appropriate rates)  
- [ ] Cargo load optimization respects weight/volume constraints while maximizing total revenue within available AstroLift capacity
- [ ] Trade balance monitoring correctly calculates net GCC flow direction and magnitude over configurable time windows  
- [ ] All new behavior covered by focused RSpec specs (factories for export manifests, market price service tests)
- [ ] No regressions in existing Luna Phase 3 Earth→Moon import request functionality  

---

## Testing Requirements  

### Targeted Specs to Add  
1. `generate_return_manifest` correctly identifies surplus resources beyond local consumption needs + safety buffer thresholds  
2. Cargo optimization algorithm respects weight limits (e.g., ≤50 tons per AstroLift HLT flight) and volume constraints while maximizing revenue
3. Market price service returns appropriate valuations with supply/demand modifiers applied based on destination settlement stock levels vs targets  
4. Trade balance report accurately sums import costs paid minus export revenues earned over specified time window, returning correct net flow direction (positive/negative GCC)  
5. Export manifest creation doesn't break existing import manifest generation logic or Phase 3 shortage detection → import request flow

### RSpec Command Pattern  
```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/logistics/export_manifest_generator_spec.rb 2>&1 | tail -30'
```

---

## Stop Conditions — Escalate Immediately If:  

- Market price data not available in existing schema or JSON operational files (may need investigation task first to define pricing structure)  
- Manifest model missing required fields for export tracking and revenue calculations that weren't anticipated during Phase 2/3 implementation  
- Fix requires changing more files than specified above (especially core logistics layer beyond ExportManifestGenerator + MarketPriceService scope)
- Architectural decision needed about cargo optimization algorithm choice or market price calculation weights

---

## Dependencies  

**Blocked by:** 
- Luna Phases 1-3 complete and proven reliable — import dependency loop must work before return cargo becomes relevant (can't optimize exports if imports broken)  
- Settlement operational_data includes production rates vs consumption targets for surplus identification logic to function correctly

**Blocks:** Multi-system trade routing via wormhole network, TerraGen consortium formation detection with shared resource pools  

**Related Tasks:**
- Original source: `docs/agent/archive/backlog_april_2026/deprecated/implement_luna_base_buildup_test_case.md#task-14-return-cargo-profit-optimization`  
- Active Luna Phase 3 (import dependency foundation — must complete before return cargo optimization relevant)

---

## Future Seams (Document in Code, Don't Implement)  

Add TODO comments for Phase 5+ enhancements:
```ruby
# TODO PHASE 6+: Integrate with TerraGen consortium formation detection — shared resource pools change optimal export allocation when corporations collaborate across systems on joint production targets  
# TODO POST-MVP: Multi-system trade routing via wormhole network (current model assumes Luna→Earth only; future expansion needs Venus/Titan skimmer logistics integration for exotic gas exports)
# TODO FUTURE: Player mission offer generation based on export opportunities detected by this service — Act 2+ player-facing feature where players can bid on excess production capacity or provide transport services in exchange for cut of profits (separate task needed when that scope becomes active priority)  
```

---

## Completion Report Template  

*Fill in after implementation:*

**Completed by:** [agent name]  
**Completion date:** YYYY-MM-DD  
**Final test result:** X examples, Y failures  

### What was changed  
- `[file]` — [description of change including any modifications to existing logistics layer services or manifest model schema updates if migration required]

### Issues discovered
[Any problems found during implementation that weren't in original task file assumptions about market pricing structure availability or settlement operational_data completeness for surplus identification logic]

### Follow-up tasks needed  
[Any new backlog items identified — do not create files, just list here such as TerraGen consortium integration requirements discovered during testing of export allocation algorithms across multiple settlements sharing production capacity]

### Lessons learned  
[What worked well for cargo optimization under constraints (e.g., greedy vs linear programming approach performance), what didn't (market price data gaps requiring fallback heuristics), and recommendations for future trade balance or logistics coordination tasks building on this foundation]  

---

## Notes for Future Reviewers  

This task extracted from April 2026 backlog item `implement_luna_base_buildup_test_case.md` which was over-scoped (mixed Luna MVP work with Phase 5+ export economics). The original document correctly identified return cargo optimization as critical to logistics realism — one-way GCC flow and empty AstroLift HLT flights back from Moon break economic simulation credibility.

**Why This Matters:**
- Realistic trade requires bidirectional resource flows, not just imports  
- Empty return flights waste 50% of available cargo capacity per round-trip mission (AstroLift flies Earth→Moon loaded with survival supplies; should fly Moon→Earth carrying He-3 and excess regolith products)
- GCC circulation through NPC-to-NPC trade settlements requires export revenues to balance import costs — otherwise LDC anchor must continuously inject currency without organic economic activity generating returns

**Extraction sequence from original April 2026 backlog item:**  
1. ✅ Import Request System (Task 1.2) → Already implemented in Luna Phase 3 active work
2. ✅ Manifest Generation for Earth→Luna imports (Task 1.3) → Completed in Luna Phase 2 via `Logistics::ManifestGenerator.create_manifest` integration into AI Manager decision loop  
3. ⏸️ Return Cargo Profit Optimization (this task — Task 1.4, extracted now as independent Phase 5+ backlog item ready for implementation after import dependency proven reliable)

**Remaining from original April 2026 document:** Infrastructure Expansion Triggers (Task 1.6) already covered by Foothold Establishment Service Design we created earlier in this session — no additional extraction needed there.

---
