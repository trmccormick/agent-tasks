---
title: "Feature — Craft exhaust → atmosphere feedback"
priority: MEDIUM
status: backlog
phase: phase5+
owner: Implementation Agent (Qwen)
type: feature
system_domain: TERRA_SIM | AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
created: 2026-07-17
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/drafts/2026-07-17/2026-07-17-MEDIUM-FEATURE-CRAFT-EXHAUST-ATMOSPHERE-FEEDBACK.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/drafts/2026-07-17/2026-07-17-MEDIUM-FEATURE-CRAFT-EXHAUST-ATMOSPHERE-FEEDBACK.md \
         projects/galaxy_game/tasks/active/2026-07-17-MEDIUM-FEATURE-CRAFT-EXHAUST-ATMOSPHERE-FEEDBACK.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-17-MEDIUM-FEATURE-CRAFT-EXHAUST-ATMOSPHERE-FEEDBACK.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-FEATURE-CRAFT-EXHAUST-ATMOSPHERE-FEEDBACK.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# FEATURE TASK: Craft exhaust → atmosphere feedback

**Status**: DRAFT (not active — awaiting human review)  
**Priority**: MEDIUM  
**Type**: feature  
**Created**: 2026-07-17  

---

## Objective

Wire harvester/craft propellant consumption back into the planet's atmosphere. When a craft operates near or on a world, its exhaust emissions should be added to that world's atmospheric composition — following the same pattern volcanic emissions already use.

**Confirmed gap (verified 2026-07-17)**: No code currently ties harvester/craft propellant consumption back into a planet's atmosphere. This is NOT speculative — it was planned conceptually but never implemented in code.

---

## Gap Verification (2026-07-17)

### What EXISTS (infrastructure confirmed):

| Component | Status | Used For |
|-----------|--------|----------|
| `CelestialBodies::Spheres::Atmosphere#add_gas` | ✅ Exists | Volcanic emissions, biosphere production, hydrosphere evaporation |
| `TerraSim::AtmosphericTransferService` | ✅ Exists | Cargo delivery between bodies (source → target) |
| `AIManager::AtmosphericExtractionService` | ✅ Exists | Skimmers extract raw gases from source body |
| Volcanic emission tracking | ✅ Implemented | `full_run_order.txt` shows: "Adding 347.0 kg of Carbon Dioxide to atmosphere [Emission: CO2_c158]" |

### What's MISSING (confirmed gap):

- **No exhaust/emission/pollutant tracking** on any craft model
- **No code that adds gases to an atmosphere based on nearby harvester operation**
- `Craft::Harvester#extract_resources` only handles resource extraction, not atmospheric impact
- HLT harvesters have their own `atmosphere` (craft life support), but nothing ties their exhaust back to the planetary atmosphere
- `AtmosphericTransferService` only handles cargo delivery; `AtmosphericExtractionService` only removes gas from a source

---

## Implementation Pattern: Follow Volcanic Emissions

### Reference: How volcanic emissions work

From `full_run_order.txt`:
```
💥 EXECUTING eruption 50902-1767586856-659 for body 50902
Adding 347.0 kg of Carbon Dioxide to atmosphere [Emission: CO2_c158]
Adding 241.2 kg of Sulfur Dioxide to atmosphere [Emission: SO2_e767]
Adding 745.5 kg of Water to atmosphere [Emission: H2O_d288]
```

The volcanic emissions code path:
1. Geosphere simulation service computes eruption gases (composition + mass)
2. Calls `@celestial_body.atmosphere.add_gas(gas_name, mass)` for each gas
3. Emissions are logged with format `[Emission: CO2_c158]`

### Target: How craft exhaust should work

Same pattern, different source:
1. Harvester model tracks propellant consumption during operation
2. Propellant type determines exhaust composition (e.g. CH4+O2 combustion → CO2 + H2O)
3. Exhaust rate tied to operational duration and propellant flow rate
4. Calls `@celestial_body.atmosphere.add_gas(gas_name, mass)` for each exhaust gas
5. Emissions logged with format `[Exhaust: CO2_xxx]`

---

## Implementation Requirements

### 1. Harvester Model — Add Exhaust Tracking

```ruby
# app/models/craft/harvester.rb (or relevant craft model)

# New attribute/column on harvester model
attr_accessor :exhaust_emissions

# Exhaust composition by propellant type
EXHAUST_COMPOSITION = {
  'CH4_O2' => { 'CO2' => 0.73, 'H2O' => 0.27 },  # Methane/oxygen combustion
  'LH2_LOX' => { 'H2O' => 1.0 },                    # Liquid hydrogen/oxygen
  'HYPERGOLIC' => { 'N2' => 0.6, 'CO2' => 0.4 }    # Hydrazine-based (example)
}.freeze

# Exhaust rate: kg of exhaust per kg of propellant consumed
EXHAUST_RATE = {
  'CH4_O2' => 1.37,  # ~1.37 kg exhaust per kg CH4+O2 consumed
  'LH2_LOX' => 9.0,  # ~9 kg H2O per kg LH2+LOX consumed
  'HYPERGOLIC' => 1.0
}.freeze

# Called during harvester operation loop
def apply_exhaust_to_atmosphere!
  return unless atmosphere && operational?
  
  propellant_type = definition_data['propellant_type'] || 'CH4_O2'
  exhaust_composition = EXHAUST_COMPOSITION[propellant_type]
  exhaust_rate = EXHAUST_RATE[propellant_type]
  
  # Calculate exhaust mass from propellant consumption this tick
  propellant_consumed = calculate_propellant_consumption_this_tick
  exhaust_mass_total = propellant_consumed * exhaust_rate
  
  exhaust_composition.each do |gas_name, fraction|
    gas_mass = exhaust_mass_total * fraction
    source_body.atmosphere.add_gas(gas_name, gas_mass) if source_body&.atmosphere&.present?
    
    Rails.logger.info "[Exhaust: #{gas_name}_#{SecureRandom.hex(4)}] " \
      "Harvester #{id} on #{source_body.name}: +#{gas_mass.round(2)}kg"
  end
end

private

def calculate_propellant_consumption_this_tick
  # Use existing propellant consumption data from operational_data
  (operational_data['propellant_flow_rate_kg_per_s'] || 0) * TICK_INTERVAL_SECONDS
end
```

