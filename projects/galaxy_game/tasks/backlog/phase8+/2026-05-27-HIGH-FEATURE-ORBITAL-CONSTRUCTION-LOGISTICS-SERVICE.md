---
status: backlog
priority: HIGH
type: feature
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT | ISRU_PRODUCTION
local_worker_safe: false
---

# TASK: Implement OrbitalConstructionLogisticsService

**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-05-27
**Last Updated**: 2026-05-27

---

## Local Worker Triage Report

- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS
- **MVP Alignment**: VALID — required for AI Manager to ferry materials to L1 depot construction without hardcoded logic
- **MVP Impact Note**: Without this service, AI Manager cannot autonomously supply orbital construction projects on Luna
- **Action Line**: READY FOR CLOUD HANDOFF after prerequisites complete

---

## Agent Assignment

**Assigned To**: GPT-4.1 0x
**Why This Agent**: Service implementation with clear spec, blueprint-driven logic
**Supervision Level**: Watched carefully

---

## Context

After `orbital_resupply_cycle` is extracted from `TaskExecutionEngine` (see prerequisite task), the logistics behavior needs a proper home. `OrbitalConstructionLogisticsService` is the blueprint-driven, world-neutral replacement. It reads `OrbitalConstructionProject.required_materials` minus `delivered_materials` to determine what's needed, finds a source settlement with surplus, and schedules a material ferry.

Two modes are required:
- **Bootstrap mode**: No marketplace exists yet at the orbital settlement — ferry directly from surface settlement
- **Market mode**: Marketplace exists — place buy orders, NPC ferry only as fallback

For Luna MVP, bootstrap mode is the priority. Market mode is Phase 2.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules

---

## Problem Statement

**Current behavior**: No blueprint-driven logistics service exists. `orbital_resupply_cycle` in TaskExecutionEngine hardcodes Luna/ibeam logic.

**Expected behavior**: `OrbitalConstructionLogisticsService` reads any `OrbitalConstructionProject`, calculates material shortfall from blueprint data, finds a world-neutral source settlement, and schedules a ferry mission.

---

## Prerequisites — must be complete before this task starts
- [ ] `2026-05-27-CRITICAL-REFACTOR-EXTRACT-ORBITAL-RESUPPLY-CYCLE.md` complete
- [ ] `2026-05-27-HIGH-ARCHITECTURE-VERIFY-STRUCTURE-CORE-ORBITAL-STRUCTURE.md` complete

---

## Files Involved

### Primary Files — you will edit/create these
| File | Purpose |
|---|---|
| `app/services/construction/orbital_construction_logistics_service.rb` | Main service |
| `spec/services/construction/orbital_construction_logistics_service_spec.rb` | Spec |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/models/orbital_construction_project.rb` | required_materials, delivered_materials |
| `app/models/settlement/orbital_settlement.rb` | Station model |
| `app/models/settlement/base_settlement.rb` | Source settlement |
| `app/services/ai_manager/logistics_coordinator.rb` | Existing logistics patterns |

---

## Service Interface

```ruby
# Bootstrap mode — Phase 1 (MVP)
service = Construction::OrbitalConstructionLogisticsService.new(project)
service.run  # finds shortfall, finds source, schedules ferry

# Market mode — Phase 2 (post-MVP)
service.run_market_mode  # places buy orders on marketplace
```

## Acceptance Criteria
- [ ] Service created — blueprint-driven, no hardcoded world/material names
- [ ] Bootstrap mode implemented — calculates shortfall, finds source, schedules ferry
- [ ] Spec written — covers shortfall calculation, source finding, ferry scheduling
- [ ] 0 spec failures
- [ ] No regressions in full suite

---

## Stop Conditions
- `OrbitalConstructionProject` missing `required_materials` or `delivered_materials` — stop, report
- No ferry mechanism exists in codebase — flag before implementing custom ferry logic
- Market mode required for MVP — escalate, this task scopes bootstrap only

---

## Commit Instructions
```bash
git add galaxy_game/app/services/construction/orbital_construction_logistics_service.rb
git add galaxy_game/spec/services/construction/orbital_construction_logistics_service_spec.rb
git commit -m "feature: OrbitalConstructionLogisticsService — blueprint-driven material ferry for orbital construction"
git push
```

---

## Dependencies
**Blocked by**: Extract orbital_resupply_cycle task + StructureCore verification task
**Blocks**: AI Manager autonomous construction, DC establishment
**Related**: `2026-05-27-CRITICAL-REFACTOR-EXTRACT-ORBITAL-RESUPPLY-CYCLE.md`

---

## Completion Report
**Completed by**:
**Completion date**:
**Final test result**:

### What was changed
### Issues discovered
### Follow-up tasks needed
### Lessons learned

---

## Handoff Summary
HANDOFF SUMMARY: OrbitalConstructionLogisticsService implemented | Bootstrap mode working | Blueprint-driven material ferry | AI Manager construction unblocked
