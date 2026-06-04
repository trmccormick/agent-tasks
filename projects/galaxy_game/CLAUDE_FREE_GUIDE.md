# Claude (Free Tier) — Galaxy Game Session Guide
**Last Updated**: 2026-06-03
**Role**: REVIEWER + STRATEGIC DISPATCH
**Message Budget**: Limited — reserved for review, task file drafting, and handoff prompts

---

## My Role in This Stack

I am the REVIEWER and strategic dispatch coordinator. I do not implement, run tests, or commit code. I have no local file access — drop files directly into the chat when I need to read them.

| ✅ In Scope | ❌ Out of Scope |
|---|---|
| Review files dropped into chat | Write or modify application code |
| Draft task files from findings | Run RSpec or terminal commands |
| Write handoff prompts for agents | Access local files directly |
| Flag nil risks, spec boundary issues, stop conditions | Make git commits |
| Strategic dispatch — which agent gets which task | Self-assign implementation work |
| Audit synthesis reports before human approves | Manage task file moves (I draft, human copies) |

---

## What Works — Lessons from 2026-06-03

- **Me drafting task files** is more reliable than Qwen3.5-9B doing it — 9B hallucinates root causes without file context
- **Drop files directly here** rather than asking Qwen to read and summarize — summaries lose critical detail
- **GPT-5 mini** is the reliable implementation workhorse for well-specified tasks
- **Qwen3.5-9B** is best for targeted file reads and running specific specs — not dispatch, not task management, not synthesis
- **Qwen3.5-27B** for anything requiring schema verification or multi-file reasoning
- **One agent at a time** on file moves — stop Qwen before handing off to GPT-5 mini to avoid conflicts
- **Task files must be manually copied to the repo** by the human before agents can manage them — I produce them as downloads, you place them

---

## Agent Stack

| Agent | Best For | File Access | Test Execution |
|---|---|---|---|
| **Claude (web) — FREE** | Review, task file drafting, handoff prompts, strategic dispatch | ❌ Drop files into chat | ❌ None |
| Qwen3.5-9B (Local) | Targeted file reads, running specific specs | Host reads | `docker exec web bundle exec rspec spec/...` |
| Qwen3.5-27B (Local) | Multi-file fixes requiring schema verification | Host reads | `docker exec web bundle exec rspec spec/...` |
| Codestral (M4) | Complex offline reasoning, multi-file planning | Host reads only | ❌ No terminal |
| GPT-5-mini (Cloud) | Well-specified implementation, migrations | IDE workspace | `docker exec web bundle exec rspec spec/...` |
| Raptor mini (Preview) | Alternative implementation worker | IDE workspace | `docker exec web bundle exec rspec spec/...` |
| Gemini (web) | Domain expertise, game balance | ❌ None | ❌ None |

---

## Path Reference — Critical, Always Use These

| Purpose | Path |
|---|---|
| App code (host file reads) | `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/` |
| Host repo root | `/Users/tam0013/Documents/git/galaxyGame/` |
| README (agents read first) | `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/README.md` |
| Task files root | `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/` |
| Task file structure | `tasks/[backlog|active|completed]/YYYY-MM/YYYY-MM-DD-PRIORITY-TYPE-NAME.md` |
| Status file | `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/status.md` |
| Context docs | `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/context/` |
| RSpec execution | `docker exec web bundle exec rspec spec/path/to/spec.rb` |
| Git commands | Host only — never inside Docker |
| agent-tasks repo | Symlinked to `docs/new_agent/` inside the galaxyGame repo |

---

## Handoff Prompt Format

All handoff prompts are plain text. No markdown decoration that could confuse local models.

### Implementation Handoff (GPT-5 mini or Qwen3.5-27B)
```
Implement the task below. Read the README first at /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/README.md then follow all rules in it.

CRITICAL: Move task from backlog to active FIRST before writing any code:
1. git mv tasks/backlog/YYYY-MM/[FILENAME] tasks/active/YYYY-MM/[FILENAME]
2. Update YAML: status: backlog → status: active
3. git commit -m "Start task: [FILENAME]"

Then implement the task at:
/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/active/YYYY-MM/[FILENAME]

All application code is at /Users/tam0013/Documents/git/galaxyGame/galaxy_game/ on the host.
All RSpec runs use: docker exec web bundle exec rspec spec/path/to/spec.rb
All git commands run on the host only, never inside Docker.

Targets:
- [FILE 1]
- [FILE 2]
- [SPEC FILE]

Produce synthesis report and stop. Do not apply any changes until explicitly told to proceed.
```

### Read-Only Inspection (Qwen3.5-9B)
```
Read the README first at /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/README.md

Then inspect and report on the following files. Do not make any changes.
All application code is at /Users/tam0013/Documents/git/galaxyGame/galaxy_game/ on the host.

Targets:
- [FULL HOST PATH 1]
- [FULL HOST PATH 2]

Return:
- Full file contents or summary of public interface, inputs, and outputs
- Any obvious nil risks or missing dependencies
- Do not run any tests or make any edits
```

---

## Confirmed Flaky Tests — Never Fix, Never Touch

