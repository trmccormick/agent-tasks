---
status: backlog
priority: MEDIUM
type: documentation
system_domain: OTHER
mvp_alignment: SPEC_HEALTH
local_worker_safe: false
---

# TASK: Validate Planet Template Loader Against alien_world_templates_v1.1.json
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: documentation
**Created**: 2026-05-23
**Last Updated**: 2026-05-23

---

## Local Worker Triage Report
*Filled in by local model (Ollama via Continue) during backlog review*

- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS — RSpec commands use correct docker exec format
- **MVP Alignment**: VALID — template loader is used for ~40% of procedurally generated planets; invalid templates silently produce bad seeds
- **MVP Impact Note**: Broken templates produce planets with missing required fields that fail AR validation silently during generation
- **Action Line**: READY FOR CLOUD HANDOFF

---

## Agent Assignment

**Assigned To**: GPT-4.1 0x
**Why This Agent**: Mechanical verification task — read JSON, cross-reference against spec, write a guard spec, update wiki
**Supervision Level**: watched carefully

---

## Context

`StarSim::ProceduralGenerator` loads terraformable planet templates from `alien_world_templates_v1.1.json` (fallback: `alien_world_templates_v1.json`) at initialization. Approximately 40% of procedurally generated terrestrial planets (`TERRAFORMABLE_CHANCE = 0.4`) are derived from these templates via `generate_from_template`. The template file has not been reviewed during the Phase 3 documentation sprint. If a template entry is missing a required field, `generate_from_template` will produce an invalid seed that fails silently or raises at AR creation time.

Additionally, `known_pressure` is set as `rand(0.1..2.0)` in procedural generation but its unit (atm, kPa, or Pa) is not documented anywhere in the generator code.

**Relevant Architecture Docs** — read before starting:
- `docs/wiki/System-Blueprints.md` — Section 7 (Template System) for confirmed required fields
- `docs/GUARDRAILS.md` — path configuration standards (Section 7)

---

## Problem Statement

**Current behavior**: Template file content is unknown. No spec guards against template entries missing required fields. `known_pressure` unit is undocumented.

**Expected behavior**:
- Template file reviewed, field list confirmed
- Spec exists that loads the real template file and asserts required fields are present on every entry
- `known_pressure` unit confirmed and documented in wiki and inline code comment
- `docs/wiki/System-Blueprints.md` Section 7 updated with confirmed template details

Required fields per spec confirmation (verify these are still current):
`type`, `mass`, `radius`, `density`, `gravity`, `albedo`, `known_pressure`, `surface_temperature`, `geological_activity`, `geosphere_attributes`

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `spec/services/star_sim/procedural_generator_spec.rb` | Generator spec | Add template field validation context |
| `docs/wiki/System-Blueprints.md` | System blueprint wiki | Section 7 (Template System) |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `data/json-data/templates/alien_world_templates_v1.1.json` | Primary template file to audit (confirm path via `GalaxyGame::Paths::TEMPLATE_PATH`) |
| `data/json-data/templates/alien_world_templates_v1.json` | Fallback template file |
| `star_sim/procedural_generator.rb` | Shows `generate_from_template` and `TERRAFORMABLE_CHANCE` |
| `config/initializers/game_data_paths.rb` | Confirms `GalaxyGame::Paths::TEMPLATE_PATH` value |

### Migration (if needed)
- [x] No migration needed

---

## Implementation Steps

### Step 1 — Read template files

Read `alien_world_templates_v1.1.json` in full. Record:
- Total number of template entries
- All fields present on each entry
- Any entries missing required fields (list by name)
- Value ranges for key fields (mass, radius, gravity, known_pressure)

### Step 2 — Resolve known_pressure unit

In `star_sim/procedural_generator.rb`, trace how `known_pressure` is consumed after generation — check whether it is passed to `Atmosphere`, compared against `HUMAN_LIFE_SUPPORT` constants, or used in `NpcPriceCalculator`. Confirm the unit (atm, kPa, or Pa).

Add an inline comment to the generator at the `known_pressure` assignment line:
```ruby
known_pressure: planet_data["known_pressure"] || rand(0.1..2.0), # unit: [atm | kPa | Pa — confirm from trace]
```

### Step 3 — Write template guard spec

Add a context block to `spec/services/star_sim/procedural_generator_spec.rb`:

```ruby
describe 'alien_world_templates_v1.1.json integrity' do
  let(:template_path) { GalaxyGame::Paths::TEMPLATE_PATH.join('alien_world_templates_v1.1.json') }
  let(:templates) { JSON.parse(File.read(template_path)) }
  let(:required_fields) do
    %w[type mass radius density gravity albedo known_pressure
       surface_temperature geological_activity geosphere_attributes]
  end

  it 'loads without error' do
    expect { templates }.not_to raise_error
  end

  it 'contains at least one template entry' do
    expect(templates).not_to be_empty
  end

  it 'has all required fields on every entry' do
    templates.each do |template|
      missing = required_fields - template.keys
      expect(missing).to be_empty,
        "Template '#{template['name'] || '(unnamed)'}' is missing fields: #{missing.join(', ')}"
    end
  end

  it 'only contains terrestrial type entries' do
    non_terrestrial = templates.reject { |t| t['type'] == 'terrestrial' }
    expect(non_terrestrial).to be_empty,
      "Non-terrestrial templates found: #{non_terrestrial.map { |t| t['name'] }.join(', ')}"
  end
end
```

### Step 4 — Verify spec

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/star_sim/procedural_generator_spec.rb 2>&1 | tail -20'
```

Expected result: 0 failures

### Step 5 — Update wiki

Update `docs/wiki/System-Blueprints.md` Section 7 with:
- Confirmed template count
- Confirmed field list (add or remove from current list based on Step 1 findings)
- Confirmed `known_pressure` unit
- Note any template entries found to be missing required fields

---

## Acceptance Criteria
- [ ] Template file read in full; field list confirmed
- [ ] Template guard spec passes with 0 failures
- [ ] Any template entries missing required fields identified and noted in completion report
- [ ] `known_pressure` unit confirmed and added as inline comment in generator
- [ ] `docs/wiki/System-Blueprints.md` Section 7 updated with confirmed values
- [ ] No new wiki pages created

---

## Stop Conditions — escalate to user immediately if:
- Template file path does not match `GalaxyGame::Paths::TEMPLATE_PATH` — do not guess an alternate path
- Multiple template entries are missing required fields — this is a data quality issue requiring human decision on whether to fix templates or relax the validation
- `known_pressure` unit cannot be determined from code trace alone — flag for human review

---

## Commit Instructions
Run git commands on **host**, not inside container:
```bash
git add spec/services/star_sim/procedural_generator_spec.rb star_sim/procedural_generator.rb docs/wiki/System-Blueprints.md
git commit -m "documentation: verify alien_world_templates and resolve known_pressure unit ambiguity"
git push
```

---

## Documentation
- [ ] Update `docs/wiki/System-Blueprints.md` — Section 7 template details confirmed

---

## Dependencies
**Blocked by**: JSON template file access (not available during current documentation sprint)
**Blocks**: none
**Related tasks**: `2026-05-23-HIGH-BUG-FIX-UNIVERSE-REGISTRATION-JOB-SPATIAL-BOUNDARY-VALIDATION.md`

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Final test result**:

### What was changed
-

### Issues discovered
-

### Follow-up tasks needed
-

### Lessons learned
-

---

## Handoff Summary
HANDOFF SUMMARY: [files updated] | template integrity spec added | known_pressure unit resolved | next: expand fallback_build planet type coverage
