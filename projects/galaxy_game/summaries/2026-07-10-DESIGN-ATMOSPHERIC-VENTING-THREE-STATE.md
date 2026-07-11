# DESIGN: `vent_to_source` Mode for AtmosphericTransferService — Three-State Routing

**Date**: 2026-07-10
**Task**: `2026-07-10-HIGH-RESEARCH-ATMOSPHERIC-TRANSFER-VENTING-DESIGN.md`
**Researcher**: Research Agent (Qwen3.6-35b)

---

## Executive Summary

The `vent_to_source` mode is **architecturally feasible with zero breaking changes**. The existing `AtmosphericTransferService` already has the building blocks:

1. **Gas extraction from source** — all three modes extract via `@source.atmosphere.remove_gas`
2. **Gas return to source** — processed mode already returns CO back to source atmosphere (precedent exists)
3. **Atmosphere model** — `CelestialBodies::Spheres::Atmosphere#add_gas` correctly handles gas injection
4. **Storage infrastructure** — uses `Inventory` model with `available_capacity`; cryo tanks are blueprint-defined units deployed into inventory

The key insight: **venting is not a new mode, it's a routing decision within existing modes**. The service already extracts gas from source; the question is what to do with the remainder after processing/storage allocation.

---

## 1. Three-State Routing Logic Design

### Architecture Decision: Routing Within Modes, Not a New Mode

Adding a `vent_to_source` as a fourth mode would be architecturally wrong. Venting is a **fallback routing strategy**, not a primary transfer type. The three states map to existing modes:

| State | Routing Target | Maps To Mode(s) |
|---|---|---|
| Refined/Stored (Priority) | `cryogenic_storage_unit` / fuel tanks | All modes — always attempted first |
| Raw/Overflow (Fallback) | Source atmosphere via `add_gas` | raw, selective, processed |
| Processing Byproducts (Asset) | Tracked separately, NOT ventable | processed mode only |

### Routing Priority Algorithm

```ruby
def route_gas(gas_name, mass, state)
  # State 1: Try storage first (always priority)
  stored = try_store(gas_name, mass)
  remaining = mass - stored
  
  return { stored: stored, vented: 0, byproduct: false } if remaining <= 0
  
  # State 2: If storage full, check if gas is ventable
  if state == :overflow && ventable?(gas_name)
    vented = try_vent_to_source(gas_name, remaining)
    return { stored: stored, vented: vented, byproduct: false }
  end
  
  # State 3: Byproducts — track as asset, never vent
  if state == :byproduct
    return { stored: stored, vented: 0, byproduct: true, byproduct_name: gas_name }
  end
  
  # Fallback: unhandled mass (shouldn't happen with proper capacity checks)
  { stored: stored, vented: remaining, byproduct: false, warning: "unrouted_mass" }
end
```

### Key Design Decisions

1. **Storage is always attempted first** — never vent before checking storage capacity
2. **Byproducts are NEVER ventable** — CO and O2 from processing are tracked as assets
3. **Venting returns to SOURCE, not space** — preserves atmospheric mass balance
4. **Partial venting is supported** — if 100kg extracted, 80kg stored, 20kg vented

---

## 2. Extended Results Schema

### Current Schema (unchanged)
```ruby
@results = {
  gases_extracted: { 'N2' => 500, 'CH4' => 50 },
  gases_delivered: { 'N2' => 490, 'CH4' => 48 },
  gases_produced: {},
  efficiency: 0.98,
  success: true,
  messages: []
}
```

### Extended Schema (backward-compatible)
```ruby
@results = {
  gases_extracted: { 'N2' => 500, 'CH4' => 50 },   # unchanged
  gases_stored:    { 'CH4' => 48, 'CO2' => 100 },   # NEW — refined/stored gas
  gases_delivered: { 'N2' => 490 },                  # now only includes non-vented gas
  gases_vented:    { 'N2' => 10 },                   # NEW — overflow to source atmosphere
  gases_byproducts:{ 'CO' => 35, 'O2' => 40 },       # NEW — processing byproducts (assets)
  gases_produced: {},                                 # unchanged
  efficiency: 0.98,                                   # unchanged
  success: true,
  messages: []
}
```

