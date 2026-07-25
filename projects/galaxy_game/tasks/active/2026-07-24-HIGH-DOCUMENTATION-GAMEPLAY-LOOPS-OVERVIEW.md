---
title: "Gameplay loops overview — exploration, terraforming, settlement, logistics, trading, combat"
priority: HIGH
status: backlog
phase: phase1
owner: Implementation Agent (Qwen)
type: documentation
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
created: 2026-07-24
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/drafts/2026-07-24/2026-07-24-HIGH-DOCUMENTATION-GAMEPLAY-LOOPS-OVERVIEW.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/drafts/2026-07-24/2026-07-24-HIGH-DOCUMENTATION-GAMEPLAY-LOOPS-OVERVIEW.md \
         projects/galaxy_game/tasks/active/2026-07-24-HIGH-DOCUMENTATION-GAMEPLAY-LOOPS-OVERVIEW.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-24-HIGH-DOCUMENTATION-GAMEPLAY-LOOPS-OVERVIEW.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-DOCUMENTATION-GAMEPLAY-LOOPS-OVERVIEW.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Gameplay loops overview documentation

**Status**: BACKLOG
**Priority**: HIGH
**Type**: documentation
**Created**: 2026-07-24
**Last Updated**: 2026-07-24

---

## Context

Galaxy game features multiple distinct gameplay loops: exploration, terraforming, settlement, logistics, trading, and combat. Players can focus on one loop without needing to engage with the others. Each loop has different entry points, mechanics, and progression systems. This documentation should explain each loop independently while clarifying how they interconnect for players who want to engage with multiple loops.

---

## Problem Statement

- Multiple gameplay loops exist but are undocumented as distinct systems
- No overview explaining that players can focus on a single loop without engaging others
- Entry points, mechanics, and progression for each loop are unclear
- Inter-loop connections (how loops feed into each other) are not documented

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: Gameplay loops span the entire codebase**
- ❌ Wrong: Only look at service files — gameplay loops involve models, controllers, views, and data files
- ✅ Right: Trace each loop from entry point (controller/action) through services to models to data
- Why: The loop's full behavior is distributed across the MVC stack

⚠️ **GOTCHA 2: Some loops are MVP-relevant, others are phase-deferred**
- ❌ Wrong: Document all loops as equally implemented
- ✅ Right: Clearly mark which loops are playable in current MVP vs. which are planned/prototype-only
- Why: Contributors need to know what's actually functional vs. what's aspirational

### Files to Audit (Read-Only)

| File/Directory | Purpose |
|---|---|
| `app/controllers/` | Entry points for each gameplay loop |
| `app/services/` | Core logic for each loop |
| `app/models/` | Data models used by loops |
| `data/json-data/gameplay/` or similar | Gameplay configuration data |
| `docs/new_agent/projects/galaxy_game/MVP.md` or status.md | MVP scope documentation |
| `README.md` (project root) | High-level game description |

---

## Implementation Steps

### Step 1 — Identify all gameplay loops

```bash
# List controllers to find entry points
find /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/controllers -name "*.rb" | sort

# Check for gameplay-related services
find /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/services -type d | sort
```

For each loop, identify:
- Entry point (controller/action or service entry method)
- Core mechanics (what the player does in this loop)
- Progression system (how players advance in this loop)
- MVP status (playable now / phase-deferred / prototype-only)

### Step 2 — Create gameplay loops overview doc

Create: `docs/new_agent/projects/galaxy_game/gameplay/gameplay_loops_overview.md`

Structure:
```markdown
# Gameplay Loops Overview

## Overview
[1-2 paragraph summary: galaxy game supports multiple independent gameplay loops]

## Key Principle
Players can focus on a single gameplay loop without needing to engage with others.
Loops are designed to be optional — each provides a complete, satisfying experience on its own.
Loops interconnect for players who want deeper engagement across systems.

## Loop 1: Exploration
### Entry Point
[Controller/action or service entry]

### Core Mechanics
- What the player does
- Key models/services involved
- Resources discovered/collected

### Progression
[How players advance in exploration]

### MVP Status
[Playable / Phase-deferred / Prototype-only]

### Inter-loop Connections
[How exploration feeds into other loops: terraforming, settlement, trading]

## Loop 2: Terraforming
### Entry Point
...

### Core Mechanics
...

### Progression
...

### MVP Status
...

### Inter-loop Connections
...

## Loop 3: Settlement
### Entry Point
...

### Core Mechanics
...

### Progression
...

### MVP Status
...

### Inter-loop Connections
...

## Loop 4: Logistics
### Entry Point
...

### Core Mechanics
...

### Progression
...

### MVP Status
...

### Inter-loop Connections
...

## Loop 5: Trading
### Entry Point
...

### Core Mechanics
...

### Progression
...

### MVP Status
...

### Inter-loop Connections
...

## Loop 6: Combat
### Entry Point
...

### Core Mechanics
...

### Progression
...

### MVP Status
...

### Inter-loop Connections
...

## Loop Dependency Matrix
| Loop | Requires Exploration | Requires Terraforming | Requires Settlement | Requires Logistics | Requires Trading | Requires Combat |
|---|---|---|---|---|---|---|
| Exploration | — | optional | optional | optional | optional | optional |
| Terraforming | recommended | — | optional | optional | optional | optional |
| Settlement | recommended | recommended | — | optional | optional | optional |
| Logistics | required | optional | optional | — | optional | optional |
| Trading | optional | optional | optional | required | — | optional |
| Combat | optional | optional | optional | optional | optional | — |

## MVP Scope Summary
[Which loops are playable in current MVP vs. which are planned]
```

### Step 3 — Verify

- [ ] All 6 loops documented (exploration, terraforming, settlement, logistics, trading, combat)
- [ ] Each loop has: entry point, core mechanics, progression, MVP status, inter-loop connections
- [ ] Loop dependency matrix accurately reflects code behavior
- [ ] MVP scope summary matches actual implementation status
- [ ] No speculative claims — every statement backed by code evidence

---

## Acceptance Criteria
- [ ] All 6 gameplay loops documented with entry points, mechanics, progression
- [ ] MVP status clearly marked for each loop
- [ ] Loop dependency matrix accurately reflects code behavior
- [ ] Inter-loop connections explained
- [ ] No speculative claims — every statement backed by code evidence

---

## Stop Conditions — escalate to user immediately if:
- Cannot determine entry point or core mechanics for a loop (implementation gap?)
- MVP status is unclear (no documentation indicating which features are in/out of scope)
- A migration is needed (unlikely for docs-only task)

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add docs/new_agent/projects/galaxy_game/gameplay/gameplay_loops_overview.md
git commit -m "docs: Gameplay loops overview — exploration, terraforming, settlement, logistics, trading, combat"
```

---

## Documentation
- [x] New doc created (gameplay loops overview)
- [ ] Update existing architecture doc — [path TBD after audit]
- [ ] Flag doc gap: [description if needed]

---

## Dependencies
**Blocked by**: none
**Blocks**: none directly (but benefits from AI Manager service inventory for terraforming/settlement references)
**Related tasks**: 2026-07-24-CRITICAL-DOCUMENTATION-AI-MANAGER-SERVICE-INVENTORY.md, 2026-07-24-HIGH-DOCUMENTATION-MANUFACTURING-CHAIN-OVERVIEW.md

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation]

### Follow-up tasks needed
[Any new backlog items identified]

### Lessons learned
[What worked, what didn't]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
