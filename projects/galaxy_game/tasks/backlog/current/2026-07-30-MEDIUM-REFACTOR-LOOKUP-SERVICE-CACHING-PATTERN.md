---
status: backlog
priority: MEDIUM
type: refactor
system_domain: AI_MANAGER | MANUFACTURING | TERRA_SIM | OTHER
mvp_alignment: SPEC_HEALTH | OTHER
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-30-MEDIUM-REFACTOR-LOOKUP-SERVICE-CACHING-PATTERN.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-30-MEDIUM-REFACTOR-LOOKUP-SERVICE-CACHING-PATTERN.md \
         projects/galaxy_game/tasks/active/2026-07-30-MEDIUM-REFACTOR-LOOKUP-SERVICE-CACHING-PATTERN.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-30-MEDIUM-REFACTOR-LOOKUP-SERVICE-CACHING-PATTERN.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains context, the known-working
pattern from today's MaterialLookupService fix, and a gotcha from a real
crash that happened during that fix. This task's Prerequisites and Files
Involved sections were intentionally left for you to fill in with your own
research (grep/read access) — Claude drafted the strategy/context only,
since it has no filesystem/terminal access to verify exact paths.

Also confirm the current docker-compose.dev.yml location as part of your
setup — it was not found at the expected path in the last session.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE
starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-07-30-REFACTOR-LOOKUP-SERVICE-CACHING-PATTERN.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Extract MaterialLookupService caching pattern, apply to remaining lookup services
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: refactor
**Created**: 2026-07-30
**Last Updated**: 2026-07-30

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below
4. **Reference Implementation**: `galaxy_game/app/services/lookup/material_lookup_service.rb`
   — verified method names today (2026-07-30):
   - `self.materials_cache` ✅ matches task file
   - `self.reset_cache!` ✅ matches task file
   - `self.load_materials_class` ✅ matches task file
   - `self.load_json_files_class` ✅ exists (not mentioned in task file)
   - `self.load_json_files_recursively_class` ✅ exists (not mentioned in task file)
   All method names referenced elsewhere in this task file are correct.

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat
> BEFORE starting work.

---

## Context

`Lookup::MaterialLookupService` was fixed 2026-07-30 (same-day, earlier
session) to use class-level memoization instead of re-scanning all JSON
files on every `.new` call. Excessive JSON re-scanning on lookup
instantiation has been a recurring, repeatedly-noticed problem across
multiple sessions — not a one-off complaint about materials specifically.

Several other lookup services under `app/services/lookup/` likely load
JSON data fresh on every instantiation the same way MaterialLookupService
did before today's fix. Known candidates (unconfirmed — verify each):
Blueprint, Unit, Structure, Module, Item, Craft. Some may already be
optimized — `StarSystemLookupService` was noted as already using lazy
loading; confirm rather than assume.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `app/services/lookup/base_lookup_service.rb` — already has a `data_cache`
  class method available; MaterialLookupService's original bug was that it
  ignored this and loaded into an instance variable instead. Check whether
  `data_cache` should be the shared mechanism other services adopt, rather
  than reinventing per-service caching.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Do not call a private instance method on another instance
via `send` from a class method — this caused a live production crash today
(`private method 'load_materials' called for an instance of
Lookup::MaterialLookupService`), even though tests were green.
- ❌ Wrong: class method does `new.send(:load_materials)`
- ✅ Right: extract the loading logic into class methods entirely (no
  instance method call needed from class context) — this is what the fixed
  MaterialLookupService does; read its current state as the reference.
- Why: cross-instance private method calls via `send` behaved unexpectedly
  in this codebase's context and broke at runtime despite passing specs.

⚠️ **GOTCHA 2**: Green RSpec tests did not catch the above crash — 43-44
passing tests coexisted with an actively-crashing live run
(`luna:simulate_operations`). Do not treat a green spec suite as sufficient
verification for this task.
- ❌ Wrong: convert a service, run its spec file, mark done.
- ✅ Right: convert a service, run its spec file, AND run/trigger a real
  code path that exercises it (e.g. relevant rake task or console call),
  confirming the "Found N JSON files... Loaded N items" log line appears
  only once per process, not repeatedly.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before running any commands or modifying any files, post a synthesis
report (saved as MD to the summaries folder, not pasted in chat) covering:
what services you found need conversion, which are already fine, the
files you'll touch, and your verification plan for each — per the
standard template in the project README.

---

## Problem Statement

**Current behavior**: Some/most lookup services under `app/services/lookup/`
re-load and re-parse their backing JSON data on every `.new` call instead
of once per process. Confirmed for MaterialLookupService prior to today's
fix; unconfirmed but suspected for others.

**Expected behavior**: Every lookup service that currently loads data
fresh on each `.new` uses the same class-level caching pattern
MaterialLookupService now uses, each individually verified with a real
run — not just green specs.

---

## Files Involved

### Primary Files — you will edit these

**Inventory of `app/services/lookup/*.rb` (verified 2026-07-30):**

