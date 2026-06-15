# AI Manager Training Pipeline Audit & tasks_v2 Integration
**Date:** 2026-06-12
**Priority:** MEDIUM
**Type:** RESEARCH
**Phase:** phase5+
**Estimated complexity:** Medium (audit + targeted update, not full retrain)

---

## Background & Context

The AI Manager was trained in January 2026 using the original mission-specific task files
in `data/json-data/missions/tasks/` and mission profile JSON files. That training produced
real, validated learned patterns with meaningful success rates:

| Pattern | Success Rate | Learned At |
|---------|-------------|------------|
| `lunar_precursor_lunar` | 0.92 | 2026-01-09 |
| `venus_pattern` | 0.88 | 2026-01-19 |
| `venus_atmospheric_elevators` | 0.85 | 2026-01-19 |
| `venus_forge_operations` | 0.78 | 2026-01-19 |

**These learned patterns are valuable and must be preserved.** This is not a retrain-from-
scratch task. The goal is targeted extension and gap-filling only.

Since January, two significant things happened:

1. **tasks_v2 was built** (`data/json-data/missions/tasks_v2/` — 140+ generic reusable
   tasks, parameter-driven, location-agnostic, April 2026). These tasks are composable
   via mission profiles using `task_reference` + `parameters` pattern. The AI Manager
   has never been trained on this vocabulary.

2. **The `lunar_pattern` in `mission_profile_patterns.json` is hollow** — `total_unit_count: 0`,
   `equipment_requirements: {}`, `economic_model: {}`. The Luna mission profile that
   `MissionProfileAnalyzer` read in January (`lunar-precursor-ai_profile_v1.json`) did not
   have populated manifests at training time. Meanwhile `luna_settlement_patterns.json`
   exists with real data (equipment, ISRU rates, economic model) and was never fed into
   training.

**This is the most critical gap for Luna MVP simulation testing.** The AI Manager's Luna
pattern has no substance to draw from, which explains why settlement decisions default to
`maintain/stable` with no learned behavior.

---

## Data Inventory — Read Before Starting

### Training input files (January 2026 — original)
```
data/json-data/missions/tasks/                    # original mission-specific tasks
data/json-data/missions/tasks_v2/                 # generic tasks (NOT yet in training)
data/json-data/missions/luna_base_establishment/  # Luna mission folder — check for profile
data/json-data/missions/phases/                   # phase composition files
data/json-data/missions/profiles/                 # orchestrator profiles
data/json-data/missions/tasks/lunar-precursor-ai/ # original Luna precursor files
```

### Training output files (January 2026 — preserve these)
```
data/json-data/ai-manager/learned_patterns.json         # SUCCESS RATES — DO NOT OVERWRITE
data/json-data/ai-manager/mission_profile_patterns.json # pattern shells — lunar_pattern is hollow
data/json-data/ai-manager/training_results.json         # last run summary
data/json-data/ai-manager/enhanced_training_report.json # last run report
```

### Key reference files
```
data/json-data/missions/luna_settlement_patterns.json   # real Luna data — use to populate lunar_pattern
data/json-data/ai-manager/resource_acquisition_logic_v1.json # decision tree — needs review
data/json-data/ai-manager/performance/                  # ~1,309 settlement decision files
```

### Training script
```
# Locate via:
find ~/Documents/git/galaxyGame -name "*mission_profile_analyzer*" -o -name "*training*" \
  | grep -v deprecated | grep -v ".json"
# Also check lib/tasks/ for rake tasks
```

---

## Audit Steps

### Step 1 — Locate and read MissionProfileAnalyzer

Find `AIManager::MissionProfileAnalyzer` service file. Read it fully. Document:
- What file paths does it read from? (`tasks/`, `tasks_v2/`, both, neither?)
- Does it understand the `task_reference` + `parameters` pattern from tasks_v2 README?
- Does it know about `luna_settlement_patterns.json`?
- What triggers pattern population vs leaving `equipment_requirements: {}` empty?

Do NOT modify yet — read and document only.

### Step 2 — Audit the hollow lunar_pattern

Compare:
- `mission_profile_patterns.json` → `lunar_pattern` (hollow — what January produced)
- `luna_settlement_patterns.json` (real data — equipment, ISRU rates, economic model)
- `luna_base_establishment/` mission folder contents — does it have a profile? manifests?

Document: what data exists that could populate the hollow pattern, and what path the
analyzer would need to follow to find it.

### Step 3 — Review resource_acquisition_logic_v1.json

Read `data/json-data/ai-manager/resource_acquisition_logic_v1.json`. Flag these specific
issues for correction:

**Issue 1 — Dead steps during pre-player arc (Phase 1-8):**
- `check_player_market` (step 2) always fails — no players exist yet
- `check_wormhole_network` (step 4) always fails — wormhole tech is Phase 9+
- Both steps waste decision cycles. Document whether these should be skipped via
  phase-awareness or left as graceful-fail passthrough.

