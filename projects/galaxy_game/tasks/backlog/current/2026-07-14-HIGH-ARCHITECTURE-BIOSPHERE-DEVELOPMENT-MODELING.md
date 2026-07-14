# Task File: Biosphere Development Modeling (Planetary Age & History)
# Copy this file, rename it to describe the task, fill in all sections.
# Delete these instruction comments before saving.

---
status: backlog
priority: HIGH
type: architecture
system_domain: TERRA_SIM
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-14-HIGH-ARCHITECTURE-BIOSPHERE-DEVELOPMENT-MODELING.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-14-HIGH-ARCHITECTURE-BIOSPHERE-DEVELOPMENT-MODELING.md \
         projects/galaxy_game/tasks/active/2026-07-14-HIGH-ARCHITECTURE-BIOSPHERE-DEVELOPMENT-MODELING.md
  Then open the moved file and change: status: backlog → status: active
  
VERIFY:
  find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
       -name "2026-07-14-HIGH-ARCHITECTURE-BIOSPHERE-DEVELOPMENT-MODELING.md"
  Paste find output in chat (expect one result at active/).

READ FIRST (after Step 0): Task file contains all prerequisites and research requirements.

CRITICAL: This is a DESIGN/RESEARCH task, not implementation. Do NOT modify seed data.
  Deliverable: Architecture design doc + research summary (save to summaries folder)
  This informs a future implementation task.
```

---

# TASK: Biosphere Development Modeling — Planetary Age & History Attributes

**Status**: BACKLOG
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-07-14
**Last Updated**: 2026-07-14

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: PASS
- **MVP Alignment**: VALID — affects biosphere spec health and scientific grounding
- **MVP Impact Note**: Enables accurate modeling of ancient habitability vs. current habitability — critical for terraforming, settlement planning, and gameplay scenarios
- **Action Line**: READY FOR RESEARCH DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary, for research phase)
**Why This Agent**: Local model with semantic search access can research existing codebase, identify data structures, and synthesize architecture without external network calls
**Local attempts before cloud**: N/A — research phase, no implementation
**Supervision Level**: Standard

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **Biosphere Architecture**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/README.md` (biosphere section)
4. **Recent Biosphere Commits**: Review commit 49a6894e (data-driven biosphere creation) and e4340383 (configurable biosphere generation)
5. **Earth & Mars References**:
   - Earth timeline: 3.8 Ga (first life) → 2.4 Ga (GOE) → 540 Ma (Cambrian) → present
   - Mars: 4.1 Ga (old), had water 3.8-2.2 Ga, lost magnetic field ~4.0 Ga, atmosphere stripped by solar wind

---

## Context

The game's biosphere system is currently **data-driven but incomplete**: seed JSON files specify `biosphere_attributes`, but planets lack the **historical and physical context** needed to justify why a biosphere exists (or doesn't).

**Current gap**: A world can have `biosphere_attributes: {}` (empty), but we don't know:
- Was this planet ever habitable? (Mars: yes, ancient. Venus: never.)
- Could it have supported microbial life in the past? (Mars: probably. Moon: no.)
- Why did life disappear or never emerge? (Mars: lost magnetic field → lost atmosphere.)
- What stage of development is *current* biosphere? (Ancient microbes vs. complex ecosystems vs. extinct.)

**Scientific foundation**: Earth's biosphere took 3.8 billion years to evolve from prokaryotes → eukaryotes → multicellular life. Mars had only 1-2 billion years of suitable conditions. Magnetic field loss = atmospheric loss = habitability loss.

**Game impact**: Enables scenarios like:
- Ancient Mars with early microbes (biosphere_stage_historical: microbial_anaerobic)
- Young hot planet with no life yet (age: 0.5 Ga, water present, but sterile)
- Terraformed world inheriting ancient dead biosphere (biosphere_stage_historical: extinct)
- Super-Earth with complex life (age: 2.5 Ga, strong field, stable water)

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE design)

⚠️ **GOTCHA 1**: Do NOT add arbitrary attributes to seed data without research
- ❌ Wrong: Add `planetary_age_ga` to aol-732356.json immediately
- ✅ Right: Design the model first, get approval, THEN add to seed data in a separate task
- Why: Seed data is ground truth. If we get planetary age wrong, it propagates through all gameplay logic.

