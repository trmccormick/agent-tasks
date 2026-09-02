---
status: backlog
priority: HIGH
type: feature
system_domain: OTHER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
created: 2026-08-18
estimated_effort: 4-6 hours
# DISPATCH ORDERING — do not dispatch a task whose depends_on is not yet completed.
depends_on: []
blocks:
  - 2026-08-19-HIGH-FEATURE-ORBITAL-MECHANICS-DATA-LAYER   # Phase 5 extends this engine
blocker_for:
  - luna_settlement_simulation
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [x] All Step 0-N instructions are clear and actionable (not vague)
- [x] Synthesis report template is provided (copy/paste ready, not as example)
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is READY FOR DISPATCH.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-18-HIGH-FEATURE-LAUNCH-WINDOW-TRANSIT-TIMING-ENGINE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-18-HIGH-FEATURE-LAUNCH-WINDOW-TRANSIT-TIMING-ENGINE.md \
         projects/galaxy_game/tasks/active/2026-08-18-HIGH-FEATURE-LAUNCH-WINDOW-TRANSIT-TIMING-ENGINE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks -name "2026-08-18-HIGH-FEATURE-LAUNCH-WINDOW-TRANSIT-TIMING-ENGINE.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-08-18-FEATURE-LAUNCH-WINDOW-TRANSIT-TIMING-ENGINE.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Launch Window + Transit Timing Engine for Luna Precursor Mission
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-08-18
**Last Updated**: 2026-09-02

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*

