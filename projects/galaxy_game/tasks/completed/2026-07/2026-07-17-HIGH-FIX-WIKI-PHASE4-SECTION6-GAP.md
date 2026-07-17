# Task: Fix Wiki Reorganization Phase 4 Section 6 Gap

**Created**: 2026-07-17  
**Priority**: HIGH  
**Project**: galaxy_game  
**Role**: Implementation Agent (Qwen)  
**Status**: active  

---

## Problem

Phase 4 wiki reorganization has a structural gap: **Section 6 (Simulation Engine) is completely missing from all 9 deliverable files.**

WIKI_SITE_MAP.md jumps from `## 5. Game World Model` directly to `## 7. Economy`. No other Phase 4 file contains section 6 content either. This was introduced when two new sections (4. Planetary Simulation, 5. Game World Model) were inserted after the original 12-section structure, shifting everything forward by 2 — but section 6 itself was never written.

The canonical numbering table (provided in the handoff below) explicitly includes section 6:

```
1. Vision
2. Story
3. Universe Generation
4. Planetary Simulation
5. Game World Model
6. Simulation Engine        ← MISSING FROM ALL FILES
7. Economy
8. Manufacturing
9. Settlements
10. Transportation
11. AI Manager
12. Gameplay
13. Development
14. Reference
```

---

## Context: What Section 6 Should Contain

Section 6 is **"Simulation Engine"** — the architectural glue that coordinates StarSim and TerraSim as fundamentally different concerns.

### Core Architectural Truths (from 10-session framework):

- **StarSim** = solar system generation/loading layer only. Creates stars, planets, moons, orbital parameters. Does NOT simulate planets.
- **TerraSim** = planetary evaluation engine. Takes a planet's current state and models gradual, industrially-driven change over time. Does NOT generate planets.
- **Game World Model** = how systems are represented (Galaxy → Solar System → Star → Celestial Body → Planet Environment → Settlement → Structure → Units)
- **Simulation Engine** = the layer that coordinates StarSim and TerraSim

### Simulation Data Flow (ChatGPT recommended this as a core document):

```
Universe Generation
        │
        ▼
    StarSim
    (Create stars, planets, moons,
     asteroids, orbital parameters)
        │
        ▼
Planetary Database
        │
        ▼
    TerraSim
    (Evaluate planetary state)
     • Atmosphere
     • Geosphere
     • Hydrosphere
     • Biosphere
     • Climate
        │
        ▼
World Model Layer
        │
        ▼
Settlements / Economy / Manufacturing
        │
        ▼
   Gameplay Systems
```

### Layer Ownership (resolved 2026-07-17):

| Asset | Layer 0 (Terrain) | Layer 1 (Biome) | Notes |
|-------|-------------------|-----------------|-------|
| desert | ✅ | ❌ | Base geological substrate |
| tundra | ✅ | ❌ | Climate-controlled substrate |
| ocean | ✅ | ❌ | Opaque fluid base |
| mountains | ✅ | ❌ | Opaque geological base |

This means CanonicalMapService queries desert/tundra/ocean as terrain types (Layer 0), and Layer 1 can overlay sparse vegetation when climate variables allow.

### ⚠️ Hydrosphere — Computed Overlay, Not Static Terrain

**Important**: The hydrosphere (liquid layer) in the current monitor view uses **bathtub fill logic**:
- Water fills low-elevation areas based on terrain elevation data
- Liquid layer tiles are **variable/computed**, not fixed per-tile assignments
- The surface UI abstracts this away — it's about gameplay (settlements near water, trade routes), not raw simulation data

**Implications for Section 6 wiki docs**:
- Hydrosphere is a **computed overlay**, not a static terrain/biome assignment
- It sits between Layer 0 and Layer 1: derived from elevation + climate state, then rendered as liquid tiles
- CanonicalMapService needs a special case for hydrosphere: it's not a lookup, it's a computation
- Document in section 6 that the simulation pipeline produces hydrosphere state from elevation + climate variables; the renderer maps that state to liquid tiles at render time

---

## Target Files

All files are in `/Users/tam0013/Documents/git/galaxyGame/docs/wiki_reorganization/phase4/`:

