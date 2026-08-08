---
status: backlog
priority: MEDIUM
type: feature
system_domain: AI_MANAGER
mvp_alignment: OTHER
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase8+/2026-04-17-ADVANCED-CLAUDE-IMPLEMENT-AI-MANAGER-OPERATIONAL-ESCALATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/phase8+/2026-04-17-ADVANCED-CLAUDE-IMPLEMENT-AI-MANAGER-OPERATIONAL-ESCALATION.md \
         projects/galaxy_game/tasks/active/2026-04-17-ADVANCED-CLAUDE-IMPLEMENT-AI-MANAGER-OPERATIONAL-ESCALATION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-04-17-ADVANCED-CLAUDE-IMPLEMENT-AI-MANAGER-OPERATIONAL-ESCALATION.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Implement AI Manager Operational Escalation System
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: feature
**Created**: 2026-06-21
**Last Updated**: 2026-08-07

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model during backlog review*

- **Template Conformance**: PASS — updated to current template 2026-08-07
- **Docker Wrapper Check**: N/A — service-level work, no RSpec strings needed
- **MVP Alignment**: STALE — phase8+ priority, not Luna MVP scope
- **MVP Impact Note**: Cross-cutting escalation logic feeds Mars/Venus expansion (Phase 9+)
- **Action Line**: NEEDS MANUAL REVIEW — phase8+ priority, deferred until Luna MVP complete

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Escalation Path**: Cloud fallback after two local failures

---

## Prerequisites

1. Verify current codebase state:
   ```bash
   grep -rn "TODO\|stub\|placeholder" galaxy_game/app/services/ai_manager/escalation_service.rb galaxy_game/app/services/ai_manager/emergency_mission_service.rb galaxy_game/app/services/ai_manager/contract_creation_service.rb 2>/dev/null | head -20
   ```
2. Review existing service implementations before starting work
3. Check PHASE_STRUCTURE.md for phase8+ context

---

## Architecture Gotchas

- **Do NOT** create new database migrations — this task finalizes existing stub logic
- EscalationService, EmergencyMissionService, ContractCreationService are the three target files
- All stubs use placeholder return values (nil, empty arrays) that must be replaced with real logic
- Integration points in ResourceAcquisitionService must be verified, not modified

---

## REQUIRED: Synthesis Report

**Before starting any work**, agent must:
1. Read all three service files completely
2. Count remaining TODO/stub/placeholder markers
3. Identify which methods need real implementation vs verification
4. Save synthesis report to `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/YYYY-MM-DD-ESCALATION-SYNTHESIS.md`
5. Report findings in chat before proceeding

---

## Problem Statement

The core escalation system is implemented but has remaining stub/TODO logic. EmergencyMissionService and ContractCreationService are present with some methods as stubs or placeholders. Features remain incomplete: full resupply manifest, real consumption tracking, broadcasting, and some job logic.

**Current**: EscalationService exists with stub implementations  
**Expected**: Fully functional escalation system with tested resupply manifests, consumption tracking, and import delivery jobs

## Evidence of Incompleteness

```bash
grep -n "TODO\|stub\|placeholder" galaxy_game/app/services/ai_manager/emergency_mission_service.rb 2>/dev/null | head -10
# Likely shows remaining stub methods
```

## Files to Edit/Create

| File | Purpose | Key Section |
|------|---------|-------------|
| `galaxy_game/app/services/ai_manager/escalation_service.rb` | Core escalation logic | Finalize and test |
| `galaxy_game/app/services/ai_manager/emergency_mission_service.rb` | Emergency missions | Expand beyond stubs |
| `galaxy_game/app/services/ai_manager/contract_creation_service.rb` | Contract creation | Finalize beyond stub level |
| `galaxy_game/app/services/ai_manager/resource_acquisition_service.rb` | Integration | Verify integration points |

## Acceptance Criteria

- [ ] All escalation logic is implemented, tested, and documented
- [ ] Database migration is run and verified
- [ ] EmergencyMissionService and ContractCreationService are fully functional
- [ ] Resupply manifest, consumption tracking, and import delivery jobs are complete
- [ ] Performance criteria met (<100ms per order)
- [ ] Economic balance validated (AI uses players when possible, maintains market liquidity)

## Implementation Steps

1. Run and verify all database migrations
2. Complete remaining stub/TODO logic (resupply manifest, consumption tracking, broadcasting, job scheduling)
3. Expand EmergencyMissionService for special missions as needed
4. Implement or verify import delivery job for scheduled imports
5. Write and run unit tests for EscalationService, harvester deployment, mission creation, import scheduling
6. Run integration and simulation tests for end-to-end escalation flow
7. Validate economic balance and performance criteria
8. Document all escalation logic, integration points, and test results

## Commit Instructions

```bash
git add galaxy_game/app/services/ai_manager/escalation_service.rb galaxy_game/app/services/ai_manager/emergency_mission_service.rb
git commit -m "feat: complete AI Manager operational escalation system"
```