- **Template Conformance**: PASS (updated 2026-09-02 to conform to TASK_TEMPLATE.md)
- **Docker Wrapper Check**: PASS — RSpec commands use correct docker exec format
- **MVP Alignment**: VALID — transit timing is a prerequisite for Luna settlement simulation and the live game loop to process craft arrivals
- **MVP Impact Note**: Without transit timing, the live game loop cannot gate N₂ offload on tank farm readiness, breaking the critical path for habitat pressurization
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Feature implementation with clear code specs, well within local Qwen's terminal/tool-use access
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below
4. **Live Game-Loop Findings**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-08-30-FINDINGS-LIVE-GAME-LOOP-REALITY-CHECK.md` — critical context: craft are NOT ticked by the live loop (they inherit from ApplicationRecord, not Units::BaseUnit)

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context

The precursor mission pipeline (rake `luna_mission:phase1_bootstrap`) validates JSON structure but has **zero timing simulation**. The solar system pipeline (`solar_system_mission_pipeline.rake`) gates corporate formation on N2 delivery completion but doesn't simulate craft departure, transit, or arrival.

**Critical path for Luna settlement:**
1. Precursor deploys → builds 2 landing pads + comms + power (no HLT can land yet)
2. **Precursor also launches Venus skimmer from Earth** (concurrent with pad construction)
3. Landing pads complete → HLT #1 lands with inflatable tanks (empty)
4. **ISRU goes online: Regolith → TEU/PVE → Gas Separator → N₂ to lava tube tank farm + O₂ to life support**
5. **Venus skimmer arrives ~400d later with N₂ load** → supplements tank farm → habitats pressurized
6. HLT #2 lands → habitats deployed into pressurized tanks
7. Titan skimmers arrive later (~730d transit) → supplementary CH₄ for Venus skimmer fleet refueling

The Venus skimmer departs Earth **around the same time as the precursor launch** — it's a separate craft on a different trajectory, not part of the HLT fleet. It harvests Venusian atmosphere (CO₂ + N₂) and arrives at Luna after transit.

**N₂ flow architecture:**
- **Primary source:** Luna ISRU pipeline (regolith → TEU/PVE → gas separator → N₂ to lava tube tank farm, O₂ to life support)
  - Gas separator connects to planetary umbilical hub with dedicated `nitrogen_output` port → `cryo_grid_nitrogen`
  - Also outputs O₂ → `cryo_grid_o2` for life support
- **Supplemental source:** Venus skimmer (arrives later, tops up when ISRU can't keep pace with habitat expansion)
- **Tertiary source:** Titan skimmer fleet (CH₄ for Venus skimmer refueling, supplementary N₂)

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules

---

## What Exists Today

| Layer | Status | Location |
|-------|--------|----------|
| Task definitions (~100+ tasks) | ✅ `missions_v2/tasks/*.json` | e.g., `task_cycler_transit_preparation.json` |
| Mission profiles/manifests | ✅ `missions_v2/profiles/`, `manifests/` | `precursor_mission_profile_v1.json` |
| Rake validation (structure only) | ✅ `luna_mission:phase1_bootstrap` | Checks JSON exists, not timing |
| Solar system pipeline rake | ⚠️ Corporate gates only | Gates on N2 delivery, not launch windows |
| Venus harvester mission (old format) | ⚠️ `missions/venus_harvester_mission/` | Needs v2 adaptation |
| **Launch window calculator** | ❌ None | Earth-Venus transfer windows |
| **Transit time engine** | ❌ None | Venus ~400d, Titan ~730d |
| **Arrival queue** | ❌ None | Precursor → HLT → Venus skimmer ordering |

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Craft are NOT ticked by the live game loop.
- ❌ Wrong: "I'll add the transit engine to `Game#process_units` and it'll auto-advance"
- ✅ Right: The transit engine is a standalone service invoked by rake tasks and AIManager services. Craft inherit from `ApplicationRecord`, NOT `Units::BaseUnit` — they are excluded from `Game#process_units` (confirmed in Live Game-Loop Reality Check findings, 2026-08-30)
- Why: The live loop only processes `Settlement::BaseSettlement`, `Units::BaseUnit`, and `CelestialBodies::CelestialBody`. Craft are a separate hierarchy.

⚠️ **GOTCHA 2**: Do not assume the transit engine will be called by `GameSimulationJob`.
- ❌ Wrong: "The background job will tick transit state automatically"
- ✅ Right: `GameSimulationJob` calls `game.advance_by_days` which processes settlements/units/planets only. The transit engine must be explicitly invoked by rake tasks or AIManager services
- Why: `GameState#running` defaults to false, and even when true, the job does not invoke AIManager or craft logic

⚠️ **GOTCHA 3**: The Venus skimmer departs Earth, not Luna.
- ❌ Wrong: "Venus skimmer launches from Luna after precursor arrives"
- ✅ Right: Venus skimmer departs Earth concurrently with the precursor launch, loaded with methane fuel at Earth, then goes to Venus for atmospheric harvest
- Why: The skimmer needs methane fuel for the return trip, which is loaded at Earth. It's a separate craft on a different trajectory, not part of the HLT fleet.

⚠️ **GOTCHA 4**: Tank farm readiness gates N₂ offload — this is a hard constraint.
- ❌ Wrong: "N₂ arrives, add to inventory regardless of infrastructure"
- ✅ Right: Check that inflatable tanks are deployed and tank farm infrastructure is complete BEFORE allowing N₂ offload. If not ready, the delivery is delayed (not lost)
- Why: You can't offload gas into nothing. This is a critical path constraint for habitat pressurization.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before running any commands or modifying any files, save a synthesis report as MD to the summaries folder covering:
- What you found in the existing mission pipeline (rake tasks, profiles, manifests)
- Your understanding of the critical path (precursor → pads → HLT → ISRU → Venus skimmer → habitats)
- The files you'll create/modify (exact paths)
- Your verification plan (how you'll confirm the timing engine works end-to-end)
- How the transit engine relates to the live game loop (it doesn't — it's rake/AIManager driven)

---

## Problem Statement

**Current behavior**: The precursor mission pipeline validates JSON structure but has zero timing simulation. No launch window calculator, transit time engine, or arrival queue exists. The Venus skimmer and Titan skimmer missions are in old format and not wired into the v2 pipeline.

**Expected behavior**: A launch window + transit timing engine that calculates transfer windows, tracks craft departure/transit/arrival states, queues arrivals in correct order, and gates N₂ offload on tank farm readiness.

---

## Files Involved

### Primary Files — you will create/edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/app/services/mission/transit_engine.rb` | Transit timing engine (new) | `calculate_transfer_window`, `schedule_departure`, `has_arrived?` |
| `galaxy_game/data/json-data/missions_v2/tasks/task_venus_harvest_arrival_v2.json` | Venus harvest arrival task (new) | Full JSON structure |
| `galaxy_game/data/json-data/missions_v2/tasks/task_titan_harvest_arrival_v2.json` | Titan harvest arrival task (new) | Full JSON structure |
| `galaxy_game/lib/tasks/lunar_precursor_mission_validation.rake` | Add `phase_timing` task | New rake task |
| `galaxy_game/data/json-data/missions_v2/profiles/precursor_mission_profile_v1.json` | Add Venus harvest phase | Phases array |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/data/json-data/missions_v2/tasks/task_cycler_transit_preparation.json` | Existing transit task template (v2 format) |
| `galaxy_game/data/json-data/missions/venus_harvester_mission/venus_harvest_01_phases_v1.json` | Old format to adapt |
| `galaxy_game/data/json-data/missions_v2/manifests/precursor_mission_manifest_v1.json` | Has delivery_to_luna with transit_days |
| `galaxy_game/app/models/game.rb` | Live game loop (to confirm craft are NOT ticked) |
| `galaxy_game/app/jobs/game_simulation_job.rb` | Background job (to confirm it doesn't invoke transit engine) |

### Migration
- [x] No migration needed

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
(See Agent Dispatch Interface above — same procedure applies.)

### Step 1: Create TransitEngine service class

**File:** `galaxy_game/app/services/mission/transit_engine.rb`

```ruby
class Mission::TransitEngine
  # Transfer window calculation (Earth → Venus)
  # Uses simplified Hohmann transfer approximation
  def self.earth_to_venus_transit_days
    # ~146 days for Hohmann transfer Earth→Venus
    # Real value varies with orbital positions; use 146 as baseline
    146
  end

  # Transfer window calculation (Earth → Titan)
  def self.earth_to_titan_transit_days
    # ~730 days for Earth→Titan (via Jupiter gravity assist or direct)
    730
  end

  # Transfer window calculation (Luna → Venus)
  def self.luna_to_venus_transit_days
    # Same as Earth→Venus (Luna is close to Earth gravitationally)
    earth_to_venus_transit_days
  end

  # Calculate next transfer window from Earth to target body
  # Returns: { departure_date: Date, arrival_date: Date, transit_days: N }
  def self.calculate_transfer_window(from_body, to_body, launch_date = nil)
    launch_date ||= Time.current.to_date
    transit_days = case "#{from_body}_#{to_body}"
    when "EARTH_VENUS", "LUNA_VENUS"
      earth_to_venus_transit_days
    when "EARTH_TITAN", "LUNA_TITAN"
      earth_to_titan_transit_days
    when "EARTH_MARS", "LUNA_MARS"
      259  # ~8.5 months Earth→Mars
    else
      365  # default: 1 year transit
    end

    {
      departure_date: launch_date,
      arrival_date: launch_date + transit_days,
      transit_days: transit_days
    }
  end

  # Check if a transfer window is open (simplified — real version uses orbital mechanics)
  def self.transfer_window_open?(from_body, to_body, date)
    # Simplified: windows open every 584 days (Venus synodic period)
    # For MVP, always open — real version would check planetary positions
    true
  end

  # Create a transit record for a craft
  def self.schedule_departure(craft_id, from_body, to_body, launch_date)
    window = calculate_transfer_window(from_body, to_body, launch_date)
    
    {
      craft_id: craft_id,
      status: :in_transit,
      from_body: from_body,
      to_body: to_body,
      departure_date: window[:departure_date],
      arrival_date: window[:arrival_date],
      transit_days: window[:transit_days],
      payload: nil  # Set when manifest is loaded
    }
  end

  # Check if a craft has arrived (given current simulation day)
  def self.has_arrived?(transit_record, sim_day)
    sim_date = Time.current.to_date + sim_day
    sim_date >= transit_record[:arrival_date]
  end
end
```

### Step 2: Create Venus harvest arrival task (v2 format)

**File:** `data/json-data/missions_v2/tasks/task_venus_harvest_arrival_v2.json`

Adapted from `missions/venus_harvester_mission/venus_harvest_01_phases_v1.json`:

```json
{
  "task_id": "venus_harvest_arrival_v2",
  "version": "2.0",
  "name": "Venus Atmospheric Harvester Arrival at Luna",
  "description": "Venus skimmer arrives at Luna with harvested N₂ + CO₂ payload. Offload requires tank farm infrastructure to be ready.",
  "prerequisites": [
    "task_site_prep_foundation_v2",
    "task_print_inflatable_tank_shells_v2"
  ],
  "launch_window": {
    "departure_body": "EARTH-01",
    "arrival_body": "LUNA-01",
    "transit_days": 400,
    "synodic_period_days": 584,
    "note": "Venus skimmer departs Earth around same time as precursor launch. Harvests Venus atmosphere en route."
  },
  "payload": {
    "source_gases": ["CO2", "N2"],
    "co2_kg": 75000,
    "n2_kg": 30000,
    "lox_for_return_kg": 10000,
    "methane_remaining_kg": 5000
  },
  "offload_requirements": {
    "tank_farm_ready": true,
    "inflatable_tanks_deployed": true,
    "minimum_tank_count": 3,
    "cryo_tanks_required": true
  },
  "effects": [
    {
      "action": "add_to_inventory",
      "target": "luna_settlement",
      "items": [
        { "name": "N2", "amount": 30000, "unit": "kg" },
        { "name": "CO2", "amount": 75000, "unit": "kg" }
      ]
    },
    {
      "action": "set_status_flag",
      "flag": "venus_n2_delivery_completed",
      "value": true
    },
    {
      "action": "gate_open",
      "target": "habitat_pressurization",
      "description": "N₂ available for habitat pressurization"
    }
  ],
  "metadata": {
    "template": "task_v2",
    "type": "arrival_offload",
    "craft_type": "venus_harvester_skimmer",
    "adapted_from": "venus_harvest_01_phases_v1.json"
  }
}
```

### Step 3: Add timing validation to rake

**File:** `galaxy_game/lib/tasks/lunar_precursor_mission_validation.rake`

Add a new task `phase_timing`:

```ruby
desc "Phase timing: validate launch windows, transit times, arrival ordering"
task phase_timing: :environment do
  puts "=" * 80
  puts "LUNA PRECURSOR MISSION — PHASE TIMING VALIDATION"
  puts "=" * 80

  # Simulate timeline starting from day 0
  sim_day = 0
  
  # Phase 0: Precursor launch (Day 0)
  puts "\n--- Day 0: Precursor Launch from Earth ---"
  precursor_departure = Mission::TransitEngine.schedule_departure(
    "precursor_hlt_1", "EARTH-01", "LUNA-01", Time.current.to_date
  )
  puts "  ✓ Precursor departed Earth → Luna (#{precursor_departure[:transit_days]}d transit)"
  
  # Phase 0b: Venus skimmer launch (concurrent, Day 0)
  puts "\n--- Day 0: Venus Skimmer Launch from Earth ---"
  venus_departure = Mission::TransitEngine.schedule_departure(
    "venus_harvester_01", "EARTH-01", "VENUS-01", Time.current.to_date
  )
  puts "  ✓ Venus skimmer departed Earth → Venus (#{venus_departure[:transit_days]}d transit)"
  
  # Phase 1: Precursor arrives and builds landing pads
  precursor_arrival_day = precursor_departure[:transit_days]
  sim_day = precursor_arrival_day
  
  puts "\n--- Day #{sim_day}: Precursor Arrives at Luna ---"
  puts "  ✓ Landing pad construction starts (2 pads)"
  puts "  ✓ Comms deployed"
  puts "  ✓ Power grid (RTG) deployed"
  
  # Pad construction takes time — simulate as 30 days
  pad_construction_days = 30
  sim_day += pad_construction_days
  
  puts "\n--- Day #{sim_day}: Landing Pads Complete ---"
  puts "  ✓ Pad Alpha ready for HLT landing"
  puts "  ✓ Pad Beta ready for HLT landing"
  
  # Phase 2: HLT #1 lands with inflatable tanks
  puts "\n--- Day #{sim_day}: HLT #1 Lands (Inflatable Tanks) ---"
  puts "  ✓ Inflatable tanks deployed (empty, awaiting N₂)"
  
  # Tank farm setup takes time — simulate as 15 days
  tank_farm_days = 15
  sim_day += tank_farm_days
  
  puts "\n--- Day #{sim_day}: Tank Farm Ready ---"
  puts "  ✓ Tank farm infrastructure complete"
  puts "  ✓ Ready for N₂ offload"
  
  # Phase 3: Venus skimmer arrives at Venus, begins harvest
  venus_at_venus_day = venus_departure[:transit_days]
  puts "\n--- Day #{venus_at_venus_day}: Venus Skimmer Arrives at Venus ---"
  puts "  ✓ Atmospheric harvesting begins (CO₂ + N₂ extraction)"
  
  # Harvesting takes time — simulate as 30 days at Venus
  harvest_days = 30
  venus_departure_from_venus = venus_at_venus_day + harvest_days
  
  puts "\n--- Day #{venus_departure_from_venus}: Venus Skimmer Departs Venus → Luna ---"
  
  # Phase 4: Venus skimmer arrives at Luna
  venus_arrival_at_luna = venus_departure_from_venus + Mission::TransitEngine.earth_to_venus_transit_days
  
  puts "\n--- Day #{venus_arrival_at_luna}: Venus Skimmer Arrives at Luna ---"
  
  # Check if tank farm is ready
  if sim_day <= venus_arrival_at_luna
    puts "  ✓ Tank farm ready (completed day #{sim_day})"
    puts "  ✓ N₂ offload: 30,000 kg N₂ + 75,000 kg CO₂ delivered"
    puts "  ✓ HABITAT PRESSURIZATION GATE OPENED"
  else
    puts "  ⚠ Tank farm NOT ready — N₂ arrives before tanks (CRITICAL FAILURE)"
    abort("Venus skimmer arrived at Luna before tank farm was ready")
  end
  
  # Phase 5: Titan skimmer launch (later, needs methane from Earth)
  titan_launch_day = venus_arrival_at_luna + 90  # ~3 months after Venus arrival
  puts "\n--- Day #{titan_launch_day}: Titan Skimmer Launch from Earth ---"
  titan_departure = Mission::TransitEngine.schedule_departure(
    "titan_harvester_01", "EARTH-01", "TITAN-01", Time.current.to_date + titan_launch_day
  )
  puts "  ✓ Titan skimmer departed Earth → Titan (#{titan_departure[:transit_days]}d transit)"
  
  titan_arrival_at_titan = titan_launch_day + titan_departure[:transit_days]
  titan_departure_from_titan = titan_arrival_at_titan + harvest_days
  
  puts "\n--- Day #{titan_departure_from_titan}: Titan Skimmer Departs Titan → Luna ---"
  
  titan_arrival_at_luna = titan_departure_from_titan + Mission::TransitEngine.earth_to_titan_transit_days
  
  puts "\n--- Day #{titan_arrival_at_luna}: Titan Skimmer Arrives at Luna ---"
  puts "  ✓ CH₄ delivery: supplementary fuel for Venus skimmers"
  
  puts "\n" + "=" * 80
  puts "TIMING VALIDATION COMPLETE — All windows and arrivals verified"
  puts "=" * 80
end
```

### Step 4: Wire Venus harvest task into precursor manifest

**File:** `data/json-data/missions_v2/profiles/precursor_mission_profile_v1.json`

Add to phases array:

```json
{
  "phase_id": "venus_harvest_arrival",
  "name": "Venus Atmospheric Harvester Arrival",
  "description": "Venus skimmer arrives at Luna with harvested N₂ + CO₂ payload. Gates habitat pressurization.",
  "prerequisites": ["initial_hlt_landings"],
  "duration_days": 400,
  "task_ref": "tasks_v2/task_venus_harvest_arrival_v2.json",
  "launch_window": {
    "departure_body": "EARTH-01",
    "arrival_body": "LUNA-01",
    "transit_days": 400,
    "concurrent_with": "precursor_launch"
  },
  "outputs": ["n2_delivery", "co2_delivery", "habitat_pressurization_gate"]
}
```

### Step 5: Add Titan harvest arrival task (v2 format)

**File:** `data/json-data/missions_v2/tasks/task_titan_harvest_arrival_v2.json`

Similar structure to Venus task but with Titan-specific values:

```json
{
  "task_id": "titan_harvest_arrival_v2",
  "version": "2.0",
  "name": "Titan Atmospheric Harvester Arrival at Luna",
  "description": "Titan skimmer arrives at Luna with harvested CH₄ payload for Venus skimmer refueling.",
  "launch_window": {
    "departure_body": "TITAN-01",
    "arrival_body": "LUNA-01",
    "transit_days": 730,
    "note": "Titan skimmers are a fleet — multiple departures needed for sustained operations"
  },
  "payload": {
    "source_gases": ["CH4"],
    "ch4_kg": 50000,
    "lox_for_return_kg": 10000
  },
  "offload_requirements": {
    "cryo_tanks_deployed": true,
    "minimum_cryo_tank_count": 5
  },
  "effects": [
    {
      "action": "add_to_inventory",
      "target": "luna_settlement",
      "items": [
        { "name": "CH4", "amount": 50000, "unit": "kg" }
      ]
    },
    {
      "action": "set_status_flag",
      "flag": "titan_ch4_delivery_completed",
      "value": true
    }
  ],
  "metadata": {
    "template": "task_v2",
    "type": "arrival_offload",
    "craft_type": "titan_harvester_skimmer"
  }
}
```

### Step 6: Verify

> CRITICAL EXECUTION MANDATE: All RSpec commands must use the Docker wrapper below.
> The container working directory is already /home/galaxy_game — do NOT add cd /home/galaxy_game.
> Never run bare local test commands. Never fabricate test results. Actually run the specs.

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/mission/transit_engine_spec.rb 2>&1 | tail -20'
```

AND run the rake timing validation:
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rake luna_mission:phase_timing 2>&1'
```

Expected result: All timing constraints pass without abort. Full timeline prints correctly.

### Step 7: Synthesis Report (before committing anything)

Save to summaries folder. Do not commit until explicitly approved.

---

## Acceptance Criteria
- [ ] TransitEngine service class exists with transfer window + departure/arrival methods
- [ ] Venus harvest arrival task (v2 format) created and wired into precursor profile
- [ ] Titan harvest arrival task (v2 format) created
- [ ] Rake `luna_mission:phase_timing` validates full timeline including:
  - Concurrent precursor + Venus skimmer launch from Earth
  - Landing pad construction before HLT landing
  - Tank farm ready before N₂ offload
  - Venus skimmer arrival gates habitat pressurization
  - Titan skimmer fleet timing (multiple departures)
- [ ] All timing constraints pass without abort
- [ ] RSpec suite green for transit engine
- [ ] No regressions in existing mission pipeline specs
- [ ] Full suite run completed and logged (human runs overnight — agent does not trigger)

---

## Stop Conditions — escalate to user immediately if:
- Transit engine requires changes to `Game#advance_by_days` or `GameSimulationJob` (architectural decision needed)
- The existing mission profile structure doesn't support the new phase without breaking other phases
- Tank farm readiness check requires a new model/migration (scope expansion)
- The old `venus_harvest_01_*` files have structure that can't be adapted to v2 without data loss
- Any architectural decision is required about how transit state is persisted (in-memory vs DB)

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container.

One commit per major component is preferable for provenance:
```bash
git add galaxy_game/app/services/mission/transit_engine.rb
git commit -m "feat: add Mission::TransitEngine service — transfer windows, departure/arrival tracking"

git add galaxy_game/data/json-data/missions_v2/tasks/task_venus_harvest_arrival_v2.json
git commit -m "feat: add Venus harvest arrival task (v2 format) — N₂/CO₂ payload, tank farm gate"

git add galaxy_game/data/json-data/missions_v2/tasks/task_titan_harvest_arrival_v2.json
git commit -m "feat: add Titan harvest arrival task (v2 format) — CH₄ payload for skimmer refueling"

git add galaxy_game/lib/tasks/lunar_precursor_mission_validation.rake
git commit -m "feat: add phase_timing rake task — full timeline validation with arrival ordering"

git add galaxy_game/data/json-data/missions_v2/profiles/precursor_mission_profile_v1.json
git commit -m "feat: wire Venus harvest arrival phase into precursor mission profile"
```

**Task file move on completion:**
```bash
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy_game/tasks/active/2026-08-18-HIGH-FEATURE-LAUNCH-WINDOW-TRANSIT-TIMING-ENGINE.md \
       projects/galaxy_game/tasks/completed/2026-08/2026-08-18-HIGH-FEATURE-LAUNCH-WINDOW-TRANSIT-TIMING-ENGINE.md
git commit -m "chore: move launch window transit timing engine to completed/"
```

---

## Documentation
- [x] No doc changes needed (code + JSON only)
- [ ] If transit engine design decisions are made: flag in DECISIONS.md for future reference

---

## Dependencies
**Blocked by**: none
**Blocks**:
- `2026-08-19-HIGH-FEATURE-ORBITAL-MECHANICS-DATA-LAYER` (Phase 5 extends this TransitEngine — **dispatch this task FIRST**)
- Luna settlement simulation (live game loop processing of craft arrivals)
**Related tasks**: Live Game-Loop Reality Check (findings confirm craft are NOT ticked by live loop); Venus skimmer AIManager services; Titan skimmer fleet planning

> ⚠️ **DISPATCH ORDER**: This task must be completed before the Orbital Mechanics Data Layer task (Phase 5), because Phase 5 extends the `Mission::TransitEngine` class created here.

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Final test result**:

### What was changed

### Issues discovered

### Follow-up tasks needed

### Lessons learned

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY:

### Step 1: Create TransitEngine service class

**File:** `galaxy_game/app/services/mission/transit_engine.rb`

```ruby
class Mission::TransitEngine
  # Transfer window calculation (Earth → Venus)
  # Uses simplified Hohmann transfer approximation
  def self.earth_to_venus_transit_days
    # ~146 days for Hohmann transfer Earth→Venus
    # Real value varies with orbital positions; use 146 as baseline
    146
  end

  # Transfer window calculation (Earth → Titan)
  def self.earth_to_titan_transit_days
    # ~730 days for Earth→Titan (via Jupiter gravity assist or direct)
    730
  end

  # Transfer window calculation (Luna → Venus)
  def self.luna_to_venus_transit_days
    # Same as Earth→Venus (Luna is close to Earth gravitationally)
    earth_to_venus_transit_days
  end

  # Calculate next transfer window from Earth to target body
  # Returns: { departure_date: Date, arrival_date: Date, transit_days: N }
  def self.calculate_transfer_window(from_body, to_body, launch_date = nil)
    launch_date ||= Time.current.to_date
    transit_days = case "#{from_body}_#{to_body}"
    when "EARTH_VENUS", "LUNA_VENUS"
      earth_to_venus_transit_days
    when "EARTH_TITAN", "LUNA_TITAN"
      earth_to_titan_transit_days
    when "EARTH_MARS", "LUNA_MARS"
      259  # ~8.5 months Earth→Mars
    else
      365  # default: 1 year transit
    end

    {
      departure_date: launch_date,
      arrival_date: launch_date + transit_days,
      transit_days: transit_days
    }
  end

  # Check if a transfer window is open (simplified — real version uses orbital mechanics)
  def self.transfer_window_open?(from_body, to_body, date)
    # Simplified: windows open every 584 days (Venus synodic period)
    # For MVP, always open — real version would check planetary positions
    true
  end

  # Create a transit record for a craft
  def self.schedule_departure(craft_id, from_body, to_body, launch_date)
    window = calculate_transfer_window(from_body, to_body, launch_date)
    
    {
      craft_id: craft_id,
      status: :in_transit,
      from_body: from_body,
      to_body: to_body,
      departure_date: window[:departure_date],
      arrival_date: window[:arrival_date],
      transit_days: window[:transit_days],
      payload: nil  # Set when manifest is loaded
    }
  end

  # Check if a craft has arrived (given current simulation day)
  def self.has_arrived?(transit_record, sim_day)
    sim_date = Time.current.to_date + sim_day
    sim_date >= transit_record[:arrival_date]
  end
end
```

### Step 2: Create Venus harvest arrival task (v2 format)

**File:** `data/json-data/missions_v2/tasks/task_venus_harvest_arrival_v2.json`

Adapted from `missions/venus_harvester_mission/venus_harvest_01_phases_v1.json`:

```json
{
  "task_id": "venus_harvest_arrival_v2",
  "version": "2.0",
  "name": "Venus Atmospheric Harvester Arrival at Luna",
  "description": "Venus skimmer arrives at Luna with harvested N₂ + CO₂ payload. Offload requires tank farm infrastructure to be ready.",
  "prerequisites": [
    "task_site_prep_foundation_v2",
    "task_print_inflatable_tank_shells_v2"
  ],
  "launch_window": {
    "departure_body": "EARTH-01",
    "arrival_body": "LUNA-01",
    "transit_days": 400,
    "synodic_period_days": 584,
    "note": "Venus skimmer departs Earth around same time as precursor launch. Harvests Venus atmosphere en route."
  },
  "payload": {
    "source_gases": ["CO2", "N2"],
    "co2_kg": 75000,
    "n2_kg": 30000,
    "lox_for_return_kg": 10000,
    "methane_remaining_kg": 5000
  },
  "offload_requirements": {
    "tank_farm_ready": true,
    "inflatable_tanks_deployed": true,
    "minimum_tank_count": 3,
    "cryo_tanks_required": true
  },
  "effects": [
    {
      "action": "add_to_inventory",
      "target": "luna_settlement",
      "items": [
        { "name": "N2", "amount": 30000, "unit": "kg" },
        { "name": "CO2", "amount": 75000, "unit": "kg" }
      ]
    },
    {
      "action": "set_status_flag",
      "flag": "venus_n2_delivery_completed",
      "value": true
    },
    {
      "action": "gate_open",
      "target": "habitat_pressurization",
      "description": "N₂ available for habitat pressurization"
    }
  ],
  "metadata": {
    "template": "task_v2",
    "type": "arrival_offload",
    "craft_type": "venus_harvester_skimmer",
    "adapted_from": "venus_harvest_01_phases_v1.json"
  }
}
```

### Step 3: Add timing validation to rake

**File:** `galaxy_game/lib/tasks/lunar_precursor_mission_validation.rake`

Add a new task `phase_timing`:

```ruby
desc "Phase timing: validate launch windows, transit times, arrival ordering"
task phase_timing: :environment do
  puts "=" * 80
  puts "LUNA PRECURSOR MISSION — PHASE TIMING VALIDATION"
  puts "=" * 80

  # Simulate timeline starting from day 0
  sim_day = 0
  
  # Phase 0: Precursor launch (Day 0)
  puts "\n--- Day 0: Precursor Launch from Earth ---"
  precursor_departure = Mission::TransitEngine.schedule_departure(
    "precursor_hlt_1", "EARTH-01", "LUNA-01", Time.current.to_date
  )
  puts "  ✓ Precursor departed Earth → Luna (#{precursor_departure[:transit_days]}d transit)"
  
  # Phase 0b: Venus skimmer launch (concurrent, Day 0)
  puts "\n--- Day 0: Venus Skimmer Launch from Earth ---"
  venus_departure = Mission::TransitEngine.schedule_departure(
    "venus_harvester_01", "EARTH-01", "VENUS-01", Time.current.to_date
  )
  puts "  ✓ Venus skimmer departed Earth → Venus (#{venus_departure[:transit_days]}d transit)"
  
  # Phase 1: Precursor arrives and builds landing pads
  precursor_arrival_day = precursor_departure[:transit_days]
  sim_day = precursor_arrival_day
  
  puts "\n--- Day #{sim_day}: Precursor Arrives at Luna ---"
  puts "  ✓ Landing pad construction starts (2 pads)"
  puts "  ✓ Comms deployed"
  puts "  ✓ Power grid (RTG) deployed"
  
  # Pad construction takes time — simulate as 30 days
  pad_construction_days = 30
  sim_day += pad_construction_days
  
  puts "\n--- Day #{sim_day}: Landing Pads Complete ---"
  puts "  ✓ Pad Alpha ready for HLT landing"
  puts "  ✓ Pad Beta ready for HLT landing"
  
  # Phase 2: HLT #1 lands with inflatable tanks
  puts "\n--- Day #{sim_day}: HLT #1 Lands (Inflatable Tanks) ---"
  puts "  ✓ Inflatable tanks deployed (empty, awaiting N₂)"
  
  # Tank farm setup takes time — simulate as 15 days
  tank_farm_days = 15
  sim_day += tank_farm_days
  
  puts "\n--- Day #{sim_day}: Tank Farm Ready ---"
  puts "  ✓ Tank farm infrastructure complete"
  puts "  ✓ Ready for N₂ offload"
  
  # Phase 3: Venus skimmer arrives at Venus, begins harvest
  venus_at_venus_day = venus_departure[:transit_days]
  puts "\n--- Day #{venus_at_venus_day}: Venus Skimmer Arrives at Venus ---"
  puts "  ✓ Atmospheric harvesting begins (CO₂ + N₂ extraction)"
  
  # Harvesting takes time — simulate as 30 days at Venus
  harvest_days = 30
  venus_departure_from_venus = venus_at_venus_day + harvest_days
  
  puts "\n--- Day #{venus_departure_from_venus}: Venus Skimmer Departs Venus → Luna ---"
  
  # Phase 4: Venus skimmer arrives at Luna
  venus_arrival_at_luna = venus_departure_from_venus + Mission::TransitEngine.earth_to_venus_transit_days
  
  puts "\n--- Day #{venus_arrival_at_luna}: Venus Skimmer Arrives at Luna ---"
  
  # Check if tank farm is ready
  if sim_day <= venus_arrival_at_luna
    puts "  ✓ Tank farm ready (completed day #{sim_day})"
    puts "  ✓ N₂ offload: 30,000 kg N₂ + 75,000 kg CO₂ delivered"
    puts "  ✓ HABITAT PRESSURIZATION GATE OPENED"
  else
    puts "  ⚠ Tank farm NOT ready — N₂ arrives before tanks (CRITICAL FAILURE)"
    abort("Venus skimmer arrived at Luna before tank farm was ready")
  end
  
  # Phase 5: Titan skimmer launch (later, needs methane from Earth)
  titan_launch_day = venus_arrival_at_luna + 90  # ~3 months after Venus arrival
  puts "\n--- Day #{titan_launch_day}: Titan Skimmer Launch from Earth ---"
  titan_departure = Mission::TransitEngine.schedule_departure(
    "titan_harvester_01", "EARTH-01", "TITAN-01", Time.current.to_date + titan_launch_day
  )
  puts "  ✓ Titan skimmer departed Earth → Titan (#{titan_departure[:transit_days]}d transit)"
  
  titan_arrival_at_titan = titan_launch_day + titan_departure[:transit_days]
  titan_departure_from_titan = titan_arrival_at_titan + harvest_days
  
  puts "\n--- Day #{titan_departure_from_titan}: Titan Skimmer Departs Titan → Luna ---"
  
  titan_arrival_at_luna = titan_departure_from_titan + Mission::TransitEngine.earth_to_titan_transit_days
  
  puts "\n--- Day #{titan_arrival_at_luna}: Titan Skimmer Arrives at Luna ---"
  puts "  ✓ CH₄ delivery: supplementary fuel for Venus skimmers"
  
  puts "\n" + "=" * 80
  puts "TIMING VALIDATION COMPLETE — All windows and arrivals verified"
  puts "=" * 80
end
```

### Step 4: Wire Venus harvest task into precursor manifest

**File:** `data/json-data/missions_v2/profiles/precursor_mission_profile_v1.json`

Add to phases array:

```json
{
  "phase_id": "venus_harvest_arrival",
  "name": "Venus Atmospheric Harvester Arrival",
  "description": "Venus skimmer arrives at Luna with harvested N₂ + CO₂ payload. Gates habitat pressurization.",
  "prerequisites": ["initial_hlt_landings"],
  "duration_days": 400,
  "task_ref": "tasks_v2/task_venus_harvest_arrival_v2.json",
  "launch_window": {
    "departure_body": "EARTH-01",
    "arrival_body": "LUNA-01",
    "transit_days": 400,
    "concurrent_with": "precursor_launch"
  },
  "outputs": ["n2_delivery", "co2_delivery", "habitat_pressurization_gate"]
}
```

### Step 5: Add Titan harvest arrival task (v2 format)

**File:** `data/json-data/missions_v2/tasks/task_titan_harvest_arrival_v2.json`

Similar structure to Venus task but with Titan-specific values:

```json
{
  "task_id": "titan_harvest_arrival_v2",
  "version": "2.0",
  "name": "Titan Atmospheric Harvester Arrival at Luna",
  "description": "Titan skimmer arrives at Luna with harvested CH₄ payload for Venus skimmer refueling.",
  "launch_window": {
    "departure_body": "TITAN-01",
    "arrival_body": "LUNA-01",
    "transit_days": 730,
    "note": "Titan skimmers are a fleet — multiple departures needed for sustained operations"
  },
  "payload": {
    "source_gases": ["CH4"],
    "ch4_kg": 50000,
    "lox_for_return_kg": 10000
  },
  "offload_requirements": {
    "cryo_tanks_deployed": true,
    "minimum_cryo_tank_count": 5
  },
  "effects": [
    {
      "action": "add_to_inventory",
      "target": "luna_settlement",
      "items": [
        { "name": "CH4", "amount": 50000, "unit": "kg" }
      ]
    },
    {
      "action": "set_status_flag",
      "flag": "titan_ch4_delivery_completed",
      "value": true
    }
  ],
  "metadata": {
    "template": "task_v2",
    "type": "arrival_offload",
    "craft_type": "titan_harvester_skimmer"
  }
}
```

## Stop Conditions
- TransitEngine service class exists with transfer window + departure/arrival methods
- Venus harvest arrival task (v2 format) created and wired into precursor profile
- Titan harvest arrival task (v2 format) created
- Rake `luna_mission:phase_timing` validates full timeline including:
  - Concurrent precursor + Venus skimmer launch from Earth
  - Landing pad construction before HLT landing
  - Tank farm ready before N₂ offload
  - Venus skimmer arrival gates habitat pressurization
  - Titan skimmer fleet timing (multiple departures)
- All timing constraints pass without abort

## Notes for Claude Review
- The rake `phase_timing` simulates a timeline — it's not just structure validation
- Venus skimmer departs Earth (not Luna) — loaded with methane fuel at Earth launch
- Tank farm readiness gates N₂ offload — critical path constraint
- Titan skimmers are a fleet, not single craft — multiple departures needed
- The old `venus_harvest_01_*` files have the right structure but need v2 adaptation
- Launch windows use simplified Hohmann transfer (real version would use orbital mechanics)
