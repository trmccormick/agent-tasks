---
status: active
priority: MEDIUM
type: data
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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/ai-manager/2026-09-24-MEDIUM-DATA-MATERIAL-JSON-FACILITY-BASED-MIGRATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/ai-manager/2026-09-24-MEDIUM-DATA-MATERIAL-JSON-FACILITY-BASED-MIGRATION.md \
         projects/galaxy_game/tasks/active/2026-09-24-MEDIUM-DATA-MATERIAL-JSON-FACILITY-BASED-MIGRATION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks -name "2026-09-24-MEDIUM-DATA-MATERIAL-JSON-FACILITY-BASED-MIGRATION.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-DATA-MATERIAL-JSON-FACILITY-BASED-MIGRATION.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.

---

# TASK: Migrate Material JSON Offenders to Facility-Based Contract
**Status**: BACKLOG  
**Priority**: MEDIUM  
**Type**: data  
**Created**: 2026-09-24  
**Last Updated**: 2026-09-24 (post-Qwen proof: C1–C3 applied)  

---

## Local Worker Triage Report (Optional)

- **Template Conformance**: To be confirmed by Qwen proof
- **Docker Wrapper Check**: N/A unless a loader/spec touches these files
- **MVP Alignment**: VALID — remove location-keyed sourcing so materials stay world-agnostic
- **MVP Impact Note**: Implements audit from material data contract synthesis
- **Action Line**: NEEDS MANUAL REVIEW before dispatch

---

## Agent Assignment

**Assigned To**: Qwen local via Copilot (primary)  
**Why This Agent**: Terminal access for JSON edit + grep verification  
**Supervision Level**: watched carefully  

---

## Prerequisites — READ FIRST

1. Workflow README (EXECUTOR section)
2. Project guide
3. This task file
4. Prior art (read-only):
   - `summaries/2026-09-24-ARCHITECTURE-MATERIAL-DATA-CONTRACT-FACILITY-BASED.md` — **authoritative contract + audit counts**
   - Pre-player acquisition tree is **done** — do not touch EscalationService / ResourceAcquisitionService for this task

---

## Context

Architecture contract (2026-09-24 synthesis):

- Materials are passive: facility + production + baseline economics
- **Forbidden:** location-keyed `sourcing` blocks and body-named production keys (`lunar_production`, etc.)
- ~198/207 material JSON files already conform
- Offenders are few:

| Pattern | Count (from synthesis) | Notes |
|---------|------------------------|--------|
| Location-keyed `sourcing` blocks | **3** | High priority |
| Body-named production keys (e.g. `lunar_production`) | **6** | Includes overlap |
| Both patterns | **2** | e.g. high severity |

**Named exemplars from synthesis:**
- High severity: `regolith_composite.json` (both patterns)
- Exemplar candidate: `epoxy_resin.json` (both patterns) under processed polymers
- Conforming majority already use `facility_type` without location keys

**This task migrates only the offender set.** No acquisition routing changes. No mass rewrite of all 207 files.

---

## Critical Information

### Target shape (from contract)

```json
{
  "production": {
    "facility_type": "...",
    "input_materials": [],
    "energy_kwh_per_kg": 0,
    "production_time_hours": 0
  },
  "cost_data": {
    "purchase_cost": { "amount": 0 },
    "import_config": { "transport_category": "standard" }
  },
  "pricing": {
    "local_production": {
      "facility_required": "...",
      "cost_per_kg": 0,
      "energy_kwh_per_kg": 0
    }
  }
}
```

Preserve existing numeric values where a body-named key is being renamed (e.g. copy `lunar_production` numbers into `local_production` then remove the body key). Do not invent new economics.

### Architecture Gotchas

⚠️ **GOTCHA 1: Data only — no service rewrites**
- ❌ Wrong: Change EscalationService, ResourceAcquisitionService, or ProcurementService
- ✅ Right: Edit offender JSON files only (+ optional loader test if one fails)
- Why: Acquisition routing is already implemented

⚠️ **GOTCHA 2: Remove forbidden keys; do not leave dual shapes**
- ❌ Wrong: Keep `sourcing.lunar` “for compatibility” after adding facility fields
- ✅ Right: Delete location-keyed sourcing blocks and body-named production keys after mapping values into facility-based keys
- Why: Contract forbids those patterns

