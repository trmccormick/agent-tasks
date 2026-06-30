---
name: "Refine Mission Plans and AI Manager Alignment"
priority: MEDIUM
phase: phase8+
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Refine Mission Plans and AI Manager Alignment (Bootstrap & Operational Phases)

**Priority**: MEDIUM  
**Phase**: phase8+ (Cycler Arc)  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

The AI Manager's bootstrap harvesting logic is implemented and used for initial base setup and resource gathering. The distinction between bootstrap and operational phases is present in both code and documentation, but the operational escalation system remains unimplemented. Pattern documentation and explicit transition triggers are still missing or incomplete.

**Current**: Bootstrap phase documented but operational escalation unimplemented; no transition triggers  
**Expected**: Clear bootstrap/operational phase distinction with transition triggers and implemented escalation system

## Evidence of Incompleteness

```bash
grep -n "bootstrap\|operational" galaxy_game/app/services/ai_manager/**/*.rb 2>/dev/null | head -10
# Likely shows bootstrap logic but no operational mode implementation
```

## Files to Edit/Create

| File | Purpose | Key Section |
|------|---------|-------------|
| `galaxy_game/app/services/ai_manager/escalation_service.rb` | Operational escalation | Implement beyond stubs |
| `data/json-data/missions/**/*.json` | Mission profiles | Add bootstrap phase labels |
| `docs/architecture/ai_manager/bootstrap_operational_transition.md` (new) | Transition documentation | Triggers and handoff logic |

## Acceptance Criteria

- [ ] Bootstrap harvesting phases clearly labeled in all mission profiles
- [ ] Transition triggers and handoff logic defined and implemented
- [ ] Pattern learning documentation enhanced for bootstrap techniques
- [ ] Operational escalation system implemented and tested
- [ ] Clear distinction between bootstrap and operational AI harvesting roles
- [ ] Player integration points and resource buffer requirements documented

## Implementation Steps

1. **Bootstrap Phase Clarification**
   - Label and document all bootstrap harvesting phases in mission profiles
   - Define and document transition conditions for when a base becomes "operational" (player-accessible)
   - Clearly document AI Manager behaviors in both bootstrap and operational modes
2. **Transition Integration**
   - Define and implement handoff triggers for AI Manager to switch from bootstrap to operational mode
   - Specify player integration points and resource buffer requirements for transition
3. **Pattern Learning Enhancement**
   - Create or update pattern documentation for bootstrap harvesting techniques, success metrics, and failure scenarios
4. **Operational Escalation Implementation (BLOCKER)**
   - Implement the documented operational escalation system:
     - handle_expired_buy_orders
     - EscalationService
     - Automated harvester deployment
     - Special mission creation for expired orders
     - Scheduled import coordination
   - Update ContractCreationService beyond stub level

## Commit Instructions

```bash
git add data/json-data/missions/**/*.json docs/architecture/ai_manager/bootstrap_operational_transition.md
git commit -m "feat: implement bootstrap to operational phase transition for AI Manager"
```
