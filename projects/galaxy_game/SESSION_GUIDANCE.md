# Galaxy Game — Session Guidance

Project-specific guidance for any agent working on Galaxy Game, read at startup once the project is named. Universal rules live in `rules/GUARDRAILS.md` and `SESSION_CLOSEOUT.md`; this file does not repeat them.

Moved and adjusted from `GALAXY_GAME_CONTEXT.md` (last updated 2026-07-08). The original is preserved verbatim at `projects/galaxy_game/archive/GALAXY_GAME_CONTEXT-2026-07-08.md`, including the sections not carried over (dated state, machine and model setup, tool-use troubleshooting).

Evidence label: everything below is **REPORTED** (carried over from the original, from the former `CLAUDE_SESSION_START.md` for the path-mapping table, or from the former `GROK_SESSION_START.md` for section 8; both former docs are in git history) unless an agent re-checks it and says so. Where this file and the live repo disagree, the live repo wins; report the difference.

---

## 1. Startup

1. Do the universal startup in your session-start doc.
2. Read this file, then `projects/galaxy_game/README.md`, `projects/galaxy_game/status.md`, and the latest handoff in `projects/galaxy_game/handoffs/`.
3. **Test log check.**
   - Find the latest completed full-suite test log. The usual host location is `<galaxyGame>/data/logs/` (`log/` inside the container; see section 2).
   - If the logs are not there, read the Docker Compose configuration and volume mappings to find them. Do not ask first.
   - Separate completed, interrupted and running logs.
   - Report the latest completed run's date and results as historical evidence. If you ran a fresh full suite this session, report that separately as current evidence.
   - Ask whether a fresh full-suite run is wanted. Never start one automatically, and never run tests while another RSpec process is running.

---

## 2. Paths

`<galaxyGame>` is the root of the Galaxy Game code repo and `<agent-tasks>` is the root of this repo, wherever each is checked out on the machine you are on. Never use another machine's absolute path.

| Purpose | Path |
|---|---|
| App code | `<galaxyGame>/galaxy_game/` |
| JSON data root | `<galaxyGame>/data/json-data/` (at the repo root, not inside `galaxy_game/`) |
| V2 mission files | `<galaxyGame>/data/json-data/missions_v2/` (gitignored; local and Time Machine only) |
| Task files | `<agent-tasks>/projects/galaxy_game/tasks/` (`backlog/`, `active/`, `completed/`) |
| Status | `<agent-tasks>/projects/galaxy_game/status.md` |
| Context docs | `<agent-tasks>/projects/galaxy_game/context/` (including `PATTERNS.md`) |
| Handoffs | `<agent-tasks>/projects/galaxy_game/handoffs/` |
| Docker commands | `<agent-tasks>/projects/galaxy_game/DOCKER_COMMAND_GUIDE.md` |
| RSpec | `docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [SPEC_PATH] 2>&1 \| tail -30'` |
| Git | On the host only, never inside Docker |

**Symlink:** `docs/new_agent/` inside the `<galaxyGame>` repo is a symlink to `<agent-tasks>`. For git operations always use the real `<agent-tasks>` path, never the symlink. Agents get confused by it and create stray duplicate files.

**Host path to container path** (from `docker-compose.dev.yml`; container working directory `/home/galaxy_game`):

| Host (`<galaxyGame>/`) | Container (`web`) |
|---|---|
| `data/json-data/` | `app/data/` |
| `data/maps/` | `app/data/maps/` |
| `data/tilesets/` | `app/data/tilesets/` |
| `data/geotiff/` | `app/data/geotiff/` |
| `data/images/` | `app/data/images/` |
| `data/logs/` | `log/` |

`data/json-data/` and `galaxy_game/app/` are both at the repo root, not nested in each other. `galaxy_game/data/json-data/` and `galaxy_game/app/data/` do not exist on the host, so a search there always comes back empty. Search from the repo root.

---

## 3. Critical rules

- **JSON data is gitignored.** Everything under `data/json-data/` is local only; Time Machine is the backup.
- **Nothing under `data/` goes through git, in any form.** No `git add -f`, no `git mv`, no `.gitignore` edits. Use plain `mv`, `cp` or `rm`. See GUARDRAILS Rule 28; host and container path discipline is Rule 10.
- **Use `GalaxyGame::Paths` constants.** Never `Rails.root.join` directly.
- **Chemical formulas in the backend.** Ruby code and JSON data use O2, H2O, CH4, CO2, N2, Ar, CO. Full English names are display-layer only. Flag any violation immediately.
- **Mk1 convention.** All initial blueprint versions are Mk1: blueprint `"id"` fields carry the `_mk1` suffix unless a specific version is named. Deploy lookup normalizes unit names the same way: `unit_name.to_s.downcase.gsub(/[^a-z0-9]+/, "_").gsub(/^_+|_+$/, "")` turns "Comms Equipment Mk1" into `comms_equipment_mk1`.
- **`git mv` only for task files**, never `cp` or plain `mv` (GUARDRAILS Rule 12).
- **No commit without explicit human approval** (GUARDRAILS Rule 26). Show the diff and wait.

