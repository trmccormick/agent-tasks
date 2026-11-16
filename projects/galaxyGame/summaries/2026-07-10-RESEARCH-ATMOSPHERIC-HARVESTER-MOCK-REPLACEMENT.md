# RESEARCH: Design Replacement for `AIManager::AtmosphericHarvesterService` (Mock Data)

**Status**: BACKLOG → ACTIVE
**Priority**: HIGH
**Type**: research
**Created**: 2026-07-10
**Last Updated**: 2026-07-10 — Research Agent (Qwen3.6-35b)

---

## Dependency

**Primary**: `docs/new_agent/projects/galaxy_game/summaries/2026-07-09-RESEARCH-ATMOSPHERIC-SKIMMER-AUDIT.md`
- Section "AIManager::AtmosphericHarvesterService — Returns MOCK data"
- Current code returns hardcoded values: `collect_nitrogen(titan) = 200`, `collect_methane(titan) = 150`

---

## Problem Statement

The current `AIManager::AtmosphericHarvesterService` is a **placeholder with mock data** that does NOT modify actual atmosphere state:

```ruby
# CURRENT (WRONG) — hardcoded mock values, no real extraction
def collect_nitrogen(titan)
  titan[:nitrogen_collected] = 200  # Hardcoded mock value
end

def collect_methane(titan)
  titan[:methane_collected] = 150   # Hardcoded mock value
end
```

**This is fundamentally wrong for two reasons:**

1. **Mock data**: Returns hardcoded values instead of real atmospheric extraction
2. **Wrong semantics**: Implies selective gas collection at the source, which contradicts skimmer intent (skimmers extract raw mixed gas, not targeted individual gases)

### Correct Behavior Per Skimmer Intent

Skimmers extract **raw mixed atmospheric gas** proportionally by percentage — they cannot target specific gases in the atmosphere. The separation happens later at the LDC Gas Separator on Luna.

---

## Findings: Current Codebase State

### 1. Model Hierarchy (Ownership Confirmed)

```
CelestialBody (has :atmosphere)
    ↓
Craft::Harvester < BaseCraft (owns skimmer craft instances)
    ├── includes HasExtraction, Crafts::HasProcessing
    ├── has_one :inventory
    ├── belongs_to :owner (polymorphic — Corporation/Player)
    └── harvest_atmosphere(celestial_body, gas_name:, dust_amount:)
            ↓ calls AtmosphericHarvester (standalone class)

Craft::Transport::Cycler < ApplicationRecord
    ├── has_many :docked_crafts (BaseCraft)
    ├── has_many :scheduled_arrivals/departures
    └── stores cargo in definition_data/cargo hash
```

**Key finding**: `Harvester` model already has a `harvest_atmosphere` method that delegates to `AtmosphericHarvester` (a standalone class, not the AIManager service). The AIManager service is a **separate legacy layer** that operates on plain hashes (`titan[:nitrogen_collected] = 200`).

### 2. AtmosphericTransferService Already Exists (TerraSim)

Location: `galaxy_game/app/services/terra_sim/atmospheric_transfer_service.rb`

This service already implements correct atmospheric transfer:

```ruby
# TRANSFER_MODES: raw, selective, processed

# Raw transfer (proportional extraction of ALL gases):
# - Titan: 5% limit to preserve atmosphere while extracting CH4
# - Venus: No limit (cycler capacity is constraint)
# - Others: 20% default
# - Transport efficiency: 98% delivery
# - Extracts proportionally by gas percentage
# - Updates source.atmosphere.remove_gas() and target.atmosphere.add_gas()

# Selective transfer: requires :gases hash parameter
# Processed transfer: requires :capacity parameter
```

**This is the correct extraction engine.** The AIManager service should delegate to this, not use mock data.

### 3. Ownership Model (Confirmed)

- `BaseCraft` has `belongs_to :owner, polymorphic: true` — supports both `Player` and `Corporation`
- `Corporation < BaseOrganization` with types: `corporation`, `development_corporation`, `insurance_corporation`
- `Account < ApplicationRecord` linked to Corporation for financial tracking
- **AstroLift is a Corporation** — skimmers are owned via `owner_id`/`owner_type` on BaseCraft

### 4. Market Infrastructure (Exists)

- `Market::NpcPriceCalculator` — cost-based pricing (early game) → market-based pricing (mature markets)
- `MissionPlannerService` — already uses "real market pricing with transport costs" for inter-corporation transactions
- `MarketListingService` — handles strategic pricing for manufactured units
- **No dedicated gas market service exists yet** — gases are tracked in atmosphere composition, not as tradeable items

---

## Design: Replacement Service Structure

### Recommendation: Refactor, Don't Remove

