# Galaxy Game — Project-Specific Context for Review Agents
**Last Updated**: 2026-06-23
**For Role**: REVIEWER / PLANNING agents reviewing Galaxy Game work
**Generic Guide**: See `/Users/tam0013/Documents/git/agent-tasks/REVIEW_AGENT_GUIDE.md` for generic setup, dispatch workflow, and role definition

---

## Quick Start

When you're assigned to review Galaxy Game work:

1. **Read the generic guide first**: `/Users/tam0013/Documents/git/agent-tasks/REVIEW_AGENT_GUIDE.md`
2. **Then read this file** for Galaxy Game–specific context
3. **Then read**: Project README, status.md, previous session handoff (pasted in chat)
4. **Then**: Create STATUS REPORT and proceed with your assignment

---
## Path Reference (Galaxy Game Specific)

| Purpose | Path |
|---|---|
| App code (host file reads) | `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/` |
| Host repo root | `/Users/tam0013/Documents/git/galaxyGame/` |
| Project README (read first) | `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/README.md` |
| Task files root | `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/` |
| Task file structure | `tasks/[backlog|active|completed]/YYYY-MM/YYYY-MM-DD-PRIORITY-TYPE-NAME.md` |
| Status file (track progress here) | `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/status.md` |
| Context docs | `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/context/` |
| Session handoffs | `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/handoffs/` |
| RSpec execution | `docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [SPEC_PATH] 2>&1 | tail -30'` |
| Git commands | Host only — never inside Docker |
| Agent-tasks symlink | `docs/new_agent/` inside galaxyGame repo symlinks to `/Users/tam0013/Documents/git/agent-tasks` |

---

## Executor Agent Setup for Galaxy Game

When dispatching executor agents to work on Galaxy Game, provide them with:

1. Generic guide: `/Users/tam0013/Documents/git/agent-tasks/REVIEW_AGENT_GUIDE.md`
2. Project README: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/README.md`
3. Galaxy Game project guide: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md` (or in galaxyGame repo)
4. Task file: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/active/[FILENAME].md`
5. Previous session handoff (if applicable): `handoffs/session_handoff_[DATE]_[TOPIC].md`

**Example executor handoff**:
```
You are Implementation Agent for Galaxy Game.

Project: Galaxy Game (Luna Phase — AI Manager integration)
Previous session: See session_handoff_2026-06-23_LOGISTICS_PHASE_2.md (pasted below)

Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/active/YYYY-MM-DD-PRIORITY-TYPE-TASK.md

STEP 1 — Read these files in order:
1. Generic setup: /Users/tam0013/Documents/git/agent-tasks/REVIEW_AGENT_GUIDE.md
2. Project README: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/README.md
3. Task file (above)

STEP 2 — Save STATUS SYNTHESIS REPORT as MD file to summaries folder (see TASK_TEMPLATE.md for path/pattern)
[Template in task file]

STEP 3 — Wait for approval, then implement

