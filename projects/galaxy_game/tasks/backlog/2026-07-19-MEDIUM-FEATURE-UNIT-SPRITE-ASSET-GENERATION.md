---
date_created: 2026-07-19
date_modified: 2026-07-19
status: backlog
priority: MEDIUM
type: FEATURE
component: Asset Generation
phase: Phase 2 - Asset Registry
relates_to:
  - 2026-07-13-HIGH-FEATURE-UNIT-LAYER-RENDERING (parent task, blocks visual deployment)
  - NEEDS_REVIEW.md entry (sprite visual validation gap)
---

# Unit Sprite Asset Generation

## Summary

Replace misaligned placeholder sprites from imprecise source image with properly generated or commissioned unit/structure sprites. Current sprites (sprite_00.png through sprite_15.png) are visually broken due to AI-generated collage source material that isn't precision-gridded for extraction.

## Problem Statement

**Current State**: Layer 5 rendering (TerrainDataBuilder + SurfaceView.js) is fully implemented and tested (18/18 RSpec passing), but sprite assets are **production-unsuitable**:
- Source image: AI-generated mockup collage (`test-images/ChatGPT Image Mar 4 2026...`)
- Extraction result: Progressive misalignment (sprite_00 OK, sprite_15 severely degraded)
- RSpec validation: Insufficient (validates structure only, never inspects PNG visual content)
- Current location: Tracked as game assets under `app/assets/images/unit_sprites/`

**Impact**: Cannot deploy Layer 5 unit rendering without gating it behind a feature flag or replacing sprites.

**Root Cause**: Source image lacks uniform grid precision; any small drift between cells compounds over distance.

---

## Scope

### Option A: ChatGPT Direct Generation (Preferred, if viable)
**Approach**: Use ChatGPT's image generation to create 16 individual unit/structure sprites

**Specifications**:
- **Count**: 16 sprites (8 units + 5 craft + 3 structures per UNIT_SPRITE_MAP in TerrainDataBuilder)
- **Format**: PNG, 256x256px each, flat 1-pixel white or transparent background
- **Style**: Consistent art style across all 16 (same artist/model context)
- **Output naming**: `sprite_00.png` through `sprite_15.png` for direct replacement

**Unit Mapping** (from UNIT_SPRITE_MAP constant):
- Sprites 0-7: Units
  - 0: Extractor (mining equipment)
  - 1: Habitat (dome structure)
  - 2: Fabricator (manufacturing unit)
  - 3: Computer (data center)
  - 4: Battery (energy storage)
  - 5: Propulsion (engine/thruster)
  - 6: Storage (cargo container)
  - 7: Robot (autonomous worker)
- Sprites 8-12: Craft
  - 8: Rover (wheeled vehicle)
  - 9: Harvester (industrial harvester)
  - 10: Ship (small spacecraft)
  - 11: Spaceship (large spacecraft)
  - 12: Satellite (orbital platform)
- Sprites 13-15: Structures
  - 13: PlanetaryUmbilicalHub (surface-to-orbit elevator)
  - 14: BaseUnit (generic structure fallback)
  - 15: BaseCraft (generic craft fallback)

**Implementation**:
1. Generate via ChatGPT image API or web UI with detailed prompts
2. Verify each sprite is properly framed and complete
3. Replace PNGs in `app/assets/images/unit_sprites/`
4. Commit with detailed source provenance note
5. Update status.md with completion

**Acceptance Criteria**:
- All 16 sprites present and 256x256px
- Consistent visual style across set
- Each sprite clearly readable (subject not cut off or misaligned)
- Sprites commit-tracked in `app/assets/images/unit_sprites/`
- RSpec validation still passing (no code changes needed)

### Option B: Commissioned Sprite Sheet (Fallback)
**Approach**: Commission or find pre-existing 4×4 sprite sheet with uniform grid

**Requirements**:
- 4×4 grid (1024x1024 or proportionally scaled)
- Uniform 256px cells with consistent padding
- Same 16 unit/craft/structure subjects
- Extract and verify alignment before committing

**Timeline**: Longer (requires artist coordination)

### Option C: Hybrid: Generate Individual + Compose Sheet (Alternative)
- Generate 16 sprites via ChatGPT
- Compose into single 4×4 sheet for performance optimization
- Update SurfaceView.js sprite loading to use sheet instead of 16 files

---

## Decision Point

**Recommend Option A (ChatGPT Direct Generation)** for speed:
- Viable if ChatGPT/DALL-E can produce consistent-style 16-sprite set
- Fast iteration (prompt → image → verify → commit in hours)
- No external artist coordination needed
- Risk: Consistency across 16 items depends on prompt engineering

**Fallback to Option B** if Option A produces inconsistent results.

---

## Technical Integration

### Code Changes Required
- **None** if replacing PNGs directly (Layer 5 rendering already complete)
- Optional: If moving to sprite sheet (Option C), update SurfaceView.js sprite loading
- Optional: Add PNG visual validation to RSpec (prevent future false-confidence)

### File Locations
- Source generation: (ChatGPT / artist deliverable)
- Destination: `/app/assets/images/unit_sprites/sprite_00.png` through `sprite_15.png`
- Git tracking: Yes (tracked game assets, part of deployment)

### Deployment Impact
- **Blocking**: Cannot enable Layer 5 rendering in production without this task or feature flag gate
- **No database changes**, **no service changes**, **no spec changes**
- Simple PNG replacement + commit

---

## Testing & Validation

**Pre-commit Checklist**:
- [ ] All 16 PNGs present and 256×256px
- [ ] No file corrupted (can open in image viewer)
- [ ] Visual content complete (subject not cut off)
- [ ] Consistent art style across sprites (no jarring changes)
- [ ] Sprites replace old files without git merge conflicts

**Post-commit Verification**:
- [ ] RSpec still passing (18/18 TerrainDataBuilder tests)
- [ ] SurfaceView.js sprite loading works (manual browser test)
- [ ] No console errors on surface.html.erb load

---

## Related Work

- **Task 2026-07-13-HIGH-FEATURE-UNIT-LAYER-RENDERING**: Parent task, Layer 5 rendering complete pending sprite replacement
- **NEEDS_REVIEW.md**: Escalation entry documenting sprite visual validation gap and RSpec insufficient coverage
- **Design**: Phase 2 Asset Registry planning (sprites are first asset type needing formal generation pipeline)
- **Future**: Layer 5 rendering gate behind FEATURE_FLAG_UNITS_ENABLED until task complete (recommended by review)

---

## Notes

- Placeholder sprites currently committed to git (commit `9ee37c6b`), marked non-production via `0886581c`
- This task replaces them with production-ready alternatives
- Lesson learned: Image asset validation requires visual inspection, not just RSpec structure checks
- Consider adding PNG validation to future asset generation checklist
