---
status: backlog
priority: MEDIUM
type: feature
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: false
---

# TASK: AI Manager Expansion Readiness Scorer Integration (Phase 2B)
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: feature
**Created**: 2026-05-18
**Last Updated**: 2026-05-18

---

## Local Worker Triage Report
*Filled in by local model (Ollama via Continue) during backlog review*
*Local models read task files only - they cannot run commands or access the DB*

- **Template Conformance**: PASS - all required sections included
- **Docker Wrapper Check**: REQUIRED - spec run required
- **MVP Alignment**: VALID - expansion must gate on readiness before mission creation
- **MVP Impact Note**: Prevents premature expansion when resources/logistics are below thresholds
- **Action Line**: NEEDS MANUAL REVIEW (local prep) -> CLOUD OR HUMAN EXECUTION FOR SPECS

---

## Agent Assignment

**Assigned To**: Qwen3.5-9B (implementation) with Codestral (M4) architecture check if thresholds conflict
**Why This Agent**: Clear scoped integration task with possible policy edge cases
**Supervision Level**: standard

---

## Context
This task wires expansion readiness into existing scoring logic so autonomous expansion decisions are gated by mission viability. It must reuse existing `MissionScorer` patterns and persist computed readiness traces in JSONB keys, without table changes.

**Relevant Architecture Docs** - read before starting:
- `docs/new_agent/rules/DECISIONS.md`
- `docs/new_agent/tasks/backlog/2026-05/2026-05-18-HIGH-ARCHITECTURE-AI-MANAGER-AUTONOMOUS-EXPANSION-DESIGN-BLUEPRINT.md`
- `docs/systems/ai-manager.md`

---

## Problem Statement
Expansion workflows currently do not have a dedicated readiness gate tied to mission-scoring surfaces for autonomous decisions.

**Current behavior**: Expansion logic can proceed without transparent readiness thresholding.
**Expected behavior**: Readiness scorer integration returns explicit score + reasons and blocks unsafe launches.

---

## Files Involved

### Primary Files - you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/ai_manager/mission_scorer.rb` | Add expansion readiness scoring entrypoint | `#expansion_readiness` |
| `app/services/ai_manager/autonomous_expansion_service.rb` | Consume readiness gate before scheduling | `#evaluate_expansion_readiness` |
| `spec/services/ai_manager/mission_scorer_spec.rb` | Validate new readiness score behavior | readiness contexts |
| `spec/services/ai_manager/autonomous_expansion_service_spec.rb` | Validate gating integration | block/allow scenarios |

### Reference Files - read but do not edit
| File | Why You Need It |
|---|---|
| `app/services/ai_manager/expansion_service.rb` | Existing expansion semantics |
| `app/models/star_system.rb` | Inputs used by scoring context |

### Migration (if needed)
- [x] No migration needed
- [ ] Migration needed: `[describe the schema change]`

---

## Implementation Steps

### Step 1 - Gate check
Do not start until design blueprint task is complete and service boundaries are locked.

### Step 2 - Add scorer integration points
Implement readiness dimensions using existing scorer conventions:
- resource surplus sufficiency
- logistics viability
- route confidence
- risk burden

### Step 3 - Define decision thresholds
Add explicit thresholds and reason codes for:
- allow
- defer
- reject

### Step 4 - Persist score traces
Save readiness score components and decision reasons to JSONB `operational_data` string keys.

### Step 5 - Wire expansion service gate
`AutonomousExpansionService` must stop before mission creation when readiness does not meet threshold.

### Step 6 - Verify
Run isolated specs and log output exactly.

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/mission_scorer_spec.rb spec/services/ai_manager/autonomous_expansion_service_spec.rb 2>&1 | tail -20'
```

Expected result: X examples, 0 failures

---

## Acceptance Criteria
- [ ] `MissionScorer` exposes expansion readiness score and reason set
- [ ] Expansion service blocks/defer/allow behavior uses readiness thresholds
- [ ] Score trace persistence exists in JSONB string keys
- [ ] No schema/table changes introduced
- [ ] Isolation run: 0 failures

---

## Stop Conditions - escalate to user immediately if:
- Threshold logic conflicts with existing mission-scorer contract
- Readiness keys overlap or break existing JSONB consumers
- Changes require modifying shared base concerns

---

## Commit Instructions
Run git commands on **host**, not inside container:
```bash
git add app/services/ai_manager/mission_scorer.rb app/services/ai_manager/autonomous_expansion_service.rb spec/services/ai_manager/mission_scorer_spec.rb spec/services/ai_manager/autonomous_expansion_service_spec.rb
git commit -m "feat: integrate expansion readiness scoring into AI manager expansion flow"
git push
```

---

## Documentation
- [ ] No doc changes needed
- [ ] Update `docs/systems/ai-manager.md` - add readiness threshold section
- [ ] Flag doc gap: [description] - do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: 2026-05-18-HIGH-ARCHITECTURE-AI-MANAGER-AUTONOMOUS-EXPANSION-DESIGN-BLUEPRINT.md
**Blocks**: 2026-05-18-MEDIUM-FEATURE-AI-MANAGER-FOOTHOLD-ESTABLISHMENT-SERVICE-IMPLEMENTATION.md
**Related tasks**: 2026-05-18-MEDIUM-FEATURE-AI-MANAGER-SYSTEM-DISCOVERY-SERVICE-IMPLEMENTATION.md

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `[file]` - [description of change]

### Issues discovered
[Any problems found during implementation that were not in the original task]

### Follow-up tasks needed
[Any new backlog items identified - do not create the files, just list them here]

### Lessons learned
[What worked, what did not, what future tasks in this area should know]

---

## Handoff Summary
HANDOFF SUMMARY: [readiness scorer integrated] | [threshold gates active] | [foothold task unblocked]