**Issue 2 — Player market = NPC market:**
- Steps 2 and 3 are logically the same market. One unified market exists.
- Document whether steps should be collapsed or step 2 reframed as
  "check unified market (GCC + Virtual Ledger)"

**Issue 3 — Intra-solar logistics routing:**
- Step 4 label `check_wormhole_network` is misleading for Luna MVP era
- The correct concept is: check if any station, cycler, or logistics provider
  in the intra-solar network has surplus and can route it
- This step is architecturally correct at position 4, just mislabeled and
  under-specified. Document recommended rename/reframe.

**Issue 4 — Unvalidated tuning parameters:**
- `import_dependency_threshold: 0.3` — untested assumption
- `target_self_sufficiency: 0.8` — Luna ISRU currently at 0.7 capability
  (observed in performance/7414 file) meaning Luna is perpetually under target
- `wormhole_priority_boost: 1.2` — meaningless until intra-solar routing exists
- Document all as unvalidated, flag for calibration during simulation testing

Do NOT modify the file yet — document findings only.

### Step 4 — Assess tasks_v2 integration path

Read `data/json-data/missions/tasks_v2/README.md`. Then check 3-5 tasks_v2 files
to understand the parameter schema. Document:
- Is MissionProfileAnalyzer capable of resolving `task_reference` paths?
- What would be required to make it tasks_v2-aware?
- Which tasks_v2 files are most relevant to Luna MVP operations?
  (Focus on: `task_deploy_solar_rig.json`, `task_deploy_pve_unit.json`,
  `task_begin_regolith_harvest_for_solar_array.json`,
  `regolith_processing_and_infrastructure_construction.json`,
  `task_deploy_volatiles_storage.json`)

### Step 5 — Sample performance corpus

Read 10 performance JSON files from `data/json-data/ai-manager/performance/` spread
across the date range. For each note:
- `outcome` null or populated?
- `success_score` null or populated?
- `lessons_learned` empty or populated?
- `pattern_performance` empty or populated?

Document: is outcome recording broken everywhere, or are there any files with
non-null outcomes that survived from January testing?

### Step 6 — Write findings document

Create `research/AI-MANAGER-TRAINING-PIPELINE-FINDINGS.md` in agent-tasks repo:

```markdown
# AI Manager Training Pipeline Findings
Date: YYYY-MM-DD

## MissionProfileAnalyzer — Current Behavior
[what it reads, what it skips, why lunar_pattern is hollow]

## lunar_pattern Gap Analysis
[what data exists to fix it, what path is needed]

## resource_acquisition_logic_v1.json Issues
[four issues documented above with recommended fixes]

## tasks_v2 Integration Assessment
[what's needed for MissionProfileAnalyzer to understand tasks_v2]

## Performance Corpus Sample
[10 file sample — outcome recording status]

## Recommended Fix Sequence
1. [ordered list of changes, smallest first]

## Estimated Work
[rough estimate per fix]

## Risk: Do Not Break January Learned Patterns
[confirm learned_patterns.json will be preserved through any retraining]
```

Commit findings document to agent-tasks research/ folder.

---

## What This Task Does NOT Do

- Does NOT run the training script
- Does NOT modify any training output files
- Does NOT modify `learned_patterns.json` — preserve all success rates
- Does NOT modify `mission_profile_patterns.json`
- Does NOT modify `resource_acquisition_logic_v1.json`
- Does NOT deprecate original `tasks/` mission files — they remain the fallback
- Does NOT implement tasks_v2 integration — documents what's needed only
- Does NOT touch GeoTIFF or map generation data

**All changes are deferred to the implementation task that follows this audit.**

---

## Success Criteria

- [ ] `MissionProfileAnalyzer` located and its read paths documented
- [ ] Root cause of hollow `lunar_pattern` identified
- [ ] `luna_settlement_patterns.json` confirmed as correct data source for fix
- [ ] All four `resource_acquisition_logic_v1.json` issues documented
- [ ] tasks_v2 integration path assessed
- [ ] Performance corpus sampled — outcome recording status confirmed
- [ ] `research/AI-MANAGER-TRAINING-PIPELINE-FINDINGS.md` committed
- [ ] Recommended fix sequence written with effort estimates

---

## Constraints

- Read-only audit — no code or data file modifications
- Original tasks/ files are not deprecated — additive only
- tasks_v2 integration must not break existing pattern success rates
- Luna MVP simulation testing is blocked on fixing the hollow `lunar_pattern`
  specifically — prioritize that finding above all others

---

## Dependencies

- Phase 4 complete ✅ (01919af8)
- Luna MVP design complete ✅ (`LUNA-MVP-SIMULATION-DESIGN.md`)

## Blocks

- AI Manager training pipeline implementation task (follows this audit)
- Luna simulation calibration testing (Phase 5)
- Behavioral correctness validation against Phase 1-4 service architecture