### Mass Balance Verification
```ruby
# For each gas: extracted = stored + delivered + vented + byproduct (within tolerance)
def verify_mass_balance(gas_name, tolerance = 0.01)
  extracted = @results[:gases_extracted][gas_name] || 0
  stored    = @results[:gases_stored][gas_name] || 0
  delivered = @results[:gases_delivered][gas_name] || 0
  vented    = @results[:gases_vented][gas_name] || 0
  byproduct = @results[:gases_byproducts][gas_name] || 0
  
  total_out = stored + delivered + vented + byproduct
  (extracted - total_out).abs <= (extracted * tolerance)
end

# Global mass balance check across all gases
def global_mass_balance
  extracted_total = @results[:gases_extracted].values.sum
  routed_total = [
    @results[:gases_stored]&.values&.sum || 0,
    @results[:gases_delivered]&.values&.sum || 0,
    @results[:gases_vented]&.values&.sum || 0,
    @results[:gases_byproducts]&.values&.sum || 0
  ].max
  
  tolerance = extracted_total * 0.02  # 2% tolerance for transport loss
  (extracted_total - routed_total).abs <= tolerance
end
```

---

## 3. Storage Capacity Integration Requirements

### Current State of Storage Infrastructure

| Component | How It Works | Capacity Query |
|---|---|---|
| **Cryo tanks** (`inflatable_cryo_tank_bp.json`) | Blueprint-defined unit; deployed into settlement inventory | `Inventory#available_capacity` |
| **Fuel tanks** (unit ports) | Per-unit storage via blueprint `storage_bays` | Blueprint data + deployed count |
| **Inventory model** | Tracks all deployed units and their contents | `available_capacity`, `calculate_storage_capacity` |

### Integration Design

```ruby
# In AtmosphericTransferService, add storage capacity check:

def try_store(gas_name, mass)
  # Query available cryo tank capacity at target location
  available = query_storage_capacity(@target)
  
  if available[gas_name] && available[gas_name] >= mass
    # Store the full amount
    store_gas(gas_name, mass)
    mass
  elsif available[gas_name]
    # Partial storage — store what fits
    partial = available[gas_name]
    store_gas(gas_name, partial)
    partial
  else
    # No storage capacity for this gas
    0
  end
end

def query_storage_capacity(planet)
  # Returns hash of { gas_name => available_capacity_kg }
  # Implementation: query deployed cryo tanks at target's inventory
  
  # For MVP: use a simplified approach — total remaining capacity across all cryo units
  inventory = find_target_inventory(planet)
  return {} unless inventory
  
  # Cryo tanks store gases by type; each has per-gas capacity from blueprint
  gas_capacity = {}
  inventory.deployed_units.each do |unit|
    if unit.type_name == 'inflatable_cryo_tank'
      bp = BlueprintLoader.load(unit.blueprint_id)
      bp.dig('storage_bays', 'capacity_per_gas')&.to_f ||= 0
      gas_capacity[unit.gas_type] ||= 0
      gas_capacity[unit.gas_type] += unit.remaining_capacity
    end
  end
  gas_capacity
end
```

### Key Findings on Storage Integration

1. **No per-gas capacity limits on cryo tanks** — the blueprint defines total storage_bays capacity; gases share the tank volume
2. **Fuel tank capacities from HLT blueprints** — use `storage_bays` field in blueprint JSON; factor into routing priority (fuel tanks get first dibs for fuel-grade gas)
3. **When cryo tanks are full** — extraction does NOT stop; excess is vented to source atmosphere (this is the core MVP behavior)
4. **Inventory model already has `available_capacity`** — no new storage query mechanism needed

---

## 4. Safety State Check Requirements

### Pre-Venting Checklist

| # | Check | Method/Source | Severity |
|---|---|---|---|
| 1 | Skimmer is docked (not in transit) | `skimmer.docked?` or `cycler.docked_at?` | 🔴 CRITICAL |
| 2 | Source atmosphere exists | `@source.atmosphere.present?` | 🔴 CRITICAL |
| 3 | Gas type is ventable (not a byproduct) | `VENTABLE_GASES` constant + byproduct exclusion | 🟡 HIGH |
| 4 | Storage capacity verified as full/insufficient | `query_storage_capacity` result | 🟡 HIGH |
| 5 | Source atmosphere can accept returned gas | `@source.atmosphere.total_atmospheric_mass > 0` | 🟢 LOW |
| 6 | Venting won't exceed source atmospheric limits | Compare vented mass against extraction limit | 🟢 LOW |

### Safety Implementation