The AIManager service should be **refactored to delegate to `TerraSim::AtmosphericTransferService`**, not removed. The AI Manager's role is decision-making (when/where/how much to extract), while the transfer service handles the physics/state mutation.

### New Service Architecture

```ruby
# app/services/ai_manager/atmospheric_extraction_service.rb
module AIManager
  class AtmosphericExtractionService
    attr_reader :skimmer, :source_body, :owner_corporation

    def initialize(skimmer, source_body)
      @skimmer = skimmer
      @source_body = source_body
      @owner_corporation = resolve_owner_corporation(skimmer)
    end

    # Primary public API — called by AI Manager decision logic
    def execute_extraction(transfer_params = {})
      validate_skimmer_ownership!
      validate_source_atmosphere!

      # Delegate to correct transfer mode based on skimmer capability
      # Skimmers ALWAYS extract raw (proportional) — they cannot selectively target gases
      transfer_mode = :raw
      capacity = transfer_params[:capacity] || default_skimmer_capacity

      TerraSim::AtmosphericTransferService
        .new(source_body, target_body, mode: transfer_mode, logger: Rails.logger)
        .transfer_atmosphere({ capacity: capacity })
    end

    # Skimmer docks with Cycler to offload cargo
    def dock_and_transfer_to_cycler(cycler, max_capacity = nil)
      return false unless can_dock?(cycler)

      # Transfer from skimmer's atmospheric cargo to cycler's cargo storage
      cargo_amount = [
        skimmer.atmosphere&.total_atmospheric_mass || 0,
        (max_capacity || cycler.available_storage)
      ].min

      transfer_gases_to_cycler(cycler, cargo_amount)
    end

    private

    def validate_skimmer_ownership!
      return if owner_corporation

      raise ArgumentError, "Skimmer #{skimmer.id} has no owner — cannot extract"
    end

    def validate_source_atmosphere!
      unless source_body.atmosphere&.present?
        raise ArgumentError, "Source body #{source_body.name} has no atmosphere"
      end
    end

    def resolve_owner_corporation(skimmer)
      return nil unless skimmer.owner
      skimmer.owner.is_a?(Corporation) ? skimmer.owner : skimmer.owner&.account&.corporation
    end

    def can_dock?(cycler)
      skimmer.has_available_docking_port? && cycler.has_available_docking_port?
    end

    def transfer_gases_to_cycler(cycler, amount)
      # Move atmospheric mass from skimmer to cycler's cargo hash
      gases = skimmer.atmosphere&.gases || []
      cycler_cargo = (cycler.definition_data || {})['cargo'] ||= {}

      gases.each do |gas|
        gas_mass = (amount * (gas.percentage / 100.0)).to_i
        cycler_cargo[gas.name] = (cycler_cargo[gas.name] || 0) + gas_mass
      end

      skimmer.atmosphere&.remove_gas('all', amount) if skimmer.atmosphere
    end
  end
end
```

### Key Design Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Service pattern | Synchronous delegation | MVP doesn't need async jobs; extraction is a single tick operation |
| Transfer mode | `:raw` only for skimmers | Skimmers cannot selectively target gases — proportional extraction only |
| Ownership check | Polymorphic `owner` → Corporation | Already exists on BaseCraft; no new auth infrastructure needed |
| Market integration | Deferred to MVP+ | Gases flow to cycler cargo first; market listing is a separate concern |
| Cycler docking | Reuse existing `docked_crafts` association | Cycler already stores docked craft; cargo transfer uses definition_data hash |

---

## AstroLift Asset Authentication Requirements

### Current State (Sufficient for MVP)

The ownership model already exists:
- `BaseCraft#owner` (polymorphic) → Corporation or Player
- `Corporation` has type discrimination (`corporation?`, `development_corporation?`)
- No separate "AstroLift" entity needed — any Corporation can own skimmers

### MVP Authentication Check

```ruby
def astro_lift_owns_skimmer?(skimmer)
  skimmer.owner&.organization_type == 'corporation' ||
  skimmer.owner&.development_corporation?
end
```

**No credentials/tokens needed for MVP.** Cross-corporation transactions (AstroLift → LDC) are handled via the existing `Account` financial model and `LedgerEntry.off_market_transfer?` scope.

### Phase 9+ Authentication (Future)

- Inter-corporation API tokens
- Consortium membership verification (ConsortiumMembership model exists)
- Rate limiting per corporation

---

## Request Routing Design

### MVP: Synchronous Delegation (Recommended)

