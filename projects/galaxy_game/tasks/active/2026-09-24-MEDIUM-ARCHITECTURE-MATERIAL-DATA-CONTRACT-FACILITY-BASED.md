---
status: active
priority: MEDIUM
type: architecture
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/ai-manager/2026-09-24-MEDIUM-ARCHITECTURE-MATERIAL-DATA-CONTRACT-FACILITY-BASED.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/ai-manager/2026-09-24-MEDIUM-ARCHITECTURE-MATERIAL-DATA-CONTRACT-FACILITY-BASED.md \
         projects/galaxy_game/tasks/active/2026-09-24-MEDIUM-ARCHITECTURE-MATERIAL-DATA-CONTRACT-FACILITY-BASED.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-24-MEDIUM-ARCHITECTURE-MATERIAL-DATA-CONTRACT-FACILITY-BASED.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-ARCHITECTURE-MATERIAL-DATA-CONTRACT-FACILITY-BASED.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Material Data Contract — Facility-Based (No Location-Keyed Sourcing)
**Status**: BACKLOG  
**Priority**: MEDIUM  
**Type**: architecture  
**Created**: 2026-09-24  
**Last Updated**: 2026-09-24 (post-Qwen proof: Step 0/1 path clarity)  

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: To be confirmed by Qwen proof
- **Docker Wrapper Check**: N/A for architecture-only deliverable
- **MVP Alignment**: VALID — materials must stay world-agnostic for Luna training and procedural bodies
- **MVP Impact Note**: Closes the remaining gap from the superseded Material Sourcing task (data contract only)
- **Action Line**: NEEDS MANUAL REVIEW before dispatch

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Terminal access for JSON sample grep and path verification
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: agent-tasks README.md (EXECUTOR Role section)
2. **Project Guide**: projects/galaxy_game/README.md
3. **This Task File**: Everything below
4. **Prior art (read-only)**:
   - `summaries/2026-09-16-ARCHITECTURE-PRE-PLAYER-ACQUISITION-DECISION-TREE.md` — acquisition routing **done**
   - Pre-player tree **wired** on EscalationService → ResourceAcquisitionService (completed task 2026-09-17)
   - `Market::NpcPriceCalculator.evaluate_strategy` — sole strategy-cost API
   - Superseded: `2026-09-03-MEDIUM-ARCHITECTURE-MATERIAL-SOURCING-AND-ACQUISITION-LOGIC.md` — **do not implement that file**

> Agent MUST read in this order. Do not skip. Synthesis report goes to summaries/ BEFORE starting work.

---

## Context

**Acquisition routing is no longer this task’s job.** It is already defined and implemented for the pre-player phase:

1. Stockpile / inventory sufficient? → done  
2. Local production / harvest if capability exists  
3. Normal shortage → prefer cycler / resupply wait  
4. Emergency → force local if possible, else continue  
5. Last resort → import via `evaluate_strategy`  

**Owner:** EscalationService (decision) → ResourceAcquisitionService (execution).  
**Not owner:** ProcurementService (Path A market side remains non-canonical).

**This task only covers the material data contract:** what JSON (and related lookups) must provide so local-production and cost baselines work on any body without location-keyed sourcing blocks.

### Why the 2026-09-03 task is superseded

That file mixed (a) facility-based material shape with (b) a full acquisition decision tree centered on ProcurementService and player-first defaults. (b) is obsolete for early game and conflicted with the locked pre-player tree. Remaining valuable work is (a) only — this task.

### Anti-pattern (forbidden)

Material JSON must **not** carry location-keyed sourcing blocks:

```json
"sourcing": {
  "lunar": { "availability": "very_high", "in_situ": true },
  "martian": { ... },
  "earth": { ... }
}
```

Materials are **passive** (what + how to produce + baseline economics). Routing is **active** (AI Manager at need-time — already owned by EscalationService / ResourceAcquisitionService).

### Canonical material shape (target)

```json
{
  "production": {
    "facility_type": "chemical_synthesis_plant",
    "input_materials": [],
    "energy_kwh_per_kg": 8.5,
    "production_time_hours": 0.5
  },
  "cost_data": {
    "purchase_cost": { "amount": 10000 },
    "import_config": { "transport_category": "standard" }
  },
  "pricing": {
    "local_production": {
      "facility_required": "chemical_synthesis_plant",
      "cost_per_kg": 7500,
      "energy_kwh_per_kg": 8.5
    }
  }
}
```

- No per-body sourcing keys  
- Earth baseline in `cost_data.purchase_cost` when present  
- Local production economics via facility requirement (works anywhere the facility exists)  
- Prefer `local_production` (facility-based) over body-named keys like `lunar_production`  

