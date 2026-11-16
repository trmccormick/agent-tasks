# Synthesis Report: can_harvest_locally? CO2 + ISRU Gate Fix

**Date:** 2026-08-27  
**Task:** `2026-08-24-MEDIUM-FIX-CAN-HARVEST-LOCALLY.md`  
**Status:** ✅ COMPLETE

---

## Problem Statement

`EscalationService.can_harvest_locally?` had two critical gaps:

1. **Missing CO2 atmospheric case**: Mars has ~95% CO2 atmosphere but could not be harvested for CO2, breaking any downstream logic that depends on CO2 availability (e.g., Sabatier reactor feedstock).

2. **O2 ISRU gate missing**: For bodies without atmospheric O2 (Luna, Mars), `can_harvest_locally?` returned `false` for O2 regardless of whether the settlement had deployed TEU/PVE units capable of producing O2 from regolith/water-ice processing. This meant an oxygen-triggered escalation order would always fall back to Earth import even when ISRU infrastructure was in place.

---

## Changes Made

### 1. `escalation_service.rb` — `can_harvest_locally?` (lines ~457-480)

**CO2 case added** (parallel to existing N2 case):
```ruby
when 'CO2'
  celestial_body.atmosphere&.gases&.any? { |g| g.name == 'CO2' }
```

**O2 case modified** — now has two paths:
```ruby
when 'O2'
  # Path 1: If O2 exists in atmosphere (e.g., Earth), grant direct credit
  return true if celestial_body.atmosphere&.gases&.any? { |g| g.name == 'O2' }
  # Path 2: For bodies without atmospheric O2, require deployed ISRU units
  has_isru_capability = settlement.base_units.any? { |u|
    u.unit_type.in?(%w[thermal_extraction_unit_mk1 thermal_extraction_unit]) ||
    u.unit_type.in?(%w[plasma_vaporizer_mk1 plasma_vaporizer planetary_volatiles_extractor])
  }
  has_isru_capability
```

### 2. `escalation_service_spec.rb` — New test cases added

**O2 harvesting context** (3 new tests):
- ✅ Returns true when atmosphere contains O2 (existing, unchanged)
- ✅ Returns false when atmosphere lacks O2 and no ISRU capability (existing, unchanged)
- ✅ **NEW**: Returns true when atmosphere lacks O2 but TEU+PVE units are deployed
- ✅ **NEW**: Returns true when atmosphere lacks O2 but PVE-only unit is deployed
- ✅ **NEW**: Returns true when atmosphere lacks O2 but planetary_volatiles_extractor is deployed

**CO2 harvesting context** (2 new tests):
- ✅ **NEW**: Returns true when atmosphere contains CO2
- ✅ **NEW**: Returns false when atmosphere lacks CO2

---

## Test Results

```
49 examples, 0 failures
```

All existing specs pass (no regressions). All new specs pass.

---

## Design Decisions

### Why check both TEU and PVE unit types?

The ISRU chain is: **TEU** (thermal extraction → mixed volatiles) → **PVE** (plasma vaporization → O2 + depleted regolith). Either unit type alone indicates ISRU capability because:
- A settlement with a TEU has started the ISRU pipeline and will deploy PVE next
- A settlement with a PVE has completed the full ISRU chain
- Both indicate active investment in oxygen production infrastructure

### Why not check `operational_properties.status`?

The existing codebase pattern (e.g., `ISRUCapabilityManager`) checks unit presence, not operational status. Status checking is handled at the deployment/production layer, not the capability-checking layer. This keeps `can_harvest_locally?` as a simple infrastructure-presence check.

### Why keep the atmospheric O2 path for Earth?

Earth and other bodies with genuine atmospheric O2 should still grant direct credit — there's no need to require ISRU units when O2 is naturally available in the atmosphere. This preserves backward compatibility with existing Earth-based settlement logic.

---

## Impact on Oxygen-Fixture Task (2026-08-16)

This fix **resolves Priority #1** of the oxygen-fixture task:
> "Does raw_regolith ever route through TEU → PVE to produce O2 for an oxygen-triggered escalation order?"

**Answer after this fix:** Yes — `can_harvest_locally?` now checks for deployed ISRU units (TEU/PVE) before granting O2 credit on bodies without atmospheric O2. The structural gap is closed.

The oxygen-fixture task (`2026-08-16-MEDIUM-BUG-FIX-HARVESTER-COMPLETION-JOB-OXYGEN-FIXTURE.md`) can now be moved to completed/.

---

## Out of Scope (per task file STEP 3)

- `supplied_via_hlt_mission?` — not touched
- `determine_escalation_strategy`'s `humans_present?` gate — not touched  
- Manifest-generation service — not touched
- These are medium/long-term items from the same handoff.