```ruby
# In AI Manager decision logic (e.g., TitanSettlementService):
extraction = AIManager::AtmosphericExtractionService.new(skimmer, titan)
result = extraction.execute_extraction(capacity: skimmer.atmosphere&.total_atmospheric_mass || 5000)

# Result contains:
# result[:gases_extracted] => { "N2" => 1000, "CH4" => 500, ... }
# result[:gases_delivered] => { "N2" => 980, "CH4" => 490, ... } (98% transport efficiency)
# result[:success] => true/false
```

**Why synchronous:**
- MVP extraction is a single tick operation (no queue needed)
- AI Manager needs immediate results for downstream decisions (fuel delivery, market pricing)
- Simpler to test and debug

### Phase 9+: Asynchronous Jobs (Future)

If extraction becomes multi-tick or involves multiple worlds:
```ruby
extraction_job = AtmosphericExtractionJob.perform_later(skimmer_id, titan_id, capacity)
# Job tracks progress via ExtractionTask model
```

---

## Market Integration Requirements for MVP

### MVP Scope (Minimal Viable)

1. **Gases flow to cycler cargo** — not directly to market yet
2. **Cycler delivers to Luna** — gases arrive at LDC settlement atmosphere
3. **LDC processes raw gas** → separated gases (N2, CH4, etc.) via Gas Separator module
4. **No inter-corporation pricing in MVP** — gases are internal transfer, not market trade

### Phase 9+ Market Integration

1. **Gas commodity types** — define N2, CH4, H2 as tradeable commodities
2. **Market listing service** — AstroLift lists raw gas on inter-corporation market
3. **NPC price calculator** — already exists; extend for gas commodities
4. **Inter-corporation ledger entries** — use existing `LedgerEntry` model with `off_market_transfer?` scope

---

## MVP vs Phase 9+ Scope Boundary

### MVP (Must Have)

| Item | Status | Notes |
|------|--------|-------|
| Replace mock service with `AtmosphericTransferService` delegation | **NEW** | Core deliverable |
| Ownership verification on skimmer | **EXISTS** | `BaseCraft#owner` already works |
| Raw transfer mode (proportional extraction) | **EXISTS** | `TerraSim::AtmosphericTransferService` already implements this |
| Cycler docking + cargo transfer | **NEW** | Reuse existing associations, add cargo hash transfer |
| Titan 5% atmosphere preservation limit | **EXISTS** | Already in `AtmosphericTransferService` |
| 98% transport efficiency | **EXISTS** | Already in `AtmosphericTransferService` |

### Phase 9+ (Deferred)

| Item | Reason to Defer |
|------|-----------------|
| Inter-corporation pricing | No gas commodity types defined yet |
| Market listing for gases | No market infrastructure for raw gases |
| Asynchronous extraction jobs | MVP doesn't need multi-tick extraction |
| Consortium membership checks | Not needed for MVP scope |
| Multi-world atmospheric interaction | Out of scope for Luna settlement MVP |

---

## Implementation Plan (Next Steps)

1. **Create `AIManager::AtmosphericExtractionService`** — delegates to `TerraSim::AtmosphericTransferService` with ownership validation
2. **Add cargo transfer method** — skimmer → cycler docking protocol using existing associations
3. **Update AI Manager decision logic** — replace mock calls (`titan_harvest`) with real extraction service calls
4. **Write specs** — test ownership validation, raw transfer mode, cycler cargo transfer

---

## Stop Conditions — Escalated Findings

### No Blockers Found

- ✅ Ownership model exists for skimmers (`BaseCraft#owner` polymorphic)
- ✅ `AtmosphericTransferService` already implements correct physics
- ✅ Market infrastructure exists (NPC pricing, ledger entries)
- ✅ Cycler docking infrastructure exists (`docked_crafts` association)

### Architectural Decisions Made During Research

1. **Skimmers use `:raw` transfer mode only** — confirmed by skimmer intent (proportional extraction, not selective)
2. **No new Corporation entity needed** — any `Corporation` can own skimmers via existing polymorphic association
3. **MVP defers market integration** — gases flow to cycler → Luna settlement atmosphere; market listing is Phase 9+

---

## References

- Current mock service: `galaxy_game/app/services/ai_manager/atmospheric_harvester_service.rb`
- Correct transfer engine: `galaxy_game/app/services/terra_sim/atmospheric_transfer_service.rb`
- Harvester model: `galaxy_game/app/models/craft/harvester.rb` (extends `BaseCraft`)
- Cycler model: `galaxy_game/app/models/craft/transport/cycler.rb`
- Base craft ownership: `galaxy_game/app/models/craft/base_craft.rb` (`belongs_to :owner, polymorphic: true`)
- Corporation model: `galaxy_game/app/models/organizations/corporation.rb`
- NPC pricing: `galaxy_game/app/services/market/npc_price_calculator.rb`
