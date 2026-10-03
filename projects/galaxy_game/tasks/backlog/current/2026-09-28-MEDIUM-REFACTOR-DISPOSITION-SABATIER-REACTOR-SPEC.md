---
status: backlog
priority: MEDIUM
type: refactor
system_domain: OTHER
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
created: 2026-09-28
last_updated: 2026-09-29
depends_on: []
blocks: []
notes: "Not a blind data patch. Research whether app/ still consumes pricing.lunar_production before changing material data or deleting examples. Spec is misplaced under ai_manager/."
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable
- [ ] Synthesis report template is provided (copy/paste ready)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific
- [ ] Acceptance Criteria are measurable — needs human review of disposition options
- [ ] Dependencies and Blocked/Blocks relationships are clear

**READY FOR DISPATCH**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

You are Implementation Agent.
Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-09-28-MEDIUM-REFACTOR-DISPOSITION-SABATIER-REACTOR-SPEC.md
STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
```bash
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy_game/tasks/backlog/current/2026-09-28-MEDIUM-REFACTOR-DISPOSITION-SABATIER-REACTOR-SPEC.md \
       projects/galaxy_game/tasks/active/2026-09-28-MEDIUM-REFACTOR-DISPOSITION-SABATIER-REACTOR-SPEC.md
```
Then open the moved file and change: `status: backlog` → `status: active`
Paste the output of both commands in chat before proceeding.
Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed

Tracked file: git mv (never cp or plain mv)
Verify with: `find projects/galaxy_game/tasks -name "2026-09-28-MEDIUM-REFACTOR-DISPOSITION-SABATIER-REACTOR-SPEC.md"`
Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any implementation.
Summaries path: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/`
Filename pattern: `2026-09-29-SYNTHESIS-SABATIER-REACTOR-SPEC-DISPOSITION.md`
Chat is for questions only — never paste synthesis into chat.


---

# TASK: Disposition sabatier_reactor_spec (misplaced material-data checks)

**Status**: BACKLOG  
**Priority**: MEDIUM  
**Type**: refactor  
**Created**: 2026-09-28  
**Last Updated**: 2026-09-28  

---

## Context

Full-suite baseline (2026-09-28, correct env prefix): **4764 examples, 143 failures, 55 pending**.

Three failures in `spec/services/ai_manager/sabatier_reactor_spec.rb` (full-suite run numbered 2, 3, 4):

**Failure 2** — `methane material data pricing.lunar_production has available set to true`
- Line 25: `expect(lunar_prod['available']).to be true`
- Error: `NoMethodError: undefined method '[]' for nil`
- Cause: `material_data.dig('pricing', 'lunar_production')` returns nil → next line indexes into nil

**Failure 3** — `methane material data pricing.lunar_production requires sabatier_reactor facility`
- Line 30: `expect(lunar_prod['facility_required']).to eq('sabatier_reactor')`
- Error: `NoMethodError: undefined method '[]' for nil`

**Failure 4** — `methane material data pricing.lunar_production has cost_per_kg set`
- Line 35: `expect(lunar_prod['cost_per_kg']).to be > 0`
- Error: `NoMethodError: undefined method '[]' for nil`

These are the only failures in this file. No other examples in `sabatier_reactor_spec.rb` fail.


### What the spec actually tests

| Describe block | Code under test | Belongs under ai_manager? |
|----------------|-----------------|---------------------------|
| `methane material data` / `pricing.lunar_production` | `MaterialGeneratorService.generate_material('methane')` | **No** |
| `NpcPriceCalculator.can_produce_locally?` | `Market::NpcPriceCalculator` (private method) | **No** |

No `AiManager::*` class is exercised. Real Sabatier behavior lives in **units / blueprints / ISRU / manufacturing**. AI manager only decides whether/when to use local CH₄.

Hard-coding `facility_required == 'sabatier_reactor'` will fight legitimate blueprint or workflow changes.

### Related baseline note

Same class of debt as missing `binding_agent` material/item lookup data: specs assume material schema that generator/lookup may not provide.

---

## Pre-dispatch Notes (read-only checks)

| Check | Result |
|---|---|
| `spec/services/ai_manager/sabatier_reactor_spec.rb` exists | ✅ `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/spec/services/ai_manager/sabatier_reactor_spec.rb` |
| `spec/services/ai_manager/sabatier_refinements_spec.rb` exists | ✅ `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/spec/services/ai_manager/sabatier_refinements_spec.rb` |
| `lunar_production` in app/ | ✅ 4 hits — all in `app/services/market/npc_price_calculator.rb` (lines 112, 173, 252, 491) |
| `facility_required` in app/ | ❌ None found |

**Key finding**: `pricing.lunar_production` IS consumed by production code — `Market::NpcPriceCalculator` has four call sites:

| Line | Code (verbatim) | Nil-safe? |
|------|-----------------|----------|
| 112 | `local_cost = material_data.dig('pricing', 'lunar_production', 'cost_per_kg')`<br>`return local_cost if local_cost` | ✅ Yes — guard returns nil |
| 173 | `local_cost = material_data.dig('pricing', 'lunar_production', 'cost_per_kg')`<br>`return local_cost if local_cost` | ✅ Yes — guard returns nil |
| 252 | `lunar_prod = material_data.dig('pricing', 'lunar_production')`<br>`return false unless lunar_prod && lunar_prod['available']` | ⚠️ Partial — checks `lunar_prod` before indexing, but spec triggers this via `can_produce_locally?` which calls `load_material_data` returning nil dig |
| 491 | `local_cost = material_data.dig('pricing', 'lunar_production', 'cost_per_kg')`<br>`return local_cost if local_cost` | ✅ Yes — guard returns nil |

The spec's three failures all stem from the same root: methane has no `pricing.lunar_production` block, so dig returns nil, and the spec indexes into nil at lines 25/30/35. Disposition C (data fix) may be required unless the agent determines these paths are dead code or should be refactored separately.

---

## Architecture Gotchas

1. **Do not invent `pricing.lunar_production` solely to green the suite** unless production code still reads that path.
2. **Do not change shared BaseUnit / Inventory** in this task.
3. **RSpec must use correct env prefix**:
   ```bash
   docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec …'
   ```
4. **Material generator vs static JSON**: confirm which path `MaterialGeneratorService.generate_material('methane')` uses before editing data files.
5. If moving examples, update only this spec (and any direct requires); do not broaden into Sabatier unit implementation.
6. **Zero sourcing blocks in material JSONs**: Earlier work found no `sourcing` blocks in any material JSON file. The calculator's `lunar_production` branch has likely never fired against real data — it is effectively dead code path.
7. **Do NOT add `pricing.lunar_production` to methane or any material file.** The geography-agnostic sourcing schema is an open architecture question (see the epoxy/pricing-resolver task).
8. **The `can_produce_locally?` example tests a private method.** Prefer testing through the public interface (`calculate_extraction_floor`, `calculate_local_production_cost`, etc.).

---

## Implementation Steps

### Step 1 — Synthesis report (required before any code change)
Write to:
`agent-tasks/projects/galaxy_game/summaries/2026-09-29-SYNTHESIS-SABATIER-REACTOR-SPEC-DISPOSITION.md`
Must answer:

Path(s) for methane material data (static JSON and/or generator output).
Grep of app/ (not only spec/) for lunar_production, facility_required, sabatier_reactor in pricing/material context — does production code consume this schema?
Current Sabatier unit/blueprint identifier(s) if present on disk.
Chosen disposition: A, B, or D (below) with one-paragraph rationale.
**NEW**: At each of the four call sites (lines 112, 173, 252, 491), what does the calculator do when `lunar_production` is nil? (fallback to EconomicConfig, raise, or wrong price?)

Step 2 — Disposition (pick one)
A. Delete the three pricing.lunar_production examples if nothing in app/ consumes that schema. Keep or relocate the NpcPriceCalculator.can_produce_locally? example only if it still has value (prefer a market/pricing spec path).
B. Move remaining valid checks out of spec/services/ai_manager/ into an appropriate home (spec/services/generators/, material data specs, or market specs). Do not leave material-schema tests under ai_manager/.
D. Keep calculator coverage in a spec under `spec/services/market/` that injects a stubbed material hash, then delete the 3 misplaced examples from `sabatier_reactor_spec.rb`. **If the synthesis shows nil BREAKS the calculator (i.e., one of the four call sites silently returns wrong price instead of falling back), STOP — write a NEEDS_REVIEW.md entry and do not fix it inside this task.**
### Step 3 — Verify

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/sabatier_reactor_spec.rb'
```
Expect: 0 failures, or file removed and no dangling references.
Optional: if examples moved, run the new path as well.

