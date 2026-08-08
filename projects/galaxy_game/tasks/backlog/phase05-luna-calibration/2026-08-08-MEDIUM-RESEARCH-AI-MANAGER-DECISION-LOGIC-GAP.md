# TASK: AI Manager Decision-Logic Gap — Codebase Analysis

**Task ID:** `2026-08-08-MEDIUM-RESEARCH-AI-MANAGER-DECISION-LOGIC-GAP`
**Date Created:** 2026-08-08
**Priority:** MEDIUM
**Type:** research
**System Domain:** AI_MANAGER
**MVP Alignment:** AI_MANAGER_LUNA_SETTLEMENT
**Local Worker Safe:** true

---

## Context

`LUNA-MVP-SIMULATION-DESIGN.md` (lines ~375-390) lists 6 "Outstanding Architecture Questions" about the AI Manager decision-logic layer. Claude's handoff narrowed these to 5 core questions. This task answers them against the real codebase — **no code changes, just analysis.**

The design doc states: *"The AI Manager decision-logic layer — 'given a location + intent, evaluate settlement options' — does not exist yet."* This task confirms that claim and maps what DOES exist vs what's missing.

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

## Stop Conditions

- **STOP** if you find evidence that any of these systems DO exist but are hidden behind an unusual interface — report the finding and stop researching that question.
- **DO NOT** write any code, create models, or modify any files. This is analysis only.

---

## Output Format

Return a structured report:

```
AI Manager Decision-Logic Gap Analysis
========================================

Q1: ImportRequestGenerator multi-source support
  Answer: [Earth-only / Multiple sources listed]
  Evidence: [file.rb:line — brief description]
  Confidence: [high/medium/low]

Q2: Inbound cargo/manifest tracking
  Answer: [Exists / Does not exist]
  Evidence: [file.rb:line or "no matches found"]
  Confidence: [high/medium/low]

Q3: Precursor build dependency graph
  Answer: [Wired into code / JSON-only / Not modeled]
  Evidence: [file.rb:line or "no matches found"]
  Confidence: [high/medium/low]

Q4: CH4/scarce-resource priority-queue arbitration
  Answer: [Exists / Does not exist]
  Evidence: [file.rb:line or "no matches found"]
  Confidence: [high/medium/low]

Q5: Skimmer persistent asset model
  Answer: [Exists / Does not exist]
  Evidence: [file.rb:line or "no matches found"]
  Confidence: [high/medium/low]

Summary:
  - Systems that EXIST: [list]
  - Systems that DO NOT EXIST (confirmed gap): [list]
  - Recommendations for next steps: [brief]
```

---

## References

- `LUNA-MVP-SIMULATION-DESIGN.md` — Outstanding Architecture Questions section
- `luna_precursor_mission_plan_v2.json` — DAG execution order (if wired)
- `AIManager::TaskExecutionEngineV2` — Mission execution engine
- `Logistics::ImportRequestGenerator` — Import request generation
- `StrategySelector` — AI Manager strategy selection

---

## Minimal Handoff Block

```
You are the Implementation Agent.

Project: galaxy_game
Task: Analyze codebase for 5 AI Manager decision-logic capabilities
Scope: grep/find analysis only — NO code changes, NO model creation
Return structured report per task specification above.
```
