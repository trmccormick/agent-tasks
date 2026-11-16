# TASK: Inventory existing mission profiles for post-Luna macro build order stages

**Task ID:** `2026-08-08-MEDIUM-RESEARCH-INVENTORY-POST-LUNA-MISSION-PROFILES`
**Date Created:** 2026-08-08
**Priority:** MEDIUM
**Type:** research
**Status:** completed
**Completed Date:** 2026-08-08
**System Domain:** MISSION_PLANNING
**MVP Alignment:** AI_MANAGER_LUNA_SETTLEMENT (informs Phase 6+ task staging)
**Local Worker Safe:** true

---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase09-sol-expansion/2026-08-08-MEDIUM-RESEARCH-INVENTORY-POST-LUNA-MISSION-PROFILES.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/galaxy_game/tasks/backlog/phase09-sol-expansion/2026-08-08-MEDIUM-RESEARCH-INVENTORY-POST-LUNA-MISSION-PROFILES.md \
         projects/galaxy_game/tasks/active/2026-08-08-MEDIUM-RESEARCH-INVENTORY-POST-LUNA-MISSION-PROFILES.md

Then open the moved file and change: status: backlog → status: active
Paste the output of both commands in chat before proceeding.
Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-08-MEDIUM-RESEARCH-INVENTORY-POST-LUNA-MISSION-PROFILES.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-RESEARCH-INVENTORY-POST-LUNA-MISSION-PROFILES.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

## Context

The confirmed macro build order is: **Earth → Luna → L1 → Mars → Phobos/Deimos (shipyard conversion) → asteroid belt (tug relocation) → Venus station → cycler network**.

Mission profiles/plans may already exist from past design work for stages beyond Luna. This task does NOT design anything new — it inventories what exists so future task-staging work doesn't duplicate existing design.

---

## Research Scope

Search the following directories for profile, plan, or phase files referencing: Mars, Phobos, Deimos, asteroid belt, tug relocation, Venus station/skimmer, cycler operations, L1/LEO depot, Psyche.

### Directories to Search (Active — NOT old-code)
| Directory | What It Contains |
|---|---|
| `data/json-data/missions/tasks_v2/` | Luna precursor tasks only (already known) |
| `data/json-data/missions/quests/` | Unknown — check contents |
| `data/json-data/missions/miranda-mining-v2/` | Miranda (Uranus moon) mining |
| `data/json-data/missions/super-mars-relocation/` | Phobos/Deimos relocation ✅ |
| `data/json-data/missions/psyche_mining_hub/` | Psyche mining hub ✅ |
| `data/json-data/missions/tasks/` | Multiple sub-tasks — check each |
| `data/json-data/missions_v2/profiles/` | Luna base profile v2 only (already known) |
| `data/json-data/missions_v2/mission_plans/` | Luna precursor plan v2 only (already known) |
| `data/json-data/missions_v2/phases/` | Luna phases only (already known) |
| `data/json-data/missions_v2/tasks/` | Luna tasks only (already known) |
| `data/json-data/missions_v2/migration_studies/venus_foundry/` | Venus foundry migration study ✅ |

### Directories to Search (Old Code — for historical reference only)
| Directory | What It Contains |
|---|---|
| `data/old-code/galaxyGame-01-08-2026/galaxy_game/data/json-data/missions/mars_settlement/` | Mars settlement profiles ✅ |
| `data/old-code/galaxyGame-01-08-2026/galaxy_game/data/json-data/missions/venus_settlement/` | Venus settlement profiles ✅ |
| `data/old-code/galaxyGame-01-08-2026/galaxy_game/data/json-data/missions/titan-resource-hub/` | Titan resource hub ✅ |
| `data/old-code/galaxyGame-01-08-2026/galaxy_game/data/json-data/missions/old_mission_ideas/` | Various old mission ideas ✅ |

---

## Research Steps

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP)

Follow the Minimal Handoff block above. Paste outputs in chat before proceeding.

### Step 1 — Inventory Active Missions Directory

For each directory listed above under "Directories to Search (Active)", run:
```bash
find /Users/tam0013/Documents/git/galaxyGame/data/json-data/missions/[DIR] -type f 2>/dev/null
```

Report every file found with its full path. For files that are JSON profiles/plans/phases, read the first 20 lines to determine:
- What celestial body it targets (Mars, Venus, Psyche, etc.)
- Maturity level: profile-only, profile+plan, or full profile+plan+phases

