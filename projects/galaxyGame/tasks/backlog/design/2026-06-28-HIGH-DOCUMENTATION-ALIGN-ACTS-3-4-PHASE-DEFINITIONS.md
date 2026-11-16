---
status: backlog
priority: HIGH
type: documentation
system_domain: ARCHITECTURE | PHASE_STRUCTURE | NARRATIVE
mvp_alignment: GAME_PROGRESSION_FRAMEWORK
local_worker_safe: false
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Design Architect**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/design/2026-06-28-HIGH-DOCUMENTATION-ALIGN-ACTS-3-4-PHASE-DEFINITIONS.md

READ FIRST: Task file specifies exact misalignments and required clarifications. This is design work: determine phase mappings, player entry mechanics, and structural requirements before implementation.

CRITICAL: Create DESIGN SYNTHESIS REPORT in chat BEFORE any writing (template in task file). Do not propose solutions until requirements are explicitly confirmed with user.
```

---

# TASK: Align Acts 3–4 Phase Definitions & Player Entry Point

**Status**: BACKLOG
**Priority**: HIGH
**Type**: documentation (design phase)
**Created**: 2026-06-28
**Domain**: Narrative Acts, Technical Phase Mapping, Game Progression Architecture

---

## Context

The documentation files defining game progression (Acts 1–4) currently have significant gaps that cause agent misunderstanding of the intended game scope and phase structure. Acts 3–4 definitions are incomplete or missing entirely, leading to:

1. **Agents organizing backlog tasks incorrectly** — they don't know which phases belong to Act 3 vs Act 4
2. **Misaligned expectations** — agents assume player entry occurs during Act 2, not after Act 4
3. **Incomplete technical phase mapping** — technical implementation phases (Phase 5+) don't extend to Acts 3–4, making phase folder organization in backlog confusing
4. **Crisis response scope missing** — Act 3 is defined as only "The Snap event" when it should include post-Snap crisis response + research + stabilization tech + AWS development

---

## Problem Statement

**Current State of Documentation:**

### File: `docs/storyline/01_story_arc.md`
- Act 1: ✅ Clear (Phases 1–14, NPC-only AI training)
- Act 2: ✅ Clear (Phase 15+, Eden expansion test)
- Act 3: ❌ Incomplete — only describes "Snap Crisis (Phase 16+)" as the event trigger, no post-Snap response phases
- Act 4: ❌ Missing — section states "Not yet planned: Deferred until Act 3 framework is established"

**Problem**: Agents reading this file believe:
- Act 3 ends when the Snap occurs
- Act 4 doesn't exist yet
- Players may enter during Act 2

### File: `docs/storyline/10_implementation_phases.md`
- Phases 1–4: ✅ Complete (System A)
- Phases 5–16: ✅ Mapped to Acts 1–3
- Phases 17+: ❌ Not defined — table stops at Phase 16

**Problem**: Agents reading this file cannot organize backlog tasks for Acts 3–4 because no technical phases are defined.

### File: `docs/storyline/02_crisis_mechanics.md`
- Snap event mechanics: ✅ Clear (mass-limit trigger, exit-shift)
- Post-Snap narrative: ❌ Not covered — only describes the crisis event, not the crisis response

**Problem**: Agents don't understand that Act 3 includes active crisis response (research, stabilization, AWS development), not just the triggering event.

---

## Clarifications Needed from User (Design Questions)

These questions must be answered BEFORE any documentation is written. This is design work — we're confirming intent, not guessing at implementation.

### Question 1: Act 3 Scope & Phase Mapping
**User stated**: "Act 3 = Snap crisis THEN active wormhole research + stabilization tech + AWS (Artificial Wormhole Station) development to reconnect to Eden"

**Design Question**: Which technical phases map to these Act 3 sub-phases?
- Phase 16+: Snap trigger event (existing)
- Phase 17: Post-Snap research & wormhole physics discovery?
- Phase 18: Stabilization tech development?
- Phase 19: AWS (Artificial Wormhole Station) construction?
- Or different numbering?

**Confirmation needed from user**: "Act 3 spans Phases [X] through [Y]" with sub-phase breakdowns

---

### Question 2: Act 4 Scope & Phase Mapping
**User stated**: "Act 4 = Portal tech development + system expansion + force-shifting exit to scout systems + AWS construction for new systems + AI Manager evaluates terraforming viability"

**Design Question**: Which technical phases map to these Act 4 features?
- Phase 20?: Portal tech development?
- Phase 21?: System scouting & force-shifting mechanics?
- Phase 22?: AWS construction for discovered systems?
- Phase 23?: AI terraforming viability evaluation?
- Or different numbering?

**Confirmation needed from user**: "Act 4 spans Phases [X] through [Y]" with sub-phase breakdowns

---

### Question 3: Player Entry Point
**User stated**: "Players enter AFTER Act 4 complete (not mid-Act 4)"

**Design Question**: Does this mean:
- Acts 1–4 are all NPC-only simulation/AI training (no player agency)?
- Players first enter game at the END of Act 4, inheriting the resulting world-state?
- All Acts 1–4 are "backstory" that happened off-screen before gameplay begins?

**Confirmation needed from user**: Exact description of what "player entry AFTER Act 4" means in terms of game progression and first player action

---

### Question 4: "System B" Context
**In 02_crisis_mechanics.md**, the file mentions:
- "The result: The origin point (Sol) remains stable and anchored, but the exit point shifts to a different, random location in deep space, leaving the original colony 'orphaned' and isolated."

**Design Question**: Is there a defined "System B" (the new exit point) or is it randomized each game?
- If defined: What system is it, what are its properties?
- If randomized: How does this affect act structure and game progression?

**Confirmation needed from user**: How is the post-Snap exit point determined?

---

### Question 5: AWS (Artificial Wormhole Station) Specifications
**User mentions AWS multiple times** but no technical definition exists.

**Design Question**: What are AWS key properties?
- Scope: Single station or multiple stations per system?
- Function: Just reconnects wormholes or also stabilizes them?
- Construction: Built by players, NPCs, or AI Manager during Act 3-4?
- Location: Fixed system locations or mobile/deployable?
- Cost: Resource requirements for construction?

**Confirmation needed from user**: High-level AWS specifications and role in Acts 3-4

---

## Files Involved

### Primary Files to Update
| File | Current State | Required Changes |
|---|---|---|
| `docs/storyline/01_story_arc.md` | Acts 1-2 clear; Acts 3-4 incomplete | Add complete Act 3 post-Snap phases; add full Act 4 scope; clarify player entry point |
| `docs/storyline/10_implementation_phases.md` | Phases 1-16 mapped; Phases 17+ missing | Add Phase 17-20+ definitions for Acts 3-4 with technical sub-phases |
| `docs/storyline/02_crisis_mechanics.md` | Snap event described; response not covered | Add post-Snap narrative arc, research phases, stabilization tech intro |

### Reference Files (Read but do not edit)
| File | Why You Need It |
|---|---|
| `docs/new_agent/SYSTEM_INTENT.md` | Overall game design philosophy |
| `docs/new_agent/rules/DECISIONS.md` | Locked architectural decisions |
| Any existing "System B" or "Eden" documentation if it exists | Understand established system definitions |

---

## 🔴 REQUIRED: Design Synthesis Report (Before You Start)

**DO NOT PROPOSE SOLUTIONS** until you confirm these five questions with the user. This is design work — confirm requirements first.

**Design Synthesis Report Template**:
```
## DESIGN SYNTHESIS REPORT

