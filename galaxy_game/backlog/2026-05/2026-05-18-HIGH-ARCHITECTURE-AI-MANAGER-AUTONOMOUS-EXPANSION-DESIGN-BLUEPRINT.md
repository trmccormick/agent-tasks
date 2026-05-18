---
status: backlog
priority: HIGH
type: architecture
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

# TASK: AI Manager Autonomous Expansion Design Blueprint (Phase 1 Gate)
**Status**: BACKLOG
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-05-18
**Last Updated**: 2026-05-18

---

## Local Worker Triage Report
*Filled in by local model (Ollama via Continue) during backlog review*
*Local models read task files only - they cannot run commands or access the DB*

- **Template Conformance**: PASS - all required sections included
- **Docker Wrapper Check**: N/A - design/architecture task only
- **MVP Alignment**: VALID - defines AI_MANAGER expansion architecture before implementation
- **MVP Impact Note**: Provides the gating design needed before system-discovery/foothold services can be safely built
- **Action Line**: READY FOR LOCAL HANDOFF

---

## Agent Assignment

**Assigned To**: Codestral (M4) local, with Qwen3.5-27B local as second-opinion reviewer
**Why This Agent**: This is a high-reasoning architecture breakdown that should stay local per routing rules
**Supervision Level**: watched carefully

**Supervision Legend**:
- Watched carefully = 0x/0.33x cloud agents and all local models
- Standard = 0.33x agents on well-specified tasks
- Autonomous OK = 1x agents only

> Local Ollama agents: you cannot execute terminal commands, Docker, RSpec, or git.
> You can read files provided to you and create/edit files via Continue.
> Never fabricate command output. If you need a command run, ask the human.

---

## Context
AI Manager currently handles local settlement progression but does not perform autonomous inter-system expansion planning. We need a design-first blueprint that integrates with existing JSONB `operational_data` and existing service object patterns before any new runtime services are implemented. The goal is to avoid schema bloat and keep all expansion telemetry in existing model hashes.

**Relevant Architecture Docs** - read before starting:
- `docs/new_agent/rules/DECISIONS.md` - locked architecture and no-table-bloat constraints
- `docs/new_agent/rules/GUARDRAILS.md` - execution and escalation rules
- `docs/systems/ai-manager.md` - current AI manager architecture
- `docs/systems/wormhole-network.md` - topology and pathing assumptions

> If a doc does not exist for this area, do not create one during this task.
> Flag the gap in your completion report instead.

---

## Problem Statement
Autonomous expansion requirements were previously mixed with implementation instructions, creating ambiguity for lower-tier agents and high risk of premature coding. We need one architecture blueprint that gates all Phase 2 implementation work.

**Current behavior**: AI Manager supports in-system expansion only.
**Expected behavior**: A documented, service-object blueprint for multi-hop system evaluation and foothold planning with JSONB persistence and clear error boundaries.

---

## Files Involved

### Primary Files - you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `docs/new_agent/tasks/backlog/2026-05/2026-05-18-HIGH-ARCHITECTURE-AI-MANAGER-AUTONOMOUS-EXPANSION-DESIGN-BLUEPRINT.md` | Fill architecture design content | All sections |

### Reference Files - read but do not edit
| File | Why You Need It |
|---|---|
| `docs/new_agent/tasks/backlog/reorganization attempt 2/2026-02/2026-02-11-HIGH-FEATURE-AI-MANAGER-AUTONOMOUS-EXPANSION.md` | Prior implementation-heavy task to normalize |
| `docs/new_agent/tasks/backlog/reorganization attempt 2/2026-02/2026-02-11-HIGH-DOCUMENTATION-AI-MANAGER-AUTONOMOUS-EXPANSION.md` | Prior doc variant to reconcile |
| `docs/new_agent/tasks/backlog/reorganization attempt 2/2026-02/2026-02-11-HIGH-DOCUMENTATION-AI-MANAGER-AUTONOMOUS-EXPANSION-MVP-FOCUS.md` | Prior MVP doc expectations |

### Migration (if needed)
- [x] No migration needed
- [ ] Migration needed: `[describe the schema change]`

---

## Implementation Steps

> 0x/0.33x agents: follow these steps exactly in order.
> 1x agents: use as a guide, apply judgment.
> Local Ollama agents: read steps carefully, ask human to run any commands, never fabricate output.

### Step 1 - Write expansion flow decomposition
Create a clear flow from trigger to foothold outcome using either a text flowchart or ordered service sequence:
- Trigger conditions (resource surplus, strategic pressure, mission slots)
- Wormhole topology query
- Candidate system shortlist
- MissionScorer readiness gate
- Foothold planning gate
- Persistence and retry/error branch