⚠️ **GOTCHA 3: Do not rewrite conforming files**
- ❌ Wrong: “Normalize” all 207 materials
- ✅ Right: Only files that still have sourcing blocks or body-named production keys
- Why: Scope is the audit offender list

⚠️ **GOTCHA 4: Phase the work**
- ❌ Wrong: Edit every offender in one unreviewed dump
- ✅ Right: **Phase A** — exemplar only (`epoxy_resin.json`); stop for human OK. **Phase B** — remaining offenders after approval
- Why: Catch bad mapping early

⚠️ **GOTCHA 5: Valid JSON only**
- ❌ Wrong: Trailing commas, comments, or broken structure
- ✅ Right: Valid JSON; re-parse after each file
- Why: Loaders will fail on bad JSON

---

## 🔴 REQUIRED: Status Synthesis Report (Before Any Edits)

Save to summaries/ before Phase A edits:

```markdown
## STATUS SYNTHESIS REPORT

**Task**: 2026-09-24-MEDIUM-DATA-MATERIAL-JSON-FACILITY-BASED-MIGRATION
**Status**: backlog → active
**Date**: YYYY-MM-DD

### What I'm About to Do
Re-confirm the offender list from the architecture synthesis, migrate epoxy_resin.json as Phase A exemplar, then remaining offenders in Phase B after human approval. No service code changes.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| summaries/2026-09-24-ARCHITECTURE-MATERIAL-DATA-CONTRACT-FACILITY-BASED.md | Contract + audit | pending |
| epoxy_resin.json and other offenders | Migration targets | pending |

### Prerequisites Completed
- ✅ Step 0 done
- ✅ Read contract synthesis
- ✅ Understand Phase A then Phase B

### Expected Outcomes
- Zero location-keyed sourcing blocks in materials tree
- Zero body-named production keys in offenders
- Valid JSON; no acquisition service edits

### Critical Gotchas I Will Avoid
- ❌ Service rewrites — instead ✅ JSON only
- ❌ Edit all 207 files — instead ✅ offenders only
- ❌ Skip Phase A approval — instead ✅ stop after exemplar

---
**SYNTHESIS COMPLETE.** Ready for Phase A after human OK on synthesis.
```

---

## Problem Statement

A small set of material JSON files still use location-keyed `sourcing` and/or body-named production keys. The facility-based contract is documented; offenders must be migrated without touching acquisition services or conforming materials.

**Current:** 3 sourcing-block files, 6 body-named keys (per 2026-09-24 audit).  
**Expected:** Those files conform to facility-based shape; grep shows zero remaining offenders in the materials tree.

---

## Files Involved

### Primary (Phase A)
| File | Change |
|---|---|
| `data/json-data/resources/materials/processed/polymers/epoxy_resin.json` | Remove sourcing / body-named keys; map to facility-based shape |

### Primary (Phase B — after human approval of Phase A)
| File | Change |
|---|---|
| Remaining files from Step 1 offender list (including `regolith_composite.json` and others) | Same migration rules |

### Reference
| File | Why |
|---|---|
| `summaries/2026-09-24-ARCHITECTURE-MATERIAL-DATA-CONTRACT-FACILITY-BASED.md` | Contract + counts |
| One already-conforming material JSON | Shape reference |

### Migration
- [x] No DB migration
- [x] JSON data edits only

**Qwen proof:** Confirm exact paths of all offenders before editing.

---

## Implementation Steps

### Step 0 — Move to active/, status active (MANDATORY)

```bash
git mv projects/galaxy_game/tasks/backlog/ai-manager/2026-09-24-MEDIUM-DATA-MATERIAL-JSON-FACILITY-BASED-MIGRATION.md \
       projects/galaxy_game/tasks/active/2026-09-24-MEDIUM-DATA-MATERIAL-JSON-FACILITY-BASED-MIGRATION.md
```

Change YAML `status: backlog` → `status: active`.  
Verify with full find path (one copy only). Paste output.

### Step 1 — Re-confirm offender list (read-only)

From galaxy game app root (where `data/json-data` exists):

```bash
grep -rn '"sourcing"' data/json-data/resources/materials/ 2>/dev/null
grep -rn 'lunar_production\|martian_production' data/json-data/resources/materials/ 2>/dev/null
```