| File | What Needs Fixing |
|------|------------------|
| `WIKI_SITE_MAP.md` | Insert section 6 between sections 5 and 7. Update navigation map (currently shows 14 but skips 6). Update reading order. |
| `README.md` | Tree diagram jumps 3→4→7→8... Fix to show all 14 sections. Change "12-section" → "14-section" in 5 places. |
| `DOCUMENT_RELOCATION_PLAN.md` | Section headings need section 6 inserted; shift 7+→8+ accordingly. |
| `CANONICAL_DOCUMENT_INDEX.md` | Add section 6 entries; shift 7+→8+ references. |
| `CROSS_REFERENCE_PLAN.md` | Add section 6 cross-links; fix all section-to-section references (currently jumps 4→7). |
| `MISSING_WIKI_PAGES.md` | No changes needed — uses descriptive headers, not numbered sections. |
| `ARCHIVE_PLAN.md` | No changes needed — uses descriptive headers, not numbered sections. |
| `CONTRIBUTOR_GUIDE.md` | Section placement table needs section 6; shift 7+→8+. |
| `DOCUMENT_CLASSIFICATION.md` | Section references need section 6; shift 7+→8+. |

---

## Implementation Steps

### STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
```bash
cd /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks
git mv backlog/current/2026-07-17-HIGH-FIX-WIKI-PHASE4-SECTION6-GAP.md active/
```

### STEP 1 — Read the canonical numbering table above. Memorize it. Do not proceed until you have it committed to memory.

### STEP 2 — Fix WIKI_SITE_MAP.md (most critical file)
- Insert a new `## 6. Simulation Engine` section between sections 5 and 7
- Section 6 content should include:
  - Core truth box: "Simulation Engine coordinates StarSim and TerraSim as fundamentally different concerns"
  - Table with pages: SIMULATION_ENGINE_OVERVIEW (Canonical), STAR_SIM_COORDINATION, TERRA_SIM_COORDINATION, SIMULATION_PIPELINE (all Supporting)
  - Simulation data flow diagram (the ASCII art from the context above)
- Update navigation map to show all 14 sections (not skipping any)
- Update reading order to include section 6

### STEP 3 — Fix README.md
- Change "12-section" → "14-section" in ALL places (5 occurrences)
- Fix tree diagram to show all 14 sections sequentially (currently jumps 3→4→7→8)
- Update reading order to include section 6

### STEP 4 — Fix DOCUMENT_RELOCATION_PLAN.md
- Insert section 6 heading with appropriate relocation entries
- Shift all section 7+ headings to 8+
- Update all cross-references

### STEP 5 — Fix CANONICAL_DOCUMENT_INDEX.md
- Add section 6 canonical entries
- Shift all section 7+ references to 8+

### STEP 6 — Fix CROSS_REFERENCE_PLAN.md
- Add section 6 cross-links (Simulation Engine → Economy, Manufacturing, Settlements, Transportation)
- Fix all section-to-section references (currently jumps 4→7)
- Update section numbering throughout

### STEP 7 — Fix CONTRIBUTOR_GUIDE.md
- Update section placement table to include section 6
- Shift all section 7+ references to 8+

### STEP 8 — Fix DOCUMENT_CLASSIFICATION.md
- Update section references to include section 6
- Shift all section 7+ references to 8+

### STEP 9 — Verify (ONE PASS ONLY)
- Read each file once. Confirm all sections 1-14 are present and sequential.
- No section numbers should be missing, duplicated, or out of order.
- **DO NOT re-verify in a loop.** One pass per file, then move to the next file.

---

## Workflow Rules (CRITICAL — Read Before Starting)

**DO NOT make incremental section edits.** This is the exact failure mode that caused this gap in the first place.

**For each file:**
1. Read the ENTIRE file once
2. Apply ALL numbering changes in ONE complete rewrite pass using the canonical table above as your single source of truth
3. Verify numbering exactly once
4. Save the file
5. Move to the next file

**DO NOT:**
- Edit section-by-section
- Re-verify the same file multiple times
- Leave any old section numbers behind
- Skip any of the 9 files

---

## Acceptance Criteria

- [ ] All 9 Phase 4 files have sections 1-14 present and sequential (no gaps)
- [ ] WIKI_SITE_MAP.md has a complete `## 6. Simulation Engine` section with content
- [ ] README.md tree diagram shows all 14 sections without skipping any numbers
- [ ] No file contains "12-section" — all references updated to "14-section"
- [ ] CROSS_REFERENCE_PLAN.md includes section 6 cross-links
- [ ] All cross-references between sections are consistent (no jumps from 5→7 or 4→7)
- [ ] Layer ownership table (desert/tundra/ocean/mountains → Layer 0) is reflected in any terrain/biome documentation

---

## Stop Conditions

- Report to chat when all 9 files are complete with exact commit message ready
- Wait for explicit approval before running `git commit` or `git push`

---

## Return Format

When complete, provide:
1. Brief summary of what was done
2. Exact commit message (do NOT execute it yet)
3. `git diff --stat` output (do NOT execute git add/commit/push yet)
4. Wait for explicit approval before committing