⚠️ **GOTCHA 2**: Magnetic field strength ≠ habitability directly
- ❌ Wrong: "Strong field = life, weak field = no life"
- ✅ Right: "Strong field protects atmosphere from solar wind, which matters most for thin atmospheres on old planets"
- Why: A young planet with strong field and water *now* could still be sterile if it never had enough time for life to emerge.

⚠️ **GOTCHA 3**: Historical water presence != current water presence
- ❌ Wrong: Assume ancient water history = current water
- ✅ Right: Model three states: "never had water" / "had it in past, lost it" / "has it now"
- Why: Mars is the classic case — had water 3.8-2.2 Ga, none now. This is crucial for gameplay.

⚠️ **GOTCHA 4**: Biosphere development stages are NOT tied to single attributes
- ❌ Wrong: "if age > 1.5 Ga then eukaryotic"
- ✅ Right: Score multiple factors (age + water stability + current conditions + magnetic field), then assign stage
- Why: A 2.0 Ga planet with no water can't evolve eukaryotes. Age alone isn't enough.

### Why This Matters for Gameplay

| Scenario | Current System | With This Design | Gameplay Impact |
|----------|---|---|---|
| Ancient Mars | `"biosphere": {}` (empty, why?) | `age_ga: 4.1, had_magnetic_field: true, historical_water: [3.8, 2.2], biosphere_stage_historical: microbial_anaerobic, biosphere_stage_current: sterile` | Player knows Mars *could have had* life, why it's dead now, and whether terraforming could restore conditions |
| Young Venus | `"biosphere": {}` (empty, why?) | `age_ga: 4.5, magnetic_field_strength: 0.1, current_habitable: false, biosphere_stage_historical: sterile, biosphere_stage_current: sterile` | Player understands Venus was never habitable and why |
| Future-Terraformed World | `"biosphere": {}` or complex data? | `age_ga: 3.2, had_magnetic_field: true, historical_water: past, terraformation_index: 0.6, biosphere_stage_historical: extinct, biosphere_stage_current: establishing` | Player can track terraforming progress |

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

This is a **research and design task**, not implementation. Your synthesis report should outline:
1. What attributes are missing from the current model
2. What research is needed to define biosphere development stages
3. How to score multiple factors into a single stage recommendation
4. What questions need answers before implementation

**Synthesis Report Template** (save as MD file in `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/`):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Biosphere Development Modeling
**Status**: backlog → active (research phase)
**Date**: 2026-07-14

### What I'm About to Research
[Describe the design goal: model planetary age, water history, magnetic field, and atmospheric retention to determine biosphere development stage]

### Research Areas
| Area | What I Need to Understand | Source |
|---|---|---|
| Earth timeline | 3.8 Ga first life → 2.4 Ga GOE → 540 Ma Cambrian | Scientific literature, existing code comments |
| Mars history | 4.1 Ga age, 3.8-2.2 Ga water, ~4.0 Ga field loss | Scientific literature |
| Current model | Where planetary age is stored (if at all) | Grep `planetary_age`, `age_ga`, date fields in CelestialBody |
| Biosphere stage | How to calculate from multiple factors | BiosphereGeneratorService, CelestialBody spec |

### Key Design Questions to Answer
- Q1: Should `planetary_age_ga` be stored in CelestialBody model or only in JSON seed data?
- Q2: How do we weight the factors? (age: 40%, water: 30%, field: 20%, pressure: 10%?)
- Q3: Should biosphere development stage be calculated or stored? (calculated = dynamic; stored = historical record)
- Q4: What's the minimum age for each stage? (eukaryotes need 1.5+ Ga?)

### Expected Outcomes
1. Document listing 5-7 missing attributes (with rationale)
2. 8 biosphere development stages defined (sterile → complex_ecosystems)
3. Scoring algorithm outline (pseudocode)
4. Mars & Venus & Earth examples worked through
5. Open questions for product team
6. Recommendation: which attributes go in seed data, which are calculated

### Critical Gotchas I Will Avoid
- ❌ Do NOT modify seed data during research — design first
- ❌ Do NOT assume age_ga = biosphere stage — need to score multiple factors
- ❌ Do NOT conflate "had water" with "has water" — Mars shows why
- ❌ Do NOT create new code during research — summarize design only

---

**SYNTHESIS COMPLETE.** Ready to research and design (no implementation).
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement

**Current system status**: Biosphere seeding is data-driven (commit 49a6894e removed hard-coded "Earth" check), and procedural generation supports complexity levels (commit e4340383). However, both assume planets are **contemporary snapshots** with no historical context.

