---
title: "Precursor Mission Rake — Phase 1 (GCC, HLT Landings, Power Grid)"
priority: HIGH
status: active
phase: phase5
owner: Implementation Agent (Qwen)
type: feature
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
created: 2026-07-17
---

# TASK: Create Precursor Mission Rake — Phase 1

**Status**: ACTIVE  
**Priority**: HIGH (Phase 5 — MVP prerequisite)  
**Type**: feature  
**Created**: 2026-07-17  
**Last Updated**: 2026-07-17  

---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase5+/2026-07-17-HIGH-FEATURE-PRECURSOR-MISSION-RAKE-PHASE1.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/phase5+/2026-07-17-HIGH-FEATURE-PRECURSOR-MISSION-RAKE-PHASE1.md \
         projects/galaxy_game/tasks/active/2026-07-17-HIGH-FEATURE-PRECURSOR-MISSION-RAKE-PHASE1.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-17-HIGH-FEATURE-PRECURSOR-MISSION-RAKE-PHASE1.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-FEATURE-PRECURSOR-MISSION-RAKE-PHASE1.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*
*This section is NOT sent to agents — it's for human task management only*

- **Template Conformance**: PASS
- **Docker Wrapper Check**: N/A (rake creation, no RSpec)
- **MVP Alignment**: VALID — Phase 5 prerequisite for Luna MVP
- **MVP Impact Note**: Bootstrap rake validates the first 3 phases before interplanetary logistics
- **Action Line**: READY FOR LOCAL DISPATCH (depends on Task A profile + Task B manifest completion)

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Rake creation is straightforward, well-specified task with clear pattern reference
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully — first dispatch of this task type

> **Primary executor is always local Qwen via the GitHub Copilot custom agent config.**
> Cloud/paid agents are fallback only.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below
4. **Reference Files**: Listed in "Files to Read" section
5. **Task A Output**: `precursor_mission_profile_v1.json` — phase data source
6. **Task B Output**: `precursor_mission_manifest_v1.json` — hardware data source

> Agent MUST read in this order. Do not skip.

---

## Objective

Create `lunar_precursor_mission_validation.rake` implementing **Phase 1 only**: GCC mining → Initial HLT landings → Power grid deployment. This is the bootstrap layer that must work before any interplanetary logistics can be tested.

**Parent Task**: Full Precursor Mission Validation (4-part sequence)  
**Depends On**: Task A (profile), Task B (manifest) — both provide data-driven inputs  
**Feeds Into**: Task D (Phase 2 rake extension) which adds Titan/Venus transit logic

---

## Scope: Phase 1 Only (3 Phases)

| Phase | What It Tests | Duration |
|-------|--------------|----------|
| gcc_mining | Currency generation via GCC satellite | 30 days |
| initial_hlt_landings | Multi-cargo HLT deliveries (habitat, ISRU, tank farm) | 180 days |
| power_grid_deployment | RTG + solar array + umbilical hub | 120 days |

**Phase 2 (Titan/Venus transit, ISRU production, L1/LEO supply)** is NOT in scope for this task.

---

## Implementation Requirements

### Target File
`galaxy_game/lib/tasks/lunar_precursor_mission_validation.rake`

### Pattern Reference
- `galaxy_game/lib/tasks/luna_mission.rake` — current rake pattern (deployment only)
- Use existing `Manufacturing::ProductionService` for ISRU validation where applicable

### Key Design Constraints

1. **Data-driven from profile/manifest**
   - Read phase data from `precursor_mission_profile_v1.json` and `precursor_mission_manifest_v1.json`
   - Do NOT hardcode hardware counts or durations in the rake — read from JSON
   - The rake validates that the data-driven pipeline works end-to-end

2. **No world-name hardcoding**
   - Use planet properties (atmosphere composition, distance) to determine HLT delivery contents
   - Pattern must work for any system with gas giants + terrestrial planets

