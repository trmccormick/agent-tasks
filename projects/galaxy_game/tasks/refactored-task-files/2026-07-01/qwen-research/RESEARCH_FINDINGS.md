# RESEARCH FINDINGS: Settlement View Phase 3

**Research Agent**: Qwen  
**Date**: 2026-07-01  
**Original Task**: /tasks/review/settlement-view-phase3.md  
**Work Folder**: /docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/

---

## Research Question 1: Phase Relevance & Luna Mission Rake Impact

### Answer: NOT related to Phase 5 Luna MVP. Belongs in **Phase 14+** (Eden system / Act 2).

**Evidence**:
- luna_mission.rake uses TaskExecutionEngineV2 with JSON mission profiles and settlement data models (ActiveRecord)
- No JavaScript admin views, canvas rendering, or sprite atlas references exist in the rake file
- The rake creates/finds settlements via Settlement::BaseSettlement, organizations via Organizations::BaseOrganization, and seeds inventory — all backend Rails logic
- Settlement view is an **observation/management UI layer** with zero simulation dependency

**Would removing this task break luna_mission.rake?** No. The rake has no dependency on any admin JS views or canvas rendering.

**Phase routing recommendation**: **Phase 14+** (Eden system / Act 2). This is a player-facing management interface, not simulation work. Phases 5-13 are all NPC-only — players don't enter the game during Act 1.

---

## Research Question 2: Current Codebase Status vs Task Requirements

### Current State:
| Component | What Exists | Evidence |
|-----------|-------------|----------|
| settlement_view.js | Does NOT exist | find galaxy_game/app/javascript/admin -name "*settlement*" returns nothing |
| settlement_view.html.erb | Does NOT exist | No settlement view template in celestial_bodies/ |
| settlement_sprites.png | Does NOT exist | No settlement sprite atlas in public/tilesets/ or galaxy_game/public/tilesets/ |
| Admin canvas system | monitor.js — planetary/regional views only | galaxy_game/app/javascript/admin/monitor.js (1000+ lines) |
| Highest-res view | Regional view — 16K canvas (16384x8192 at 100m/pixel) | docs/agent/rules/PHASE2_CANVAS_COMMIT.md |
| Sprite atlas system | Phase 2 regional atlas — 288x32, 9 terrain tiles | galaxy_surface.png + galaxy_regional_atlas.json |
| Canvas rendering engine | monitor.js with adaptive scaling, pan/zoom, layer overlays | Lines 597-800+ of monitor.js |

### Task Requirements:
| Requirement | Status | Gap |
|-------------|--------|-----|
| 65536x32768 canvas at 10m/pixel | Not implemented | Needs entirely new rendering approach |
| Sprite atlas (640x64, 10 sprites) | Not implemented | Phase 3 sprite prompt exists in docs but no asset or JSON mapping |
| Worldhouse placement grid overlay | Not implemented | No grid system exists in any admin view |
| Construction preview | Not implemented | No hover/preview state system exists |

### Gap Analysis:
The existing monitor.js provides a **foundation for the rendering engine pattern** (pan/zoom, viewport management, layer overlays) but is **capped at 4096px max canvas size** (line 597). The settlement view requires a fundamentally different approach — either WebGL or tiled rendering — because:

1. monitor.js enforces `maxCanvasSize = 4096` with adaptive scaling down
2. Settlement view needs 65536x32768 — **16x wider** than the current max
3. The sprite atlas system used for Phase 2 (288x32 terrain tiles) is a different pattern from the settlement structure atlas (640x64, 10x64px sprites)

### Supporting Evidence Found:
- docs/agent/planning/PHASE_3_SETTLEMENT_VIEW_PLAN.md — Original Phase 3 plan with canvas specs
- docs/agent/planning/PHASE_3_SPRITE_SHEET_PROMPT.md — Sprite atlas spec (10 sprites, 64x64 each, single row)
- CHROMAKEY_PHASE3_README.md — Chromakey workflow for extracting settlement sprites from AI-generated images
- scripts/chromakey_spritesheet.py — Sprite sheet generation script exists but was never run for Phase 3

---

## Research Question 3: Game Design Validity

### Answer: Still valid because...

The high-resolution settlement view concept is strategically sound for late-game worldhouse management. The game design vision (SimCity-style isometric pixel art at 10m/pixel scale for detailed settlement construction) aligns with the overall game's simulation depth.

What's still valid:
- The 640x64 sprite atlas spec is clear and well-defined: 10 sprites (dome, solar panel, factory, hydroponics farm, research lab, power station, water extractor, landing pad, storage module, construction crane) arranged in a single row at 64x64 each
- The worldhouse placement grid concept is game-design sound for late-game settlement management
- Construction preview (hover/valid placement zones) is a standard and useful UX pattern

What's outdated:
- The assumption that Phase 1 (Planetary) → Phase 2 (Regional) → Phase 3 (Settlement) was a linear UI upgrade chain — only Phases 1-2 were implemented; Phase 3 never materialized
- The "65536x32768 canvas" specification is technically problematic (see Q4)

