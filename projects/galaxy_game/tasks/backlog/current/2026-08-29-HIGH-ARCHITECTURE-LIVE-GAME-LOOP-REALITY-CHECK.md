---
status: backlog
priority: HIGH
type: architecture
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate (no placeholders) — `[project]` = `galaxy_game`, `[SUBFOLDER]` = `current` (verified 2026-08-29)
- [x] All Step 0-N instructions are clear and actionable (not vague)
- [x] Synthesis report template is provided (copy/paste ready, not as example) — adapted for a research task (no code fix to propose)
- [x] No placeholder text remains in Implementation Steps — N/A, this is research-only, section adapted accordingly
- [x] All file paths are verified to exist — **VERIFIED 2026-08-29** (see Files Involved table; all primary + reference paths confirmed on disk)
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is READY FOR DISPATCH.** All placeholders resolved and all file paths verified against the live filesystem on 2026-08-29.

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-29-HIGH-ARCHITECTURE-LIVE-GAME-LOOP-REALITY-CHECK.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  NOTE: This file is currently UNTRACKED in git (verified 2026-08-29). Use the
  "New/untracked file" path below, NOT git mv.
  mv projects/galaxy_game/tasks/backlog/current/2026-08-29-HIGH-ARCHITECTURE-LIVE-GAME-LOOP-REALITY-CHECK.md \
     projects/galaxy_game/tasks/active/2026-08-29-HIGH-ARCHITECTURE-LIVE-GAME-LOOP-REALITY-CHECK.md
  git add projects/galaxy_game/tasks/active/2026-08-29-HIGH-ARCHITECTURE-LIVE-GAME-LOOP-REALITY-CHECK.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path  ← THIS FILE (untracked as of 2026-08-29)
  - Never leave stale copies in the source folder
  - Verify with: find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks -name "2026-08-29-HIGH-ARCHITECTURE-LIVE-GAME-LOOP-REALITY-CHECK.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Live Game-Loop Reality Check (Foundation for Real Integration Testing)
**Status**: BACKLOG
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-08-29
**Last Updated**: 2026-08-29

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*

- **Template Conformance**: PASS (verified 2026-08-29 against TASK_TEMPLATE.md) — YAML frontmatter, readiness checklist, Agent Dispatch Interface immediately after frontmatter, triage report, agent assignment, prerequisites, context, gotchas, synthesis report, problem statement, files involved, implementation steps, acceptance criteria, stop conditions, commit instructions, documentation, dependencies, completion report, and handoff summary all present. Research-task adaptations (no credentials, no migration, no code-fix synthesis) are explicitly noted where the template's implementation-oriented sections don't apply.
- **Docker Wrapper Check**: N/A — no RSpec commands in this task, research only
- **MVP Alignment**: VALID — directly gates whether further Luna/Venus MVP work can be trusted as integration-verified rather than isolated-script-verified
- **MVP Impact Note**: Answers whether a real live game loop exists at all; everything downstream (event log, admin UI status view, ice-mining/skimmer fixes) depends on this
- **Action Line**: READY FOR LOCAL DISPATCH — placeholders resolved, all file paths verified on disk 2026-08-29

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Read-only research task, well within local Qwen's terminal/grep access — no cloud escalation needed unless local Qwen genuinely cannot locate the relevant code
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully (first dispatch of this task)

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context
Every validated piece of Luna/Venus/GCC work to date — the Luna precursor mission rake (`luna_mission:execute`), the GCC mining satellite rake (`orbital_mining:gcc_sat`), and the Venus skimmer design/logic — has been tested as an isolated mechanical step: a rake script or RSpec suite standing in for real game time, not the live game itself advancing these assets. Nothing has been observed running end-to-end through whatever actually advances the game — launch, transit tracking, arrival, deployment, build-out, and resource accumulation all happening together, driven by real game time.

This task is the gate for planned follow-on work: a real-time event log (text output plus an admin UI / game-status section showing what the AI Manager is doing — commands issued, routine loops like the Venus skimmer's cycle, milestones like landings and orbit insertions) and, after that, fixes to ice-mining/skimmer mechanics. None of that follow-on work should be scoped or built until this task's questions are answered.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- Any existing doc on `LunaOperationsSimulationService` / `luna_operations_simulation.rake` — confirm whether it's documented as a standalone simulation (per this task's Gotcha 1) or something more

> If a doc doesn't exist for this area, do not create one during this task. Flag the gap in your completion report instead.

---

## Critical Information for This Task

### Credentials
N/A — no external systems, read-only codebase research.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Don't count `luna_operations_simulation.rake`'s daily-tick loop as evidence of a real live game loop.
- ❌ Wrong: "The daily-tick loop in `LunaOperationsSimulationService` proves the game ticks assets automatically."
- ✅ Right: Confirm whether that service is invoked by anything other than its own rake task — if it's only ever called by `luna:simulate_operations`, it's a bespoke standalone simulation, not the live game's own mechanism.
- Why: This exact conflation is the root confusion this task exists to resolve — treating a test harness as if it were the production mechanism.

