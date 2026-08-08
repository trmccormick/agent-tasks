---
status: backlog
priority: MEDIUM
type: feature
system_domain: AI_MANAGER
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send the agent)

```
You are Implementation Agent.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/phase7+/2026-07-04-MEDIUM-FEATURE-POPULATION-MORALE-SYSTEM.md

READ FIRST: Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Create STATUS SYNTHESIS REPORT in chat BEFORE starting any work (template in task file).
```

---

# TASK: Implement Population Morale & Wellbeing System
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: feature
**Created**: 2026-07-04
**Phase**: 7+

---

## Context
PopulationManagement tracks headcount and resources but lacks morale/wellbeing. Isolation without green life, sensory variety, and routine currently causes no psychological decline, resulting in an unrealistic "perfect efficiency" model for long-term settlements.

## Problem Statement
The current system lacks morale mechanics. We need to introduce wellbeing contributors and retention mechanics to prevent unrealistic settlement efficiency in long-term play.

---

## Files Involved

### Primary — edit these
| File | Purpose | Key Section |
|---|---|---|
| `galaxy_game/app/concerns/population_management.rb` | Extend with morale tracking | Main module |
| `galaxy_game/spec/models/concerns/population_management_spec.rb` | Add morale unit tests | Lifecycle tests |

### Reference — read only
| File | Why You Need It |
|---|---|
| `galaxy_game/app/models/units/greenhouse.rb` | Check for biophilia/greenery hooks |
| `docs/architecture/economy/MARKET_VS_BUILD_LOGIC.md` | Ensure morale logic doesn't break market loops |

---

## Architecture Gotchas

⚠️ **GOTCHA 1: Do not create tight coupling with Market logic**
- ❌ Wrong: Market service calling Population concern directly
- ✅ Right: Population concern reading aggregate consumption logs
- Why: Keep concerns decoupled to prevent circular dependencies.

⚠️ **GOTCHA 2: Simulation vs. UI**
- ❌ Wrong: Building the Morale Monitoring UI before the calculation logic is verified in specs
- ✅ Right: Solidify `wellbeing_index` math via TDD first
- Why: The UI is purely a mirror of the data; if the data math is wrong, the UI is useless.

---

## Implementation Steps

### Step 1 — Add Morale Tracking
Extend `PopulationManagement` to include a `wellbeing_index` (float 0.0-1.0).

### Step 2 — Integrate Contributors
Implement calculations for:
- **Biophilia**: Greenhouse density multiplier.
- **Variety**: Market consumption logs variance.
- **Isolation**: Duration of travel/comm lag.

### Step 3 — Apply Modifiers
Apply a `productivity_modifier` (e.g., 0.8x to 1.2x) based on `wellbeing_index` to existing production loops.

### Step 4 — Verify Specs
Add unit tests in `population_management_spec.rb` to ensure productivity modifiers scale correctly.

### Step 5 — Commit
Commit only the logic and tests. UI implementation is out of scope for this task.

---

## Acceptance Criteria
- [ ] Morale/wellbeing tracking active on populations
- [ ] Biophilia effects (Greenhouse) accounted for
- [ ] Productivity decline/retention mechanics active
- [ ] Isolation run: 0 failures

---

## Stop Conditions — escalate to user immediately if:
- Fix causes new failures in existing production loops
- Root cause for a logic error is in a shared concern or base class
- Any architectural decision is required (e.g., changing data structure for logs)

---

## Commit Instructions
```bash
git add galaxy_game/app/concerns/population_management.rb galaxy_game/spec/models/concerns/population_management_spec.rb
git commit -m "feat: add population morale and wellbeing system with retention mechanics"
git push
```

---

## Completion Report
**Completed by**: [agent]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures (excluding :152/:157)

### What was changed
- `[file]` — [description]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

---

## Handoff Summary
HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]