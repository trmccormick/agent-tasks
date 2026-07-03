# ANALYSIS: Settlement View Phase 3

**Planning Agent**: Planning Agent  
**Date**: 2026-07-01  
**Original Task**: `/tasks/review/settlement-view-phase3.md`

---

## Phase Routing Gate

**Does this task relate to Phase 5 Luna MVP (luna_mission.rake)?**

- [ ] YES — Phase 5 (Luna mission validation, settlement testing, AI training)
- [x] YES — Phase 6+ (Luna worldhouse, depots, shipyards, Mars/Venus/Psyche footholds)
- [x] YES — Design/Reference (Game design, architecture documentation, pattern research)

**Rationale**: This task is a **UI/cosmetic feature** (settlement view canvas rendering) that has no dependency on luna_mission.rake execution. The rake file uses `TaskExecutionEngineV2` with JSON mission profiles and settlement data models — it does not reference any JavaScript admin views or canvas rendering. Settlement view is an observation/management UI layer, not a simulation prerequisite. It belongs in **Phase 6+** (post-infrastructure) or as **Design/Reference** since the game design concept may still be valid for later-phase worldhouse management interfaces.

---

## Executive Summary

This task specifies implementing a high-resolution (65536x32768px) settlement view canvas with sprite atlas integration for domes, factories, and panels — the third phase of a monitor UI upgrade chain. It is a **Phase 7+ cosmetic/observation feature** with no impact on Phase 5 Luna MVP execution or any simulation logic.

---

## Issues Identified

### 1. Overlap with Recent Work

**Finding**: The task references "Phase 1: Planetary → Phase 2: Regional → Phase 3: Settlement" as a monitor UI upgrade chain, but there is no evidence these earlier phases were ever implemented. The current codebase has `luna_mission.rake` for simulation execution and admin views that are focused on mission/profile management, not settlement-level canvas rendering.

- **Related task**: Monitor/Canvas rendering bugfix (listed as Phase 5 deliverable) — this is a foundational fix, not the high-res canvas work described here
- **Impact**: The "Phase 1/2" foundation this task depends on likely doesn't exist. This task may be building on phantom prerequisites.

### 2. Stale Content/References

**Finding**: The task states "Current: No settlement view implementation exists" and uses `find` to verify — but this is a **Phase 7+** task (late tutorial), not a Phase 5 prerequisite. The phase classification itself may be stale.

- **Example**: Priority listed as MEDIUM, phase as phase7+, created 2026-06-21
- **Current status**: Phase 5 is ACTIVE with Luna mission validation work. This task sits in backlog under `phase7+/` — correctly classified but not actionable until much later in the roadmap.

### 3. Ambiguities

**Finding**: Several requirements are underspecified for implementation:

- **Unclear aspect**: "640x64 sprite atlas" — what does this mean? 640x64 pixels total? 10 sprites of 64x64? The atlas dimensions aren't explained.
- **Why it matters**: Without sprite layout specs, an implementer can't create the asset or write coordinate mapping logic.
- **Unclear aspect**: "Worldhouse placement grid" — what is a worldhouse in this context? Is this Luna-specific or generalizable to Mars/Venus?
- **Why it matters**: The concept bridges game design and implementation; without definition, the feature scope is ambiguous.

### 4. Prerequisites/Dependencies

**Finding**: This task depends on:
- **Dependency**: Phase 7 completion (orbital infrastructure) which triggers parallel expansion
- **Blocker?**: Yes — this is explicitly Phase 7+ (late tutorial). It cannot be worked on until Phases 5-6 complete, and even then it's lower priority than the actual simulation work.

---

## Questions for Gemini

1. **Overlap**: Does any existing admin canvas/monitor code provide a foundation for settlement view, or is this a greenfield implementation? What does the "Phase 1: Planetary → Phase 2: Regional" chain actually consist of (if anything exists)?
2. **Data gap**: What sprites are needed in the atlas (exact list of domes, factories, panels)? What are their individual dimensions and layout within the 640x64 atlas?
3. **Scope**: Is a 65536x32768 canvas still appropriate, or would a tiled/zoomable approach be more practical for browser rendering? Has browser canvas performance been tested at this scale?
4. **Approach**: Should this be split into (a) sprite atlas creation and (b) canvas rendering as separate tasks? Would the game design value exist even if the implementation timing shifts to Phase 10+?

---

## Concerns/Recommendations

**Concern 1: Canvas size is extreme.** 65536x32768 pixels = ~2.1 billion pixels. Even at 1 byte per pixel, that's 2GB of texture data. This will likely crash browsers or require WebGL with careful tiling/streaming. The task should be reviewed for technical feasibility before implementation.

**Concern 2: Low priority relative to simulation work.** Phase 5 Luna MVP is ACTIVE and focuses on simulation validation (luna_mission.rake). Settlement view is a cosmetic/observation layer that doesn't unblock any simulation work. It should remain in backlog but not be prioritized above simulation prerequisites.

**Recommendation**: Keep this task in backlog under Phase 7+ or later. Before implementation, research browser canvas limits and consider a tiled rendering approach instead of a single massive canvas. The game design concept (high-res settlement management view) is valid for late-game worldhouse management but needs technical refinement.

---

## Next Steps

Once Gemini responds to these questions, they will:
- Identify what Qwen research is needed (browser canvas limits, existing admin canvas code audit)
- Create a handoff for Qwen
- (You'll receive research assignment)
