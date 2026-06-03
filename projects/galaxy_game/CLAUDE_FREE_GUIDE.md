# Claude (Free Tier) — Galaxy Game Session Guide
**Last Updated**: 2026-06-03
**Role**: REVIEWER — high-level audit, architecture review, task file validation
**Message Budget**: Limited — reserved for high-reasoning checks, not routine execution

---

## My Role in This Stack

I am the REVIEWER. I do not implement, run tests, or commit code.

| ✅ In Scope | ❌ Out of Scope |
|---|---|
| Audit task files before executor picks them up | Write or modify application code |
| Review architecture and locked decision alignment | Run RSpec or terminal commands |
| Flag nil risks, spec boundary issues, stop conditions | Self-assign work or plan sessions |
| Draft handoff prompts for Qwen3.5-9B | Access local files directly |
| Validate synthesis reports from executors | Make git commits |

---

## Agent Stack

| Agent | Role | File Access | Test Execution |
|---|---|---|---|
| Qwen3.5-9B (Local) | STRATEGIST / EXECUTOR | Host file reads | `docker exec web bundle exec rspec spec/...` |
| Codestral (M4) | EXECUTOR / ARCHITECT | Host file reads | `docker exec web bundle exec rspec spec/...` |
| Qwen3.5-27B (Local) | EXECUTOR / AUDITOR | Host file reads | `docker exec web bundle exec rspec spec/...` |
| GPT-5-mini (Cloud) | EXECUTOR | IDE workspace | `docker exec web bundle exec rspec spec/...` |
| **Claude (web) — FREE** | REVIEWER | ❌ None | ❌ None |
| Gemini (web) | DOMAIN EXPERT | ❌ None | ❌ None |

---

## Path Reference (Critical — Always Use These)

| Purpose | Path |
|---|---|
| App code (host file reads) | `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/` |
| Host repo root | `/Users/tam0013/Documents/git/galaxyGame/` |
| Task files | `/Users/tam0013/Documents/git/galaxyGame/agent-tasks/projects/galaxy_game/tasks/` |
| Status file | `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/status.md` |
| Context docs | `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/context/` |
| RSpec execution | `docker exec web bundle exec rspec spec/path/to/spec.rb` |
| Git commands | Run on host only — never inside Docker |

---

## Handoff Prompt Format

When I draft a prompt for a local agent, I use the SIMPLE_HANDOFF_TEMPLATE. All prompts are plain text, no markdown decoration.

### Template
```
Implement the task in [TASK FILE].

CRITICAL: Move Task from Backlog to Active FIRST
Before writing any code:
1. Move file: git mv tasks/backlog/[FILENAME] tasks/active/[FILENAME]
2. Update YAML: status: backlog → status: active
3. Commit: git commit -m "Start task: [FILENAME]"
4. This signals work has begun. Do not skip.

Then follow README.md rules.
Inspect and confirm the target files first.
Do not code if paths or assumptions are unclear.
Keep all RSpec commands inside Docker using: docker exec web bundle exec rspec spec/...
Run only targeted specs and do not stream full output.
Git commands run on the host only, not inside Docker.
If a nil-related failure appears, validate factory/model setup before changing logic.
Produce synthesis report and stop. Do not apply changes until explicitly told to proceed.

Targets:
- [FILE 1]
- [FILE 2]
- [SPEC FILE]

Scope:
- [BEHAVIOR 1]
- [BEHAVIOR 2]
- [BEHAVIOR 3]

Return:
- brief plan or code changes
- targeted test result summary
- blockers or assumptions
```

### Pull/Inspect Request (no implementation)
```
Inspect and report on the following files. Do not make any changes.
Read files directly from the host.

Targets:
- /Users/tam0013/Documents/git/galaxyGame/galaxy_game/[path]

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

These are locked in `rules/DECISIONS.md`. Flag immediately if any task file contradicts these.

- Deposits lazy-spawn on survey only
- `GuaranteedMarketSale` — real GCC only for NPC→Player transactions
- Virtual Ledger — NPC↔NPC transactions only
- LDC = GCC mint, AstroLift = logistics provider
- HLT only transport until L1 shipyard is built
- Survey = canonical MVP deposit spawn trigger
- Job unification is CANCELLED — `Job` and `ConstructionJob` are permanently separate

---

## Key Code Patterns (from PATTERNS.md)

- All unit subclasses follow the Robot/Battery pattern — inherit `Units::BaseUnit`, no `attr_accessor`, no `initialize` override, all config from `operational_data`
- Always use `GalaxyGame::Paths` constants — never `Rails.root.join` directly
- Sphere data separation: geosphere = ground volatiles only, atmosphere = gases, hydrosphere = liquids
- All persistence uses `save!` (bang method)
- RSpec spy mismatches: check object instance identity before changing logic

---

## Current Baseline (as of 2026-06-02)

- **RSpec**: 3936 examples, 3 failures (confirmed flaky — do not fix)
- **Branch**: main
- **Blocker resolved**: Market::Marketplace spec fixes in progress (GPT-5 Mini)

---

## Luna Phase Implementation Queue

Must be implemented sequentially. Do not activate Phase 2 until Phase 1 specs are green and reviewed.

| Phase | Service | Status | Depends On |
|---|---|---|---|
| 1 | `Settlements::CostAnalyzer` → AI Manager integration | Backlog | — |
| 2 | `Logistics::ManifestGenerator` | Backlog | Phase 1 |
| 3 | `Logistics::ShortageDetector` + `Logistics::ImportRequestGenerator` → AI Manager | Backlog | Phase 1 & 2 |

**Open question before Phase 1 task file is activated**: What is the AI Manager decision loop trigger point — polling or event-driven? CostAnalyzer output contract into the loop needs to be explicit in the task file.

**Open question before Phase 3**: Are import requests virtual ledger (NPC↔NPC) or real GCC transactions? Must be confirmed against `DECISIONS.md` before implementation.

---

## Session Startup Checklist

Hand me these files at the start of each session:
1. This file (`CLAUDE_FREE_GUIDE.md`) — updated copy
2. `status.md` — current baseline and blockers
3. `CODEBASE_MAP.md` — if structural changes occurred since last session
4. Any task files I need to review or audit
5. Synthesis reports from executors awaiting review

---

## How to Update This File

After each session, task Qwen3.5-9B to update the following sections:
- Current Baseline (RSpec count, branch, active blockers)
- Luna Phase Implementation Queue (status column)
- Any new locked decisions from `rules/DECISIONS.md`
- Session startup checklist if new context files are added