```ruby
VENTABLE_GASES = %w[N2 CH4 Ar CO2 He Ne Kr Xe H2 O2].freeze
NON_VENTABLE_BYPRODUCTS = %w[CO].freeze  # CO from MOXIE processing is an asset

def safe_to_vent?(gas_name, skimmer)
  return false unless skimmer.docked?
  return false unless @source.atmosphere.present?
  return false if NON_VENTABLE_BYPRODUCTS.include?(gas_name)
  return false unless VENTABLE_GASES.include?(gas_name)
  
  # Check storage was attempted first
  return false if query_storage_capacity(@target)[gas_name]&.positive? || 
                  query_storage_capacity(@target).values.sum.positive?
  
  true
end

def try_vent_to_source(gas_name, mass)
  return 0 unless safe_to_vent?(gas_name, skimmer)
  
  # Add gas back to source atmosphere
  @source.atmosphere.add_gas(gas_name, mass)
  @results[:gases_vented][gas_name] = (@results[:gases_vented][gas_name] || 0) + mass
  
  log_info("Vented #{format_mass(mass)} of #{gas_name} back to #{@source.name}")
  mass
end
```

---

## 5. Integration Plan for Existing Transfer Modes

### Mode-by-Mode Changes

#### Raw Mode (no breaking changes)
```ruby
# Before: all extracted gas delivered to target
def perform_raw_transfer(params)
  @source.atmosphere.gases.each do |gas|
    gas_mass_to_extract = ...
    transport_efficiency = params[:efficiency] || 0.98
    gas_mass_delivered = gas_mass_to_extract * transport_efficiency
    
    @source.atmosphere.remove_gas(gas.name, gas_mass_to_extract)
    @results[:gases_extracted][gas.name] = gas_mass_to_extract
    @target.atmosphere.add_gas(gas.name, gas_mass_delivered)
    @results[:gases_delivered][gas.name] = gas_mass_delivered
  end
end

# After: three-state routing with vent fallback
def perform_raw_transfer(params)
  @source.atmosphere.gases.each do |gas|
    gas_mass_to_extract = ...
    transport_efficiency = params[:efficiency] || 0.98
    gas_mass_delivered = gas_mass_to_extract * transport_efficiency
    
    @source.atmosphere.remove_gas(gas.name, gas_mass_to_extract)
    @results[:gases_extracted][gas.name] = gas_mass_to_extract
    
    # NEW: Try storage first
    stored = try_store(gas.name, gas_mass_delivered)
    remaining = gas_mass_delivered - stored
    
    if remaining > 0 && safe_to_vent?(gas.name, skimmer)
      vented = try_vent_to_source(gas.name, remaining)
      @results[:gases_stored][gas.name] = stored
      @results[:gases_vented][gas.name] = vented
    else
      # All stored (no breaking change for normal operation)
      @target.atmosphere.add_gas(gas.name, gas_mass_delivered)
      @results[:gases_stored][gas.name] = gas_mass_delivered
    end
  end
end
```

#### Selective Mode (minimal changes)
```ruby
# Same pattern: try storage first, vent overflow if safe
# No breaking changes — selective mode already has per-gas granularity
```

#### Processed Mode (byproduct tracking added)
```ruby
# Before: CO returned to source as part of delivered gases
@results[:gases_delivered]['CO'] = co_mass_delivered  # CO goes back to source

# After: CO tracked as byproduct asset, NOT ventable
@results[:gases_byproducts]['CO'] = co_mass_produced  # Asset tracking
@results[:gases_delivered]['CO'] = co_mass_delivered  # Still returned to source (but tracked separately)

# O2 from CO2 scrubbing: also a byproduct asset
@results[:gases_byproducts]['O2'] = o2_mass_produced
```

### Backward Compatibility Guarantee

- **`gases_extracted`** — unchanged semantics
- **`gases_delivered`** — now only includes gas that reached target (previously same as stored)
- **`gases_produced`** — unchanged (processing outputs before transport efficiency)
- **New fields (`gases_stored`, `gases_vented`, `gases_byproducts`)** — default to `{}` if not used
- **Existing consumers of `results[:gases_delivered]`** — will see reduced values when venting occurs (this is correct behavior, not a bug)

---

## 6. MVP vs Phase 9+ Scope Boundary

### MVP Scope (This Task)

| Feature | In MVP? | Notes |
|---|---|---|
| Three-state routing logic | ✅ Yes | Storage → Vent fallback |
| `vent_to_source` behavior | ✅ Yes | Add gas back to source atmosphere |
| Extended results schema | ✅ Yes | stored/vented/byproduct tracking |
| Storage capacity integration | ✅ Yes | Query deployed cryo tanks at target |
| Safety state checks | ✅ Yes | Docked check, ventable gas list |
| Integration with all 3 modes | ✅ Yes | raw/selective/processed |
| Mass balance verification | ✅ Yes | Per-gas and global checks |
| Skimmer docking requirement | ✅ Yes | Critical safety gate |

