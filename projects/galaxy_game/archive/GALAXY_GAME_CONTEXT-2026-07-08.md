# Galaxy Game — Project-Specific Context for Review Agents
**Last Updated**: 2026-07-08
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
| JSON data root | `data/json-data/` at galaxyGame repo root (NOT inside galaxy_game/) |
| V2 mission files | `data/json-data/missions_v2/` — gitignored, local + Time Machine only |
| RSpec execution | `docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [SPEC_PATH] 2>&1 \| tail -30'` |
| Git commands | Host only — never inside Docker |
| Agent-tasks symlink | `docs/new_agent/` inside galaxyGame repo symlinks to `/Users/tam0013/Documents/git/agent-tasks` — ALWAYS use real agent-tasks path, never the symlink |

---

## Critical Path Rules

- **JSON data is gitignored** — all files under `data/json-data/` are local only. Time Machine is the backup. Never `git add -f` JSON data files.
- **docs/new_agent/ is a symlink** — always use `/Users/tam0013/Documents/git/agent-tasks/` real path for git operations. The symlink causes agent confusion.
- **Always use `GalaxyGame::Paths` constants** — never `Rails.root.join` directly
- **Chemical formulas in backend** — Ruby code and JSON data always use O2, H2O, CH4, CO2, N2, etc. Full English names (oxygen, methane) are UI/display layer only. Flag any violation immediately.
- **All initial blueprint versions are Mk1** — blueprint `"id"` fields must include `_mk1` suffix unless a specific version is named. Deploy lookup normalizes unit names to match: "Comms Equipment Mk1" → `comms_equipment_mk1`.
- **git mv only for task files** — never `cp` or plain `mv`. GUARDRAILS Rule 12.
- **No git commit without explicit human approval** — GUARDRAILS Rule 26. Show diffs, wait for "yes, commit".

---

## Multi-Agent Hardware Setup

| Machine | Model | Role |
|---|---|---|
| M4 MacBook (primary) | qwen3.6:27b via Ollama | Implementation agent — Galaxy Game surface, WVU Libraries |
| Ryzen 7 Windows | qwen3.6:35b via Ollama (128GB RAM, GPU) | Implementation agent — heavy tasks |
| Intel MacBook | VS Code / Copilot host | No local models |
| Claude web | Planning/strategy | Review, task file authoring, architecture decisions |

Gemini: document analysis and design passes
Perplexity: research handoffs
ChatGPT free tier: image generation only

**Ollama config rule**: base tags + `/set nothink` in Modelfiles only. Never set `num_ctx` explicitly.

---

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
- LDC = GCC mint/currency anchor, AstroLift = HLT fleet + cycler logistics provider
- HLT only transport until L1 shipyard — Luna HLT maps to `surface_conveyance` on `Logistics::Contract`
- Survey = canonical MVP deposit spawn trigger
- Job unification is CANCELLED — `Job` and `ConstructionJob` are permanently separate
- Cape Canaveral Spaceport = canonical Earth source settlement (seeded, owned by AstroLift)
- LEO depot is planned future infrastructure — not yet implemented
- L1 station = central logistics hub — planned, not yet implemented
- **Chemical formulas in backend**: O2, H2O, CH4, CO2, N2, Ar, CO — never full English names in Ruby/JSON
- **Mk1 convention**: all initial blueprint versions are Mk1 unless specified
- **luna_mission.rake is an integration test harness** — not a simulation driver. Seeds Earth-sourced inventory. Does not simulate skimmer missions or GCC sat.
- **LegacyPortAdapter**: already implemented production code — no new work needed
- **DAG dependencies**: declared in `mission_plans/` only, never in phase or task files
- **`manifest` naming**: reserved for cargo/hardware lists. Mission orchestration files use `mission_plan`.
- **missions_v2/ folder names**: clean (no `_v2` suffix on folder names)
- **`"manifest"` term**: reserved for cargo/hardware. Orchestration = `mission_plan`.

---

## V2 Mission System Architecture (Locked — 2026-07-05)

Three-tier structure under `data/json-data/missions_v2/`:

```
missions_v2/
├── profiles/          — body-specific profiles (luna_base_profile_v2.json)
├── phases/            — phase files (*_v2.json) — NO DAG here
├── mission_plans/     — DAG orchestration (luna_precursor_mission_plan_v2.json)
├── manifests/         — cargo/hardware manifests (lunar_precursor_manifest_v2.json)
├── tasks/             — 17 Luna task files (*_v2.json, template: task_v2.1)
├── task_index/        — all_tasks.json lookup
└── phase_registry.json — AI Manager phase lookup index (not yet created)
```

**MISSIONS_V2_* path constants** in `game_data_paths.rb`:
- `MISSIONS_V2_PATH` — `data/json-data/missions_v2/`
- `MISSIONS_V2_PROFILES_PATH`, `MISSIONS_V2_PHASES_PATH`, `MISSIONS_V2_PLANS_PATH`
- `MISSIONS_V2_TASKS_PATH`, `MISSIONS_V2_REGISTRY_PATH`, `MISSIONS_V2_MANIFESTS_PATH`

**luna_mission.rake** — updated to resolve v2 paths first, legacy fallback. Manifest passed as Hash to engine to avoid MISSIONS_PATH join bug.

---

## Key Code Patterns (from PATTERNS.md)

- All unit subclasses follow Robot/Battery pattern — inherit `Units::BaseUnit`, no `attr_accessor`, no `initialize` override, all config from `operational_data`
- Always use `GalaxyGame::Paths` constants — never `Rails.root.join` directly
- Sphere data separation: geosphere = ground volatiles only, atmosphere = gases, hydrosphere = liquids
- All persistence uses `save!` (bang method)
- `operational_data` on settlements is JSONB — use string keys or `with_indifferent_access`, never symbol keys directly
- Blueprint `"id"` fields must include `_mk1` suffix for initial versions
- Unit name normalization: `unit_name.to_s.downcase.gsub(/[^a-z0-9]+/, "_").gsub(/^_+|_+$/, "")` — "Comms Equipment Mk1" → `comms_equipment_mk1`

---

## Confirmed Architecture (as of 2026-07-08)

### AI Manager Decision Loop
- Entry point: `AIManager::Manager#advance_time` → `@strategy_selector.evaluate_next_action`
- `StateAnalyzer#analyze_state` returns `cost_analysis: { viable:, cost_pressure:, recommendations: }`
- `StrategySelector` handles: `:resource_acquisition`, `:system_scouting`, `:settlement_expansion`, `:infrastructure_building`, `:cost_reduction`
- Strategic focus areas: `:resource_focus`, `:scouting_focus`, `:building_focus`, `:cost_focus`, `:balanced_approach`
- `EscalationService` entry point: `handle_expired_buy_orders` only — not in AI Manager decision loop
- `TaskExecutionEngineV2` is the active engine — `load_settlement` uses find-or-create
- `execute_cost_reduction` — Phase 2 complete, calls `ManifestGenerator.create_manifest` with Cape Canaveral as source

### CostAnalyzer
- `Settlements::CostAnalyzer.compare_costs(resource_name, settlement)` — confirmed public API
- `Settlements::CostAnalyzer.current_import_price(resource_name, settlement)` — confirmed exists
- Returns: `{ resource:, local_cost:, import_cost:, local_cheaper:, cost_delta:, recommendation:, confidence:, error: }`

### Logistics Layer — Fully Implemented
- `Logistics::Manifest`, `Logistics::ImportRequest`, `Logistics::ManifestGenerator`
- `Logistics::Contract` transport methods: `orbital_transfer`, `surface_conveyance`, `drone_delivery`, `direct_import`, `contracted_harvesting`
- `Logistics::Contract::EARTH_LUNA_TRANSIT_DAYS = 3`
- `Logistics::ShortageDetector`, `Logistics::ImportRequestGenerator`
- `Logistics::ServiceCoordinator` — orchestrates via `detect_and_request_imports`

### EscalationService — Implemented
- Robot blueprint scope: confirmed correct
- No-Magic Robot Deployment refactor still in backlog (Units::Robot.create! calls need removal)

### luna_mission.rake — Current State (2026-07-08)
- V2 path resolution implemented (profile, phase, task_ref, manifest)
- 9/13 tasks passing after Mk1 unit name fix
- PUH port schema fix in progress (6 cascading failures)
- CAR-300 unit name + manifest location fix in progress

---

## Current Baseline (as of 2026-07-08)

