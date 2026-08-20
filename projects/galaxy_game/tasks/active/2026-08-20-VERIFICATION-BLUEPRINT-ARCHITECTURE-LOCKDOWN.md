---
status: active
priority: HIGH
type: documentation
system_domain: SPECIFICATIONS
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
You are **Verification Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/2026-08-20-VERIFICATION-BLUEPRINT-ARCHITECTURE-LOCKDOWN.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/galaxy_game/tasks/backlog/2026-08-20-VERIFICATION-BLUEPRINT-ARCHITECTURE-LOCKDOWN.md \
         projects/galaxy_game/tasks/active/2026-08-20-VERIFICATION-BLUEPRINT-ARCHITECTURE-LOCKDOWN.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-20-VERIFICATION-BLUEPRINT-ARCHITECTURE-LOCKDOWN.md"
    Only ONE result should exist at active/. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, verification steps, gotchas, and completion report template.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting verification.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-08-20-VERIFICATION-BLUEPRINT-ARCHITECTURE.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Verify Blueprint Loading + PHASE_STRUCTURE.md Architecture Lockdown

**Status**: BACKLOG
**Priority**: HIGH
**Type**: documentation
**Created**: 2026-08-20
**Last Updated**: 2026-08-20

---

## Local Worker Triage Report

- **Template Conformance**: PASS — full task template format
- **System Domain**: Architecture documentation verification (not production code)
- **MVP Alignment**: SPEC_HEALTH — validates correctness of design documents
- **Action Line**: READY FOR DISPATCH — checklist-based verification, low risk, report-only

---

## Agent Assignment

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Requires terminal access to read files, run git commands, locate loader service
**Supervision Level**: Standard — verification checklist with report-only findings

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR role)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

---

## Context

Session 2026-08-20 produced significant architecture documentation and JSON blueprints. Across three iterations, work included:
- PHASE_STRUCTURE.md mk2 tech unlock timing (went through Phase 11 → Phase 9-10 → Foundation iterations)
- mk2/mk3 storage blueprints + graphene_composite material chain
- HLT mk2/mk3 variants + Venus/Titan operational data

Claude's review flagged: "That's a coherent, defensible design chain" BUT raised three "close the loop" verification needs (not new blockers):

1. **PHASE_STRUCTURE.md edit history shows iterations** — current state needs plain read to verify Foundation phase mk2 unlock is correctly documented
2. **JSON blueprints never load-tested** — 10+ files written but never run through actual blueprint loader
3. **Graphene_composite dependency unverified** — graphite + epoxy_resin must both exist as game materials
4. **Git hygiene** — confirm no JSON files accidentally committed to git (should remain gitignored)

**Relevant Architecture Docs**:
- `docs/new_agent/projects/galaxy_game/tasks/backlog/PHASE_STRUCTURE.md` — file to verify
- `data/json-data/blueprints/` — JSON files to load-test

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1 — PHASE_STRUCTURE.md Edit History Is Not Current Truth**
- ❌ Wrong: Assume file is correct because edits were made carefully; trust commit history
- ✅ Right: Read the file directly and verify Foundation phase mk2 unlock is documented correctly
- Why: File went through 3 iterations (Phase 11 → Phase 9-10 → Foundation); each was committed as if final before discovering the error

⚠️ **GOTCHA 2 — JSON Blueprints May Not Load Through Actual Loader**
- ❌ Wrong: Assume JSON is correct because it looks well-formed and was written carefully
- ✅ Right: Run actual blueprint loader to verify files parse + schema validation passes
- Why: 10+ files created/edited this session but never tested in runtime; assumes loader exists

⚠️ **GOTCHA 3 — epoxy_resin May Not Exist in Materials List**
- ❌ Wrong: Assume epoxy_resin is a real game material because Gemini suggested it
- ✅ Right: Verify epoxy_resin exists in the game's materials palette before assuming the chain is valid
- Why: This is the exact mistake caught and fixed during this session (non-existent materials breaking blueprints); cannot repeat it

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before reading any files or running any commands, create and save a **synthesis report**:

