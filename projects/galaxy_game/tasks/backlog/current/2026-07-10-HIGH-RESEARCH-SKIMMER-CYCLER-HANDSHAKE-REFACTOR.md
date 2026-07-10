---
status: backlog
priority: HIGH
type: research
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

# RESEARCH: Design `SkimmerCyclerHandshakeService` Refactor — Vessel Role Differentiation and Depot/Shipyard Logistics

**Status**: BACKLOG
**Priority**: HIGH
**Type**: research
**Created**: 2026-07-10
**Last Updated**: 2026-07-10 — Implementation Agent (Qwen3.6-35b)

---

## Dependency

**Primary**: `docs/new_agent/projects/galaxy_game/summaries/2026-07-09-RESEARCH-ATMOSPHERIC-SKIMMER-AUDIT.md`
- Section "SkimmerCyclerHandshakeService — Conflicts with skimmer intent"
- Current code processes ALL cargo at 90% efficiency, contradicting MVP design constraint

---

## Problem Statement

The current `SkimmerCyclerHandshakeService#process_cargo` method **processes the entire raw cargo load** during cycler transit, treating all vessels identically regardless of their operational role. This contradicts both the skimmer intent (limited onboard processing) and the emerging Depot/Shipyard-based logistics model.

### Current Code (Wrong — No Role Differentiation)

```ruby
# CURRENT (WRONG) — processes ALL cargo for ALL vessel types
def process_cargo(skimmer, cycler)
  required_energy = skimmer.raw_cargo.values.sum * 2
  return false if cycler.energy_reserve < required_energy
  cycler.energy_reserve -= required_energy
  skimmer.processed_cargo = skimmer.raw_cargo.transform_values { |v| v * 0.9 }
  skimmer.raw_cargo = {}
  skimmer.available = true
  true
end
```

**This contradicts the skimmer intent and emerging Depot/Shipyard logistics model**: Skimmers have **limited onboard processing** — only enough to refill their own fuel tanks. The vast majority of harvested atmospheric cargo is delivered as **raw mixed gas** to Depots for LDC processing units.

### Correct Behavior — Two Vessel Profiles

**Harvester Variant** (Local Circuit: Atmosphere ↔ Depot)
- Designed for atmospheric extraction and delivery to nearby Depot
- Logic: `if cargo_tanks.full? then depart_for_depot`
- Primary role: Extract raw mixed gas → deliver to Depot cryo tanks
- Onboard processing: Only fuel-tank refilling during transit

**Tanker Variant** (Cycler Circuit: Depot ↔ Cycler)
- Designed for bulk transport between Depots and Cyclers
- Logic: Optimize for `logistics_priority` routing
- Uses 8x `cryogenic_storage_unit` configuration for high-volume transfer
- Refuel logic: Pull fuel directly from Depot reserves upon docking (NOT onboard processing)

---

## Research Requirements

### 1. Vessel Role Differentiation — Harvester vs Tanker

Research and design logic to differentiate between two vessel profiles:

**Harvester Variant (Local Circuit)**
- Operational domain: Atmosphere ↔ Depot (local circuit)
- Depart trigger: `if cargo_tanks.full? then depart_for_depot`
- Onboard processing mode: **Onboard Long-Dwell Processing** when no Depot is detected during transit
- When a Depot IS detected: Shift to **Rapid-Shuttle Logistics** — offload to Depot infrastructure instead of processing onboard
- Fuel tank refill only during transit (limited capacity)

**Tanker Variant (Cycler Circuit)**
- Operational domain: Depot ↔ Cycler (inter-depot circuit)
- Optimize for `logistics_priority` routing between orbital nodes
- Uses 8x `cryogenic_storage_unit` configuration for high-volume transfer
- Refuel logic: Pull fuel directly from Depot reserves upon docking (NOT onboard processing)
- No atmospheric extraction — only bulk gas transport between Depots/Cyclers

### 2. Station-Linked Operations Trigger

Design a "Station-Detected" trigger that changes vessel behavior:
- **When no Depot detected**: Harvester uses onboard long-dwell processing during transit
- **When Depot detected**: Shift to rapid-shuttle logistics — offload cargo to Depot infrastructure immediately
- How does the vessel detect nearby Depots? (proximity check, beacon signal, AI Manager routing?)
- What is the fallback if a Depot becomes unavailable mid-transit?

### 3. Refuel Logic for Tanker Variant

Design how Tankers refuel at Depots:
- Tankers pull fuel directly from Depot reserves upon docking (NOT from onboard processing)
- Which fuel types are available from Depot reserves? (LOX, CH4, etc.)
- How is fuel availability checked before Tanker arrival?
- What happens if Depot reserves are insufficient for Tanker refueling?

### 4. Vessel Variant Identification Verification

Design verification that the handshake service correctly identifies vessel type:
- How does the handshake service determine if a vessel is a Harvester or Tanker variant?
- Is this determined by blueprint data, operational_data flags, or explicit assignment?
- What happens if the wrong variant logic is executed (e.g., Tanker logic on a Harvester)?
- Should there be an explicit `identify_vessel_variant` method before any transfer operations?

### 5. Capacity Constraints

Research and document:
- HLT fuel tank capacity from blueprint specs
- HLT cargo tank capacity from blueprint specs
- Tanker's 8x cryogenic_storage_unit configuration details
- How these constraints limit the ratio of processed vs raw gas
- What happens when skimmer's fuel tanks are full but cargo is not — does processing stop?

### 6. New Service Structure

Design the refactored service:
- Should `process_cargo` be split into variant-specific methods? (`harvest_offload` + `tanker_transfer`)
- Should vessel identification be a prerequisite step before any transfer?
- How does the Depot detection trigger integrate with existing docking logic?
- How does the skimmer's onboard processing capacity integrate with L1 Depot's `MainRefineryController` during dock time?

### 7. MVP Scope vs Phase 9+

Determine what is MVP-relevant vs terraforming:
- **MVP**: Vessel variant differentiation, Depot detection trigger, Tanker refuel from Depot reserves
- **Phase 9+**: Full skimmer processing augmentation at L1 Depot (+15% throughput per docked skimmer), multi-depot logistics optimization

---

## Deliverables

1. Design document specifying Harvester vs Tanker vessel role differentiation logic
2. Station-Detected trigger design (Onboard Long-Dwell → Rapid-Shuttle Logistics shift)
3. Tanker refuel-from-Depot-reserves logic design
4. Vessel variant identification verification requirements
5. HLT blueprint capacity constraints (fuel tank, cargo tank, 8x cryo config) documented
6. Refactored service structure proposal with variant-specific methods
7. MVP vs Phase 9+ scope boundary definition

---

## Stop Conditions — escalate to user immediately if:
- HLT blueprint specs do not contain fuel/cargo tank capacity data
- Any architectural decision is required before research can continue