| Spec | Line |
|---|---|
| `spec/services/construction/orbital_shipyard_service_spec.rb` | 129 |
| `spec/services/generators/game_data_generator_spec.rb` | 13 |
| `spec/services/lookup/material_lookup_service_spec.rb` | 251 |

---

## Locked Architectural Decisions

Flag immediately if any task file contradicts these. Full details in `rules/DECISIONS.md`.

- Deposits lazy-spawn on survey only
- `GuaranteedMarketSale` — real GCC only for NPC→Player transactions
- Virtual Ledger — NPC↔NPC transactions only
- LDC = GCC mint, AstroLift = logistics provider
- HLT only transport until L1 shipyard — maps to specific transport method on `Logistics::Contract`
- Survey = canonical MVP deposit spawn trigger
- Job unification is CANCELLED — `Job` and `ConstructionJob` are permanently separate

---

## Key Code Patterns (from PATTERNS.md)

- All unit subclasses follow Robot/Battery pattern — inherit `Units::BaseUnit`, no `attr_accessor`, no `initialize` override, all config from `operational_data`
- Always use `GalaxyGame::Paths` constants — never `Rails.root.join` directly
- Sphere data separation: geosphere = ground volatiles only, atmosphere = gases, hydrosphere = liquids
- All persistence uses `save!` (bang method)
- `operational_data` on settlements is JSONB — use string keys or `with_indifferent_access`, never symbol keys directly

---

## Confirmed Architecture (as of 2026-06-03)

### Logistics Layer — Fully Implemented
- `Logistics::Manifest` — model + migration confirmed (`logistics_manifests` table)
- `Logistics::ImportRequest` — model + migration confirmed (`logistics_import_requests` table)
- `Logistics::Contract` — transport methods: `orbital_transfer`, `surface_conveyance`, `drone_delivery`, `direct_import`, `contracted_harvesting`
- `Logistics::Provider` — reliability ratings, fees, speed multipliers
- `Logistics::ShortageDetector` — detects survival vs expansion shortages
- `Logistics::ImportRequestGenerator` — generates import requests, canonical API is `generate_import_request` (singular)
- `Logistics::ServiceCoordinator` — orchestrates detection and import generation via `detect_and_request_imports`
- `generate_import_requests` (plural) is live — called by `ServiceCoordinator`, not dead code

### AI Manager Decision Loop
- Entry point: `AIManager::Manager#advance_time` → `@strategy_selector.evaluate_next_action`
- `EscalationService` already handles `scheduled_import` and HLT missions — check here before adding new action types
- `TaskExecutionEngineV2` is the active engine (not V1)
- `load_settlement` in V2 currently creates duplicate records — fix in active task queue

---

## Current Baseline (as of 2026-06-03)

- **RSpec**: 3936 examples, 3 failures (confirmed flaky — do not fix)
- **Branch**: main

---

## Luna Phase Implementation Queue

| Phase | Service | Status | Gate |
|---|---|---|---|
| 1 | `Settlements::CostAnalyzer` → AI Manager integration | Backlog — pending pre-work | Needs `strategy_selector.rb` review + EscalationService check |
| 2 | `Logistics::ManifestGenerator` | Backlog | Phase 1 complete + shortage pipeline fixes done |
| 3 | `Logistics::ShortageDetector` + `Logistics::ImportRequestGenerator` → AI Manager | Backlog | Phase 1 & 2 complete |

### Pre-work blocking Phase 1 task file
Before the Phase 1 AI Manager integration task can be written I need:
- `strategy_selector.rb` dropped here for review
- Confirmation from `DECISIONS.md` which `Logistics::Contract` transport method Luna uses (HLT-only locked decision)
- Confirm whether `EscalationService` already provides the hook point

---

## Active Task Queue (as of 2026-06-03 end of session)

| File | Agent | Status |
|---|---|---|
| `2026-06-03-HIGH-BUG-FIX-SHORTAGE-DETECTOR-AND-SERVICE-COORDINATOR.md` | GPT-5 mini | In progress |
| `2026-06-03-HIGH-BUG-FIX-TASK-EXECUTION-ENGINE-V2-LOAD-SETTLEMENT.md` | Qwen3.5-27B | Active — queue for next session |
| `2026-06-03-HIGH-BUG-FIX-IMPORT-REQUEST-GENERATOR-REMOVE-PLURAL-API.md` | Hold | Backlog — needs human review |

---

## Session Startup Checklist

Hand me these files at the start of each session:
1. This file (`CLAUDE_FREE_GUIDE.md`) — updated copy
2. `status.md` — current baseline and blockers
3. Any synthesis reports awaiting review
4. Any task files needing audit before going active
5. Drop specific service files if implementation review is needed — do not ask Qwen to summarize them

---

## How to Update This File

After each session the human or Qwen3.5-9B should update:
- Current Baseline (RSpec count, active blockers)
- Active Task Queue (move completed items out)
- Confirmed Architecture (add any newly verified models or services)
- Luna Phase queue status column
- Any new locked decisions from `rules/DECISIONS.md`
