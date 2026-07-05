---
status: backlog
priority: LOW
type: architecture
system_domain: AI_MANAGER
mvp_alignment: OTHER
local_worker_safe: true
phase: design
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Research Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/design/2026-07-05-LOW-RESEARCH-EMERGENCY-MISSION-SYSTEM-DESIGN.md

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv to new folder
  - New/untracked file: move with filesystem (mv), then git add the final path
  - Never copy task files between folders
READ FIRST: Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Research Emergency Mission System Design — Player GCC Earning Mechanism

**Status**: BACKLOG
**Priority**: LOW
**Type**: architecture
**Created**: 2026-07-05
**Phase**: design (post-MVP)

---

## Context

`EmergencyMissionService` exists in the codebase (`app/services/ai_manager/emergency_mission_service.rb`) and is already wired into `OperationalManager` and `EscalationService`. Its stated purpose is to offer emergency missions to **players** that NPCs would handle automatically during the pre-player phase. The intent is to give players a way to earn GCC by accepting crisis missions from struggling settlements.

This is **post-player-entry gameplay design**, not a bugfix or Phase 5 work. It needs Gemini-assisted design research before implementation:
- Mission acceptance/rejection mechanics
- GCC reward scaling (currently hardcoded `EMERGENCY_REWARD_MULTIPLIER = 2.0`)
- Player settlement capacity to accept missions (no overflow)
- Time pressure vs. player agency balance
- Integration with existing mission system (is this a new mission type or reuse?)

**Current state**: Service exists but is functionally hollow — `broadcast_to_players` is a placeholder, reward calculation is hardcoded, no mission acceptance flow, no player-facing UI spec.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (RESEARCHER Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/README.md`
3. **Existing Code**: `galaxy_game/app/services/ai_manager/emergency_mission_service.rb` — current implementation
4. **Architecture Docs**: `docs/architecture/ai_manager/RESUPPLY_AND_ESCALATION_ARCHITECTURE.md` — escalation design context
5. **This Task File**: Everything below

---

## Critical Information for This Task

### What Already Exists (Do NOT Recreate)

| Component | Status | Notes |
|-----------|--------|-------|
| `EmergencyMissionService` | ✅ Exists | `create_emergency_mission`, `qualifies_for_emergency?`, `calculate_emergency_reward` |
| `OperationalManager` integration | ✅ Wired | Calls `EmergencyMissionService.create_emergency_mission` at line ~853 |
| `EscalationService` integration | ✅ Wired | Calls via `handle_resource_shortage` at line ~504 |
| Reward calculation | ⚠️ Hardcoded | `EMERGENCY_REWARD_MULTIPLIER = 2.0`, base price from Earth Anchor |
| Broadcast to players | ❌ Placeholder | Only logs, no actual broadcasting mechanism |
| Mission acceptance flow | ❌ Missing | No player-facing mission accept/reject |
| GCC reward payout | ❌ Missing | Reward is calculated but never paid to player |

### Design Questions to Resolve (With Gemini)

1. **Mission Type**: Should emergency missions be a new `:emergency` mission type, or reuse existing `:resupply`/`:procurement` types?
2. **Reward Scaling**: Is 2x Earth Anchor price appropriate? Should it scale with settlement distress level?
3. **Player Constraints**: Can any player accept, or only those near the settlement? Does the player's settlement need capacity?
4. **Time Pressure**: Current `BASE_DURATION_HOURS = 24` — is this realistic for player response time?
5. **Failure Consequences**: What happens if a mission expires unaccepted? Does the settlement suffer?
6. **Anti-Exploit**: Can players farm emergency missions for GCC? How to prevent abuse?

---

## Acceptance Criteria

- [ ] Design document created in `design/` folder with Gemini-assisted research
- [ ] Mission acceptance/rejection flow specified
- [ ] GCC reward scaling model defined (with rationale)
- [ ] Player constraints and anti-exploit measures documented
- [ ] Integration points with existing mission system identified
- [ ] Implementation tasks created (if design is approved)

---

## Stop Conditions — escalate to user immediately if:
- Research reveals this conflicts with core game loop design
- Gemini determines the current `EmergencyMissionService` implementation is fundamentally incompatible with player-facing design
- Scope exceeds a single design document (split into multiple research tasks)

---

## Dependencies

**Blocked by**: None — can be researched independently
**Blocks**: Implementation of emergency mission system (when approved)
**Related tasks**: None yet — depends on design outcomes

---

## Handoff Summary

HANDOFF SUMMARY: Research task — EmergencyMissionService exists but needs player-facing design work with Gemini | Next action: create design document in design/ folder