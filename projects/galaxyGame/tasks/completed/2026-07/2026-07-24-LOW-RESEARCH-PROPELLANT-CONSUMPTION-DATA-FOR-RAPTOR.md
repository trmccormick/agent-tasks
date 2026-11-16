---
status: completed
priority: LOW
type: research
system_domain: CRAFT/EXHAUST
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
created: 2026-07-24
last_updated: 2026-08-02
completed: 2026-08-02
---

## ⚡ Minimal Handoff (Copy this to send to agent)
```text
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-24-LOW-RESEARCH-PROPELLANT-CONSUMPTION-DATA-FOR-RAPTOR.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-24-LOW-RESEARCH-PROPELLANT-CONSUMPTION-DATA-FOR-RAPTOR.md \
         projects/galaxy_game/tasks/active/2026-07-24-LOW-RESEARCH-PROPELLANT-CONSUMPTION-DATA-FOR-RAPTOR.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-24-LOW-RESEARCH-PROPELLANT-CONSUMPTION-DATA-FOR-RAPTOR.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-07-24-RESEARCH-PROPELLANT-CONSUMPTION-DATA.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Propellant Consumption Data for Generic Engine Types
**Status**: BACKLOG
**Priority**: LOW
**Type**: research
**Created**: 2026-07-24
**Last Updated**: 2026-07-30

## Context

`galaxy_game/app/models/craft/harvester.rb` line 127 uses a documented placeholder:
```ruby
propellant_consumed = (extraction_rate || 100) * 0.1
```
Comment says: "This is still a design parameter — real propellant tracking requires craft blueprint data"

The EXHAUST_COMPOSITION constants use generic methane/oxygen stoichiometry, but the scalar multiplier (0.1) has no basis in published performance data for any engine class.

**Important**: The codebase uses **generic engine names** (`methane_engine`, `lox_tank`, etc.), NOT real-world SpaceX/Raptor references. Research should use generic engine classes consistent with the codebase naming convention.

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: The codebase uses generic engine names, NOT real-world SpaceX/Raptor references.
- ❌ Wrong: Reference "SpaceX Raptor" or "Starship" in the task deliverable — the codebase uses `methane_engine`, `lox_tank` as generic types
- ✅ Right: Frame research around generic engine classes (methane/LOX, hydrogen/LOX, kerosene/LOX) with names consistent with the codebase
- Why: The project is a fictional game, not a SpaceX simulator. Real-world references in task files create confusion about what's canonical vs. inspiration

⚠️ **GOTCHA 2**: Multiple engine types exist or may be added — don't anchor research to a single class.
- ❌ Wrong: Provide data only for methane/LOX engines (the current harvester type)
- ✅ Right: Research propellant consumption ranges for at least 3 engine classes currently in the codebase ecosystem: methane/LOX, hydrogen/LOX (shuttle/SLS-class), kerosene/LOX (soyuz-class)
- Why: The game supports multiple craft types; each will need its own multiplier

⚠️ **GOTCHA 3**: `extraction_rate` in the harvester model is NOT analogous to engine thrust or flow rate — it's a mining throughput metric.
- ❌ Wrong: Assume extraction_rate maps directly to Isp, thrust, or propellant flow rate from aerospace data
- ✅ Right: Determine what physical quantity `extraction_rate` represents in the game's economy model, then derive the multiplier from that mapping
- Why: The multiplier bridges a mining metric to a fuel consumption metric — they are not the same physical dimension

---

## REQUIRED Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Propellant Consumption Data for Generic Engine Types
**Status**: [backlog → active → completed]
**Date**: YYYY-MM-DD

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| harvester.rb (line 127) | Understand extraction_rate context and current placeholder | [not started / pending / done] |
| base_craft.rb | Identify all engine types in NAVIGATION_UNIT_TYPES | [not started / pending / done] |
| Research sources | Find propellant consumption data for generic engine classes | [not started / pending / done] |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above
- ✅ Know which domain/credentials to use

### Expected Outcomes
[Exact description of what "done" looks like]

### Critical Gotchas I Will Avoid
- ❌ Using real-world SpaceX/Raptor names — instead ✅ Use generic engine class names consistent with codebase
- ❌ Providing data for only one engine type — instead ✅ Cover all relevant engine classes in the ecosystem
- ❌ Assuming extraction_rate = thrust/flow rate — instead ✅ Analyze what extraction_rate actually represents

---

**SYNTHESIS COMPLETE.** Ready to proceed with [PRIORITY 1 / PRIORITY 2 / etc].
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Research Goal

Find publicly available propellant consumption rates for **generic engine classes** and determine what values should replace the `0.1` placeholder in harvester.rb.

### Engine Classes to Cover (consistent with codebase naming)
1. **Methane/LOX engines** (`methane_engine`) — current harvester type
2. **Hydrogen/LOX engines** (shuttle/SLS-class) — high-Isp, used in upper stages
3. **Kerosene/LOX engines** (soyuz-class) — traditional launch vehicle propellant

### Sources to Check
- Published performance data for each engine class (thrust, Isp, chamber pressure, propellant flow rate)
- Compare against our `extraction_rate` field — determine what it maps to in the game's economy model
- Derive multiplier ranges per engine class

### Deliverable
For each engine class: one sentence with the correct multiplier value (or range) and its source, ready to paste into harvester.rb.

Example format:
```ruby
# Methane/LOX engines (methane_engine): 0.08–0.12 based on published Raptor flow rate / thrust ratio
# Hydrogen/LOX engines (hydrogen_engine): 0.05–0.08 based on RS-25 upper-stage data
# Kerosene/LOX engines (kerosene_engine): 0.10–0.15 based on RD-180 flow rate / thrust ratio
```

## Notes
- This is a LOW-priority research task — the current 0.1 is an honest placeholder with documentation
- The real blocker was fabricated data without flagging; that's resolved
- This prevents the placeholder from silently becoming "real" in future iterations
- Use generic engine class names consistent with the codebase (`methane_engine`, etc.), NOT real-world SpaceX/Raptor references

## Stop Conditions
- Stop if harvester.rb line 127 context cannot be understood without running Docker — report blocker before proceeding
- Stop if no published performance data exists for any of the 3 engine classes — note which classes lack data and skip them

## Completion Report

When done, provide:
1. **Multiplier values**: One sentence per engine class with value/range and source
2. **Code changes needed**: Exact lines in harvester.rb to modify (if any)
3. **Research sources**: URLs or references used for each engine class
4. **Known limitations**: Any engine classes where data is unavailable

## Handoff Summary

**Task**: Propellant Consumption Data for Generic Engine Types
**Status**: backlog → active → completed
**Type**: research (data lookup, no code changes required)
**Key Risk**: extraction_rate is a mining metric, not an aerospace metric — multiplier derivation requires understanding the game's economy model mapping
**Approach**: Research published data for 3 engine classes (methane/LOX, hydrogen/LOX, kerosene/LOX), derive multiplier ranges, deliver as documentation-ready values

---

## Synthesis Report

Full research findings linked here: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-07-24-RESEARCH-PROPELLANT-CONSUMPTION-DATA.md`