**Task**: Align Acts 3-4 Phase Definitions & Player Entry Point
**Type**: Documentation Design (not implementation)
**Date**: YYYY-MM-DD

### Requirements Clarification Status
- [ ] Question 1 (Act 3 phase mapping): User confirmed — [phases X-Y]
- [ ] Question 2 (Act 4 phase mapping): User confirmed — [phases X-Y]  
- [ ] Question 3 (Player entry point): User confirmed — [exact description]
- [ ] Question 4 (System B definition): User confirmed — [randomized/defined]
- [ ] Question 5 (AWS specifications): User confirmed — [scope/function/construction]

### Current Documentation State
- `01_story_arc.md`: Acts 1-2 complete, Acts 3-4 incomplete (0% alignment)
- `10_implementation_phases.md`: Phases 1-16 complete, Phases 17+ missing (0% alignment)
- `02_crisis_mechanics.md`: Snap event complete, post-Snap response missing (30% alignment)

### Alignment Gaps Identified
1. [gap description]
2. [gap description]
3. [gap description]

### Required Clarifications Before Writing
1. Act 3 phase scope — waiting for confirmation
2. Act 4 phase scope — waiting for confirmation
3. Player entry mechanics — waiting for confirmation
4. System B definition — waiting for confirmation
5. AWS specifications — waiting for confirmation

