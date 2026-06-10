# Claude (Free Tier) — Galaxy Game Session Guide
**Last Updated**: 2026-06-06
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

## What Works — Lessons from 2026-06-06

- **Qwen3.5-27B only** — confirmed by the agent itself. Do not switch models mid-session. Context accumulation from model switching causes token limit errors. Use 27B for everything in a single session.
- **Copilot + 27B is the preferred workflow** — restored after Ollama model re-pull. More seamless than Continue (Apply buttons work, no manual approval clicks).
- **Continue is the documented fallback** — works but has friction: manual approval on every terminal command, no Apply button for file edits in current config, manual copy-paste required.
- **Ollama model re-pull fixes tool call format issues** — if 27B starts printing XML tool calls (`<tool_use>`, `<read_file_tool>`) instead of executing, run `ollama pull qwen3.5:27b` on M4 and retry.
- **Always start Copilot 27B sessions with a full structured handoff prompt** — not a simple canary command. The overnight working session proved complex prompts trigger proper tool mode initialization.
- **Synthesis report review before approval is the key gate** — do not skip. Caught critical issues (empty stub, missing dig, sed file corruption).
- **sed is dangerous for Ruby file edits** — the Continue agent corrupted strategy_selector.rb with literal "n" characters. Always use Continue's Apply feature or manual VS Code editing for file changes.
- **Copilot tools list in agent file** — keep minimal. Expanded tools list caused premium usage jump (12% → 15%) without user requests. Stick to: `['vscode', 'execute', 'read', 'edit', 'search']`
- **Custom Copilot agent file required** — tool use in new Copilot windows requires a `.agent.md` file with explicit tool declarations. Without it, models print tool calls as text instead of executing.
- **`chat.permissions.default: bypassApprovals`** in settings.json is required for tool execution in new sessions.

---

## Agent Stack

| Agent | Best For | Interface | File Access | Test Execution |
|---|---|---|---|---|
| **Claude (web) — FREE** | Review, task file drafting, handoff prompts, strategic dispatch | claude.ai | ❌ Drop files into chat | ❌ None |
| **Qwen3.5-27B (M4)** | **Primary implementation worker** — all Galaxy Game tasks | Copilot (preferred) / Continue (fallback) | Host reads | `docker exec web bundle exec rspec spec/...` |
| Qwen3.5-9B (M4) | Fallback only if 27B unavailable | Copilot / Continue | Host reads | `docker exec web bundle exec rspec spec/...` |
| Qwen3.5-4B (Ryzen 7) | Planning/Inspector — read-only data gathering | Copilot / Continue | Host reads | ❌ Too small |
| Gemini (web) | Domain expertise, game balance | gemini.google.com | ❌ None | ❌ None |

**Cloud agents (GPT-5 mini, Raptor mini) are no longer available** — GitHub Copilot removed 0x tier agents. Do not reference them as fallback options.

---

## Hardware Topology

| System | Role | Ollama | Models | Notes |
|---|---|---|---|---|
| Intel MacBook | VS Code host only | ❌ None | — | Routes to M4 and Ryzen 7 via static IP |
| M4 Mac | Primary inference | ✅ 0.30.6 (official app) | qwen3.5:27b, qwen3.5:9b, codestral, deepseek-coder-v2:16b, qwen2.5-coder:14b, qwen2.5-coder:7b | 100% Metal GPU, fast inference |
| Ryzen 7 | Large model capacity | ✅ 0.30.6 | qwen3:8b, qwen3.5:4b, qwen2.5:7b, deepseek-r1:8b, deepseek-r1:14b, qwen2.5-coder:32b, qwen3-coder:30b, llama3.1:8b | 128GB RAM, Vulkan GPU (77% CPU / 23% GPU — slow) |

**Critical rule**: No duplicate models across M4 and Ryzen 7. Duplicates cause Copilot routing confusion on Intel Mac.

**Ollama maintenance**: M4 uses brew install (`brew upgrade ollama` to update). After any update run `ollama --version` — must show single clean version with no client/server mismatch warning. If warning appears, the brew client didn't update — run `brew upgrade ollama` again.