**Template** (save as file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Verify Blueprint Loading + PHASE_STRUCTURE.md Architecture Lockdown
**Date**: 2026-08-20

### What I'm About to Do
1. Read PHASE_STRUCTURE.md Foundation phases section; verify mk2 cooling tech unlock is documented
2. Locate blueprint loader service; test 10+ JSON blueprint files parse without errors
3. Verify graphene_composite dependency chain resolves (graphite + epoxy_resin both exist)
4. Confirm no JSON files in git history (should remain in gitignored `/data/`)

### Prerequisites Completed
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand 3 gotchas above

### Expected Outcomes
- PHASE_STRUCTURE.md Foundation phases correctly document mk2 unlock
- All 10+ JSON blueprint files parse and load without schema errors
- Material chain: graphite, epoxy_resin, fabrication_plant all exist in game
- Git shows no blueprint JSON in tracked history

### Critical Gotchas I Will Avoid
- ❌ Trust edit history — instead ✅ read current file state directly
- ❌ Assume JSON is valid — instead ✅ test with actual blueprint loader
- ❌ Assume epoxy_resin exists — instead ✅ verify against materials list

---

**SYNTHESIS COMPLETE.** Ready to proceed with Steps 1-4.
```

**SAVE TO**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-08-20-VERIFICATION-BLUEPRINT-ARCHITECTURE.md`

**POST SYNTHESIS REPORT IN CHAT** before proceeding to Step 1.

---

## Problem Statement

Session 2026-08-20 produced 10+ new/edited JSON files + PHASE_STRUCTURE.md revisions without verification:
- PHASE_STRUCTURE.md went through 3 iterations before landing on correct mk2 unlock timing
- JSON blueprints never tested through actual blueprint loader
- Graphene_composite dependency chain (graphite + epoxy_resin) unverified against materials list
- Git hygiene: confirm no JSON files accidentally in git history

---

## Files Involved

### Primary Files — verify these

| File | Purpose | What to Check |
|---|---|---|
| `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/PHASE_STRUCTURE.md` | Architecture doc | Foundation Phases (1-4) section for mk2 unlock |
| `/data/json-data/blueprints/units/storage/multi_purpose_cryogenic_storage_tank_mk2_bp.json` | mk2 storage blueprint | Graphene_composite reference |
| `/data/json-data/blueprints/materials/graphene_composite_bp.json` | New material | Inputs + outputs + facility requirement |

### Reference Files — understand blueprint loading

| File | Purpose |
|---|---|
| `app/services/blueprint_loader_service.rb` | How blueprints load; schema validation |
| Material palette file | What game materials exist (find this) |
| Git log | Verify no JSON committed this session |

---

## Implementation Steps

### Step 0 — Create Status Synthesis Report (MANDATORY FIRST)

As described above. Do NOT proceed to Step 1 until synthesis report is created and posted in chat.

---

### Step 1 — Read PHASE_STRUCTURE.md and Verify Foundation Phase mk2 Unlock

**File to read**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/PHASE_STRUCTURE.md`

**Verification checklist**:

- [ ] **Foundation Phases (1-4) section exists and explicitly states mk2 cooling technology unlocked**
  - Should contain: "mk2 technology becomes available during Phases 1-4"
  - Should explain: Early HLT harvesters equipped with mk2 storage before Phase 0 launch
  - Should NOT say: "mk2 available Phase 11" or "mk2 research Phase 9-10"

- [ ] **Phase 0 section mentions HLT harvesters launch with mk2 cooling equipped**
  - Should mention: "Venus (Month 5) and Titan (Month 18) harvesters equipped with mk2 storage"
  - Should explain: mk2 cooling (0.15% daily loss) prevents boil-off losses during 8-10 month transit

- [ ] **Phase 10 (Venus operations) clarifies early harvester returns with mk2-cooled cargo**
  - Should distinguish: Mobile HLT harvesters (Phase 0 launch, return Phase 10) vs. stationary skimmers (Phase 11+)
  - Should mention: Early harvester returns have mk2 cooling already built-in; no boil-off enforcement code needed yet

- [ ] **Phase 11+ (Cycler/Skimmer activation) mentions stationary skimmers**
  - Should distinguish: Stationary skimmers are permanently deployed infrastructure (not mobile like HLT)
  - Should mention: Boil-off enforcement activates on cycler return transits

**Success criteria**: No contradictions; clear lifecycle distinction between mobile harvesters and stationary skimmers; boil-off timing is clear.

**If issues found**: Document specific lines/sections. Do NOT edit. Report findings.

---

### Step 2 — Identify Blueprint Loader Service and Load-Test JSON Files

**Task**: Locate the blueprint loader service and verify 10+ modified JSON files parse + load correctly.

**Files to test** (11 total):
```
/data/json-data/blueprints/units/storage/
  1. multi_purpose_cryogenic_storage_tank_mk2_bp.json
  2. multi_purpose_cryogenic_storage_tank_mk3_bp.json
  3. methane_storage_tank_mk2_bp.json
  4. methane_storage_tank_mk3_bp.json
  5. lox_storage_tank_mk2_bp.json
  6. lox_storage_tank_mk3_bp.json

/data/json-data/blueprints/materials/
  7. graphene_composite_bp.json

/data/json-data/blueprints/crafts/space/spacecraft/
  8. heavy_lift_transport_mk2_bp.json
  9. heavy_lift_transport_mk3_bp.json
  10. heavy_lift_transport_mk1_bp.json (verify existing file still loads)

/data/json-data/operational_data/crafts/space/spacecraft/
  11. heavy_lift_transport_harvester_venus_data.json
  12. heavy_lift_transport_harvester_titan_data.json
```

**Sub-step 2a — Locate Blueprint Loader**
- Search workspace for `BlueprintLoaderService`, `BlueprintValidator`, or similar class
- Check: What service/class loads JSON blueprints?
- File to find: `app/services/blueprint_*.rb` or similar
- Report: Exact file path + method name used for loading

**Sub-step 2b — Verify JSON Syntax**
- For each file: `json-parse file.json 2>&1` or equivalent
- Expected: No JSON parse errors
- Report: Any files that fail JSON parsing

**Sub-step 2c — Test Blueprint Loading**
- If loader service exists and can be called:
  - Create small test to load each file through actual loader
  - Report: Success/fail for each file + any error messages
- If loader cannot be easily tested:
  - Report: "Loader exists at [path] but cannot be easily tested in verification task; recommend RSpec test to follow"

**Success criteria**: All 11 files parse without JSON errors. All 11 files pass blueprint loader schema validation (if testable).

**If issues found**: Document which files fail + exact error messages. Do NOT edit. Report findings.

---

### Step 3 — Verify Graphene_Composite Material Dependency Chain

**File to check**: `/data/json-data/blueprints/materials/graphene_composite_bp.json`

**Verification checklist**:

- [ ] **Input: graphite**
  - Verify: `graphite` exists as a game material
  - Check: Is there a graphite extraction/mining blueprint?
  - Expected: Phase 10+ availability (asteroid or crustal mining)
  - Report: Location of graphite definition + phase availability

- [ ] **Input: epoxy_resin**
  - Verify: `epoxy_resin` exists as a game material (THIS IS CRITICAL — it's Gemini's suggestion, unverified)
  - Check: Is there an epoxy_resin blueprint or is it Earth-importable?
  - Expected: Earth import or production blueprint; available early phase
  - **STOP HERE IF NOT FOUND**: This repeats the exact mistake caught this session
  - Report: Location of epoxy_resin definition OR "MISSING — material does not exist"

- [ ] **Output: graphene_composite**
  - Verify: Material is referenced in mk2/mk3 storage blueprints
  - Check: `multi_purpose_cryogenic_storage_tank_mk2_bp.json` contains graphene_composite in materials list
  - Expected: ~250 kg per mk2 tank, ~500 kg per mk3 tank
  - Report: All files that reference graphene_composite + quantities

- [ ] **Facility requirement: fabrication_plant**
  - Verify: Fabrication plant blueprint exists
  - Check: Is it constructible?
  - Expected: Available Phase 11 at latest
  - Report: Location of fabrication_plant blueprint + phase unlock

- [ ] **Production rate consistency**
  - Query: graphene_composite production (8 hours, 10 kg per cycle) vs. demand in Phase 11+
  - Report: Is production sufficient for initial skimmer build-out? (no detailed calculation needed, visual inspection OK)

**Success criteria**: All four materials in chain exist + reference correctly. No circular dependencies. No unmakeable components.

**If epoxy_resin not found**: STOP. This is blocker. Report as found issue.

**If other issues found**: Document specific material paths + missing references. Do NOT edit. Report findings.

---

### Step 4 — Git Hygiene Check

**Task**: Confirm no JSON blueprint files were accidentally committed to git.

**Command**:
```bash
cd /Users/tam0013/Documents/git/galaxyGame
git log --oneline --all -20 | grep -i "blueprint\|storage\|graphene\|mk2\|mk3\|json" || echo "No blueprint-related commits found (correct)"
```

**Verification**:
- [ ] Command output shows NO commits mentioning blueprints or JSON data files
- [ ] If commits found: Verify they only mention `.md` documentation files, not `/data/json-data/` files

**Success criteria**: No JSON files in git history. Only documentation commits (PHASE_STRUCTURE.md, etc.) are OK.

**If issues found**: Report which commits reference JSON files. Do NOT attempt to revert; report as finding.

---

## Acceptance Criteria

- [ ] Step 1: PHASE_STRUCTURE.md Foundation phase mk2 unlock documented correctly
- [ ] Step 2: All 11 JSON blueprint files parse without errors + pass loader validation
- [ ] Step 3: graphite, epoxy_resin, graphene_composite, fabrication_plant all exist in game + reference correctly
- [ ] Step 4: No JSON files in git history (only documentation commits)

---

## Stop Conditions — escalate immediately if:

- epoxy_resin material does not exist (repeats caught mistake; blocker)
- JSON files cannot parse (5+ files) — schema error across multiple files
- Multiple contradictions found in PHASE_STRUCTURE.md (file unstable)
- JSON files found in git history (git hygiene violation)
- Any error that requires architectural decision or code changes

**On stop condition**: Report findings, do NOT attempt fixes. Create new LOW-priority task for remediation.

---

## Commit Instructions

No commits expected for this task. This is verification/reporting only. If issues are found:
1. Save synthesis report to summaries folder
2. Post findings in completion report (see section below)
3. Create new LOW-priority task for fixes if needed
4. Do NOT commit changes to any code or JSON files

If verification passes cleanly:
```bash
# Update synthesis report status to "VERIFIED"
cd /Users/tam0013/Documents/git/agent-tasks
git add projects/galaxy_game/summaries/2026-08-20-VERIFICATION-BLUEPRINT-ARCHITECTURE.md
git commit -m "chore: verification complete — all checks passed"
```

---

## Dependencies

**Blocked by**: None
**Blocks**: Any boil-off enforcement implementation task (needs clean PHASE_STRUCTURE.md)
**Related tasks**: 2026-08-20-ORBITAL-MECHANICS-BLOCKERS-FOR-SKIMMERS.md

---

## Completion Report

*Filled in by implementing agent after verification completes*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### Verification Results

#### Step 1: PHASE_STRUCTURE.md
- [ ] PASS — Foundation phase mk2 unlock documented correctly
- [ ] ISSUES FOUND:
  - [ ] Gotcha: [specific issue]
  - [ ] Gotcha: [specific issue]

#### Step 2: JSON Blueprint Load Testing
- [ ] PASS — All 11 files parse + load without errors
- [ ] ISSUES FOUND:
  - [ ] File: [name] — Error: [message]
  - [ ] File: [name] — Error: [message]

#### Step 3: Graphene_Composite Dependency Chain
- [ ] PASS — All materials exist + reference correctly
- [ ] ISSUES FOUND:
  - [ ] Missing: [material name]
  - [ ] Incorrect reference: [file] → [missing reference]

#### Step 4: Git Hygiene
- [ ] PASS — No JSON files in git history
- [ ] ISSUES FOUND:
  - [ ] Commit: [hash] mentions JSON files

### Follow-up Tasks Needed
[Any new backlog items identified — do NOT create files, just list]

### Lessons Learned
[What the verification process revealed about design practices or verification procedures]

---

## Handoff Summary

HANDOFF SUMMARY: [Verification passed cleanly | Issues found in: Step 1/2/3/4] | [Next action: proceed with boil-off implementation / fix identified issues / etc]