---

**DESIGN SYNTHESIS COMPLETE.** Awaiting user responses to all 5 questions before proposing documentation updates.
```

**POST THIS TO CHAT.** Include all 5 questions above. Wait for user confirmation before proposing any document changes.

---

## Acceptance Criteria

- [ ] All 5 design questions answered and confirmed with user
- [ ] Phase numbering for Acts 3-4 explicitly defined (Phases X-Y for Act 3, Phases Z-W for Act 4)
- [ ] Player entry point clearly stated in documentation
- [ ] AWS (Artificial Wormhole Station) role and specifications documented
- [ ] System B (post-Snap exit point) definition provided
- [ ] Documentation updates proposed (do NOT apply until user approves proposed changes)

---

## Stop Conditions — Escalate Immediately If:

- User answers require architectural decisions beyond documentation scope
- Any answer contradicts existing locked decisions in `docs/new_agent/rules/DECISIONS.md`
- AWS specifications require technical feasibility discussion (escalate to implementation team)
- Player entry point requires UI/UX design considerations
- Multiple contradicting definitions of Acts 3-4 exist in other documentation

---

## Next Steps After Design Approval

Once user confirms all 5 questions, the Design phase completes with:

1. **Design Document Output**: Proposed changes to `01_story_arc.md`, `10_implementation_phases.md`, and `02_crisis_mechanics.md` (exact text, not applied yet)
2. **User Approval Gate**: User reviews proposed changes
3. **Implementation Task Created**: New task file for applying documentation updates
4. **Backlog Reorganization Task Created**: New task to reorganize phase folders in `/tasks/backlog/design/` to match new phase numbering

---

## Dependencies

**Blocked by**: User confirmation of 5 design questions above  
**Blocks**: Any backlog reorganization tasks, agent understanding of phase structure  
**Related tasks**: Future task to reorganize `/tasks/backlog/design/phase*/` folders once phase numbering is confirmed

---

## Documentation
- [ ] This task serves as the design document
- [ ] Do not create separate design documents yet — wait for approval
- [ ] Upon approval, update: `01_story_arc.md`, `10_implementation_phases.md`, `02_crisis_mechanics.md`

---

## Completion Criteria (What "Done" Looks Like)

**DESIGN PHASE COMPLETE** when:
1. All 5 clarification questions have explicit user answers
2. Phase mappings (Acts 3-4) are numerically defined
3. Player entry point is clearly described
4. Design document with proposed changes is presented to user
5. User approves proposed changes before implementation begins

**NOTE**: This is design work. Completion = approved design document, not applied changes.

---

## Critical Note for Gemini Agent

This is **design and clarification work**, not implementation. Your role is to:

1. **Ask clarifying questions** (the 5 questions above) 
2. **Wait for user responses** before proposing anything
3. **Synthesize user responses** into coherent design document
4. **Propose documentation updates** (show exact changes, but do NOT apply them)
5. **Wait for user approval** before any implementation

Do not:
- ❌ Guess at Act 3-4 scope based on existing docs
- ❌ Propose phase numbers without user confirmation
- ❌ Apply changes to documentation without explicit approval
- ❌ Create new files (only update existing ones after approval)
- ❌ Proceed until all 5 questions are answered

**Your deliverable**: A design synthesis report with user-confirmed answers + proposed documentation changes (pending approval).