- **RSpec (full suite)**: 4106 examples, 2 failures (both confirmed flaky — see list above), 57 pending
- **luna_mission.rake**: 9/13 passing — PUH port schema and manifest location fixes in progress
- **Branch**: main

---

## Luna / V2 Implementation Queue

| Item | Status | Notes |
|---|---|---|
| Phase 4 Robot Logistics | ✅ Complete | 17/0 rake confirmed 2026-07-05 |
| V2 Mission System design | ✅ Complete | Canonical examples in missions_v2/ |
| MISSIONS_V2_* path constants | ✅ Complete | game_data_paths.rb |
| luna_mission.rake v2 resolution | ✅ Complete | Profile, phase, task_ref, manifest |
| Mk1 unit name alignment | ✅ Complete | 6 v2 task files updated |
| PUH port schema fix | 🔄 Active (M4 Qwen) | Blueprint id fields missing _mk1 |
| Manifest location + CAR-300 fix | 🔄 Active (Ryzen Qwen) | missions_v2/manifests/, unit names |
| Phase Registry creation | ⏳ Backlog/current | phase_registry.json lookup index |
| EscalationService No-Magic refactor | ⏳ Backlog/current | Research summary ready |
| SurfaceSuitabilityAnalyzer fixes | ⏳ Backlog/current | slope calc bug + Atmosphere schema |
| HLT Harvester Variants V2.1 | ⏳ Backlog/phase5 | Formula fixes, connection_schema |
| Ethane Engine Blueprint | ⏳ Backlog/current | Blueprint missing for existing op data |
| Hydrolox Engine | ⏳ Backlog/current | Both blueprint + op data missing |

---

## Active Task Queue (as of 2026-07-08)

| File | Agent | Machine | Status |
|---|---|---|---|
| `2026-07-08-HIGH-PUH-PORT-SCHEMA-CONNECTION-BLOCK.md` | Qwen3.6:27b | M4 | 🔄 Active — blueprint id _mk1 fix |
| `2026-07-08-HIGH-MISSIONS-V2-MANIFEST-AND-CAR300-FIX.md` | Qwen3.6:35b | Ryzen | 🔄 Active — manifest location + CAR-300 |

---

## Known Tool Use Failure Modes

| Symptom | Cause | Fix |
|---|---|---|
| New Copilot window prints JSON tool calls as text | No custom `.agent.md` file or tools not selected | Create Ollama agent file, select tools in Configure Tools panel |
| New Copilot window prints `<tool_use>` XML | Ollama model weights outdated, wrong tool call format | `ollama pull qwen3.6:27b` on M4, retry |
| "Response too long" error | Context length reset to default 4096 after Ollama update | Restore context length in Ollama menu bar app settings |
| "Response contained no choices" | Model timeout or context overflow | Reduce task complexity, restart Ollama, retry |
| ERR_EMPTY_RESPONSE | Ollama service not running or still starting up | Wait 60 seconds, check `ollama ps`, retry |
| Client/server version mismatch warning | brew installed incomplete Ollama update | Use official Mac app updater, not brew, for Ollama on M4 |
| 77%/23% CPU/GPU split on Ryzen 7 | Vulkan GPU offloading not effective for this model | Known limitation — Ryzen 7 is slow for inference |
| Agent loops on task file edits | Context overflow or str_replace failures | Paste fresh task file content, redirect with explicit instruction |
| Agent uses symlink path for git ops | docs/new_agent/ confusion | Always redirect to real agent-tasks path |

---

## Session Startup Checklist

Hand me these files at the start of each session:
1. This file (`GALAXY_GAME_CONTEXT.md`) — updated copy
2. `status.md` — with full suite results filled in after startup run
3. Previous session handoff doc
4. Any synthesis reports awaiting review
5. Any task files needing audit before going active

**Verify before dispatching any agent task**:
- Open Ollama agent window in Copilot
- Send full handoff prompt (not a simple command) as first message
- Confirm `Ran...` or `Read [file]...` UI elements appear — not printed text
- If broken: `ollama pull qwen3.6:27b` on M4, reload VS Code, retry

---

## How to Update This File

After each session update:
- Last Updated date
- Current Baseline (both suite counts + rake pass count)
- Active Task Queue
- Luna/V2 Implementation Queue
- Confirmed Architecture if new services landed
- Known Tool Use Failure Modes if new patterns discovered
- Flaky spec list — status.md is authoritative