Usefulness for Phase 14+: High. The sprite atlas spec and game design concepts are directly applicable to late-game worldhouse management interfaces. The chromakey_spritesheet.py tool exists and could be reused.

---

## Research Question 4: Technical Feasibility (Critical)

### Answer: Not feasible as specified — needs major redesign.

### Browser Canvas Limits Analysis:

Theoretical limits (modern browsers):
- Chrome/Edge: ~32,767px max dimension (WebGL texture limit), ~16,384px for 2D canvas in practice
- Firefox: ~32,767px max dimension
- Safari: ~16,384px max dimension

The proposed 65536x32768 canvas exceeds browser limits:
- Width (65536) is 2x the theoretical maximum for Chrome
- Height (32768) is at the edge of Firefox's limit
- Memory: 65536 x 32768 x 4 bytes (RGBA) = ~8.5 GB of texture memory — far beyond any browser's capacity

Current codebase enforcement:
- monitor.js line 597: const maxCanvasSize = 4096; — hard-coded cap
- Line 601-602: Adaptive scaling kicks in when canvas exceeds 4096px, reducing tile size to fit

### Recommended Approaches:

Option A: WebGL with tiled rendering (recommended)
- Use a WebGL renderer (e.g., PixiJS, Three.js, or custom shader)
- Tile the 65536x32768 world into 1024x1024 or 2048x2048 chunks
- Only render tiles visible in the viewport + margin
- Memory: ~4-16 MB per tile (vs 8.5 GB for full canvas)
- This is how SimCity 2013 and similar games handle massive maps

Option B: Canvas tiling with 2D context
- Split the world into multiple <canvas> elements (tiles)
- Each tile renders at a manageable size (e.g., 512x512)
- CSS grid positions tiles; JavaScript updates visible tiles on pan/zoom
- No WebGL dependency but more complex DOM management

Option C: Reduce resolution, increase zoom
- Instead of 65536x32768 at 10m/pixel, use 4096x2048 with zoom levels
- Zoom in to see settlement details (similar to Google Maps)
- Loses the "always-visible high-res" feel but works within browser limits

### Recommendation: Option A (WebGL + tiling) is the only approach that preserves the original design intent of a massive, detailed settlement view. The current monitor.js (2D canvas) cannot be extended to support this scale without a complete rewrite.

---

## Phase Routing Recommendation

**Final recommendation: Phase 14+ (Eden system / Act 2) — deferred until player-facing UI is needed.**

### Reasoning against Phases 5-8:
- Phase 5 (ACTIVE): Luna mission validation — simulation testing, no UI work. Settlement view has zero dependency on luna_mission.rake.
- Phase 6: Luna surface & infrastructure — worldhouse construction mission profiles (simulation), not the management UI.
- Phase 7: Orbital depot infrastructure — LEO depots, resource deposits (simulation).
- Phase 8: Shipyard & craft construction — manufacturing validation (simulation).
- All of Phases 5-8 are NPC-only simulation work — players don't enter the game during Act 1.

### Reasoning against Phases 9-13:
- These are all parallel NPC expansion phases (Mars, Venus, logistics, optional branches, Psyche).
- Still NPC-only — no player-facing UI is built during these phases.
- The settlement view concept could be useful for observing Mars/Venus worldhouse management, but building it now would be premature since the simulation data it displays doesn't exist yet.

### Why Phase 14+ (Act 2):
- Phase 14+ is where AI operational independence begins and Eden system expansion starts — this is when player-facing interfaces become relevant.
- Settlement view is a player management interface for high-resolution settlement construction/observation.
- The game design concept (SimCity-style isometric pixel art at 10m/pixel) aligns with late-game worldhouse management that players would interact with during Act 2+.
- Building it now wastes effort on infrastructure (WebGL tiling engine) that won't be needed until Phase 14+.

### What to do with this task:
- Preserve the sprite atlas spec (PHASE_3_SPRITE_SHEET_PROMPT.md) as reference for Phase 14+
- Preserve the game design concepts (worldhouse grid, construction preview) as reference
- Deprecate the "65536x32768 canvas" specification — replace with WebGL tiling approach in a future task
- Create a new Phase 14+ task: "WebGL Settlement View Engine" that implements tiled rendering + sprite atlas integration for player-facing worldhouse management

### Corrected Phase Routing:
| Question | Answer |
|----------|--------|
| Phase routing? | Phase 14+ (Eden system / Act 2) — player-facing UI, not simulation work |
| luna_mission.rake impact? | None — no dependency |
| Current codebase overlap? | monitor.js provides rendering pattern but is capped at 4096px; no settlement-specific code exists |
| Game design valid? | Yes — sprite atlas spec (10 sprites, 64x64) and worldhouse grid are sound concepts for late-game |
| Canvas feasible as specified? | No — 65536x32768 = ~8.5 GB texture exceeds browser limits; needs WebGL tiling redesign |
| What to preserve? | Sprite atlas spec, worldhouse grid concept, construction preview UX |
| What to discard? | "65536x32768 canvas" specification; linear Phase 1→2→3 UI upgrade chain assumption |