### Step 4 — Complete

Commit message should state disposition (A/B/D) and that no BaseUnit/Inventory changes were made.
git mv task file to tasks/completed/ (or project’s completed convention) and set status: completed.
Do not push unless human requests.


Acceptance Criteria

- [ ] Synthesis report saved under summaries/ before implementation
- [ ] Production-code consumption of `pricing.lunar_production` documented (yes/no + paths)
- [ ] Nil-handling at each of the four call sites documented (fallback / raise / wrong price)
- [ ] One of A / B / D implemented — not a silent data invent without evidence
- [ ] No material JSON modified
- [ ] `sabatier_reactor_spec.rb` green or removed; no orphaned examples
- [ ] Spec no longer misrepresents material/market checks as AiManager behavior (if examples remain, they live in the correct folder)
- [ ] No changes to `app/models/units/base_unit.rb` or `app/models/inventory.rb`
- [ ] Verification command uses `unset DATABASE_URL && RAILS_ENV=test`


Out of Scope

- Tileset / biome PNG asset failures
- Luna mission execution_order topological sort
- store_resource storage.type vs subcategory (separate Synthesis Report required)
- Full-suite run unless needed to confirm no cross-file breakage from a move/delete
- Implementing Sabatier unit behavior or ISRU pipeline changes


Files Involved (starting set — verify in synthesis)

























| Path | Role |
|---|---|
| `galaxy_game/spec/services/ai_manager/sabatier_reactor_spec.rb` | Failing / misplaced spec |
| `galaxy_game/spec/services/ai_manager/sabatier_refinements_spec.rb` | Related; do not expand scope unless synthesis says overlap |
| Material generator / methane JSON ([FILL IN]) | Data under test |
| `Market::NpcPriceCalculator` ([FILL IN]) | Only if keeping `can_produce_locally?` example |

Verification Commands

```bash
# Targeted
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/sabatier_reactor_spec.rb'
```

```bash
# After move (adjust path)
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec path/to/new_spec.rb'
```