[Previous session handoff content pasted below]
---
[handoff content]
```


## Confirmed Flaky Tests — Never Fix, Never Touch

| Spec | Line |
|---|---|
| `spec/services/generators/game_data_generator_spec.rb` | 13 |
| `spec/services/lookup/material_lookup_service_spec.rb` | 251 |
| `spec/services/star_sim/procedural_generator_spec.rb` | 144 |

status.md is authoritative — do not add specs to this list without updating status.md first.

---

## Locked Architectural Decisions

Flag immediately if any task file contradicts these. Full details in `rules/DECISIONS.md`.

- Deposits lazy-spawn on survey only
- `GuaranteedMarketSale` — real GCC only for NPC→Player transactions
- Virtual Ledger — NPC↔NPC transactions only
- LDC = GCC mint, AstroLift = logistics provider
- HLT only transport until L1 shipyard — Luna HLT maps to `surface_conveyance` on `Logistics::Contract`
- Survey = canonical MVP deposit spawn trigger
- Job unification is CANCELLED — `Job` and `ConstructionJob` are permanently separate
- Cape Canaveral Spaceport = canonical Earth source settlement (seeded, owned by AstroLift)
- LEO depot is planned future infrastructure — not yet implemented

---

## Key Code Patterns (from PATTERNS.md)

- All unit subclasses follow Robot/Battery pattern — inherit `Units::BaseUnit`, no `attr_accessor`, no `initialize` override, all config from `operational_data`
- Always use `GalaxyGame::Paths` constants — never `Rails.root.join` directly
- Sphere data separation: geosphere = ground volatiles only, atmosphere = gases, hydrosphere = liquids
- All persistence uses `save!` (bang method)
- `operational_data` on settlements is JSONB — use string keys or `with_indifferent_access`, never symbol keys directly

---

## Confirmed Architecture (as of 2026-06-06)

### AI Manager Decision Loop
- Entry point: `AIManager::Manager#advance_time` → `@strategy_selector.evaluate_next_action`
- `StateAnalyzer#analyze_state` returns `cost_analysis: { viable:, cost_pressure:, recommendations: }`
- `StrategySelector` handles: `:resource_acquisition`, `:system_scouting`, `:settlement_expansion`, `:infrastructure_building`, `:cost_reduction`
- Strategic focus areas: `:resource_focus`, `:scouting_focus`, `:building_focus`, `:cost_focus`, `:balanced_approach`
- `EscalationService` entry point: `handle_expired_buy_orders` only — not in AI Manager decision loop
- `TaskExecutionEngineV2` is the active engine — `load_settlement` uses find-or-create
- `execute_cost_reduction` — Phase 2 complete, calls `ManifestGenerator.create_manifest` with Cape Canaveral as source. Falls back to logging if no source settlement found. Phase 3 will wire real quantities from ShortageDetector.

### CostAnalyzer
- `Settlements::CostAnalyzer.compare_costs(resource_name, settlement)` — confirmed public API
- `Settlements::CostAnalyzer.current_import_price(resource_name, settlement)` — confirmed exists, used by ManifestGenerator
- Returns: `{ resource:, local_cost:, import_cost:, local_cheaper:, cost_delta:, recommendation:, confidence:, error: }`
- Raises `ResourceNotFoundError` if no blueprint exists
- Stub material costs (Iron=10, Coal=5, Oxygen=2) — TODO replace with constants (future task)

### Logistics Layer — Fully Implemented
- `Logistics::Manifest` — model + migration confirmed (`logistics_manifests` table)
- `Logistics::ImportRequest` — model + migration confirmed (`logistics_import_requests` table)
- `Logistics::ManifestGenerator.create_manifest(source_settlement, destination_settlement, items)` — confirmed public API. Items format: `[{ resource: "Steel", quantity: 100 }]`
- `Logistics::Contract` — transport methods: `orbital_transfer`, `surface_conveyance`, `drone_delivery`, `direct_import`, `contracted_harvesting`
- `Logistics::Contract::EARTH_LUNA_TRANSIT_DAYS = 3` — use in Phase 3 contract creation
- `initiated_by` on Contract is polymorphic optional — set to settlement or AI Manager when creating contracts
- `Logistics::Provider` — reliability ratings, fees, speed multipliers
- `Logistics::ShortageDetector` — detects survival vs expansion shortages
- `Logistics::ImportRequestGenerator` — canonical API is `generate_import_request` (singular)
- `Logistics::ServiceCoordinator` — orchestrates via `detect_and_request_imports`; calls `generate_import_requests` (plural) — confirmed live, do not remove without review

### Earth Supply Chain (Planned Architecture)
- Cape Canaveral Spaceport = current Earth source settlement (seeded, owned by AstroLift)
- Future: multiple Earth launch sites in JSON config with organizational ownership and capacity constraints
- LEO depot planned — LDC will supply LOX there; craft leaving Earth dock and refuel before continuing
- L1 station planned — Lagrange point waystation between Earth and Luna
- Luna → LEO return trips will sell excess fuel at depot via sell orders
- Phase 3+ should route through `Logistics::Contract` with `surface_conveyance` and `EARTH_LUNA_TRANSIT_DAYS = 3`

