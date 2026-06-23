# TASK_TEMPLATE: Luna Mission — Initial Equipment Provisioning

**Phase**: phase5
**Priority**: CRITICAL 🔴
**Type**: FEATURE/CONFIG
**Status**: BACKLOG
**Created**: 2026-06-21
**Estimate**: 4 hours (define equipment, update manifest, verify test)
**Relocated**: From reorganization_attempt_3 on 2026-06-22 (confirmed Luna MVP blocker for rake task)

---

## 🎯 Objective

Add initial equipment inventory to Luna mission profile. Luna settlement is spawned with ZERO equipment, causing all unit deployment tasks to fail with `MaterialShortageError`. TaskExecutionEngineV2 expects `manifest["resources"]["initial_equipment"]` but it's missing.

**Luna Mission Impact**: Blocks Phase 1 and Phase 2. Without this fix, Luna test cannot progress.

---

## 📋 Problem

| Component | Current | Expected |
|-----------|---------|----------|
| **Manifest** | No `initial_equipment` | Should define Solar Rig, Comms Equipment, PUH, TEU, PVE, Tanks, Gas Separator |
| **Inventory** | Empty | Should have 1 of each required unit |
| **Test Result** | 7/8 tasks fail (inventory shortage) | All tasks pass or correctly fail on other criteria |
| **Root Cause** | Line 40-42 in TaskExecutionEngineV2 expects manifest resources | Mission profile provides none |

---

## 🔍 Technical Details

### Current Code (TaskExecutionEngineV2 lines 39-47)
```ruby
if @manifest["resources"].is_a?(Hash)
  @environment.merge!(
    crew: @manifest["resources"]["initial_crew"],
    equipment: @manifest["resources"]["initial_equipment"],
    budget: @manifest["resources"]["budget"]
  )
end
```

### Expected Equipment (from Phase 1-3 requirements)

**Phase 1 (power_comms)** — 3 units required:
1. Solar Expansion Rig (1x)
2. Comms Equipment (1x)
3. Planetary Umbilical Hub (PUH) (1x)

**Phase 2 (isru_deployment)** — 3 units required:
1. Thermal Extraction Unit (TEU) (1x)
2. Planetary Volatiles Extractor Mk1 (PVE) (1x)
3. Inflatable Pressure Tank (1x)

**Phase 3 (gas_processing)** — 2 units required:
1. Gas Separator (1x)
2. Inflatable Pressure Tank (1x) — additional

**Total**: 8 units (7 unique, 1 duplicate tank)

---

## ✅ Acceptance Criteria

- [ ] Luna mission profile updated with `initial_equipment` section
- [ ] Equipment list includes all 7 required units for Phases 1-3
- [ ] Manifest properly formats equipment (item IDs + quantities)
- [ ] `rake luna_mission:execute` shows 0 `MaterialShortageError` for inventory checks
- [ ] Phase 1-3 tasks attempt to execute (may fail on other criteria, but not inventory)

---

## 📂 Files to Edit

### Primary (Will Edit)
- [data/json-data/missions/luna_base_establishment/luna_settlement_profile_v1.json](data/json-data/missions/luna_base_establishment/luna_settlement_profile_v1.json)
  - Add `resources` section with `initial_equipment`
  
- [data/json-data/missions/luna_base_establishment/luna_base_establishment_manifest_v2.json](data/json-data/missions/luna_base_establishment/luna_base_establishment_manifest_v2.json)
  - OR add resources section here if that's the manifest structure

### Reference (Don't Edit)
- [galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb](galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb)
  - Lines 39-47: Shows resource expectations
  
- Mission phase files for actual unit requirements:
  - [data/json-data/missions/luna_base_establishment/phases/phase_1_power_comms.json](data/json-data/missions/luna_base_establishment/phases/phase_1_power_comms.json)
  - [data/json-data/missions/luna_base_establishment/phases/phase_2_isru_deployment.json](data/json-data/missions/luna_base_establishment/phases/phase_2_isru_deployment.json)
  - [data/json-data/missions/luna_base_establishment/phases/phase_3_gas_processing.json](data/json-data/missions/luna_base_establishment/phases/phase_3_gas_processing.json)

---

## 🚧 Implementation Checklist

### Phase 1: Understand Requirements
- [ ] Read luna_settlement_profile_v1.json structure
- [ ] Check all 3 phase files for required unit names
- [ ] List exact item IDs needed

### Phase 2: Add Resources Section
- [ ] Update luna_settlement_profile_v1.json OR luna_base_establishment_manifest_v2.json
- [ ] Add `"resources"` key with structure:
  ```json
  "resources": {
    "initial_crew": 5,
    "initial_equipment": [
      { "id": "Solar Expansion Rig", "quantity": 1 },
      { "id": "Comms Equipment", "quantity": 1 },
      ...
    ],
    "budget": 50000
  }
  ```
- [ ] Verify JSON syntax is valid

### Phase 3: Verify Test
- [ ] Run: `rake luna_mission:execute`
- [ ] Check for `MaterialShortageError` — should be GONE
- [ ] Tasks should now execute (may fail on other grounds, but inventory checks pass)

### Phase 4: Commit
- [ ] Commit: `feat: Luna mission — initial equipment provisioning`

---

## 🔗 Dependencies

- **Blocked By**: None
- **Blocks**: Luna Phase 1 execution 🔴 CRITICAL
- **Related**: PVE volatile fix (Phase 2 blocker), Tank stage advancement (Phase 3 blocker)

---

## 📝 Completion Template

*Agent completes this section upon implementation*

**Status**: [NOT STARTED]
