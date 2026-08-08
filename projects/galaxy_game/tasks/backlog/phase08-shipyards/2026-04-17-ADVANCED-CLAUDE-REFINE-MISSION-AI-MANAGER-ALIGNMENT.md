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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase8+/2026-04-17-ADVANCED-CLAUDE-REFINE-MISSION-AI-MANAGER-ALIGNMENT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/phase8+/2026-04-17-ADVANCED-CLAUDE-REFINE-MISSION-AI-MANAGER-ALIGNMENT.md \
         projects/galaxy_game/tasks/active/2026-04-17-ADVANCED-CLAUDE-REFINE-MISSION-AI-MANAGER-ALIGNMENT.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-04-17-ADVANCED-CLAUDE-REFINE-MISSION-AI-MANAGER-ALIGNMENT.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Refine Mission Plans and AI Manager Alignment (Bootstrap & Operational Phases)
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: feature
**Created**: 2026-06-21
**Last Updated**: 2026-08-07

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model during backlog review*

- **Template Conformance**: PASS — updated to current template 2026-08-07
- **Docker Wrapper Check**: N/A — service/data work, no RSpec strings needed
- **MVP Alignment**: STALE — phase8+ priority, not Luna MVP scope
- **MVP Impact Note**: Bootstrap→operational transition feeds Mars expansion (Phase 9+)
- **Action Line**: NEEDS MANUAL REVIEW — phase8+ priority, deferred until Luna MVP complete

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Escalation Path**: Cloud fallback after two local failures

---

## Prerequisites

1. Verify current bootstrap/operational phase state:
   ```bash
   grep -rn "bootstrap\|operational" galaxy_game/app/services/ai_manager/**/*.rb 2>/dev/null | head -15
   ```
2. Check AI priority system for operational logic:
   ```bash
   grep -n "check_operational\|effective_operational_priorities" galaxy_game/app/services/ai_manager/ai_priority_system.rb
   ```
3. Review existing escalation_service.rb stubs (related task: IMPLEMENT-AI-MANAGER-OPERATIONAL-ESCALATION)
4. Review PHASE_STRUCTURE.md for phase8+ context

---

## Architecture Gotchas

- **Do NOT** duplicate work from the related escalation task — this focuses on bootstrap→operational transition, not resource shortage handling
- `ai_priority_system.rb` already has operational phase logic (`check_operational`, `effective_operational_priorities`) — extend, don't replace
- Mission profile labels go in `data/json-data/missions/**/*.json` (add bootstrap phase field)
- Transition documentation goes in `docs/architecture/ai_manager/bootstrap_operational_transition.md`
- Bootstrap harvesting logic already exists — document the handoff point, don't rewrite it

---

## REQUIRED: Synthesis Report

**Before starting any work**, agent must:
1. Read `galaxy_game/app/services/ai_manager/ai_priority_system.rb` completely (operational phase logic)
2. Audit mission profile JSON files for bootstrap phase labels
3. Identify the gap between current operational detection and transition triggers
4. Map relationship to escalation task (what's bootstrap vs what's operational escalation)
5. Save synthesis report to `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/YYYY-MM-DD-BOOTSTRAP-TRANSITION-SYNTHESIS.md`
6. Report findings in chat before proceeding

---

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
