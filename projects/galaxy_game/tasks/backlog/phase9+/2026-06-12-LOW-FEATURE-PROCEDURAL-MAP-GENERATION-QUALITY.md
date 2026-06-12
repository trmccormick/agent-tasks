# Procedural Map Generation Quality Improvement
**Date:** 2026-06-12
**Priority:** LOW
**Type:** FEATURE
**Phase:** phase9+
**Estimated complexity:** High

---

## Background

In January/February 2026 a pipeline was built to extract terrain patterns from real NASA
GeoTIFF data and use them to train procedural world map generation. The extraction step
produced good pattern JSON files for Luna, Mars, Venus, Mercury, and Earth. The downstream
generation step produced pixelated, low-quality output.

The data foundation is solid. The generation quality is not. This is future tuning work —
do not prioritize before Luna MVP simulation loop is stable.

---

## What Exists (Do Not Modify)

Pattern files extracted from NASA source data, located in `json-data/ai-manager/`:

| Body | Body Type | Key Characteristics |
|------|-----------|-------------------|
| Luna | `airless_cratered` | 5,898 craters detected, elevation histogram 20-bin empirical |
| Mars | TBD | Review existing pattern file |
| Venus | TBD | Review existing pattern file |
| Mercury | TBD | Review existing pattern file |
| Earth | TBD | Review existing pattern file |

Named strategic feature data scraped from Wikipedia with real coordinates and dimensions:
- `json-data/ai-manager/craters.json` — Luna strategic craters (tier: strategic)
- Other feature files TBD

**Data provenance note:** Crater depths are mixed provenance:
- Smaller simple craters: depth estimated from diameter using empirical 1:5 ratio
- Larger complex craters: ratio breaks down, depths less reliable
- Future schema addition: `depth_source: "measured" | "estimated"` per crater record

---

## Known Problem

Generated terrain heightmaps are pixelated. Root cause not yet diagnosed but likely one
or more of:

1. **Insufficient output resolution** — generator sampling histogram at too-low resolution
2. **Nearest-neighbor interpolation** — no smoothing between sampled elevation values
3. **Noise function mismatch** — Perlin/simplex noise parameters not calibrated to
   GeoTIFF-derived statistics (std_dev, roughness mean/max)
4. **Named feature anchoring missing** — strategic craters (Shackleton etc.) not placed
   as fixed anchors before procedural fill, causing conflicts

---

## Design Constraints

- Named strategic craters must be **fixed anchors**, not procedurally generated
  - Shackleton: -89.9°, 21km diameter, 4.2km depth — must always appear correctly
  - Procedural generation fills around named features, not over them
- `smooth_fraction: 0.98` for Luna maria should produce visibly smooth lowland plains
- `rough_fraction` for highlands currently 0.0 — likely a processing artifact, needs
  investigation before using as generation input
- Elevation distribution should match empirical histogram from pattern JSON, not a
  synthetic approximation
- GeoTIFF source data: `app/data/geotiff/temp/luna_900x450.asc.gz` — 900x450 grid,
  sufficient resolution for pattern extraction

---

## Recommended Investigation Sequence

1. Identify the map generation class/service and rendering pipeline
2. Reproduce pixelation with a test render — capture exact output
3. Check output resolution vs source grid resolution (900x450 source — is output lower?)
4. Check interpolation method — replace nearest-neighbor with bilinear or bicubic
5. Verify noise calibration against GeoTIFF statistics
6. Implement named feature anchor placement before procedural fill
7. Add `depth_source` field to crater schema

---

## Success Criteria

- [ ] Luna test render produces smooth maria and credible highland/crater texture
- [ ] Shackleton appears at correct coordinates with correct proportions
- [ ] Output resolution matches or exceeds 900x450 source grid
- [ ] `depth_source` field added to crater records
- [ ] `rough_fraction: 0.0` highlands artifact investigated and resolved

---

## Notes

- This task has no Luna MVP dependency — do not pull forward
- Map generation is needed for player-facing world exploration (Phase 9+ portal tech era)
- Pattern extraction pipeline was successful — do not re-extract unless source data changes
- If NASA source data has been updated since February 2026, note it but do not re-extract
  as part of this task

---

## Dependencies

- Pattern extraction complete ✅ (February 2026)
- Named crater data complete ✅ (`craters.json` last updated 2025-12-17)
- Portal tech / player entry arc (Phase 9+)