**What's missing**:
1. Planetary age (in gigayears) — critical for understanding biosphere development timeline
2. Magnetic field information — affects atmosphere retention and habitability windows
3. Water presence history (had/has/never) — differentiates Mars (had water, lost it) from Venus (never had it)
4. Habitability window — when could this planet have supported surface life?
5. Biosphere development stage (historical and current) — "sterile" vs. "microbial" vs. "complex"

**Why it matters**:
- **Mars scenario**: 4.1 Ga old, had water 3.8-2.2 Ga, lost magnetic field → player can understand why it's dead
- **Venus scenario**: Never had conditions for life → player knows terraforming to habitability is much harder
- **Young hot planet**: 0.5 Ga old, has water, strong field → currently sterile but *could* evolve life if left alone for 2 billion years
- **Terraformed world**: Historical `biosphere_stage: extinct` → current `biosphere_stage: establishing` tracks progress

---

## Files Involved

### Reference Files — read to understand current model
| File | Purpose | Key Section |
|---|---|---|
| `app/models/celestial_bodies/celestial_body.rb` | Planet model | `has_one :biosphere`, `can_support_surface_life?` |
| `app/services/star_sim/biosphere_generator_service.rb` | Biosphere generation | `#detect_complexity`, `#generate` |
| `app/services/star_sim/procedural_generator.rb` | Procedural system | `#generate_biosphere_data` |
| `data/json-data/generated_star_systems/aol-732356.json` | Example seed data | planets with `biosphere: {}` |
| `data/json-data/sol-complete.json` | Sol system seed | planets with `biosphere_attributes` |
| `spec/services/star_sim/biosphere_generator_service_spec.rb` | Biosphere tests | complexity levels, era adjustments |

### Research Sources (outside codebase)
- Scientific literature on Earth's biosphere development (3.8 Ga → present)
- Mars climate history and magnetic field loss (~4.0 Ga)
- Venus atmospheric evolution and lack of magnetism
- Habitable zone calculations and solar evolution

---

## Design Tasks (Deliverables)

### Task 1: Define Missing Planetary Attributes

Research and document 5-7 missing attributes needed for biosphere modeling:

1. **`planetary_age_ga`** — Age in gigayears
   - Range: 0.0 (newly formed) to 13.8 (Big Bang is reference, oldest stars ~13.2)
   - Earth: 4.54 Ga
   - Mars: 4.1 Ga
   - Venus: 4.5 Ga
   - Source: Solar system formation model

2. **`magnetic_field_strength`** — Current field strength (normalized to Earth = 1.0)
   - Range: 0.0 (no field) to 2.0+ (strong like magnetars, rare)
   - Earth: 1.0
   - Mars: ~0.0 (lost it 4.0 Ga)
   - Jupiter: ~4.2 (very strong)
   - Source: Planetary physics

3. **`had_magnetic_field`** — Boolean, did it have one historically?
   - True/False
   - Mars: true (had it, lost it)
   - Venus: false (never had strong field)
   - Source: Geological evidence

4. **`historical_water_presence`** — Enum or date range
   - Options: "never" / "past" / "present" / "present_future"
   - Mars: "past" (3.8-2.2 Ga)
   - Venus: "never"
   - Earth: "present" (3.8 Ga onward)
   - Format: `water_history: { had_early_water: true/false, period_ga: [start, end] }`
   - Source: Geological/atmospheric modeling

5. **`current_habitable`** — Boolean, can surface life exist NOW?
   - Calculated from: temp, pressure, radiation, water, atmosphere
   - Mars: false (no atmosphere, no water)
   - Earth: true
   - Venus: false (too hot, wrong atmosphere)

6. **`habitability_window_ga`** — When was/is the planet habitable?
   - Format: `[start_ga, end_ga]` or open range like `[3.8, present]`
   - Mars: `[3.8, 2.2]` (had water, then lost it)
   - Earth: `[3.8, present]` (still habitable)
   - Venus: `[null, null]` (never habitable)

7. **`stellar_evolution_factor`** — Does the star's age/luminosity affect habitability?
   - Range: 0.0 to 1.0+ (depends on star type and age)
   - Young star: higher luminosity → pushes habitable zone outward
   - Old star: lower luminosity → habitable zone moves inward
   - Source: Hertzsprung-Russell diagram