### Step 2 - Define sub-services and boundaries
Document these service responsibilities explicitly:
- `AIManager::AutonomousExpansionService`
- `AIManager::SystemDiscoveryService`
- `AIManager::ExpansionReadinessScorer`
- `AIManager::FootholdEstablishmentService`
- `AIManager::WormholeRoutePlanner`

Also document strict architecture rules:
- Service object/PORO only
- No abstract model inheritance
- No new tables for design phase

### Step 3 - JSONB persistence mapping
List exact key namespaces for `operational_data` (string keys only), including:
- candidate score snapshots
- selected route metadata
- rejection reasons
- foothold bootstrap plans
- retry counters and cooldown timestamps

### Step 4 - Define deposit plausibility target list
Provide at least 8 target deposit/resource categories for future expansion scoring integration. Minimum set:
1. water_ice
2. regolith_oxides
3. iron_nickel_metallics
4. silicates
5. sulfur_compounds
6. carbonaceous_volatiles
7. helium_3_proxy
8. rare_earth_traces
9. hydrocarbons_trace
10. ammonia_nitrates

### Step 5 - Pathfinding and safety constraints
Document that multi-hop logic defers to standard graph traversal (BFS or Dijkstra by weighted cost) and avoids in-memory full-network expansion. Include max-hop and cost-threshold guards.

### Step 6 - Error namespace and stop conditions
All listed exceptions must be in the `AIManager::Error` namespace hierarchy, including:
- topology_unavailable
- readiness_input_invalid
- no_viable_route
- foothold_conflict
- persistence_conflict

Also include explicit stop/escalation conditions:
- custom astrophysics engine required
- ActiveRecord settlement creation conflicts detected

### Step 7 - Produce synthesis-ready handoff section
At bottom of task, add:
- open questions
- assumptions validated
- blocked implementation tasks now unblocked

---

## Acceptance Criteria
- [ ] Expansion mechanics are broken into clear flowchart or sub-service sequence
- [ ] Service object/PORO architecture enforced; no abstract model inheritance
- [ ] 8+ target deposit/resource types explicitly listed
- [ ] Multi-hop pathfinding defers to standard graph traversal and includes memory guards
- [ ] All design exceptions mapped to `AIManager::Error` namespace
- [ ] JSONB `operational_data` string-key persistence pattern explicitly defined
- [ ] Design gate is marked complete with implementation handoff notes

---

## Stop Conditions - escalate to user immediately if:
- Discovery approach needs high-fidelity orbital/physics simulation
- Foothold design conflicts with existing settlement creation loops
- Design implies new table creation without prior decision approval
- Required architecture docs are missing and block design confidence

---

## Commit Instructions
Run git commands on **host**, not inside container:
```bash
git add docs/new_agent/tasks/backlog/2026-05/2026-05-18-HIGH-ARCHITECTURE-AI-MANAGER-AUTONOMOUS-EXPANSION-DESIGN-BLUEPRINT.md
git commit -m "architecture: AI manager autonomous expansion design blueprint gate"
git push
```

---

## Documentation
- [x] No doc changes needed
- [ ] Update `docs/[path]/[file].md` - [what to update]
- [ ] Flag doc gap: [description] - do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: none
**Blocks**:
- 2026-05-18-MEDIUM-FEATURE-AI-MANAGER-SYSTEM-DISCOVERY-SERVICE-IMPLEMENTATION.md
- 2026-05-18-MEDIUM-FEATURE-AI-MANAGER-EXPANSION-READINESS-SCORER-INTEGRATION.md
- 2026-05-18-MEDIUM-FEATURE-AI-MANAGER-FOOTHOLD-ESTABLISHMENT-SERVICE-IMPLEMENTATION.md
**Related tasks**:
- 2026-02-11-HIGH-FEATURE-AI-MANAGER-AUTONOMOUS-EXPANSION.md
- 2026-02-11-HIGH-DOCUMENTATION-AI-MANAGER-AUTONOMOUS-EXPANSION.md

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: N/A - architecture task

### What was changed
- `[file]` - [description of design updates]

### Issues discovered
[Any problems found during implementation that were not in the original task]

### Follow-up tasks needed
[Any new backlog items identified - do not create the files, just list them here]

### Lessons learned
[What worked, what did not, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session - one scannable line for next agent*

HANDOFF SUMMARY: [design blueprint updated] | [service boundaries locked] | [phase-2 tasks ready]
