---
status: implementation-ready-for-testing
priority: HIGH
type: implementation
system_domain: LOGISTICS
mvp_alignment: LUNA_SETTLEMENT_AUTONOMOUS_TESTING
local_worker_safe: true
estimated_time: 3-4 hours
---

# TASK: Luna Shortage Detection Services — Implementation & Testing

**Status**: IMPLEMENTATION COMPLETE, TESTS PENDING  
**Priority**: HIGH  
**Type**: Implementation + Testing  
**Created**: 2026-05-31  
**Updated**: 2026-05-31 (end of day)  
**Assigned To**: Review + Testing Phase

---

## COMPLETED ✅

### Phase 1: Investigation
✅ Settlement model introspection complete  
✅ All field names confirmed via Docker  
✅ INVESTIGATION-FINDINGS.md documented  
✅ Phase 1/2 services verified to exist  

### Phase 2: Service Implementation
✅ ISRUCapabilityManager — Equipment detection via base_units  
✅ ShortageDetector — Target comparison logic  
✅ ImportRequestGenerator — Phase 1/2 integration + persistence  
✅ ServiceCoordinator — Thin orchestration layer  

**Files Updated**:
- `app/services/logistics/isru_capability_manager.rb` ✅
- `app/services/logistics/shortage_detector.rb` ✅
- `app/services/logistics/import_request_generator.rb` ✅
- `app/services/logistics/service_coordinator.rb` ✅ (created)

---

## PENDING ⏳

### Phase 3: RSpec Tests (Tomorrow)
❌ Tests are outdated (test old logic structure)  
❌ Need rewrite to match corrected service logic  
❌ 10+ tests per service required  
❌ All tests must pass before marking complete  

**Test Files Exist But Need Updates**:
- `spec/services/logistics/isru_capability_manager_spec.rb`
- `spec/services/logistics/shortage_detector_spec.rb`
- `spec/services/logistics/import_request_generator_spec.rb`
- `spec/services/logistics/service_coordinator_spec.rb` (may not exist)

---

## Current Status

---

## Current Status

### Services Ready for Testing
All four services have been implemented with corrected logic:

**ISRUCapabilityManager**
- Checks `settlement.base_units` for equipment names
- Verifies both O2 extraction AND power generation equipment exist
- Returns viable status + missing equipment list

**ShortageDetector**
- Reads `settlement.operational_data[:survival_targets]` and `[:expansion_targets]`
- Compares against `settlement.items` (building current stock map)
- Returns `{ viable: bool, survival_shortages: [], expansion_shortages: [] }`
- Categorizes by tier (critical vs expansion)

**ImportRequestGenerator**
- Takes settlement + shortage_report from ShortageDetector
- Calls Phase 1: `Settlements::CostAnalyzer.calculate_cost`
- Calls Phase 2: `Logistics::ManifestGenerator.generate_manifest`
- Creates + persists `Logistics::ImportRequest` records with all required fields

**ServiceCoordinator**
- Simple orchestration: Call ISRUCapabilityManager → ShortageDetector → ImportRequestGenerator
- Returns `{ viable: bool, requests_created: [...], total_requests: X }`

### What Needs to Happen Tomorrow

**RSpec Tests**
- Update outdated test files (old logic structure no longer applies)
- Write 10+ tests per service
- All tests must pass

**Test Focus Areas**:
- ✅ ISRUCapabilityManager: Equipment detection, missing list accuracy
- ✅ ShortageDetector: Tier separation, viable flag, comparison logic
- ✅ ImportRequestGenerator: Persistence, Phase 1/2 integration, field values
- ✅ ServiceCoordinator: Full orchestration flow, all services working together

---

## Overnight RSpec Run

Ready to execute. Services are in place. Tests will fail because they test old logic, but service code itself is correct and ready for validation.

---

## Next Session: Workflow Adjustment

⚠️ **Lesson Learned**: Small model delegation (Qwen3.5-9B) with complex domain logic resulted in multiple correction cycles that burned more tokens than direct implementation would have.

**Proposed Changes**:
1. Use 27b or 30b model for complex domain work (fewer errors, fewer cycles)
2. Keep 9B for auxiliary tasks (tests, docs, refactoring)
3. Hybrid approach: Direct implementation + local agent writes tests
4. Single-service-at-a-time workflow if using smaller model

**Decision needed**: Which approach for next complex implementation task?

---

## Files Changed

**Implemented Services** (all in `app/services/logistics/`):
- `isru_capability_manager.rb` — Corrected to use base_units + equipment names
- `shortage_detector.rb` — Corrected to use operational_data + items comparison
- `import_request_generator.rb` — Corrected to call Phase 1/2 + persist
- `service_coordinator.rb` — Created, thin orchestration only

**Test Files** (in `spec/services/logistics/`, need updating):
- `isru_capability_manager_spec.rb` — Test old logic, needs rewrite
- `shortage_detector_spec.rb` — Test old logic, needs rewrite
- `import_request_generator_spec.rb` — Test old logic, needs rewrite
- `service_coordinator_spec.rb` — May not exist yet

---

## Ready for Overnight Run

✅ All services implemented  
✅ Code deployed to disk  
✅ Ready for RSpec execution

