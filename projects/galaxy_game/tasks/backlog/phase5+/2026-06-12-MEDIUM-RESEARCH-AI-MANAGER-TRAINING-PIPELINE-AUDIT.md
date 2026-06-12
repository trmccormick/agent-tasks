# AI Manager Behavioral Training Pipeline Audit
**Date:** 2026-06-12
**Priority:** MEDIUM
**Type:** RESEARCH
**Phase:** phase5+
**Estimated complexity:** Medium (audit first, implementation scope TBD)

---

## Background

The AI Manager has been running continuously and generating performance data since at least
February 2026. As of June 2026:

- ~1,309 performance JSON files exist in `ai_manager/performance/` (~5.4MB)
- All inspected files show `outcome: null`, `success_score: null`, `lessons_learned: []`
- `pattern_performance: {}` and `adaptation_rules: {}` are empty in all files
- Last known training run: January 2026 — result was "5 failed patterns"
- Architecture has changed significantly since February (Phase 1–4 Luna MVP work)

The behavioral data corpus exists and is rich. The problem is the feedback loop that reads
performance data back into training patterns is either disconnected or was never fully wired
post-February.

**This task is a prerequisite for Luna simulation calibration testing.**
Per `LUNA-MVP-SIMULATION-DESIGN.md` line 735: "AI Manager running known mission profiles."

---

## Scope

**In scope:**
- AI Manager decision pattern training pipeline
- Outcome scoring against historical settlement data
- `pattern_performance` and `adaptation_rules` population
- Verification that `CostAnalyzer` and site selection logic references strategic crater data
  (`json-data/ai-manager/craters.json`) not just abstract resource levels

**Out of scope:**
- GeoTIFF map generation and procedural terrain quality (separate task)
- Map training data in `json-data/ai-manager/` (Jan/Feb vintage — do not touch)

---

## Audit Steps

### 1. Locate and inventory training scripts
- Find training/retraining script (likely `lib/`, `lib/tasks/`, or a rake task)
- Verify it existed and ran in January 2026 (produced the "5 failed patterns" result)
- Document what the script does: input format, output format, pattern schema

### 2. Verify script compatibility with current architecture
- Architecture has changed substantially since February (unified Job model, Phase 1–4
  service implementations, CostAnalyzer, ManifestGenerator, ShortageDetector)
- Check that training script references still resolve — classes, service names, data paths
- Flag any broken references without fixing them yet

### 3. Audit outcome scoring
- Inspect `ai_manager/performance/` sample (10–20 files across date range)
- Determine why `outcome` is always null — is outcome recording never triggered,
  or is the scoring step missing entirely?
- Identify where outcome scoring should hook in (likely post-tick in simulation loop)

### 4. Assess corpus quality
- Are the ~1,309 files representative of diverse settlement states?
- Check date distribution — are most from a narrow window or spread across months?
- Identify if any files have non-null outcomes (may exist from January test run)

### 5. Document findings
- Write `research/AI-MANAGER-TRAINING-PIPELINE-FINDINGS.md` with:
  - Script location and current status
  - Broken references list
  - Outcome scoring gap analysis
  - Recommended fix sequence
  - Estimate of work to get pipeline running against existing corpus

---

## Success Criteria

- [ ] Training script located and inventoried
- [ ] Compatibility gaps with current architecture documented
- [ ] Outcome scoring gap root-caused
- [ ] `research/AI-MANAGER-TRAINING-PIPELINE-FINDINGS.md` committed
- [ ] Recommendation made: can existing corpus be used as-is, or does outcome scoring
      need to run first?

---

## Notes

- Do NOT run the training script during this audit — read only
- Do NOT modify any performance JSON files
- Do NOT touch GeoTIFF or map training data
- Commit findings doc to agent-tasks research/ when complete
- This audit gates the implementation task — scope of fixes determined by findings

---

## Dependencies

- Phase 4 complete ✅ (01919af8)
- Luna simulation loop design complete ✅ (`LUNA-MVP-SIMULATION-DESIGN.md`)

## Blocks

- Luna simulation calibration testing (Phase 5)
- AI Manager behavioral correctness validation
