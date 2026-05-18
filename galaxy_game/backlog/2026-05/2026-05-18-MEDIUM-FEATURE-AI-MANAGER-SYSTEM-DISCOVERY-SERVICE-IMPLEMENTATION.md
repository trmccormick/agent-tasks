---
status: backlog
priority: MEDIUM
type: feature
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: false
---

# TASK: AI Manager System Discovery Service Implementation (Phase 2A)
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: feature
**Created**: 2026-05-18
**Last Updated**: 2026-05-18

---

## Local Worker Triage Report
*Filled in by local model (Ollama via Continue) during backlog review*
*Local models read task files only - they cannot run commands or access the DB*

- **Template Conformance**: PASS - all sections included
- **Docker Wrapper Check**: REQUIRED - spec run is mandatory
- **MVP Alignment**: VALID - discovery service is required for autonomous expansion
- **MVP Impact Note**: Unlocks candidate-system generation for Luna-aligned AI growth logic
- **Action Line**: NEEDS MANUAL REVIEW (local prep) -> CLOUD OR HUMAN EXECUTION FOR SPECS

---

## Agent Assignment

**Assigned To**: Qwen3.5-9B (local implementation prep) then cloud 0.33x for verification run
**Why This Agent**: Task is scoped and mechanical but requires command execution for final verification
**Supervision Level**: standard

**Supervision Legend**:
- Watched carefully = 0x/0.33x cloud agents and all local models
- Standard = 0.33x agents on well-specified tasks
- Autonomous OK = 1x agents only

---

## Context
This task builds the concrete discovery service after the design blueprint is approved. It must query reachable systems through wormhole topology and return ranked candidates without introducing new tables. Persistence must use existing JSONB hashes per blueprint decisions.

**Relevant Architecture Docs** - read before starting:
- `docs/new_agent/rules/DECISIONS.md`
- `docs/new_agent/rules/GUARDRAILS.md`
- `docs/new_agent/tasks/backlog/2026-05/2026-05-18-HIGH-ARCHITECTURE-AI-MANAGER-AUTONOMOUS-EXPANSION-DESIGN-BLUEPRINT.md`
- `docs/systems/ai-manager.md`
- `docs/systems/wormhole-network.md`

---

## Problem Statement
The codebase lacks a dedicated system-discovery service for autonomous expansion. AI Manager cannot create a structured set of viable candidate systems across multi-hop paths.

**Current behavior**: Expansion logic is local/system-bound with no reusable discovery query layer.
**Expected behavior**: `AIManager::SystemDiscoveryService` returns viable candidates with route metadata and rejection reasons.

---

## Files Involved

### Primary Files - you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/ai_manager/system_discovery_service.rb` | New discovery service | `#call`, `#reachable_systems`, `#rank_candidates` |
| `spec/services/ai_manager/system_discovery_service_spec.rb` | Discovery behavior tests | viability, ranking, rejection specs |

### Reference Files - read but do not edit
| File | Why You Need It |
|---|---|
| `app/models/star_system.rb` | Coordinates and topology inputs |
| `app/services/ai_manager/mission_scorer.rb` | Existing scoring conventions |
| `app/services/ai_manager/expansion_service.rb` | Existing expansion entrypoints |

### Migration (if needed)
- [x] No migration needed
- [ ] Migration needed: `[describe the schema change]`

---

## Implementation Steps

### Step 1 - Confirm design gate completion
Do not start code until the design blueprint task is marked complete and its handoff section exists.

### Step 2 - Create discovery service
Implement `AIManager::SystemDiscoveryService` with:
- topology-aware reachable-system query
- max-hop and route-cost guards
- candidate normalization payload
- explicit rejection reasons

### Step 3 - Add ranking and filtering
Apply lightweight scoring from blueprint dimensions:
- connectivity score
- resource potential proxy
- risk/threat proxy
- logistical distance cost

### Step 4 - JSONB persistence hooks
Persist discovery run outputs into existing `operational_data` string keys (no new tables), including:
- candidate_snapshot
- rejected_candidates
- selected_route_basis

### Step 5 - Error namespace alignment
All raised errors must use `AIManager::Error` subclasses/messages as documented in blueprint.

### Step 6 - Verify
Use isolated test command pattern and record exact output.

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/system_discovery_service_spec.rb 2>&1 | tail -20'
```

Expected result: X examples, 0 failures

---

## Acceptance Criteria
- [ ] Discovery service returns reachable multi-hop candidates
- [ ] Candidate ranking is deterministic for same inputs
- [ ] Rejection reasons are explicit and persisted
- [ ] No new table or schema introduced
- [ ] Errors use `AIManager::Error` namespace
- [ ] Isolation run: 0 failures

---

## Stop Conditions - escalate to user immediately if:
- Pathfinding requires high-fidelity astrophysics math
- Discovery implementation conflicts with existing settlement loop assumptions
- More than 4 files outside this task scope require edits

---

## Commit Instructions
Run git commands on **host**, not inside container:
```bash
git add app/services/ai_manager/system_discovery_service.rb spec/services/ai_manager/system_discovery_service_spec.rb
git commit -m "feat: AI manager system discovery service for autonomous expansion"
git push
```

---

## Documentation
- [ ] No doc changes needed
- [ ] Update `docs/systems/ai-manager.md` - add service responsibilities summary
- [ ] Flag doc gap: [description] - do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: 2026-05-18-HIGH-ARCHITECTURE-AI-MANAGER-AUTONOMOUS-EXPANSION-DESIGN-BLUEPRINT.md
**Blocks**: 2026-05-18-MEDIUM-FEATURE-AI-MANAGER-FOOTHOLD-ESTABLISHMENT-SERVICE-IMPLEMENTATION.md
**Related tasks**: 2026-05-18-MEDIUM-FEATURE-AI-MANAGER-EXPANSION-READINESS-SCORER-INTEGRATION.md

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
HANDOFF SUMMARY: [system discovery added] | [specs executed] | [ready for scorer/foothold integration]
