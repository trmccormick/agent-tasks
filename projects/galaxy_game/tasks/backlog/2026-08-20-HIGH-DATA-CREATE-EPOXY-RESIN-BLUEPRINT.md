---
status: backlog
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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/2026-08-20-HIGH-DATA-CREATE-EPOXY-RESIN-BLUEPRINT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/galaxy_game/tasks/backlog/2026-08-20-HIGH-DATA-CREATE-EPOXY-RESIN-BLUEPRINT.md \
         projects/galaxy_game/tasks/active/2026-08-20-HIGH-DATA-CREATE-EPOXY-RESIN-BLUEPRINT.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

READ FIRST (after Step 0): Task file contains all prerequisites, implementation steps, gotchas, and completion report template.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting implementation.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-08-20-EPOXY-RESIN-BLUEPRINT.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Create Epoxy Resin Blueprint (Earth Import)

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

Architecture verification (2026-08-20) uncovered blocker: `epoxy_resin` does not exist as a game material blueprint.

**Dependency Chain Failing:**
- graphene_composite blueprint exists BUT cannot be produced
- Inputs: graphite (HIGH priority task) + epoxy_resin (THIS task)
- Output: graphene_composite (✅ exists)
- Production facility: fabrication_plant (HIGH priority task)

**Impact**: mk2/mk3 storage tanks cannot be manufactured. Boil-off enforcement implementation blocked.

**Why epoxy_resin is needed:**
- mk2 storage tanks require graphene_composite shell material (carbon fiber composite)
- graphene_composite production: 8.5 kg graphite + 1.5 kg epoxy_resin → 10 kg graphene_composite
- Epoxy_resin is synthetic chemistry product (suitable for Earth import as early-game resource)

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1 — Epoxy Resin May Have Different Name in Game**
- ❌ Wrong: Assume "epoxy_resin" is the exact material name used in game
- ✅ Right: Search codebase for resin/polymer/composite materials that might serve this role
- Why: This is the exact mistake the verification was designed to catch (material exists with unrecognized name)

⚠️ **GOTCHA 2 — Epoxy Resin Sourcing Must Be Plausible**
- ❌ Wrong: Create extraction blueprint (graphite is extracted, resin is synthesized)
- ✅ Right: Create as Earth import or synthetic production (Phase 1+ available, early-game resource)
- Why: Epoxy resin is synthetic chemistry, not a natural extraction; sourcing from Earth import is most plausible

⚠️ **GOTCHA 3 — Phase Availability Must Support Graphene Production**
- ❌ Wrong: Create epoxy_resin availability Phase 9+
- ✅ Right: Available Phase 1 or early (need it for graphene_composite production Phase 11+, so must be sourced much earlier)
- Why: Three-phase lead time: source epoxy_resin early → produce graphene_composite Phase 11+ → manufacture mk2 storage Phase 11+

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before reading any files or running any commands, create and save a **synthesis report**:

**Template** (save as file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Create Epoxy Resin Blueprint (Earth Import)
**Date**: 2026-08-20

### What I'm About to Do
1. Search codebase for existing epoxy/resin/polymer materials
2. If found: Verify naming, sourcing method (import vs production), phase availability
3. If NOT found: Create epoxy_resin import blueprint (JSON)
   - File location: /data/json-data/blueprints/materials/epoxy_resin_bp.json
   - Sourcing: Earth import (synthetic chemistry product)
   - Phase: 1+ (available very early, sourced before needed for Phase 11 graphene production)
   - Output: Epoxy resin material for graphene_composite input
4. Verify blueprint references align with graphene_composite production blueprint

### Prerequisites Completed
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand 3 gotchas above

### Expected Outcomes
- Epoxy resin material is either located (existing) or created (new blueprint)
- Blueprint is Phase 1+ (early availability)
- Blueprint is sourced as Earth import (not extraction)
- Blueprint references are consistent with graphene_composite production inputs
- No naming conflicts with existing materials

### Critical Gotchas I Will Avoid
- ❌ Assume "epoxy_resin" is exact name — instead ✅ search for resin/polymer alternatives first
- ❌ Create extraction blueprint — instead ✅ create import or synthetic production blueprint
- ❌ Set Phase 9+ availability — instead ✅ enforce Phase 1+ early availability

---

**SYNTHESIS COMPLETE.** Ready to proceed with Steps 1-3.
```

**SAVE TO**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-08-20-EPOXY-RESIN-BLUEPRINT.md`

**POST SYNTHESIS REPORT IN CHAT** before proceeding to Step 1.

---

## Implementation Steps

### Step 0 — Create Status Synthesis Report (MANDATORY FIRST)

As described above. Do NOT proceed to Step 1 until synthesis report is created and posted in chat.

---

### Step 1 — Search for Existing Epoxy/Resin/Polymer Materials

**Commands**:
```bash
cd /Users/tam0013/Documents/git/galaxyGame
grep -r "epoxy\|resin\|polymer" --include="*.rb" --include="*.json" app/ db/ data/ | head -20
grep -r "composite" --include="*.rb" --include="*.json" app/ db/ data/ | grep -v graphene | head -10
```

**Verification**:
- [ ] Check output for any existing resin/polymer material definitions
- [ ] If found: Note file path, material name, sourcing method, phase availability
- [ ] If NOT found: Proceed to Step 2

**Report finding**: Exact grep results or "No epoxy/resin materials found"

---

### Step 2 — If Epoxy Resin Not Found: Create Blueprint

If Step 1 found nothing, create epoxy resin import blueprint:

**File**: `/data/json-data/blueprints/materials/epoxy_resin_bp.json`

**Blueprint structure** (reference existing material blueprints for format):
- Name: "epoxy_resin"
- Description: "Synthetic polymer resin imported from Earth; used in composite fabrication"
- Material output: epoxy_resin (raw/bulk)
- Sourcing: Earth import (not extraction)
- Phase unlock: 1 or early (available from game start; no production infrastructure needed)
- Quantity available: Sufficient for graphene_composite production (1.5 kg per 8-hour cycle at Phase 11+)

**Verification**:
- [ ] JSON validates (no parse errors)
- [ ] Material name matches graphene_composite input reference
- [ ] Phase is 1 or early (much earlier than Phase 11 graphene production)
- [ ] Sourcing marked as import (not extraction)
- [ ] No conflicts with existing materials

---

### Step 3 — Verify Alignment with Graphene_Composite Input

**File to check**: `/data/json-data/blueprints/materials/graphene_composite_bp.json`

**Verification**:
- [ ] graphene_composite inputs include "epoxy_resin" (exact name match)
- [ ] Quantity (1.5 kg) is reasonable sourcing rate from import
- [ ] No other references missing

**If name mismatch found**: Report exact discrepancy (do NOT edit, report only)

---

## Acceptance Criteria

- [ ] Epoxy resin material exists in codebase (either found or created)
- [ ] Blueprint has Phase 1+ availability (early sourcing)
- [ ] Blueprint is sourced as import (not extraction)
- [ ] JSON parses without errors
- [ ] Material name matches graphene_composite production inputs exactly
- [ ] No naming conflicts with existing materials

---

## Stop Conditions — escalate immediately if:

- Epoxy resin with conflicting definition already exists (different phase, different sourcing method)
- Cannot determine naming convention from existing materials
- Any error that requires architectural decision or code changes

**On stop condition**: Report findings, do NOT attempt fixes. Ping planning agent.

---

## Dependencies

**Blocked by**: None (can proceed independently)
**Depends on**: `2026-08-20-HIGH-DATA-CREATE-GRAPHITE-BLUEPRINT` (shared graphene_composite dependency)
**Blocks**: `2026-08-20-HIGH-DATA-CREATE-FABRICATION-PLANT-BLUEPRINT` (shared graphene_composite dependency)

---

## Completion Report

*Filled in by implementing agent after implementation completes*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### Implementation Result

- [ ] EPOXY RESIN FOUND EXISTING: Located at [path], name: [name], sourcing: [import/production], phase: [phase]
- [ ] EPOXY RESIN CREATED NEW: Blueprint saved to [path], sourcing: [import], phase: [phase], output: [quantity/rate]

### Verification Results

- [ ] JSON syntax: ✅ PASS
- [ ] Phase availability: ✅ PHASE 1+
- [ ] Sourcing method: ✅ Import (not extraction)
- [ ] Name alignment: ✅ Matches graphene_composite inputs
- [ ] Git hygiene: ✅ Added to gitignore or proper location

### Follow-up Tasks Needed

[Any issues or dependencies identified]

### Lessons Learned

[What discovery process revealed]

---

## Handoff Summary

HANDOFF: [Epoxy resin found existing | Epoxy resin blueprint created] | Sourcing: [import] | Phase: [1+] | Next: Dispatch fabrication plant blueprint creation task