### 2. Integration Points

| File | Change |
|------|--------|
| `app/models/craft/harvester.rb` | Add `apply_exhaust_to_atmosphere!` method, exhaust composition constants |
| `app/services/ai_manager/atmospheric_extraction_service.rb` | Call `harvester.apply_exhaust_to_atmosphere!` during extraction loop |
| `app/services/terra_sim/geosphere_simulation_service.rb` | Reference for volcanic emission pattern (do NOT edit) |

### 3. Scope Boundaries

**IN SCOPE:**
- Harvester exhaust emissions wired into `add_gas` on the body being operated near/at
- Exhaust composition tied to propellant type
- Logging format matches volcanic emission pattern `[Exhaust: CO2_xxxx]`

**OUT OF SCOPE (future work):**
- Cycler/transport craft exhaust (separate task)
- Settlement/industrial emissions (separate task)
- Atmospheric chemistry reactions triggered by exhaust (e.g. CH4 → CO2 conversion over time)
- Visual effects for exhaust plumes
- Exhaust accumulation/depletion modeling beyond simple `add_gas`

---

## Implementation Steps

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/drafts/2026-07-17/2026-07-17-MEDIUM-FEATURE-CRAFT-EXHAUST-ATMOSPHERE-FEEDBACK.md \
       projects/galaxy_game/tasks/active/2026-07-17-MEDIUM-FEATURE-CRAFT-EXHAUST-ATMOSPHERE-FEEDBACK.md
```

Then update YAML status: `status: backlog → status: active`

### Step 1 — Read reference implementations

Read these files to understand the existing patterns (do NOT edit):
- `galaxy_game/app/services/terra_sim/geosphere_simulation_service.rb` — volcanic emission pattern
- `galaxy_game/app/models/craft/harvester.rb` — current harvester model structure
- `galaxy_game/app/services/ai_manager/atmospheric_extraction_service.rb` — where to integrate exhaust calls

### Step 2 — Add exhaust tracking to harvester model

- Add `EXHAUST_COMPOSITION` and `EXHAUST_RATE` constants
- Add `apply_exhaust_to_atmosphere!` method
- Wire into operational loop (where propellant consumption is already tracked)

### Step 3 — Integrate with atmospheric extraction service

- Call `harvester.apply_exhaust_to_atmosphere!` during each extraction tick
- Ensure exhaust is applied to the correct body (source_body, not target_body)

### Step 4 — Verify

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/models/craft/harvester_spec.rb spec/services/ai_manager/atmospheric_extraction_service_spec.rb'
```

Expected: All existing tests pass, no regressions

---

## Acceptance Criteria

- [ ] Harvester model has `EXHAUST_COMPOSITION` and `EXHAUST_RATE` constants
- [ ] `apply_exhaust_to_atmosphere!` method exists and calls `add_gas` on source body
- [ ] Exhaust composition is tied to propellant type (CH4_O2, LH2_LOX, etc.)
- [ ] Exhaust rate is proportional to propellant consumption
- [ ] Integration with `AtmosphericExtractionService` during extraction loop
- [ ] Logging format matches volcanic emission pattern `[Exhaust: CO2_xxxx]`
- [ ] All existing harvester and atmospheric extraction tests pass
- [ ] No regressions in related specs

---

## Stop Conditions — escalate to user immediately if:
- Harvester model has no propellant consumption tracking (flag as gap)
- Source body atmosphere is nil/absent during operation (edge case, not blocker)
- Existing harvester tests fail for unrelated reasons (flag before proceeding)
- Atmospheric composition data structure has changed since last reference file was read

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add galaxy_game/app/models/craft/harvester.rb \
       galaxy_game/app/services/ai_manager/atmospheric_extraction_service.rb
git commit -m "feat: add harvester exhaust emissions to planetary atmosphere

- Harvester model gains EXHAUST_COMPOSITION and EXHAUST_RATE constants
- apply_exhaust_to_atmosphere! method wired into extraction loop
- Exhaust composition tied to propellant type (CH4_O2, LH2_LOX, etc.)
- Logging format matches volcanic emission pattern [Exhaust: CO2_xxxx]
- Follows same add_gas pattern as geosphere volcanic emissions"
git push
```

---

## Documentation
- [ ] No doc changes needed
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies

**Blocked by**: none (standalone feature task)  
**Blocks**: None — independent of precursor mission task chain (Profile/Manifest/Rake1/Rake2)  
**Related tasks**: 
- `2026-07-17-HIGH-FEATURE-PRECURSOR-MISSION-PROFILE.md` (Phase 5 — does NOT depend on this)
- `2026-07-17-HIGH-FEATURE-PRECURSOR-MISSION-MANIFEST.md` (Phase 5 — does NOT depend on this)

---

## Completion Report Template
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]  
**Completion date**: YYYY-MM-DD  

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