⚠️ **GOTCHA 2**: A "tests pass" or "this works" claim from a prior session/handoff is not sufficient evidence on its own.
- ❌ Wrong: Citing an earlier status.md or handoff doc's claim that a mechanism "works" as the answer to any of the questions below.
- ✅ Right: Confirm live against current code with file:line references, regardless of what any earlier handoff or status doc claims.
- Why: Standing project guardrail — recurring pattern of claims not matching actual repo state.

⚠️ **GOTCHA 3**: The Venus skimmer's operational sequence is not one event.
- ❌ Wrong: Treating "skimmer arrives at Venus" and "skimmer produces gases" as the same moment/state.
- ✅ Right: Recognize the real sequence — arrival, transferring carried-in methane (return-trip fuel) from a transit tank into the main tank (emptying the transit tank first before it can be used for skimming), beginning atmospheric intake (Venus atmosphere is ~96.5% CO2, ~3.5% N2 per sol-complete.json), onboard CO2→CO+O2 processing (O2 used as LOX for the skimmer's own return trip), departure-readiness.
- Why: Question 4 and 6 below depend on knowing these are distinct sub-states, not a single "skimmer active" flag.

---

## 🟡 MANDATORY: Status Synthesis Report (Post to Chat BEFORE Any Work)

Adapted for a research task — post this after initial reconnaissance, before writing the final findings doc.

```
STATUS SYNTHESIS REPORT

Task: Live Game-Loop Reality Check
What I found so far (file:line for each):
- Q1 (Game#advance_by_days side effects): [in progress / found at X:Y / not found]
- Q2 (scheduled job/worker ticking assets): [in progress / found at X:Y / not found]
- Q3 (craft transit state machine): [in progress / found at X:Y / not found]
- Q4 (asset wiring — precursor craft / GCC sat / Venus skimmer): [in progress]
- Q5 (PSR ice mining / deposit spawning): [in progress]
- Q6 (raw vs processed gas storage distinction): [in progress]

### Critical Gotchas I Will Avoid
- ❌ Citing luna_operations_simulation.rake's loop as proof of a real game loop — instead ✅ checking what else calls the service it wraps
- ❌ Trusting a prior handoff's "this works" claim — instead ✅ confirming live with file:line

---

**SYNTHESIS COMPLETE.** Ready to proceed with full research pass.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start the full research pass until this is posted.

---

## Problem Statement
Determine whether a real live-tick mechanism exists that processes deployed assets over game time, and what is or isn't currently wired into it — as opposed to only ever being driven by bespoke rake/test scripts that re-implement the behavior externally.

**Current behavior**: Unknown/unconfirmed — this task exists because no one has verified this directly.
**Expected behavior**: A clear, evidenced answer (yes/no + file:line, or confirmed absence) to each of the six questions below.

---

## Questions to Answer (with file:line evidence for each)

1. Does `Game#advance_by_days` (or any equivalent) have real side effects — does it walk deployed assets/settlements and process them — or is it just a time counter with no asset-processing attached?
2. Is there a scheduled job, worker, or service (check `config/schedule.rb`, any `*_job.rb` classes, cron-style tasks) that ticks all live assets in the background, independent of a rake script explicitly calling it?
3. Is craft transit (launch → in-transit position/state over real time → arrival) modeled anywhere as an actual state machine advancing with game time, or is "arrival" currently just an instantaneous state flip in test/rake scripts?
4. For each of: the Luna precursor mission craft, the GCC mining satellite, and the Venus skimmer — is it actually wired into whatever real mechanism is found in Q1/Q2, or does it only behave correctly when a bespoke script drives it directly?
5. Does PSR (permanently shadowed region) ice mining and deposit spawning on Luna currently exist and function in any form?
6. Does the Inventory/storage model already distinguish a raw/mixed-gas category from a processed-gas category generally — confirmed existing pattern: Luna's TEU harvests raw regolith into a "Mixed Volatiles" pile, PVE separately processes that into O2 (+He3/H2), a distinct storage category. Does the Venus skimmer's tank `operational_data` make the same distinction between raw atmospheric intake and processed output (CO2 → CO + O2 onboard) — or is this distinction only implemented for the Luna regolith case so far?

---

## Files Involved

### Primary Files — read/investigate, do not edit (this is research-only)
> All paths verified to exist on disk 2026-08-29. Paths are relative to the galaxyGame repo root (`/Users/tam0013/Documents/git/galaxyGame/`).

| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/app/models/game.rb` | Game time-advance model — CONFIRMED exists | `#advance_time` (line 25), `#advance_by_days` (line 42) |
| `galaxy_game/app/jobs/` (directory) | 20 job classes — CONFIRMED exists. Notable tick candidates: `game_simulation_job.rb` (Sidekiq, calls `Game#advance_by_days`), `location_operations_job.rb`, `location_operations_coordinator_job.rb`, `terra_sim_job.rb`, `idle_terra_sim_job.rb`, `cycler_scheduler_job.rb`, `satellite_mining_scheduler_job.rb`, `mine_gcc_job.rb` | Identify which (if any) tick deployed assets independently of rake |
| `galaxy_game/config/schedule.rb` | Cron-style scheduled task definitions — CONFIRMED exists (many entries currently commented out) | N/A |
| `galaxy_game/app/models/craft/` (directory) | Craft models — CONFIRMED exists: `base_craft.rb`, `ship.rb`, `spaceship.rb`, `harvester.rb`, `rover.rb`, `satellite/`, `transport/` (incl. `transport/cycler.rb` — the only file matching transit/arrival keywords so far) | Locate transit/state-machine logic if it exists |
| `galaxy_game/app/services/luna_operations_simulation_service.rb` | CONFIRMED exists; paired with `galaxy_game/lib/tasks/luna_operations_simulation.rake` (task `luna:simulate_operations`) | Confirm scope — standalone sim vs. wired into something larger |
| `galaxy_game/app/models/inventory.rb` | CONFIRMED exists | Confirm raw-vs-processed gas category handling |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `data/json-data/star_systems/sol-complete.json` (Venus entry) — CONFIRMED exists | Atmosphere composition (96.5% CO2, 3.5% N2) already confirmed — use as ground truth, don't re-derive |
| Existing TEU/PVE unit blueprint + operational_data files | Confirmed precedent for raw ("Mixed Volatiles") vs processed (O2) storage split — use as the comparison baseline for Q6 |

### Migration
- [x] No migration needed — research/read-only task

---

## Implementation Steps

> This is a research task — "implementation" here means investigation, not code changes.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
(See Agent Dispatch Interface above — same procedure applies.)

### Step 1 — Investigate Q1 and Q2 (game loop existence)
Grep/read `Game` model and `app/jobs/` for any tick-processing logic. Confirm with file:line whether `advance_by_days` (or equivalent) does anything beyond incrementing a counter, and whether any background job independently processes assets.

### Step 2 — Investigate Q3 and Q4 (transit modeling and asset wiring)
Trace how craft state changes from launched → arrived in existing code (precursor craft, GCC sat, Venus skimmer). Confirm whether this is a real advancing state or an instant flip triggered only by rake/test code.

### Step 3 — Investigate Q5 (PSR ice mining)
Search for PSR/ice-mining/deposit-spawning logic on Luna. Confirm current state: exists and functions, exists but stubbed, or does not exist.

### Step 4 — Investigate Q6 (raw vs processed gas storage)
Compare TEU/PVE's Mixed-Volatiles-vs-O2 storage pattern against the Venus skimmer's tank `operational_data` structure. Confirm whether the same category distinction exists for the skimmer or is Luna-regolith-specific.

### Step 5 — Write findings document (no code changes)
Produce a findings doc (not a code fix) with file:line evidence for all six questions, following the Synthesis Report shape above but as the final deliverable.

---

## Acceptance Criteria
- [ ] Evidenced answer to all 6 questions above, each with file:line references or an explicit statement that none was found (with what was searched documented)
- [ ] No code changes made as part of this task
- [ ] Findings saved as a synthesis/summary MD file per the dispatch interface's summaries-folder instruction

---

## Stop Conditions — escalate to user immediately if:
- All 6 questions are answered — stop and report back; do not proceed into building anything (event log, loop mechanism, ice-mining/skimmer fixes) in the same pass, even if the answers make a fix look obvious or trivial
- Any question requires more than reasonable read-only investigation to answer (e.g., would require running the app in a way that risks state changes) — escalate rather than guess
- Findings contradict something asserted as settled in an existing design doc — flag the contradiction, don't silently resolve it

---

## Commit Instructions
No code commits — this is a research task. Only the findings/synthesis MD file and the task-file move (backlog → active → completed) require git actions, per the Agent Dispatch Interface and standard task lifecycle.

---

## Documentation
- [ ] No doc changes needed as part of this task itself
- [ ] Flag doc gap: if no doc currently describes the actual tick/loop mechanism (or its absence), note this as a backlog item — do not create the doc during this task

---

## Dependencies
**Blocked by**: none
**Blocks**: Event log / admin UI game-status view (not yet filed); ice-mining/PSR mine fixes (not yet filed); Venus skimmer mechanics work (not yet filed) — all of these are deliberately held pending this task's findings
**Related tasks**: escalation-service design work (`2026-08-24-ESCALATION-SERVICE-DESIGN-CLARIFICATIONS.md` handoff) — related but does not answer this task's questions

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was found
- Q1: [finding + file:line]
- Q2: [finding + file:line]
- Q3: [finding + file:line]
- Q4: [finding + file:line]
- Q5: [finding + file:line]
- Q6: [finding + file:line]

### Issues discovered
[Any problems found during investigation that weren't anticipated]

### Follow-up tasks needed
[List candidate next tasks — event log/admin UI, ice-mining fix, skimmer wiring — do not create the files, just list them here for Claude/Tracy to scope]

### Lessons learned
[What worked, what didn't, what future research tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [findings doc created] | [key discovery: real loop exists / does not exist] | [next action needed]
