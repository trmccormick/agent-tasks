---
status: active
priority: HIGH
type: data
system_domain: MANUFACTURING
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate
- [x] All Step 0-N instructions are clear and actionable
- [x] Synthesis report template is provided (copy/paste ready)
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**READY FOR DISPATCH**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase10-venus/2026-08-20-HIGH-DATA-CREATE-GRAPHITE-BLUEPRINT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/galaxy_game/tasks/backlog/phase10-venus/2026-08-20-HIGH-DATA-CREATE-GRAPHITE-BLUEPRINT.md \
         projects/galaxy_game/tasks/active/2026-08-20-HIGH-DATA-CREATE-GRAPHITE-BLUEPRINT.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

READ FIRST (after Step 0): Task file contains all prerequisites, implementation steps, gotchas, and completion report template.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting implementation.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-08-20-GRAPHITE-BLUEPRINT.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Create Graphite Blueprint (Phase 10+ Extraction)

**Status**: BACKLOG
**Priority**: HIGH
**Type**: data
**Created**: 2026-08-20
**Blocker**: Architecture verification found missing material in graphene_composite dependency chain

---

## Local Worker Triage Report

- **Template Conformance**: PASS — full task template format
- **System Domain**: Material blueprint creation (JSON data)
- **MVP Alignment**: SPEC_HEALTH — unblocks mk2/mk3 storage blueprint chain
- **Action Line**: READY FOR DISPATCH — data creation task, low risk, well-scoped

---

## Agent Assignment

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Create JSON blueprint file, verify against game materials system
**Supervision Level**: Standard

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR role)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

---

## Context

Architecture verification (2026-08-20) uncovered blocker: `graphite` does not exist as a game material blueprint.

**Dependency Chain Failing:**
- graphene_composite blueprint exists BUT cannot be produced
- Inputs: graphite (❌ missing) + epoxy_resin (❌ missing)
- Output: graphene_composite (✅ exists)
- Production facility: fabrication_plant (❌ missing)

**Impact**: mk2/mk3 storage tanks cannot be manufactured. Boil-off enforcement implementation blocked.

**Why graphite is needed:**
- mk2 storage tanks (cooling efficiency improvement) require graphene_composite shell material
- graphene_composite production: 8.5 kg graphite + 1.5 kg epoxy_resin → 10 kg graphene_composite
- Graphite must be extractable from game materials (asteroid mining, crustal drilling, or other Phase 10+ source)

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1 — Blueprint Naming Must Follow Game Conventions**
- ❌ Wrong: Invent new naming patterns or material names
- ✅ Right: Follow existing graphite references in codebase (search for "graphite" first)
- Why: Material names must match exactly what blueprint_lookup_service expects

⚠️ **GOTCHA 2 — Graphite May Already Exist with Different Name**
- ❌ Wrong: Assume "graphite" name is available; create it with that exact name
- ✅ Right: Search codebase for carbon/graphite materials already defined
- Why: This is the exact mistake the verification was designed to catch (material exists with unrecognized name)

⚠️ **GOTCHA 3 — Phase Availability Must Match Architecture**
- ❌ Wrong: Create graphite extraction as Phase 5 or earlier
- ✅ Right: Phase 10+ only (early harvesters return from Venus/Titan with cargo requiring mk2 cooling; graphite needed for downstream mk2 production)
- Why: Cannot source graphite before Phase 10 without breaking sequencing logic

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before reading any files or running any commands, create and save a **synthesis report**:

**Template** (save as file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Create Graphite Blueprint (Phase 10+ Extraction)
**Date**: 2026-08-20

### What I'm About to Do
1. Search codebase for existing "graphite" or similar carbon material references
2. If found: Verify naming and phase availability, report location
3. If NOT found: Create graphite extraction blueprint (JSON)
   - File location: /data/json-data/blueprints/materials/graphite_bp.json
   - Phase: 10 or later (asteroid/crustal extraction source)
   - Output: Raw graphite material for graphene_composite input
4. Verify blueprint references align with graphene_composite production blueprint

### Prerequisites Completed
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand 3 gotchas above

### Expected Outcomes
- Graphite material is either located (existing) or created (new blueprint)
- Blueprint has Phase 10+ availability
- Blueprint references are consistent with graphene_composite production inputs
- No naming conflicts with existing materials

### Critical Gotchas I Will Avoid
- ❌ Assume "graphite" doesn't exist — instead ✅ search codebase thoroughly first
- ❌ Invent new naming patterns — instead ✅ follow game material conventions
- ❌ Create Phase 5 graphite — instead ✅ enforce Phase 10+ only

---

**SYNTHESIS COMPLETE.** Ready to proceed with Steps 1-3.
```

**SAVE TO**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-08-20-GRAPHITE-BLUEPRINT.md`

**POST SYNTHESIS REPORT IN CHAT** before proceeding to Step 1.

---

## Implementation Steps

### Step 0 — Create Status Synthesis Report (MANDATORY FIRST)

As described above. Do NOT proceed to Step 1 until synthesis report is created and posted in chat.

---

### Step 1 — Search for Existing Graphite Material

**Command**:
```bash
cd /Users/tam0013/Documents/git/galaxyGame
grep -r "graphite" --include="*.rb" --include="*.json" app/ db/ data/ | head -20
```

**Verification**:
- [ ] Check output for any existing graphite/carbon material definitions
- [ ] If found: Note file path, material name, current phase, source type
- [ ] If NOT found: Proceed to Step 2

**Report finding**: Exact grep results or "No graphite material found"

---

### Step 2 — If Graphite Not Found: Create Blueprint

If Step 1 found nothing, create graphite extraction blueprint:

**File**: `/data/json-data/blueprints/materials/graphite_bp.json`

**Blueprint structure** (reference existing material blueprints for format):
- Name: "graphite"
- Description: "Raw carbon material extracted from asteroids or crustal deposits"
- Material output: graphite (raw)
- Phase unlock: 10 (Phase 10 early harvesters return; graphite available for graphene_composite production Phase 11+)
- Facility type: extraction (asteroid mining or crustal drilling)
- Production rate: Sufficient for graphene_composite production (8.5 kg graphite per 8-hour cycle)

**Verification**:
- [ ] JSON validates (no parse errors)
- [ ] Material name matches graphene_composite input reference
- [ ] Phase is 10 or higher
- [ ] No conflicts with existing materials

---

### Step 3 — Verify Alignment with Graphene_Composite Input

**File to check**: `/data/json-data/blueprints/materials/graphene_composite_bp.json`

**Verification**:
- [ ] graphene_composite inputs include "graphite" (exact name match)
- [ ] Quantity (8.5 kg) is reasonable extraction rate
- [ ] No other references missing

**If name mismatch found**: Report exact discrepancy (do NOT edit, report only)

---

## Acceptance Criteria

- [ ] Graphite material exists in codebase (either found or created)
- [ ] Blueprint has Phase 10+ availability
- [ ] JSON parses without errors
- [ ] Material name matches graphene_composite production inputs exactly
- [ ] No naming conflicts with existing materials

---

## Stop Conditions — escalate immediately if:

- Graphite with conflicting definition already exists (different phase, different source)
- Cannot determine naming convention from existing materials
- Any error that requires architectural decision or code changes

**On stop condition**: Report findings, do NOT attempt fixes. Ping planning agent.

---

## Dependencies

**Blocked by**: None (can proceed independently)
**Blocks**: `2026-08-20-HIGH-DATA-CREATE-EPOXY-RESIN-BLUEPRINT` (shared graphene_composite dependency)
**Blocks**: `2026-08-20-HIGH-DATA-CREATE-FABRICATION-PLANT-BLUEPRINT` (shared graphene_composite dependency)

---

## Completion Report

*Filled in by implementing agent after implementation completes*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### Implementation Result

- [ ] GRAPHITE FOUND EXISTING: Located at [path], name: [name], phase: [phase]
- [ ] GRAPHITE CREATED NEW: Blueprint saved to [path], phase: [phase], output: [quantity/rate]

### Verification Results

- [ ] JSON syntax: ✅ PASS
- [ ] Phase availability: ✅ PHASE 10+
- [ ] Name alignment: ✅ Matches graphene_composite inputs
- [ ] Git hygiene: ✅ Added to gitignore or proper location

### Follow-up Tasks Needed

[Any issues or dependencies identified]

### Lessons Learned

[What discovery process revealed]

---

## Handoff Summary

HANDOFF: [Graphite found existing | Graphite blueprint created] | Phase: [10+] | Next: Dispatch epoxy_resin blueprint creation task