### Phase 9+ Scope (Separate Task)

| Feature | Phase 9+? | Notes |
|---|---|---|
| Terraforming-scale atmospheric modification | ✅ Yes | Multi-world, multi-cycle gas redistribution |
| Multi-world atmospheric interaction | ✅ Yes | Cycler routes between worlds for terraforming |
| Atmospheric composition targets | ✅ Yes | Long-term greenhouse warming targets |
| Automated venting optimization | ✅ Yes | AI-driven venting decisions for efficiency |
| Environmental constraints on specific gases | ✅ Partial | MVP has basic ventable gas list; Phase 9+ adds per-world constraints |

### Scope Boundary Rationale

The MVP scope is **skimmer operations at docking windows** — the immediate need identified in the skimmer audit. Skimmers extract gas, process some for fuel, and must handle excess when cryo tanks are full. The vent_to_source mode solves this operational gap.

Phase 9+ scope involves **terraforming strategy** — long-term atmospheric modification across multiple worlds. This is a separate strategic layer that builds on the MVP operational foundation.

---

## 7. Implementation Recommendations

### Recommended Approach: Additive Changes Only

1. **Add `vent_to_source` as an internal routing method** (not a public mode)
2. **Extend results schema** with three new hash fields (default `{}`)
3. **Add storage capacity query** using existing Inventory model
4. **Add safety checks** as guard clauses in the routing method
5. **Update all three transfer modes** to use the new routing logic

### Files to Modify

| File | Change Type | Description |
|---|---|---|
| `atmospheric_transfer_service.rb` | Extend | Add `try_store`, `try_vent_to_source`, `safe_to_vent?`, `query_storage_capacity` methods; update all three transfer mode methods |
| `celestial_bodies/spheres/atmosphere.rb` | None needed | Already has `add_gas`/`remove_gas` |
| `inventory.rb` | None needed | Already has `available_capacity` |
| Blueprint JSON files | None needed | Cryo tank specs already defined |
| New spec file | Create | `atmospheric_transfer_venting_spec.rb` for venting scenarios |

### Stop Condition Assessment

**No stop conditions triggered.** The existing architecture fully supports this feature:
- ✅ Atmosphere model has `add_gas` for returning gas to source
- ✅ Inventory model has capacity queries
- ✅ Blueprint system defines cryo tank specs
- ✅ Processed mode already returns CO to source (precedent exists)
- ✅ No breaking changes required

---

## Appendix A: Gas Classification Reference

### Ventable Gases (can be returned to source atmosphere)
`N2`, `CH4`, `Ar`, `CO2`, `He`, `Ne`, `Kr`, `Xe`, `H2`, `O2`

### Non-Ventable Byproducts (must be tracked as assets)
`CO` — from MOXIE CO2 processing; usable as fuel/propellant

### Conditional Gases (evaluate per-world)
- `H2O` — ventable if source has water vapor capacity
- `NH3` — ventable but may have environmental constraints on Titan

---

## Appendix B: Mass Balance Example

**Scenario**: Titan CH4 harvesting with full cryo tanks

```
Input: 1000 kg raw gas from Titan (95% N2, 5% CH4)
  - N2: 950 kg
  - CH4: 50 kg

Processing: 98% transport efficiency
  - N2 delivered: 931 kg
  - CH4 delivered: 49 kg

Storage: Cryo tanks full (0 kg capacity remaining)
  - N2 stored: 0 kg
  - CH4 stored: 0 kg

Venting: Overflow returned to Titan
  - N2 vented: 931 kg → back to Titan atmosphere
  - CH4 vented: 49 kg → back to Titan atmosphere

Results:
{
  gases_extracted: { 'N2' => 950, 'CH4' => 50 },
  gases_stored:    {},
  gases_delivered: {},
  gases_vented:    { 'N2' => 931, 'CH4' => 49 },
  efficiency:      0.98,
  success:         true
}

Mass balance check:
  N2: extracted(950) = stored(0) + delivered(0) + vented(931) + transport_loss(19) ✅
  CH4: extracted(50) = stored(0) + delivered(0) + vented(49) + transport_loss(1) ✅
```

---

## Appendix C: Related Research

- **Primary dependency**: `2026-07-09-RESEARCH-ATMOSPHERIC-SKIMMER-AUDIT.md` — identified the gap
- **Cryo tank blueprint**: `data/json-data/blueprints/units/storage/inflatable_cryo_tank_bp.json`
- **Inventory model**: `app/models/inventory.rb` — capacity queries
- **Processed mode precedent**: CO already returned to source in `perform_processed_transfer`