**Deliverable**: Attribute specification document with ranges, Earth/Mars/Venus examples, and JSON schema.

---

### Task 2: Define Biosphere Development Stages

Research Earth's actual biosphere timeline and define 7-8 development stages:

| Stage | Definition | Age Range | Example | Characteristics |
|---|---|---|---|---|
| `:sterile` | Never had conditions for surface life | N/A | Venus, modern Mars | No atmosphere or wrong composition, too hot/cold |
| `:abiotic_young` | Has water now, young planet, sterile | < 0.5 Ga | Young hot planet | Too young for life to have evolved yet |
| `:microbial_anaerobic` | Early prokaryotes, anaerobic | 3.8-2.4 Ga | Ancient Earth, ancient Mars | Simple bacteria, no oxygen production |
| `:microbial_oxygenic` | Oxygen-producing microbes, GOE atmosphere | 2.4-1.5 Ga | Earth ~2.4 Ga | Cyanobacteria, ozone layer forming |
| `:eukaryotic` | Complex single-celled life, early eukaryotes | 1.5-0.6 Ga | Earth ~1.5 Ga | Eukaryotic cells, sexual reproduction, some multicellular algae |
| `:multicellular_aquatic` | Ocean ecosystems, early animals | 0.6-0.47 Ga | Earth ~600 Ma | Sponges, cnidarians, early chordates |
| `:terrestrial_colonized` | Plants and animals on land | 0.47-0 Ga | Earth ~470 Ma onward | Diverse terrestrial ecosystems, forests, megafauna |
| `:complex_ecosystems` | Modern Earth-like biodiversity | 0-present | Modern Earth | Humans, microbes, megafauna, global food webs |

**Scoring algorithm outline** (pseudocode):
```
def calculate_biosphere_stage(planet)
  # Score 4 factors (0-1 each)
  age_score = score_age(planet.planetary_age_ga)
  water_score = score_water(planet.historical_water_presence, planet.current_habitable)
  field_score = score_magnetic_field(planet.had_magnetic_field, planet.magnetic_field_strength)
  stability_score = score_atmospheric_stability(planet)
  
  # Weight and combine
  total_score = (age: 0.4, water: 0.3, field: 0.15, stability: 0.15)
  combined = age_score * 0.4 + water_score * 0.3 + field_score * 0.15 + stability_score * 0.15
  
  # Map to stage
  case combined
  when 0.0..0.15 then :sterile
  when 0.15..0.3 then :abiotic_young
  when 0.3..0.45 then :microbial_anaerobic
  when 0.45..0.6 then :microbial_oxygenic
  when 0.6..0.7 then :eukaryotic
  when 0.7..0.85 then :multicellular_aquatic
  when 0.85..0.95 then :terrestrial_colonized
  else :complex_ecosystems
  end
end
```

**Deliverable**: Stage definitions with Earth timeline mapping, scoring logic outline, and Mars/Venus worked examples.

---

### Task 3: Apply to Existing Systems

Work through real examples from the codebase:

**Example 1: Sol — Earth**
```json
{
  "name": "Earth",
  "planetary_age_ga": 4.54,
  "magnetic_field_strength": 1.0,
  "had_magnetic_field": true,
  "historical_water_presence": "present",
  "habitability_window_ga": [3.8, "present"],
  "current_habitable": true,
  "biosphere_stage_historical": "complex_ecosystems",
  "biosphere_stage_current": "complex_ecosystems"
}
```

**Example 2: Sol — Mars**
```json
{
  "name": "Mars",
  "planetary_age_ga": 4.1,
  "magnetic_field_strength": 0.0,
  "had_magnetic_field": true,
  "historical_water_presence": "past",
  "habitability_window_ga": [3.8, 2.2],
  "current_habitable": false,
  "biosphere_stage_historical": "microbial_anaerobic",
  "biosphere_stage_current": "sterile"
}
```

**Example 3: Sol — Venus**
```json
{
  "name": "Venus",
  "planetary_age_ga": 4.5,
  "magnetic_field_strength": 0.0,
  "had_magnetic_field": false,
  "historical_water_presence": "never",
  "habitability_window_ga": [null, null],
  "current_habitable": false,
  "biosphere_stage_historical": "sterile",
  "biosphere_stage_current": "sterile"
}
```

