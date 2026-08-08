---
status: backlog
priority: MEDIUM
type: feature
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: false
---

# TASK: AI Manager Foothold Establishment Service Implementation (Phase 2C)
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
- **Docker Wrapper Check**: REQUIRED - spec execution required
- **MVP Alignment**: VALID - foothold logic is required to complete autonomous expansion loop
- **MVP Impact Note**: Converts approved expansion opportunities into executable bootstrap plans
- **Action Line**: NEEDS MANUAL REVIEW (local prep) -> CLOUD OR HUMAN EXECUTION FOR SPECS

---

## Agent Assignment

**Assigned To**: Qwen3.5-9B local for implementation draft, then cloud 0.33x for verification pass
**Why This Agent**: Task is detailed and deterministic but requires runtime verification
**Supervision Level**: watched carefully

---

## Context
After discovery and readiness scoring are available, AI Manager needs a dedicated service to select foothold sites and generate bootstrap resource packages. This must respect existing settlement creation pathways and avoid bypassing core ActiveRecord loops.

**Relevant Architecture Docs** - read before starting:
- `docs/new_agent/rules/DECISIONS.md`
- `docs/new_agent/rules/GUARDRAILS.md`
- `docs/new_agent/tasks/backlog/2026-05/2026-05-18-HIGH-ARCHITECTURE-AI-MANAGER-AUTONOMOUS-EXPANSION-DESIGN-BLUEPRINT.md`
- `docs/systems/ai-manager.md`

---

## Problem Statement
There is no isolated foothold-establishment service for autonomous expansion. Site selection and bootstrap package planning are not currently modeled as reusable logic.

**Current behavior**: No autonomous foothold planning service for new systems.
**Expected behavior**: `AIManager::FootholdEstablishmentService` outputs a selected site plus resource/bootstrap plan and conflict-safe handoff payload.

---

## Files Involved

### Primary Files - you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/ai_manager/foothold_establishment_service.rb` | New foothold planner service | `#call`, `#select_site`, `#build_bootstrap_package` |
| `app/services/ai_manager/autonomous_expansion_service.rb` | Consume foothold service output | `#plan_foothold` |
| `spec/services/ai_manager/foothold_establishment_service_spec.rb` | Validate site selection/bootstrap behavior | success/failure paths |

### Reference Files - read but do not edit
| File | Why You Need It |
|---|---|
| `app/services/ai_manager/system_discovery_service.rb` | Candidate system input contract |
| `app/models/settlement.rb` | Existing settlement instantiation flow |
| `app/services/ai_manager/mission_scorer.rb` | Resource readiness dimensions |

### Migration (if needed)
- [x] No migration needed
- [ ] Migration needed: `[describe the schema change]`

---

## Implementation Steps

### Step 1 - Dependency confirmation
Do not begin unless design blueprint, discovery, and readiness tasks are complete or explicitly approved for parallel draft mode.

### Step 2 - Implement foothold planner service
Create service with:
- candidate site ranking
- minimum viability checks
- bootstrap package creation (energy/life-support/construction baseline)
- rejection reason contract

### Step 3 - Integration-safe handoff
Service output must provide a payload consumable by existing settlement creation loops, not direct bypass creation.

### Step 4 - Persistence mapping
Persist decision payload to JSONB `operational_data` string keys:
- foothold_plan
- foothold_rejection_reason
- bootstrap_estimate
- handoff_status

### Step 5 - Error namespace
All errors under `AIManager::Error` hierarchy:
- foothold_conflict
- insufficient_bootstrap_resources
- invalid_site_profile

### Step 6 - Verify
Run isolated spec command and capture final output.

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/foothold_establishment_service_spec.rb 2>&1 | tail -20'
```

Expected result: X examples, 0 failures

---

## Acceptance Criteria
- [ ] Foothold service selects viable sites from scored inputs
- [ ] Bootstrap package output includes baseline operational bundle
- [ ] Integration payload does not bypass existing settlement creation loops
- [ ] JSONB persistence uses string-key namespaces only
- [ ] Errors use `AIManager::Error` namespace
- [ ] Isolation run: 0 failures

---

## Stop Conditions - escalate to user immediately if:
- Service must bypass or rewrite core settlement creation loops
- Site viability requires external astronomy/physics library
- Bootstrap model conflicts with existing resource ledger assumptions

---

## Commit Instructions
Run git commands on **host**, not inside container:
```bash
git add app/services/ai_manager/foothold_establishment_service.rb app/services/ai_manager/autonomous_expansion_service.rb spec/services/ai_manager/foothold_establishment_service_spec.rb
git commit -m "feat: add AI manager foothold establishment service for autonomous expansion"
git push
```

---

## Documentation
- [ ] No doc changes needed
- [ ] Update `docs/systems/ai-manager.md` - foothold planner contract
- [ ] Flag doc gap: [description] - do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**:
- 2026-05-18-HIGH-ARCHITECTURE-AI-MANAGER-AUTONOMOUS-EXPANSION-DESIGN-BLUEPRINT.md
- 2026-05-18-MEDIUM-FEATURE-AI-MANAGER-SYSTEM-DISCOVERY-SERVICE-IMPLEMENTATION.md
- 2026-05-18-MEDIUM-FEATURE-AI-MANAGER-EXPANSION-READINESS-SCORER-INTEGRATION.md
**Blocks**: none
**Related tasks**: 2026-02-11-HIGH-FEATURE-AI-MANAGER-AUTONOMOUS-EXPANSION.md

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
HANDOFF SUMMARY: [foothold planner added] | [settlement-safe handoff payload defined] | [autonomous loop complete]
