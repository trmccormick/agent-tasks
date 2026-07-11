---
status: backlog
priority: HIGH
type: research
system_domain: TERRA_SIM
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Research Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-10-HIGH-RESEARCH-ATMOSPHERIC-TRANSFER-VENTING-DESIGN.md

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv to new folder
  - New/untracked file: move with filesystem (mv), then git add the final path
  - Never copy task files between folders
READ FIRST: Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# RESEARCH: Design `vent_to_source` Mode for AtmosphericTransferService

**Status**: BACKLOG
**Priority**: HIGH
**Type**: research
**Created**: 2026-07-10
**Last Updated**: 2026-07-10 — Implementation Agent (Qwen3.6-35b)

---

## Dependency

**Primary**: `docs/new_agent/projects/galaxy_game/summaries/2026-07-09-RESEARCH-ATMOSPHERIC-SKIMMER-AUDIT.md`
- Section "Gap: No vent to space mode" — gases are always extracted from source and delivered to target
- `AtmosphericTransferService` currently has no mechanism to remove gas without delivering it

---

## Problem Statement

Skimmers extract raw mixed atmospheric gas, process a portion for their own fuel tanks, and must handle the remainder through a **three-state routing model**. Currently there is **no way to route gases based on storage capacity or processing state** — all extracted gas is forced to a target body.

**Three-State Model (Architectural Requirement)**:
1. **Refined/Stored**: Priority routing to `cryogenic_storage_unit` or dedicated fuel tanks
2. **Raw/Overflow**: `vent_to_source` (add gas back to `Atmosphere` object) when storage capacity is reached or processing capacity is exceeded
3. **Processing Byproducts**: Treated as assets for storage, NOT venting (e.g., CO from Venus MOXIE processing)

---

## Research Requirements

### 1. Three-State Routing Logic Design

Research and design the priority routing logic for extracted gases:

**State 1 — Refined/Stored (Priority)**
- Route processed/refined gas to `cryogenic_storage_unit` or dedicated fuel tanks
- Respect storage capacity constraints before considering venting
- Track refined gas mass separately from raw gas mass

**State 2 — Raw/Overflow (Fallback)**
- Implement `vent_to_source` when storage capacity is reached OR processing capacity is exceeded
- Add vented gas back to the source `Atmosphere` object (NOT to space)
- Track vented mass separately in results hash

**State 3 — Processing Byproducts (Asset, Not Waste)**
- CO from Venus MOXIE processing → treat as storage asset, NOT ventable
- O2 from CO2 scrubbing → treat as storage asset, NOT ventable
- Other byproducts must be evaluated per-gas for storage vs. vent eligibility

### 2. Gas Tracking Schema

Design how all three states are tracked:
```ruby
# Current results schema
@results = {
  gases_extracted: { 'N2' => 500, 'CH4' => 50 },
  gases_delivered: { 'N2' => 490, 'CH4' => 48 },  # 98% efficiency
  gases_produced: {},
  efficiency: 0.98,
  success: true,
  messages: []
}

# Proposed extended schema (three-state model)
@results = {
  gases_extracted: { 'N2' => 500, 'CH4' => 50 },
  gases_stored:    { 'CH4' => 48, 'CO2' => 100 },   # NEW — refined/stored gas
  gases_vented:    { 'N2' => 10 },                    # NEW — overflow to source atmosphere
  gases_byproducts:{ 'CO' => 35, 'O2' => 40 },        # NEW — processing byproducts (assets)
  efficiency: 0.98,
  success: true,
  messages: []
}
```

### 3. Storage Capacity Integration

Research how to integrate with existing storage infrastructure:
- How does `cryogenic_storage_unit` report available capacity?
- What happens when cryo tanks are full — does extraction stop or vent immediately?
- Are there per-gas capacity limits on storage units?
- How do fuel tank capacities (from HLT blueprint specs) factor into routing priority?

### 4. Safety State Checks

Research what state checks are needed before allowing venting:
- Is the skimmer in transit (not docked)?
- Does the source atmosphere have capacity for returned gas?
- Are there environmental constraints on venting specific gases back to source?
- Has storage capacity been verified as full before venting is allowed?

### 5. Integration with Existing Modes

Design how `vent_to_source` interacts with existing transfer modes:
- **raw mode**: Should support three-state routing — store what fits, vent overflow
- **selective mode**: Should allow partial processing — refined gas stored, excess vented
- **processed mode**: Byproducts (CO, O2) must be tracked as assets, not ventable

### 6. MVP Scope vs Phase 9+

Determine what is MVP-relevant vs terraforming:
- **MVP**: Three-state routing for skimmer operations; vent excess to source atmosphere when storage full
- **Phase 9+**: Terraforming-scale atmospheric modification, multi-world atmospheric interaction

---

## Deliverables

1. Design document specifying `vent_to_source` mode API and three-state routing logic
2. Extended results schema with stored/vented/byproduct gas tracking
3. Storage capacity integration requirements (cryo tanks, fuel tanks)
4. Safety state check requirements list
5. Integration plan for existing transfer modes
6. MVP vs Phase 9+ scope boundary definition
7. **Verification step**: Mass balance check — total extracted mass must equal stored + vented + byproducts (within transport efficiency tolerance)

1. Design document specifying `vent_to_space` mode API and behavior
2. Extended results schema with vented gas tracking
3. Safety state check requirements list
4. Integration plan for existing transfer modes
5. MVP vs Phase 9+ scope boundary definition

---

## Stop Conditions — escalate to user immediately if:
- The existing `AtmosphericTransferService` architecture cannot support a venting mode without breaking changes
- Any architectural decision is required before research can continue