**Example 4: AOL-732356 — Eden Prime (speculative, young Earth-like)**
```json
{
  "name": "Eden Prime",
  "planetary_age_ga": 2.1,
  "magnetic_field_strength": 0.8,
  "had_magnetic_field": true,
  "historical_water_presence": "present",
  "habitability_window_ga": [2.1, "present"],
  "current_habitable": true,
  "biosphere_stage_historical": "eukaryotic",
  "biosphere_stage_current": "multicellular_aquatic"
}
```

**Deliverable**: Worked examples showing how to classify real planets using the new model.

---

### Task 4: Design Questions for Product/Design Team

Document open questions that need decisions before implementation:

1. **Model vs. Storage**: Should biosphere_stage be calculated on-the-fly from attributes, or stored as a cached value?
   - Pro (calculated): Always reflects current state, no sync issues
   - Pro (stored): Faster lookups, can track historical changes separately

2. **Seed Data Source**: Where do we get planetary ages?
   - Option A: Hardcode based on real solar system (Sol) or research + game balance (procedural)
   - Option B: Generate randomly with age distribution that matches exoplanet surveys
   - Option C: Hybrid — seed data provides ages where known, procedural fills in unknowns

3. **Terraformed Worlds**: How do we model biosphere recovery after terraformation?
   - Does `biosphere_stage_historical` stay frozen as "extinct"?
   - Does `biosphere_stage_current` change to "establishing" and evolve over time?
   - Do we need a `biosphere_recovery_timeline_ga` field?

4. **Early Game Design**: Does the player care about ancient habitability in the first mission?
   - If not, we can defer full modeling until gameplay requires it
   - If yes, we need historical stages fully working day 1

5. **Magnetic Field Threshold**: At what field strength does atmospheric retention become a problem?
   - Mars: 0.0 (lost field, lost atmosphere)
   - Earth: 1.0 (strong field, keeps atmosphere)
   - Cutoff for habitability: ~0.3 or 0.5?

---

## Acceptance Criteria

- [ ] Attribute specification document completed (5-7 attributes with ranges, examples)
- [ ] Biosphere development stages defined (7-8 stages with Earth timeline mapping)
- [ ] Scoring algorithm outlined in pseudocode (not yet implemented)
- [ ] Sol system (Earth, Mars, Venus) worked through with new model
- [ ] AOL-732356 system speculated (what stages could these planets have?)
- [ ] Open design questions documented (4-5 questions for product team)
- [ ] No code changes to seed data or models (design phase only)
- [ ] Synthesis report saved to summaries folder and linked in completion report

---

## Stop Conditions — escalate to user immediately if:

- Research discovers that magnetic field impact is more/less important than anticipated
- Need for additional attributes beyond 5-7 becomes clear
- Biosphere development timeline doesn't map cleanly to Earth's actual history
- Product team needs to weigh in on design questions before proceeding

---

## Documentation

- [ ] Architecture design doc created (summary format, no full implementation)
- [ ] Saved to: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-07-14-ARCHITECTURE-BIOSPHERE-DEVELOPMENT-MODELING.md`
- [ ] No modifications to codebase — this is pure research/design

---

## Dependencies

**Blocked by**: None
**Blocks**: Future implementation task (2026-07-XX-HIGH-IMPLEMENTATION-BIOSPHERE-DEVELOPMENT-STAGES)
**Related tasks**: 
- 2026-07-12-HIGH-ARCHITECTURE-BIOSPHERE-SEEDING-AND-INTEGRITY (completed) — established data-driven biosphere creation
- 2026-07-14-HIGH-FEATURE-CONFIGURABLE-BIOSPHERE-GENERATION (completed) — added complexity levels and era support

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was designed

- Attribute specification: [summary]
- Development stages: [7-8 stages defined]
- Scoring algorithm: [outline/pseudocode included]
- Worked examples: [Sol and AOL-732356 systems]
- Open questions: [4-5 for product team]

### Key insights discovered

[Any surprising findings from research — e.g., "Mars habitability window is shorter than thought"]

### Recommendations for next task

[Should we implement immediately? Should we get product feedback first? Any risks?]

### Follow-up tasks needed

- [ ] Product review of design questions (separate task)
- [ ] Implementation task (seed data schema + model updates)
- [ ] RSpec tests for biosphere stage calculation
- [ ] Update existing seed data (Sol, AOL-732356) with new attributes

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: Design complete (7 attributes, 8 stages, scoring outline) | Open questions documented | Ready for product review before implementation | Next: separate task for implementation
