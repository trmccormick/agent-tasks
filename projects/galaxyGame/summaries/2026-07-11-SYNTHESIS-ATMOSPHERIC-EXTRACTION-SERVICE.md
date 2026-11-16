# IMPLEMENTATION: AtmosphericExtractionService — Synthesis Report

**Date**: 2026-07-11
**Task**: `2026-07-11-HIGH-FEATURE-ATMOSPHERIC-EXTRACTION-SERVICE.md`
**Type**: implementation
**Status**: synthesis complete, ready for implementation

---

## Executive Summary

Create `AIManager::AtmosphericExtractionService` to replace mock data in `AIManager::AtmosphericHarvesterService`. The new service delegates to the existing `TerraSim::AtmosphericTransferService` (which already implements correct physics: proportional extraction, Titan 5% limit, 98% transport efficiency).

---

## Key Findings from Codebase Audit

### 1. Current Mock Service (`atmospheric_harvester_service.rb`)
- `collect_nitrogen(titan)` → hardcoded `200`
- `collect_methane(titan)` → hardcoded `150`
- No actual atmosphere state mutation
- Uses plain hash manipulation (`titan[:nitrogen_collected] = 200`)

### 2. Transfer Engine Already Exists (`terra_sim/atmospheric_transfer_service.rb`)
- Three modes: `raw`, `selective`, `processed`
- Raw mode: proportional extraction of ALL gases by percentage
- Titan: 5% limit, Venus: no limit, others: 20% default
- Transport efficiency: 98% delivery
- Returns `{ gases_extracted: {}, gases_delivered: {}, success: true/false }`

### 3. Cycler Model (`craft/transport/cycler.rb`)
- `serialize :definition_data, JSON` — cargo goes in `definition_data['cargo']` hash
- **NOT** `cycler.atmosphere` (that's for celestial body state)
- Has `docked_crafts` association for docking validation

### 4. Harvester Model (`craft/harvester.rb`)
- Inherits from `BaseCraft` → `belongs_to :owner, polymorphic: true`
- Already has `harvest_atmosphere(celestial_body, gas_name:, dust_amount:)` method
- Uses `AtmosphericHarvester` (standalone class) — separate from AIManager service

### 5. Factory Availability
- `factory :craft_harvester` — exists in `spec/factories/craft/base_craft.rb`
- `factory :corporation` — exists in `spec/factories/organizations.rb`
- No `factory :craft_cycler` — will need to use `create(:cycler)` or factory path

---

## Architecture Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Service pattern | Synchronous delegation | MVP doesn't need async jobs |
| Transfer mode | `:raw` only for skimmers | Skimmers cannot selectively target gases |
| Ownership check | Polymorphic `owner` → Corporation | Already exists on BaseCraft |
| Market integration | Deferred to MVP+ | Gases flow to cycler cargo first |
| Cycler docking | Reuse existing `docked_crafts` association | No new infrastructure needed |

---

## Files to Create/Edit

### New Files
1. `galaxy_game/app/services/ai_manager/atmospheric_extraction_service.rb` — replacement service
2. `spec/services/ai_manager/atmospheric_extraction_service_spec.rb` — test specs

### Existing Files (Edit)
3. `galaxy_game/app/services/ai_manager/skimmer_cycler_handshake_service.rb` — replace mock calls with real extraction

---

## Critical Gotchas (Confirmed from Codebase)

1. **Cycler cargo**: `cycler.definition_data['cargo']` hash — NOT `cycler.atmosphere`
2. **Transfer mode**: `:raw` only for skimmers — they cannot selectively target gases
3. **No market integration in MVP** — scope ends at delivery to Luna settlement atmosphere

---

## Implementation Plan

1. Create `AtmosphericExtractionService` with ownership validation, raw transfer delegation, cycler cargo transfer
2. Update `SkimmerCyclerHandshakeService` to use new service instead of mock calls
3. Write RSpec specs covering: ownership validation, raw transfer mode, cycler cargo transfer, no regressions

---

## Risks & Mitigations

| Risk | Mitigation |
|------|------------|
| `cycler` factory doesn't exist | Use `Craft::Transport::Cycler.create_from_definition` or create minimal factory |
| `CelestialBodies::CelestialBody` model path wrong | Verify with `grep` before implementation |
| `AtmosphericTransferService#transfer_atmosphere` signature differs | Read full method before delegating |