#### NEEDS FIX (loads fresh per-instance, no class-level cache)
| File | Spec File | Notes |
|---|---|---|
| `blueprint_lookup_service.rb` | `spec/models/blueprint_spec.rb` | `Dir.glob` + `File.read` in `initialize` — no cache |
| `craft_lookup_service.rb` | `spec/models/craft/base_craft_spec.rb` | `Dir.glob` + `File.read` in `initialize` — no cache |
| `item_lookup_service.rb` | `spec/models/item_spec.rb` | `Dir.glob` + `File.read` in `initialize` — no cache |
| `module_lookup_service.rb` | `spec/models/modules/base_module_spec.rb` | `Dir.glob` + `File.read` in `initialize` — no cache |
| `structure_lookup_service.rb` | `spec/models/structures/orbital_structure_spec.rb` | `Dir.glob` + `File.read` in `initialize` — no cache |
| `unit_lookup_service.rb` | `spec/models/units/base_unit_spec.rb` | `Dir.glob` + `File.read` in `initialize` — no cache |

#### ALREADY FINE (no action needed)
| File | Spec File | Notes |
|---|---|---|
| `material_lookup_service.rb` | `spec/services/lookup/material_lookup_service_spec.rb` | ✅ Already has class-level cache (`materials_cache`, `reset_cache!`) — reference implementation |
| `star_system_lookup_service.rb` | `spec/services/lookup/star_system_lookup_service_spec.rb` | Uses lazy loading (confirmed in prior session) |

#### NOT APPLICABLE (not data-loading lookup services)
| File | Spec File | Notes |
|---|---|---|
| `base_lookup_service.rb` | N/A | Base class — has `data_cache` mechanism but not a standalone service |
| `earth_reference_service.rb` | `spec/services/lookup/earth_reference_service_spec.rb` | Loads JSON in `initialize` but is Earth-specific reference, not a general lookup pattern |
| `legacy_port_adapter.rb` | NOT FOUND | Adapter, not a data-loading service |
| `logistics_lookup_service.rb` | NOT FOUND | No `File.read` or `Dir.glob` — no data loading |
| `planetary_geological_feature_lookup_service.rb` | `spec/services/lookup/planetary_geological_feature_lookup_service_spec.rb` | Loads features per-instance but is celestial-body-scoped, not a general lookup pattern |
| `rig_lookup_service.rb` | `spec/models/rigs/base_rig_spec.rb` | Loads in `initialize` but is rig-specific, not a general lookup pattern |

**Summary: 6 services need conversion (blueprint, craft, item, module, structure, unit). 2 are already fine. 4 are not applicable.**

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/services/lookup/material_lookup_service.rb` | Reference implementation of the working caching pattern |
| `app/services/lookup/base_lookup_service.rb` | Existing shared `data_cache` mechanism — evaluate whether to consolidate into this instead of per-service patterns |
| `spec/services/lookup/material_lookup_service_spec.rb` | Reference spec structure for the caching tests |

### Migration
- [x] No migration needed

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
(standard — see Minimal Handoff block above)

### Step 1 — Inventory
Enumerate all files in `app/services/lookup/`. For each, determine whether
it loads data fresh per-instance (needs fix) or already caches (skip).
Fill in the Files Involved table above with the real list before
proceeding.

### Step 2 — Convert each needs-fix service
Apply the MaterialLookupService pattern (class-level cache, no `send` into
private instance methods, `self.reset_cache!` for tests) to each service
identified in Step 1. One service at a time, not all at once.

### Step 3 — Verify each service individually
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [SPEC_PATH] 2>&1 | tail -20'
```
AND trigger a real code path that exercises the service (e.g. a relevant
rake task), confirming the disk-scan log line does not repeat.

### Step 4 — Synthesis Report (before committing anything)
Standard format per project README. Do not commit until explicitly
approved.

---

## Acceptance Criteria
- [ ] Full inventory of lookup services taken, each classified
- [ ] Each needs-fix service converted using the proven pattern
- [ ] For each: RSpec suite green AND a real triggering run confirms no
      repeated disk scanning
- [ ] No regressions in dependent services
- [ ] Full suite run completed and logged (human runs overnight — agent
      does not trigger)

---

## Stop Conditions — escalate to user immediately if:
- Any service's association/schema doesn't match the pattern cleanly
- A second private-method-visibility-style crash appears — stop and
  report before attempting further services
- Fix requires changing more files than this task anticipates
- Any architectural decision is required (e.g. whether to consolidate into
  `base_lookup_service.rb`'s existing `data_cache` mechanism)

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container.
One commit per converted service is preferable for provenance, matching
the convention used for the ProductionService fix earlier this week.

**Task file move on completion:**
```bash
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy_game/tasks/active/2026-07-30-MEDIUM-REFACTOR-LOOKUP-SERVICE-CACHING-PATTERN.md \
       projects/galaxy_game/tasks/completed/2026-07/2026-07-30-MEDIUM-REFACTOR-LOOKUP-SERVICE-CACHING-PATTERN.md
git commit -m "chore: move 2026-07-30-MEDIUM-REFACTOR-LOOKUP-SERVICE-CACHING-PATTERN.md to completed/"
```

---

## Dependencies
**Blocked by**: none
**Blocks**: none currently known
**Related tasks**: MaterialLookupService caching fix (completed same-day,
earlier session, no task file — done ad hoc in chat)

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