Exact key names may already vary in repo samples — **document actual vs target** and recommend a migration direction; do not mass-edit all JSON in this task unless a single exemplar update is approved.

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: Do not re-derive acquisition routing**
- ❌ Wrong: Rewrite the decision tree, wire ProcurementService as owner, or implement player-first defaults
- ✅ Right: Point at the locked pre-player tree + evaluate_strategy; only define material data
- Why: Routing is implemented; duplicating it creates drift

⚠️ **GOTCHA 2: Materials do not own routing**
- ❌ Wrong: Add `sourcing.lunar/martian/earth` (or similar) to material JSON  
- ✅ Right: Recipe + facility + baseline economics only  
- Why: Same material must work on unknown procedural worlds  

⚠️ **GOTCHA 3: No full JSON migration in this task**
- ❌ Wrong: Rewrite every file under `data/json-data/resources/materials/`  
- ✅ Right: Contract + short audit list of offenders; optional one exemplar if human approves  
- Why: Keep architecture dispatchable  

⚠️ **GOTCHA 4: Pricing multipliers are not hard rules in material JSON**
- ❌ Wrong: Encode EAP × 0.8/0.9 as permanent material fields that acquisition must obey  
- ✅ Right: Baseline costs in data; strategy cost from `evaluate_strategy`; seeds/bands are economy concerns  
- Why: Economy subsystem may replace EAP bootstrap  

⚠️ **GOTCHA 5: Pre-player vs post-player**
- ❌ Wrong: Require player market fields for materials to be “valid” in early game  
- ✅ Right: Facility + production + baseline cost are enough for pre-player local/import branches  
- Why: Players enter only after AWS network  

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Save as MD file to summaries/ (do NOT paste full report in chat):

```markdown
## STATUS SYNTHESIS REPORT

**Task**: 2026-09-24-MEDIUM-ARCHITECTURE-MATERIAL-DATA-CONTRACT-FACILITY-BASED
**Status**: backlog → active
**Date**: YYYY-MM-DD

### What I'm About to Do
Document the facility-based material data contract, forbid location-keyed sourcing, sample current JSON vs target shape, list offenders, and explicitly defer acquisition routing to the completed pre-player tree.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| Sample material JSON (e.g. epoxy_resin or processed materials) | Actual shape | pending |
| summaries/2026-09-16 pre-player architecture | Routing already done | pending |
| 2026-09-03 Material Sourcing task | Superseded — do not implement | pending |

### Prerequisites Completed
- ✅ Step 0 done
- ✅ Read this task and prior art
- ✅ Understand routing is out of scope

### Expected Outcomes
- Written material data contract (target shape + forbidden patterns)
- Short audit list of location-keyed or non-conforming files
- Explicit non-goals (no routing rewrite, no full migration)
- Note that 2026-09-03 is superseded by this task + pre-player work

### Critical Gotchas I Will Avoid
- ❌ Re-implement acquisition tree — instead ✅ data contract only
- ❌ Location-keyed sourcing — instead ✅ facility-based only
- ❌ Mass JSON rewrite — instead ✅ audit + contract

---
**SYNTHESIS COMPLETE.** Ready to proceed.
```

---

## Problem Statement

Material data still risks location-keyed sourcing patterns and inconsistent production/cost shapes. Acquisition routing is already owned by the pre-player tree; what remains is a clear **data contract** so local production and baselines work on any body.

**Current risk**: Dead or legacy `sourcing.lunar/martian/earth` patterns; unclear which JSON fields local-production branches should read.  
**Expected behavior**: Documented facility-based contract; short offender list; no new acquisition owner or player-first routing in this task.

---

## Files Involved

### Primary deliverable
| Deliverable | Purpose |
|---|---|
| Synthesis under `summaries/` (required) | Contract, audit list, non-goals, relation to pre-player tree |

### Reference — read; optional single exemplar edit only if approved mid-task
| File / area | Why |
|---|---|
| `data/json-data/resources/materials/` (relative to galaxy game app root; processed and related) | Sample actual shapes; list location-keyed offenders |
| One canonical manufactured material (e.g. epoxy_resin path when present) | Exemplar |
| `summaries/2026-09-16-ARCHITECTURE-PRE-PLAYER-ACQUISITION-DECISION-TREE.md` | Routing authority |
| Superseded task `2026-09-03-MEDIUM-ARCHITECTURE-MATERIAL-SOURCING-AND-ACQUISITION-LOGIC.md` | Do not implement |

### Migration
- [x] No DB migration  
- [ ] Full JSON migration **out of scope** (audit only unless human approves one exemplar)

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0. Write STATUS SYNTHESIS REPORT to summaries/.

### Step 0 — Move task to active/ and update status (MANDATORY)

```bash
git mv projects/galaxy_game/tasks/backlog/ai-manager/2026-09-24-MEDIUM-ARCHITECTURE-MATERIAL-DATA-CONTRACT-FACILITY-BASED.md \
       projects/galaxy_game/tasks/active/2026-09-24-MEDIUM-ARCHITECTURE-MATERIAL-DATA-CONTRACT-FACILITY-BASED.md
```

