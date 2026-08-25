---
status: backlog
priority: HIGH
type: data
system_domain: MANUFACTURING
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
notes: "blueprint drafted but premature — correctly deferred to Phase 11+ per [[boil-off-mktier-storage]], git tracking violated a standing convention and was reverted, do not re-dispatch until Phase 11+ work begins."
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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/2026-08-20-HIGH-DATA-CREATE-FABRICATION-PLANT-BLUEPRINT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/galaxy_game/tasks/backlog/2026-08-20-HIGH-DATA-CREATE-FABRICATION-PLANT-BLUEPRINT.md \
         projects/galaxy_game/tasks/active/2026-08-20-HIGH-DATA-CREATE-FABRICATION-PLANT-BLUEPRINT.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

READ FIRST (after Step 0): Task file contains all prerequisites, implementation steps, gotchas, and completion report template.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting implementation.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-08-20-FABRICATION-PLANT-BLUEPRINT.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Create Fabrication Plant Blueprint (Phase 11+ Production Facility)

**Status**: BACKLOG
**Priority**: HIGH
**Type**: data
**Created**: 2026-08-20
**Blocker**: Architecture verification found missing facility in graphene_composite production chain

---

## Local Worker Triage Report

- **Template Conformance**: PASS — full task template format
- **System Domain**: Facility blueprint creation (JSON data)
- **MVP Alignment**: SPEC_HEALTH — unblocks mk2/mk3 storage blueprint chain
- **Action Line**: READY FOR DISPATCH — data creation task, medium scope (facility is complex), well-defined requirements

---

## Agent Assignment

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Create facility JSON blueprint, verify against game facilities system, validate against similar buildings
**Supervision Level**: Standard

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR role)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

---

## Context

Architecture verification (2026-08-20) uncovered blocker: `fabrication_plant` does not exist as a game facility blueprint.

**Dependency Chain Failing:**
- graphene_composite blueprint exists BUT cannot be produced
- Inputs: graphite (HIGH priority) + epoxy_resin (HIGH priority)
- Output: graphene_composite (✅ exists)
- Production facility: fabrication_plant (THIS task)

**Impact**: mk2/mk3 storage tanks cannot be manufactured. Boil-off enforcement implementation blocked.

**Why fabrication_plant is needed:**
- Graphene_composite production requires production facility (like factories, foundries, refineries in game)
- Fabrication plant: Phase 11+ facility, produces advanced composites (graphene_composite)
- Production cycle: 8 hours, output 10 kg graphene_composite from inputs, cost ~45,000 GCC

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1 — Facility Blueprint Structure Differs from Material Blueprints**
- ❌ Wrong: Copy material blueprint format and adapt it for facility
- ✅ Right: Look at existing factory/production facility blueprints (refinery, foundry, manufacturer, etc.) for structure
- Why: Facilities have workers, power requirements, storage, production slots; materials don't

⚠️ **GOTCHA 2 — Fabrication Plant May Already Exist Under Different Name**
- ❌ Wrong: Assume "fabrication_plant" is the exact name used in game
- ✅ Right: Search codebase for production facilities that might serve this role (factory, manufacturing_plant, composite_factory, etc.)
- Why: This is the exact mistake the verification was designed to catch (facility exists with unrecognized name)

⚠️ **GOTCHA 3 — Phase Unlock Must Align with Architecture**
- ❌ Wrong: Create fabrication_plant as Phase 1 facility (no advanced infrastructure yet)
- ✅ Right: Phase 11+ facility only (stationary skimmers deploy Phase 11+; need advanced composites for long-term operations)
- Why: Early phases lack infrastructure; fabrication plants only make sense after basic ISRU is established

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before reading any files or running any commands, create and save a **synthesis report**:

**Template** (save as file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Create Fabrication Plant Blueprint (Phase 11+ Production Facility)
**Date**: 2026-08-20

### What I'm About to Do
1. Search codebase for existing production facilities (factory, foundry, refinery, manufacturer, etc.)
2. Review structure of at least 2 production facilities to understand blueprint format
3. If fabrication_plant not found: Create fabrication_plant blueprint (JSON facility)
   - File location: /data/json-data/blueprints/facilities/fabrication_plant_bp.json
   - Phase: 11 or later (stationary skimmer infrastructure)
   - Production: graphene_composite (8 hour cycle, 10 kg output, 45K GCC cost)
   - Inputs: graphite + epoxy_resin (must match production blueprint)
4. Verify facility references align with graphene_composite production specification

### Prerequisites Completed
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand 3 gotchas above

### Expected Outcomes
- Fabrication plant facility is either located (existing) or created (new blueprint)
- Blueprint is Phase 11+ (aligns with skimmer deployment)
- Blueprint has graphene_composite production configured (8hr, 10kg, 45K GCC)
- Blueprint input requirements match graphite + epoxy_resin availability
- No naming conflicts with existing facilities

### Critical Gotchas I Will Avoid
- ❌ Copy material blueprint format — instead ✅ study existing facility blueprints first
- ❌ Assume "fabrication_plant" is exact name — instead ✅ search for existing production facility alternatives
- ❌ Create Phase 1 facility — instead ✅ enforce Phase 11+ only

---

**SYNTHESIS COMPLETE.** Ready to proceed with Steps 1-4.
```

**SAVE TO**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-08-20-FABRICATION-PLANT-BLUEPRINT.md`

**POST SYNTHESIS REPORT IN CHAT** before proceeding to Step 1.

---

## Implementation Steps

### Step 0 — Create Status Synthesis Report (MANDATORY FIRST)

As described above. Do NOT proceed to Step 1 until synthesis report is created and posted in chat.

---

### Step 1 — Search for Existing Production Facilities

**Commands**:
```bash
cd /Users/tam0013/Documents/git/galaxyGame
find data/json-data/blueprints -name "*factory*" -o -name "*foundry*" -o -name "*refinery*" -o -name "*manufacturer*" -o -name "*fabrication*" | head -10
ls -la data/json-data/blueprints/facilities/ 2>/dev/null || echo "No facilities/ directory"
```

**Verification**:
- [ ] Check output for existing production facilities
- [ ] If fabrication_plant found: Note file path and contents
- [ ] If NOT found: Note existing facilities directory structure
- [ ] Proceed to Step 2 (study facility blueprint format)

**Report finding**: Exact directory listing or "No fabrication_plant found"

---

### Step 2 — Study Existing Facility Blueprint Format

**Task**: Examine at least 2 existing production facility blueprints to understand structure

**Files to examine**: Find facility blueprints in `/data/json-data/blueprints/facilities/` and pick any 2 production facilities (factory, refinery, foundry, etc.)

**What to document**:
- [ ] Top-level JSON keys (name, description, type, phase, workers, power_required, etc.)
- [ ] Production specification structure (output material, production_time, cost, inputs, etc.)
- [ ] How facility references productions vs material blueprints
- [ ] Phase unlock pattern (when facilities become available)

**Report findings**: 2-3 facility blueprint structure examples

---

### Step 3 — If Fabrication Plant Not Found: Create Blueprint

If Step 1 found nothing, create fabrication plant facility blueprint:

**File**: `/data/json-data/blueprints/facilities/fabrication_plant_bp.json`

**Blueprint structure** (based on facility blueprint format from Step 2):
- Name: "fabrication_plant"
- Description: "Advanced composite fabrication facility; produces graphene composites for high-performance storage systems"
- Type: production_facility
- Phase unlock: 11 (stationary skimmer infrastructure deployment)
- Workers required: 5-10 (reasonable for advanced facility)
- Power required: 15-20 kW (composite production is energy-intensive)
- Production specification:
  - Output: graphene_composite (10 kg per cycle)
  - Production time: 8 hours
  - Cost: 45,000 GCC per cycle
  - Inputs: graphite (8.5 kg) + epoxy_resin (1.5 kg)
  - Throughput: sustainable at Phase 11+ scale

**Verification**:
- [ ] JSON validates (no parse errors)
- [ ] Production references match graphite + epoxy_resin material names exactly
- [ ] Phase is 11 or higher
- [ ] Production parameters align with graphene_composite production spec
- [ ] No conflicts with existing facilities

---

### Step 4 — Verify Alignment with Graphene_Composite Production

**File to check**: `/data/json-data/blueprints/materials/graphene_composite_bp.json`

**Verification**:
- [ ] graphene_composite production facility is referenced as "fabrication_plant" (exact name match)
- [ ] Production parameters match (8hr cycle, 10 kg output, 45K GCC cost)
- [ ] Input materials (graphite, epoxy_resin) align with facility inputs
- [ ] No other references missing

**If name mismatch found**: Report exact discrepancy (do NOT edit, report only)

---

## Acceptance Criteria

- [ ] Fabrication plant facility exists in codebase (either found or created)
- [ ] Blueprint has Phase 11+ availability
- [ ] JSON parses without errors
- [ ] Facility name matches graphene_composite production references exactly
- [ ] Production parameters correct (8hr, 10kg output, 45K GCC, graphite + epoxy_resin inputs)
- [ ] No naming conflicts with existing facilities

---

## Stop Conditions — escalate immediately if:

- Fabrication plant with conflicting definition already exists (different phase, different production)
- Cannot determine facility blueprint format from existing examples
- Any error that requires architectural decision or code changes

**On stop condition**: Report findings, do NOT attempt fixes. Ping planning agent.

---

## Dependencies

**Blocked by**: None (can proceed independently)
**Depends on**: `2026-08-20-HIGH-DATA-CREATE-GRAPHITE-BLUEPRINT` (shared graphene_composite dependency)
**Depends on**: `2026-08-20-HIGH-DATA-CREATE-EPOXY-RESIN-BLUEPRINT` (shared graphene_composite dependency)

---

## Completion Report

*Filled in by implementing agent after implementation completes*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### Implementation Result

- [ ] FABRICATION PLANT FOUND EXISTING: Located at [path], phase: [phase], production: [output]
- [ ] FABRICATION PLANT CREATED NEW: Blueprint saved to [path], phase: [phase], production: graphene_composite (10kg, 8hr, 45K GCC)

### Verification Results

- [ ] JSON syntax: ✅ PASS
- [ ] Phase availability: ✅ PHASE 11+
- [ ] Production parameters: ✅ graphene_composite (10kg, 8hr, 45K GCC)
- [ ] Input alignment: ✅ graphite + epoxy_resin match
- [ ] Name alignment: ✅ Matches graphene_composite production reference
- [ ] Git hygiene: ✅ Added to gitignore or proper location

### Follow-up Tasks Needed

[Any issues or dependencies identified]

### Lessons Learned

[What discovery process revealed]

---

## Handoff Summary

HANDOFF: [Fabrication plant found existing | Fabrication plant blueprint created] | Phase: [11+] | Production: graphene_composite | Next: Dispatch architecture doc update task