---

## 4. Confirmed flaky tests

Never fix them and never touch them.

| Spec | Line |
|---|---|
| `spec/services/generators/game_data_generator_spec.rb` | 13 |
| `spec/services/lookup/material_lookup_service_spec.rb` | 251 |
| `spec/services/star_sim/procedural_generator_spec.rb` | 144 |

Lines are as of 2026-07-08. `status.md` is authoritative: check it before relying on this list, and do not add a spec here without updating `status.md` first.

---

## 5. Locked architectural decisions

This is a quick-flag copy as of 2026-07-08. `rules/DECISIONS.md` is authoritative. If a task file contradicts either one, flag it immediately; if this list and `rules/DECISIONS.md` disagree, report that rather than choosing one.

- Deposits lazy-spawn on survey only. Survey is the canonical MVP deposit spawn trigger.
- `GuaranteedMarketSale`: real GCC only, for NPC-to-player transactions. The Virtual Ledger is for NPC-to-NPC transactions only.
- LDC is the GCC mint and currency anchor. AstroLift is the HLT fleet and cycler logistics provider.
- HLT is the only transport until an L1 shipyard exists. Luna HLT maps to `surface_conveyance` on `Logistics::Contract`.
- Job unification is cancelled. `Job` and `ConstructionJob` stay separate.
- Cape Canaveral Spaceport is the canonical Earth source settlement (seeded, owned by AstroLift).
- The LEO depot and the L1 station (central logistics hub) are planned, not implemented.
- `luna_mission.rake` is an integration test harness, not a simulation driver. It seeds Earth-sourced inventory and does not simulate skimmer missions or the GCC satellite.
- `LegacyPortAdapter` is implemented production code; no new work is needed.
- DAG dependencies are declared in `mission_plans/` only, never in phase or task files.
- `manifest` is reserved for cargo and hardware lists. Mission orchestration files use `mission_plan`.
- `missions_v2/` folder names have no `_v2` suffix.

---

## 6. V2 mission system structure

Locked 2026-07-05. Three tiers under `data/json-data/missions_v2/`:

```
missions_v2/
├── profiles/          — body-specific profiles (luna_base_profile_v2.json)
├── phases/            — phase files (*_v2.json) — NO DAG here
├── mission_plans/     — DAG orchestration (luna_precursor_mission_plan_v2.json)
├── manifests/         — cargo/hardware manifests (lunar_precursor_manifest_v2.json)
├── tasks/             — Luna task files (*_v2.json, template: task_v2.1)
├── task_index/        — all_tasks.json lookup
└── phase_registry.json — AI Manager phase lookup index (not yet created as of 2026-07-05)
```

Path constants in `game_data_paths.rb`: `MISSIONS_V2_PATH`, `MISSIONS_V2_PROFILES_PATH`, `MISSIONS_V2_PHASES_PATH`, `MISSIONS_V2_PLANS_PATH`, `MISSIONS_V2_TASKS_PATH`, `MISSIONS_V2_REGISTRY_PATH`, `MISSIONS_V2_MANIFESTS_PATH`.

---

## 7. Code patterns

Source of truth: `projects/galaxy_game/context/PATTERNS.md`. The short list carried over from the original:

- Unit subclasses follow the Robot/Battery pattern: inherit `Units::BaseUnit`, no `attr_accessor`, no `initialize` override, all configuration from `operational_data`.
- Sphere data separation: geosphere is ground volatiles only, atmosphere is gases, hydrosphere is liquids.
- All persistence uses `save!`.
- `operational_data` on settlements is JSONB: use string keys or `with_indifferent_access`, never bare symbol keys.

---

## 8. AI Manager work

Which agent does this is a preference, not a fixed assignment. AI Manager design work has usually gone to Grok, and any web agent can take it. These constraints apply to whichever agent does it.

Items 1-4 are carried over from the archived Grok start doc (2026-09-06) and have not been re-checked; check them against the latest handoff. Item 5 was stated by the human on 2026-10-09.

1. The FootholdPlanner architecture and the Super-Mars test case are done. Extend them only for an explicit new mission; do not redo them.
2. No location-keyed material sourcing (`lunar`, `martian` or `earth` blocks on JSON).
3. The acquisition follow-on extends `EscalationService` and the existing spine, after a read-only code inventory. Do not build a parallel procurement system.
4. AI Manager task files live under `tasks/backlog/ai-manager/`, outside the phase settlement folders.
5. Live game-loop wiring is in progress and is being tested with rake tasks. Check the latest handoff and `status.md` for where it stands before treating any part of it as unstarted.

---

## 9. Keeping this file current

This file holds stable guidance only. Dated state (suite counts, active queues, what was last built) belongs in `status.md` and handoffs, never here. The planning agent proposes changes to this file; the human approves them.