---

## Copilot Agent Setup (Required for Tool Use)

Tool use in new Copilot windows requires:

**1. Custom agent file** at `~/.../globalStorage/github.copilot-chat/` or workspace level:

```markdown
---
name: Ollama
description: Local Ollama executor agent for implementation tasks.
argument-hint: A task to implement, file to read, or command to run.
tools: ['vscode', 'execute', 'read', 'edit', 'search']
---

You are a local Ollama implementation agent for the Galaxy Game project.
Always read README first at /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/README.md
All application code: /Users/tam0013/Documents/git/galaxyGame/galaxy_game/
RSpec: docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [SPEC_PATH] 2>&1 | tail -30'
Git commands: host only, never inside Docker.
Produce synthesis report and stop before applying changes.
```

**2. settings.json on Intel Mac** must include:
```json
"chat.permissions.default": "bypassApprovals",
"chat.tools.global.autoApprove": true
```

**3. Model selection**: Select qwen3.5:27b from model dropdown after opening Ollama agent window.

**4. First message must be a full structured handoff prompt** — not a simple command. Complex prompts trigger proper tool mode initialization for 27B.

**Tool use verification** — before dispatching any task, confirm tool use is active:
- Working: shows `Ran ls...` or `Read [file]...` as distinct UI elements
- Broken: shows printed JSON, XML (`<tool_use>`), or text description of commands
- Fix if broken: `ollama pull qwen3.5:27b` on M4, then retry with full handoff prompt

---

## Continue Setup (Fallback)

Continue config at `~/.continue/config.yaml` on Intel Mac. Key settings:
- M4 endpoint: `http://10.6.186.161:11434`
- Ryzen 7 endpoint: `http://10.6.186.50:11434`
- All models have `capabilities: []` — tool use is handled by Continue natively, not model function calling
- Models output code in markdown blocks, Continue applies edits
- Terminal commands require manual approval per command — no auto-approve configured yet

**Continue limitations vs Copilot**:
- No Apply button for qwen3.5:27b in current config — manual copy-paste required for file edits
- Manual approval click required for every terminal command
- Do not use `sed` for file edits — caused file corruption. Use Continue's edit feature or manual VS Code editing.

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
| RSpec execution | `docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [SPEC_PATH] 2>&1 | tail -30'` |
| Git commands | Host only — never inside Docker |
| agent-tasks repo | Symlinked to `docs/new_agent/` inside the galaxyGame repo |

---

## Handoff Prompt Format

All handoff prompts are plain text. No markdown decoration.

### Copilot Implementation Handoff (Qwen3.5-27B)
```
Implement the task below. Read the README first at /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/README.md then follow all rules in it.

CRITICAL: Move task from backlog to active FIRST before writing any code:
1. git mv tasks/backlog/YYYY-MM/[FILENAME] tasks/active/YYYY-MM/[FILENAME]
2. Update YAML: status: backlog → status: active
3. git commit -m "Start task: [FILENAME]"

Task file:
/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/active/YYYY-MM/[FILENAME]

All application code is at /Users/tam0013/Documents/git/galaxyGame/galaxy_game/ on the host.
All RSpec runs use: docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [SPEC_PATH] 2>&1 | tail -30'
All git commands run on the host only, never inside Docker.

STEP 1 BEFORE ANY CODE — read these files and report findings:
- [FILE 1]
- [FILE 2]

Targets:
- [IMPLEMENTATION FILE]
- [SPEC FILE]

Produce synthesis report and stop. Do not apply changes until explicitly told to proceed.
```

### Read-Only Inspection
```
Read the README first at /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/README.md

Then read and report on:
- [FULL HOST PATH 1]
- [FULL HOST PATH 2]

Return full contents or public interface summary, inputs, outputs, nil risks.
Do not run any tests or make any edits.
```

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
1. This file (`CLAUDE_FREE_GUIDE.md`) — updated copy
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