Paste the file list into notes. Must align with architecture synthesis (order of magnitude: ~3 sourcing, ~6 body-named). If counts differ wildly, **stop and escalate**.

### Step 2 — Synthesis report to summaries/

Write synthesis (template above). **Stop for human approval before any JSON edit.**

### Step 3 — Phase A: migrate exemplar only

Edit `epoxy_resin.json` only:

1. Ensure `production.facility_type` (and inputs/energy/time if present) remain or are filled from existing data  
2. Map any `lunar_production` / body-named pricing into `pricing.local_production` (or contract-equivalent keys already used by conforming files)  
3. **Delete** location-keyed `sourcing` block entirely  
4. **Delete** body-named production keys  
5. Validate JSON (parse / `python -m json.tool` or equivalent)

Report before/after key summary. **Stop for human approval before Phase B.**

### Step 4 — Phase B: remaining offenders

After approval, migrate each remaining offender with the same rules. One file at a time; re-parse each.

### Step 5 — Verification

```bash
grep -rn '"sourcing"' data/json-data/resources/materials/ 2>/dev/null
grep -rn 'lunar_production\|martian_production' data/json-data/resources/materials/ 2>/dev/null
```

Expected: **zero** hits (or only false positives outside material definitions — document if any).

### Step 6 — Completion report

List every file changed. No service commits. Stop for human approval before final commit.

---

## Acceptance Criteria

- [ ] Offender list re-confirmed with grep evidence  
- [ ] Phase A (`epoxy_resin.json`) migrated and human-approved before Phase B  
- [ ] All listed offenders migrated to facility-based shape  
- [ ] Zero location-keyed `sourcing` blocks remain under materials (grep)  
- [ ] Zero body-named production keys remain on those files (grep)  
- [ ] All edited files are valid JSON  
- [ ] No changes to EscalationService, ResourceAcquisitionService, ProcurementService, or other Ruby services  
- [ ] No bulk rewrite of already-conforming materials  

---

## Stop Conditions — escalate immediately if:

- Offender count is far larger than the architecture audit (~3 / ~6) without explanation  
- Required economics for a file cannot be mapped without inventing numbers  
- Any production code must change for loaders to accept the new shape  
- Pressure to “fix” all 207 materials in this task  

---

## Commit Instructions

Host only. Prefer **two commits** if Phase A and B are separate approvals:

```bash
# Phase A (after approval)
git add data/json-data/resources/materials/processed/polymers/epoxy_resin.json
git commit -m "data(materials): migrate epoxy_resin to facility-based contract (exemplar)"

# Phase B (after approval — list each offender path from Step 1)
git add data/json-data/resources/materials/processed/composites/regolith_composite.json
git add data/json-data/resources/materials/building/functional/aerogel_insulation.json
git add data/json-data/resources/materials/building/functional/regolith_shielding_layer.json
# (add any additional offender paths confirmed in Step 1)
git commit -m "data(materials): migrate remaining offenders to facility-based contract"
```

Task closeout:

```bash
git mv projects/galaxy_game/tasks/active/2026-09-24-MEDIUM-DATA-MATERIAL-JSON-FACILITY-BASED-MIGRATION.md \
       projects/galaxy_game/tasks/completed/2026-09/2026-09-24-MEDIUM-DATA-MATERIAL-JSON-FACILITY-BASED-MIGRATION.md
git commit -m "chore: move 2026-09-24-MEDIUM-DATA-MATERIAL-JSON-FACILITY-BASED-MIGRATION.md to completed/"
```

---

## Documentation

- [ ] No new architecture doc required if contract synthesis already exists  
- [ ] Note in completion report if status.md should mention materials hygiene complete  

---

## Dependencies

**Blocked by:** `2026-09-24` material data contract architecture (synthesis complete; close that task if still active)  
**Blocks:** Clean material JSON for world-agnostic local production  
**Related:** Pre-player acquisition tree (done — do not modify)

---

## Completion Report

**Completed by:**  
**Completion date:**  

### Files migrated
- 

### Grep verification
- 

### Follow-up
- None expected unless loaders fail on shape  

---

## Handoff Summary

HANDOFF SUMMARY: [offenders migrated to facility-based JSON] | [grep clean] | [next: park or loader follow-up if any]
```
