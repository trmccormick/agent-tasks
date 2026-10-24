---
status: drafts
resume: false
priority: HIGH
type: feature
system_domain: SERVICES / FIXTURE DATA (no UI)
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 DRAFT REVIEW CHECKLIST

- [ ] Acceptance Criteria are clear and measurable
- [ ] Task scope is well-bounded (one loader + two fixture files; no UI, no database)
- [ ] Dependencies listed and verified
- [ ] No contradictions in specification
- [ ] Review agent disposition: GO

> Drafted by a web review/planning agent. Facts are labelled VERIFIED (read from GitHub `review/qwen-ui-prototype`) or REPORTED (from a log or draft, not re-checked). Anything marked `[FILL IN]` must be confirmed by an agent that can read the local repo.

---

## 🔴 Agent Dispatch Interface (Required)

```
You are **Implementation Agent**.

Project: galaxy-game-ui
Task: <agent-tasks>/projects/galaxy-game-ui/tasks/drafts/[FILENAME].md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy-game-ui/tasks/drafts/[FILENAME].md \
         projects/galaxy-game-ui/tasks/active/[FILENAME].md
  Then open the moved file and change: status: drafts → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: drafts → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy-game-ui/tasks -name "[FILENAME].md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/galaxy-game-ui/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

**IMPORTANT: Do not modify or abbreviate the text above.**

---

# TASK: JSON Fixture Loader + Systems/Links Fixtures (T3b)

## Prerequisites — READ FIRST

1. `agent-tasks/rules/GUARDRAILS.md`
2. `agent-tasks/projects/galaxy-game-ui/README.md`

## Context

The prototype UI needs static data to build against. The decision (Tracy, 2026-10-23): a small service **like `UserCsvLoader`, but for JSON**, serving committed static files to the pages. No database, no migration, no `GameState`, no `users.metadata`.

The first fixtures are star systems and the links between them, so the UI can build **wayfinding** (which systems exist, what connects to what). Other domains (settlements, inventory, market, missions) will be separate small tasks that reuse this loader.

Facts:
- VERIFIED: `app/services/user_csv_loader.rb` exists on the branch and `db/seeds.rb` calls `UserCsvLoader.call` and reads `.created` / `.skipped`. `[FILL IN]` read it and mirror its style (`.call`, result object).
- VERIFIED: the repo root `.gitignore` ignores `/data/` and `/env/` (anchored at the repo root). Do **not** put fixtures under any `data/` folder.
- REPORTED: the main game's Eden identifier is `AOL-732356`. `[FILL IN]` confirm against the game's own system JSON.
- Only Earth has biomes at game start (Tracy). Do not add biome fields to systems.

## Problem Statement

There is no data source for the prototype UI. Without one, every page invents its own hard-coded values (the first attempt's `MockDataHelper` grew into 20+ methods and broke).

## Implementation Steps

1. **Read** `user_csv_loader.rb` and its tests/specs; report its shape (`[FILL IN]`).
2. **Create** `app/services/game_data_json_loader.rb` defining `GameDataJsonLoader` (the file name must match the constant for Zeitwerk). Behaviour:
   - `GameDataJsonLoader.call(:systems)` reads `Rails.root.join("db/mock_data/systems.json")`.
   - The name must match `/\A[a-z_]+\z/`; anything else returns an error result (no path traversal).
   - Parse with `symbolize_names: true` so views use symbol keys everywhere.
   - Return a result object with `ok?`, `data`, `error`. A missing or malformed file returns an error result; it must **not** raise into a view.
3. **Create fixtures** in `db/mock_data/`:
   - `systems.json`: top-level `"systems"` array. Fields: `id`, `name`, `kind` (`home` | `outpost` | `generated`), `identifier` (optional), `source` (`game_json` | `placeholder`). Include **Sol**, **Eden** and **three** generated placeholder systems named `Generated 001` … `003`.
   - `links.json`: top-level `"links"` array. Fields: `from`, `to` (system ids), `type` (`natural` | `artificial` | `unknown`), `source`. Link Sol↔Eden and connect each generated system to at least one other, so wayfinding has a graph with more than one hop.
   - For Sol and Eden, copy only fields that exist in the game's own JSON, if reachable (`[FILL IN]` path). Otherwise mark `"source": "placeholder"`. **Do not invent** masses, stability or lore values.
   - Use `"type": "unknown"` for any link whose type has not been confirmed by Tracy.
4. **Verify in the container** (commands below) and paste output.

## Gotchas

1. **Location**: `db/mock_data/` only — tracked and outside the ignored `data/`.
2. **No database work**: no migration, no model, no seeds change.
3. **Keys**: symbolized once in the loader; views must not call `dig('name')`.
4. **Terminology**: currency is GCC. No EVE terms (no ISK, no Jita).
5. **Run everything in the container.** `[FILL IN]` container name (`galaxy_game` per task drafts; GUARDRAILS is reported to say `web`).
6. **Out of scope**: controllers, views, CSS, routes, `db/seeds.rb`, auth.

## Acceptance Criteria

- [ ] `GameDataJsonLoader.call(:systems)` and `.call(:links)` return `ok?` true with the expected arrays
- [ ] Sol and Eden are present in `systems.json`; at least 3 generated systems
- [ ] Every link's `from`/`to` matches an existing system id
- [ ] `GameDataJsonLoader.call(:nope)` and `.call("../x")` return an error result (no exception)
- [ ] `bin/rails zeitwerk:check` passes (output pasted)
- [ ] Runner check passes: `bin/rails runner 'r = GameDataJsonLoader.call(:systems); puts r.ok?; puts r.data.map { |s| s[:id] }.inspect'`
- [ ] Fixtures are tracked by git (`git status` shows them; not ignored)
- [ ] `git diff --stat` pasted; nothing staged or committed

## Stop Conditions

- `zeitwerk:check` fails twice → stop and report the exact error.
- A fixture value would have to be invented → mark it `placeholder` or leave it out; do not guess.
- Any step needs a database change → stop and ask.

## Dependencies

**Depends on**: none (independent of the CSS task).
**Blocks**: `2026-10-23-HIGH-DASHBOARD-OVERVIEW-FROM-FIXTURES`.