Then open the moved file and change: `status: backlog` → `status: active`.

Verify one copy via find; paste output in chat:

```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-09-24-MEDIUM-ARCHITECTURE-MATERIAL-DATA-CONTRACT-FACILITY-BASED.md"
```

### Step 1 — Sample current material JSON

Run from the **galaxyGame / app data root** (materials live under `galaxy_game/data/...` or `data/...` depending on cwd). Paths below are relative to the app data root — no agent-tasks adjustment needed for JSON greps.

```bash
# From galaxy game app root (where data/json-data exists)
grep -rn '"sourcing"' data/json-data/resources/materials/ 2>/dev/null | head -40
grep -rn 'lunar_production\|martian_production\|"lunar"\|facility_type\|local_production' data/json-data/resources/materials/ 2>/dev/null | head -40
```

Record 2–3 representative files: conforming vs non-conforming.

### Step 2 — Write the data contract in the synthesis

Include:
- Target shape (production / cost_data / pricing.local_production or equivalent actual keys)
- Forbidden location-keyed sourcing
- How local branch of the pre-player tree should read facility + inputs (conceptual — no live-loop rewrite required)
- How baseline purchase/import cost relates to `evaluate_strategy` (data feeds pricing; acquisition does not hard-code EAP multipliers in material JSON)

### Step 3 — Audit list

Table of files (or patterns) that still use location-keyed sourcing or body-named production keys. Mark: defer migration / exemplar only.

### Step 4 — Explicit non-goals

State in synthesis:
- No reimplementation of acquisition decision tree  
- No ProcurementService as owner  
- No player-first / buy-order design  
- No full materials JSON rewrite  
- 2026-09-03 task is **superseded** by this contract + completed pre-player work  

### Step 5 — Completion

Fill Completion Report. Do not commit until human approval. Optionally add one line to the 2026-09-03 task file header comment “SUPERSEDED by 2026-09-24-…” only if instructed.

---

## Acceptance Criteria

- [ ] Synthesis report exists under summaries/ with required filename pattern  
- [ ] Facility-based target shape documented  
- [ ] Location-keyed sourcing explicitly forbidden  
- [ ] Short audit list of non-conforming materials (or “none found” with grep evidence)  
- [ ] Acquisition routing deferred to pre-player tree + evaluate_strategy (not redesigned here)  
- [ ] 2026-09-03 Material Sourcing task noted as superseded  
- [ ] No mass JSON migration performed without explicit approval  
- [ ] No new acquisition service proposed  

---

## Stop Conditions — escalate to user immediately if:

- Pressure to re-wire EscalationService / ResourceAcquisitionService acquisition tree  
- Pressure to implement player-first market behavior  
- Full materials directory rewrite demanded mid-task  
- Ambiguity about which JSON root is canonical and cannot be resolved by find/grep  

---

## Commit Instructions

Host only:

```bash
# Typically summaries/ only
git add projects/galaxy_game/summaries/
git commit -m "architecture(ai-manager): facility-based material data contract (supersedes 2026-09-03 routing mix)"
```

Task file move on completion:

```bash
git mv projects/galaxy_game/tasks/active/2026-09-24-MEDIUM-ARCHITECTURE-MATERIAL-DATA-CONTRACT-FACILITY-BASED.md \
       projects/galaxy_game/tasks/completed/2026-09/2026-09-24-MEDIUM-ARCHITECTURE-MATERIAL-DATA-CONTRACT-FACILITY-BASED.md
git commit -m "chore: move 2026-09-24-MEDIUM-ARCHITECTURE-MATERIAL-DATA-CONTRACT-FACILITY-BASED.md to completed/"
```

---

## Documentation

- [ ] Primary deliverable = summaries/ synthesis  
- [ ] Optional: one-line SUPERSEDED note on 2026-09-03 task if human requests  

---

## Dependencies

**Blocked by**: Pre-player acquisition architecture + wiring (completed)  
**Blocks**: Clean material JSON hygiene / future migration tasks  
**Related**: Supersedes routing portions of `2026-09-03-MEDIUM-ARCHITECTURE-MATERIAL-SOURCING-AND-ACQUISITION-LOGIC.md`  

---

## Completion Report

*Filled in by the implementing agent after completion*

**Completed by**:  
**Completion date**:  

### What was delivered
- 

### Audit summary
- 

### Follow-up tasks needed
- Optional JSON migration task for listed offenders  
- Post-player acquisition tree (separate, when AWS/players exist)  

### Lessons learned
- 

---

## Handoff Summary

HANDOFF SUMMARY: [facility-based material contract written] | [2026-09-03 superseded] | [next: optional JSON migration or park]
```