3. **Incremental validation**
   - Each phase should validate independently before proceeding to the next
   - Clear pass/fail output per phase
   - Stop on first failure (don't continue if gcc_mining fails)

---

## Problem Statement

The current `luna_mission:execute` rake only tests deployment of pre-built hardware on Luna. It does not test the bootstrap supply chain that makes the MVP viable:
- No GCC mining validation (currency generation for procurement)
- No multi-cargo HLT landing validation (habitat, ISRU, tank farm)
- No power grid validation (RTG + solar array + umbilical hub)

**Current behavior**: No precursor mission rake exists.
**Expected behavior**: A rake validates the first 3 phases end-to-end using data-driven profile/manifest inputs.

---

## Rake Structure

```ruby
# lib/tasks/lunar_precursor_mission_validation.rake

namespace :luna_mission do
  desc "Phase 1: GCC mining → Initial HLT landings → Power grid deployment"
  task phase1_bootstrap: :environment do
    puts "=" * 80
    puts "LUNA PRECURSOR MISSION — PHASE 1 BOOTSTRAP"
    puts "=" * 80

    # Load profile and manifest data
    profile = load_profile('precursor_mission_profile_v1')
    manifest = load_manifest('precursor_mission_manifest_v1')

    # Phase 1: GCC Mining
    puts "\n--- Phase 1/3: GCC Mining ---"
    gcc_result = validate_gcc_mining(profile, manifest)
    unless gcc_result[:success]
      abort("Phase 1 failed: #{gcc_result[:error]}")
    end
    puts "✓ Currency generation verified: #{gcc_result[:currency_per_day]} GCC/day"

    # Phase 2: Initial HLT Landings
    puts "\n--- Phase 2/3: Initial HLT Landings ---"
    landings_result = validate_initial_hlt_landings(profile, manifest)
    unless landings_result[:success]
      abort("Phase 2 failed: #{landings_result[:error]}")
    end
    puts "✓ Multi-cargo delivery verified: #{landings_result[:manifests_count]} manifests"

    # Phase 3: Power Grid Deployment
    puts "\n--- Phase 3/3: Power Grid Deployment ---"
    power_result = validate_power_grid_deployment(profile, manifest)
    unless power_result[:success]
      abort("Phase 3 failed: #{power_result[:error]}")
    end
    puts "✓ Power grid verified: RTG=#{power_result[:rtg_units]}, Solar=#{power_result[:solar_beams]}"

    puts "\n" + "=" * 80
    puts "PHASE 1 COMPLETE — All bootstrap phases validated successfully"
    puts "=" * 80
  end
end
```

---

## Phase Validation Methods

### validate_gcc_mining(profile, manifest)
- Load GCC mining phase from profile (phase_id: "gcc_mining")
- Verify GCC satellite hardware exists in manifest
- Validate currency generation via Manufacturing::ProductionService
- Return: `{ success: true/false, currency_per_day: N, error: msg }`

### validate_initial_hlt_landings(profile, manifest)
- Load initial_hlt_landings phase from profile
- Verify 3 landing manifests exist in manifest
- Validate each cargo manifest can be procured and delivered
- Return: `{ success: true/false, manifests_count: N, error: msg }`

### validate_power_grid_deployment(profile, manifest)
- Load power_grid_deployment phase from profile
- Verify RTG units, solar array beams, umbilical hub in manifest
- Validate 3D printing of structural beams from regolith feedstock
- Return: `{ success: true/false, rtg_units: N, solar_beams: N, error: msg }`

---

## Implementation Steps

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/current/2026-07-17-HIGH-FEATURE-PRECURSOR-MISSION-RAKE-PHASE1.md \
       projects/galaxy_game/tasks/active/2026-07-17-HIGH-FEATURE-PRECURSOR-MISSION-RAKE-PHASE1.md
```

Then update YAML status: `status: backlog → status: active`

### Step 1 — Read reference rake for pattern

Read `galaxy_game/lib/tasks/luna_mission.rake` to understand the current rake pattern.

### Step 2 — Read Task A and Task B outputs

Read both JSON files to understand the data-driven inputs:
- `precursor_mission_profile_v1.json` — phase structure
- `precursor_mission_manifest_v1.json` — hardware mapping

### Step 3 — Create lunar_precursor_mission_validation.rake

Create the file at `galaxy_game/lib/tasks/lunar_precursor_mission_validation.rake` with:
- `phase1_bootstrap` task implementing GCC mining, HLT landings, power grid
- Phase data read from JSON (not hardcoded)
- No world-name hardcoding in rake logic
- Clear pass/fail output per phase
- Stops on first failure

### Step 4 — Test rake execution

```bash
docker exec web bash -c 'unset DATABASE_URL && bundle exec rake luna_mission:phase1_bootstrap'
```

Expected: All 3 phases validate successfully with clear pass/fail output

---

## Success Criteria

- [ ] Rake file created at correct path
- [ ] `rake luna_mission:phase1_bootstrap` runs successfully
- [ ] All 3 phases validate independently with clear pass/fail output
- [ ] Phase data is read from JSON profile/manifest (not hardcoded)
- [ ] No world-name hardcoding in the rake logic
- [ ] Manufacturing::ProductionService used for ISRU validation where applicable
- [ ] Stops on first failure (doesn't continue if earlier phase fails)

---

## Stop Conditions — escalate to user immediately if:
- Profile_v2 schema has changed since last reference file was read
- Existing rake pattern doesn't support data-driven inputs (flag as gap)
- Manufacturing::ProductionService API has changed (flag as gap)

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add galaxy_game/lib/tasks/lunar_precursor_mission_validation.rake
git commit -m "feat: create precursor mission rake Phase 1 with bootstrap validation"
git push
```

---

## Documentation
- [ ] No doc changes needed
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: Task A (profile), Task B (manifest) — both provide data-driven inputs
**Blocks**: Task D (Phase 2 rake extension), which adds Titan/Venus transit logic
**Related tasks**: Full Precursor Mission Validation (4-part sequence)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was changed
- `galaxy_game/lib/tasks/lunar_precursor_mission_validation.rake` — created with Phase 1 rake task

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]

---

## Files to Read Before Starting

| File | Why |
|------|-----|
| `galaxy_game/lib/tasks/luna_mission.rake` | Current rake pattern reference |
| `galaxy_game/data/json-data/missions_v2/profiles/precursor_mission_profile_v1.json` | Phase data (Task A output) |
| `galaxy_game/data/json-data/missions_v2/manifests/precursor_mission_manifest_v1.json` | Hardware data (Task B output) |
| `app/services/manufacturing/production_service.rb` | ISRU validation service |

---

## Stop Conditions

- Rake file is complete and runs successfully
- All 3 phases validate with clear pass/fail output
- Phase data is read from JSON (not hardcoded)
- **Do NOT** add Titan/Venus transit logic — that's Task D
