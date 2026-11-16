---
status: active
priority: MEDIUM
type: research
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

# TASK: AI Manager Decision-Logic Gap — Codebase Analysis

---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase05-luna-calibration/2026-08-08-MEDIUM-RESEARCH-AI-MANAGER-DECISION-LOGIC-GAP.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/phase05-luna-calibration/2026-08-08-MEDIUM-RESEARCH-AI-MANAGER-DECISION-LOGIC-GAP.md \
         projects/galaxy_game/tasks/active/2026-08-08-MEDIUM-RESEARCH-AI-MANAGER-DECISION-LOGIC-GAP.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-08-MEDIUM-RESEARCH-AI-MANAGER-DECISION-LOGIC-GAP.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

## Context

`LUNA-MVP-SIMULATION-DESIGN.md` (lines ~375-390) lists 6 "Outstanding Architecture Questions" about the AI Manager decision-logic layer. Claude's handoff narrowed these to 5 core questions. This task answers them against the real codebase — **no code changes, just analysis.**

The design doc states: *"The AI Manager decision-logic layer — 'given a location + intent, evaluate settlement options' — does not exist yet."* This task confirms that claim and maps what DOES exist vs what's missing.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/projects/galaxy_game/research/LUNA-MVP-SIMULATION-DESIGN.md` — Outstanding Architecture Questions section
- `data/json-data/missions_v2/mission_plans/luna_precursor_mission_plan_v2.json` — DAG execution order (if wired)

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: Analysis only — NO code changes**
- ❌ Wrong: Write models, services, or modify any files to fill gaps
- ✅ Right: grep/find the codebase, report what exists and what doesn't
- Why: This is a research task. Creating new code here would be scope creep.

⚠️ **GOTCHA 2: Report confidence levels**
- ❌ Wrong: Say "doesn't exist" without checking thoroughly
- ✅ Right: Report confidence (high/medium/low) for each answer — low confidence means the system might exist under an unusual name/interface
- Why: Hidden behind an unusual interface is still "exists" and changes the gap analysis.

---

## Problem Statement

`LUNA-MVP-SIMULATION-DESIGN.md` lists 6 outstanding architecture questions about the AI Manager decision-logic layer. This task answers them against the real codebase to confirm what's missing before Phase 7+ implementation begins.

---

## Research Questions

Answer each question by searching the codebase. Report findings with file paths, line numbers, and brief analysis.

### Q1: Does `ImportRequestGenerator` support multiple supply sources today, or Earth-only?

**What to check:**
- Find `ImportRequestGenerator` class (`grep -rn "class ImportRequestGenerator" app/ lib/`)
- Read the full class — does it have any concept of alternative suppliers (Venus skimmer, Titan delivery, local production)?
- Check if there's a `sources` array or supplier-priority mechanism anywhere in the class
- Check `Logistics::ImportRequestGenerator` specifically

**Report:** "Earth-only" with evidence, or list all supported sources with file/line references.

### Q2: Is inbound cargo/manifest tracking modeled anywhere?

**What to check:**
- Search for any model/service that tracks "craft X is inbound with payload Y, arriving in Z days"
- Look for: `InboundManifest`, `CargoManifest`, `InboundCraft`, `TransitTracking`, `ArrivalSchedule`
- Check `Logistics::Contract` — does it have arrival tracking?
- Check `AIManager::TaskExecutionEngineV2` — does it track mission phases with ETA?
- Search for any "inbound" or "transit" or "arrival" models/services

**Report:** What exists (if anything) with file/line references, or confirm nothing exists.

### Q3: Is precursor build sequencing modeled as a dependency graph anywhere?

**What to check:**
- The mission plan JSON (`luna_precursor_mission_plan_v2.json`) has a `dag_execution_order` array — is this used by any code?
- Search for `dag_execution_order`, `dependency_graph`, `build_sequence`, `precursor_scheduler` in app/lib
- Check if `TaskExecutionEngineV2` respects phase dependencies or just executes flat lists
- Check `AIManager::PrecursorBuildScheduler` (if it exists)

**Report:** Whether the DAG from the JSON is actually wired into runtime code, or if build sequencing is still a flat list.

### Q4: Is there a CH4/scarce-resource priority-queue arbitration mechanism?

**What to check:**
- Search for `CH4Allocation`, `ResourceArbitration`, `PriorityQueue`, `ScarceResource` in app/lib
- Check if the AI Manager has any logic for allocating scarce resources between competing demands
- Look at `StrategySelector` — does it have any allocation logic?
- Check `AIManager::MarketStabilizationService` — does it handle resource allocation?

**Report:** What exists (if anything) with file/line references, or confirm nothing exists.

### Q5: Are skimmers modeled as persistent assets with fuel state, location, and mission phase?

**What to check:**
- Search for `Skimmer`, `HarvesterCraft`, `AtmosphericHarvester` models/services
- Check if there's a `skimmers` table in migrations (`grep -rn "create_table.*skimmer" db/`)
- Check operational data — does any craft type have fuel state, location tracking, mission phase fields?
- Check `HeavyLiftTransport` or similar — is this the skimmer model?

**Report:** What exists (if anything) with file/line references, or confirm nothing exists.

---

## Acceptance Criteria
- [ ] All 5 research questions answered with file/line evidence
- [ ] Confidence level (high/medium/low) reported for each answer
- [ ] Summary lists systems that EXIST vs DO NOT EXIST
- [ ] No code changes made — analysis only
- [ ] Recommendations for next steps included

---

## Stop Conditions — escalate to user immediately if:
- Evidence found that a system DOES exist but is hidden behind an unusual interface — report the finding and stop researching that question
- Any architectural decision is required beyond answering the 5 questions

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add [summary file path]
git commit -m "research: AI Manager decision-logic gap analysis (5 questions answered against codebase)"
git push
```

**Task file move on completion:**
```bash
mv projects/galaxy_game/tasks/active/2026-08-08-MEDIUM-RESEARCH-AI-MANAGER-DECISION-LOGIC-GAP.md \
   projects/galaxy_game/tasks/completed/2026-08/2026-08-08-MEDIUM-RESEARCH-AI-MANAGER-DECISION-LOGIC-GAP.md
git add projects/galaxy_game/tasks/completed/2026-08/2026-08-08-MEDIUM-RESEARCH-AI-MANAGER-DECISION-LOGIC-GAP.md
git commit -m "chore: move 2026-08-08-MEDIUM-RESEARCH-AI-MANAGER-DECISION-LOGIC-GAP.md to completed/"
```

---

## Documentation
- [ ] Update `LUNA-MVP-SIMULATION-DESIGN.md` — mark Outstanding Architecture Questions as resolved with findings

---

## Dependencies
**Blocked by**: none — requires only codebase read access
**Blocks**: Phase 7+ implementation (needs gap analysis before design)
**Related tasks**: `2026-08-08-HIGH-FEATURE-LUNA-SIMULATION-BASELINE` (simulation baseline that exposed these gaps)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:

### Findings Summary
- **Systems that EXIST:** [list with file/line]
- **Systems that DO NOT EXIST (confirmed gap):** [list]

### Recommendations for next steps
-

---

## Handoff Summary
HANDOFF SUMMARY: [5 questions answered] | gap analysis complete | next: Phase 7 implementation planning