---

## Current Baseline (as of 2026-06-06)

- **RSpec (ai_manager suite)**: 731 examples, 0 failures, 4 pending
- **RSpec (full suite — last run)**: 3948 examples, 3 failures (all confirmed flaky)
- **ACTION at next session start**: Run full suite to confirm still clean after Phase 1 + 2 commits
  ```
  docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec 2>&1 | tail -10'
  ```
- **Branch**: main

---

## Luna Phase Implementation Queue

| Phase | Service | Status | Gate |
|---|---|---|---|
| 1 | `Settlements::CostAnalyzer` → AI Manager integration | ✅ Complete (commits cd4d6800, f5fcbbe) | — |
| 2 | `Logistics::ManifestGenerator` → AI Manager integration | ✅ Complete (commit 21b10ef0) | Phase 1 complete ✅ |
| 3 | `Logistics::ShortageDetector` + `Logistics::ImportRequestGenerator` → AI Manager | Backlog — ready to plan | Phase 1 & 2 complete ✅ |

### Phase 3 open questions (document before drafting task file)
- `default_quantity_for` returns stub value of 10 — Phase 3 should wire real quantities from ShortageDetector
- Source settlement selection hardcoded to Cape Canaveral Spaceport — Phase 3 should implement launch site selection
- LEO depot as future intermediate waypoint — note in task file, don't implement yet

---

## Active Task Queue (as of 2026-06-06 end of session)

| File | Agent | Status |
|---|---|---|
| `2026-06-03-HIGH-BUG-FIX-IMPORT-REQUEST-GENERATOR-REMOVE-PLURAL-API.md` | Hold | Needs human decision — `generate_import_requests` (plural) confirmed live in ServiceCoordinator. Reassess before any removal. |

---

## Known Tool Use Failure Modes

| Symptom | Cause | Fix |
|---|---|---|
| New Copilot window prints JSON tool calls as text | No custom `.agent.md` file or tools not selected | Create Ollama agent file, select tools in Configure Tools panel |
| New Copilot window prints `<tool_use>` XML | Ollama model weights outdated, wrong tool call format | `ollama pull qwen3.5:27b` on M4, retry |
| "Response too long" error | Context length reset to default 4096 after Ollama update | Restore context length in Ollama menu bar app settings |
| "Response contained no choices" | Model timeout or context overflow | Reduce task complexity, restart Ollama, retry |
| ERR_EMPTY_RESPONSE | Ollama service not running or still starting up | Wait 60 seconds, check `ollama ps`, retry |
| Client/server version mismatch warning | brew installed incomplete Ollama update | Use official Mac app updater, not brew, for Ollama on M4 |
| 77%/23% CPU/GPU split on Ryzen 7 | Vulkan GPU offloading not effective for this model | Known limitation — Ryzen 7 is slow for inference |

---

## Session Startup Checklist

Hand me these files at the start of each session:
1. This file (`GALAXY_GAME_CONTEXT.md`) — updated copy
2. `status.md` — with full suite results filled in after startup run
3. Any synthesis reports awaiting review
4. Any task files needing audit before going active
5. Drop specific service files if implementation review is needed

**Verify before dispatching any agent task**:
- Open Ollama agent window in Copilot
- Send full handoff prompt (not a simple command) as first message
- Confirm `Ran...` or `Read [file]...` UI elements appear — not printed text
- If broken: `ollama pull qwen3.5:27b` on M4, reload VS Code, retry

---

## How to Update This File

After each session update:
- Current Baseline (both suite counts)
- Active Task Queue
- Confirmed Architecture
- Luna Phase queue status
- What Works / lessons learned
- Known Tool Use Failure Modes if new patterns discovered
- Flaky spec list — status.md is authoritative