### Step 2 — Inventory Old Code Missions (Historical Reference)

For each directory under "Directories to Search (Old Code)", run:
```bash
find /Users/tam0013/Documents/git/galaxyGame/data/old-code/galaxyGame-01-08-2026/galaxy_game/data/json-data/missions/[DIR] -type f 2>/dev/null
```

Report every file found. These are historical — not actionable for current staging, but useful context for what was designed before.

### Step 3 — Cross-Reference Against Build Order

Map findings against the confirmed macro build order:

| Build Stage | What to Look For | Found? (Y/N) | Location |
|---|---|---|---|
| **L1 Depot** | L1 station construction, orbital depot profiles | | |
| **Mars** | Mars settlement, Mars orbital establishment, terraforming phases | | |
| **Phobos/Deimos** | Asteroid relocation, shipyard conversion profiles | | |
| **Asteroid Belt** | Tug relocation, asteroid capture/conversion profiles | | |
| **Venus Station** | Venus harvester, Venus station construction, atmospheric operations | | |
| **Cycler Network** | Cycler establishment, interplanetary transit plans | | |
| **Psyche** | Psyche mining hub profiles | | |

### Step 4 — Report Maturity Per Stage

For each stage where files were found, report maturity:
- **profile-only**: Has a profile JSON but no plan or phases
- **profile+plan**: Has profile + mission_plan JSON
- **full**: Has profile + plan + phases (the Luna precursor mission is the reference for "full")

### Step 5 — Identify Gaps

For each stage where NO files were found, note it as genuinely missing/undesigned.

---

## Acceptance Criteria
- [ ] Every active missions directory listed above has been searched and reported
- [ ] Every old-code missions directory listed above has been searched and reported
- [ ] Cross-reference table completed for all 7 build stages (L1, Mars, Phobos/Deimos, Asteroid Belt, Venus Station, Cycler Network, Psyche)
- [ ] Maturity level reported per stage where files exist
- [ ] Gaps clearly identified — what's genuinely missing/undesigned
- [ ] No new designs created — this is inventory only

---

## Stop Conditions
- None — this is read-only discovery, safe to run to completion
- If a directory doesn't exist, note it as "directory not found" rather than skipping

---

## Output Format

Return a structured report:

```
Post-Luna Mission Profile Inventory Report
============================================

ACTIVE MISSIONS DIRECTORY INVENTORY
------------------------------------
[data/json-data/missions/tasks_v2/]
  - [file path] — [brief description from first 20 lines, or "Luna tasks (known)"]
...

[data/json-data/missions/quests/]
  - [file path] — [description]
...

[repeat for each directory]

CROSS-REFERENCE AGAINST BUILD ORDER
------------------------------------
| Build Stage | Found? | Location(s) | Maturity |
|---|---|---|---|
| L1 Depot | Y/N | [paths or "none"] | [profile-only/profile+plan/full/none] |
| Mars | Y/N | [paths or "none"] | [profile-only/profile+plan/full/none] |
| Phobos/Deimos | Y/N | [paths or "none"] | [profile-only/profile+plan/full/none] |
| Asteroid Belt | Y/N | [paths or "none"] | [profile-only/profile+plan/full/none] |
| Venus Station | Y/N | [paths or "none"] | [profile-only/profile+plan/full/none] |
| Cycler Network | Y/N | [paths or "none"] | [profile-only/profile+plan/full/none] |
| Psyche | Y/N | [paths or "none"] | [profile-only/profile+plan/full/none] |

GAPS (No Files Found)
----------------------
- [Stage]: [description of what's missing]

HISTORICAL REFERENCE (Old Code Only)
-------------------------------------
[data/json-data/missions/mars_settlement/] — 3 profiles, 1 manifest, 4 phases
[data/json-data/missions/venus_settlement/] — 2 profiles, 2 phases
...

SUMMARY
-------
- Total active mission files found: [count]
- Stages with full design (profile+plan+phases): [list]
- Stages with partial design (profile-only or profile+plan): [list]
- Stages with no design at all: [list]
```

---

## Dependencies
**Blocked by**: none
**Blocks**: Phase 6+ task staging work (informs what to build vs. what already exists)
**Related tasks**: None

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was found
- [Summary of findings]

### Issues discovered
[Any problems found during research that weren't anticipated]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files inventoried] | [gaps identified] | [next action needed]
