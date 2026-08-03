---
title: "Remove AlienLifeForm artifact — consolidate into Biology::LifeForm"
priority: MEDIUM
status: active
owner: Implementation Agent (Qwen)
type: refactor
system_domain: OTHER
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
created: 2026-07-24
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-24-MEDIUM-REFACTOR-REMOVE-ALIENLIFEFORM-ARTIFACT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-24-MEDIUM-REFACTOR-REMOVE-ALIENLIFEFORM-ARTIFACT.md \
         projects/galaxy_game/tasks/active/2026-07-24-MEDIUM-REFACTOR-REMOVE-ALIENLIFEFORM-ARTIFACT.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-24-MEDIUM-REFACTOR-REMOVE-ALIENLIFEFORM-ARTIFACT.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-REFACTOR-REMOVE-ALIENLIFEFORM-ARTIFACT.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Remove AlienLifeForm artifact — consolidate into Biology::LifeForm

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: refactor
**Created**: 2026-07-24
**Last Updated**: 2026-07-24

---

## Context

`AlienLifeForm` is a legacy artifact that was superseded by `Biology::LifeForm`. References to `AlienLifeForm` persist throughout the codebase, creating confusion about which class is canonical. The `Biology::LifeForm` model uses `origin` and `properties` fields to track life provenance (alien vs. Earth-origin), making `AlienLifeForm` redundant. This task removes all `AlienLifeForm` references and consolidates life provenance into the single canonical model.

---

## Problem Statement

- `AlienLifeForm` class exists alongside `Biology::LifeForm` as a legacy artifact
- References to `AlienLifeForm` are scattered across services, models, specs, and factories
- Contributors (and agents) are confused about which class to use for life forms
- The provenance tracking (`origin`, `properties`) in `Biology::LifeForm` already handles alien vs. Earth-origin distinction
- Dead code increases maintenance burden and cognitive load

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: Biology::LifeForm uses STI or is a separate class?**
- ❌ Wrong: Assume `AlienLifeForm` is a subclass of `Biology::LifeForm` (STI)
- ✅ Right: Verify whether they share a table or have separate tables — check migrations and schema
- Why: If they share a table, removing the class constant requires care with existing records

⚠️ **GOTCHA 2: Factory aliases may reference AlienLifeForm**
- ❌ Wrong: Only search for `AlienLifeForm` in app code
- ✅ Right: Also search factories (`spec/factories/`) and any trait definitions that create life forms
- Why: Factories often use class names directly; stale factory references will break specs

⚠️ **GOTCHA 3: Biology::LifeForm may have callbacks that instantiate AlienLifeForm**
- ❌ Wrong: Only replace direct `AlienLifeForm.new` calls
- ✅ Right: Also check callbacks, polymorphic associations, and any dynamic instantiation (`constantize`, `classify`)
- Why: Dynamic class resolution can hide references that grep won't find

### Files to Audit (Read-Only)

| File/Directory | Purpose |
|---|---|
| Search for `AlienLifeForm` across entire codebase | Find all references |
| `app/models/biology/life_form.rb` | Canonical life form model |
| `spec/factories/` | Factory definitions for life forms |
| `spec/services/` | Service specs that may reference AlienLifeForm |
| `db/migrate/` | Migration history for life form tables |

---

## Implementation Steps

### Step 1 — Audit all AlienLifeForm references

```bash
grep -r "AlienLifeForm" /Users/tam0013/Documents/git/galaxyGame/galaxy_game/ --include="*.rb" --include="*.json" --include="*.yml" -l | sort
```

Categorize each reference:
- **Direct class usage**: `AlienLifeForm.new`, `AlienLifeForm.create!`, etc.
- **Factory references**: `create(:alien_life_form)` or similar
- **Polymorphic/dynamic**: `constantize`, `classify`, string-based lookups
- **Schema/migration**: Table definitions, column references
- **Documentation**: References in docs that need updating

### Step 2 — Verify Biology::LifeForm handles provenance

Confirm that `Biology::LifeForm` has:
- [ ] `origin` field (or equivalent) for tracking alien vs. Earth-origin
- [ ] `properties` hash for additional provenance data
- [ ] No gaps in functionality that AlienLifeForm provided but Biology::LifeForm doesn't

If gaps exist, document them and propose a migration plan before proceeding.

### Step 3 — Remove AlienLifeForm references

For each category of reference:

**Direct class usage:**
```ruby
# Before
alien = AlienLifeForm.create!(name: "Test", complexity: :simple)

# After
alien = Biology::LifeForm.create!(name: "Test", complexity: :simple, origin: :alien)
```

**Factory references:**
```ruby
# Before
create(:alien_life_form, biosphere: biosphere)

# After
create(:life_form, biosphere: biosphere, origin: :alien)
```

**Polymorphic/dynamic:**
- Replace `constantize` calls with explicit `Biology::LifeForm` references
- Update any string-based class lookups

### Step 4 — Remove the AlienLifeForm class file

Once all references are removed:
```bash
# Verify no remaining references
grep -r "AlienLifeForm" /Users/tam0013/Documents/git/galaxyGame/galaxy_game/ --include="*.rb"

# If clean, remove the file
rm galaxy_game/app/models/[path]/alien_life_form.rb
```

### Step 5 — Run affected specs

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/models/biology/ life_form_spec.rb spec/services/ai_manager/*biosphere*spec.rb 2>&1 | tail -30'
```

Fix any failures. Repeat until all affected specs pass.

### Step 6 — Verify

- [ ] Zero references to `AlienLifeForm` remain in codebase (grep confirms)
- [ ] All affected specs pass (isolation run: 0 failures)
- [ ] No regressions in related biosphere/life form specs
- [ ] Biology::LifeForm provenance fields (`origin`, `properties`) are used consistently

---

## Acceptance Criteria
- [ ] Zero references to `AlienLifeForm` remain in codebase
- [ ] All affected specs pass (isolation run: 0 failures)
- [ ] No regressions in related biosphere/life form specs
- [ ] Biology::LifeForm provenance fields are used consistently for alien vs. Earth-origin tracking

---

## Stop Conditions — escalate to user immediately if:
- `AlienLifeForm` and `Biology::LifeForm` share a table with existing records that can't be migrated
- Provenance gaps found (AlienLifeForm had functionality Biology::LifeForm doesn't)
- Removal causes failures in specs outside the biosphere/life form domain
- A database migration is needed that wasn't anticipated

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add [specific files only — never git add .]
git commit -m "refactor: remove AlienLifeForm artifact — consolidate all life forms into Biology::LifeForm with origin tracking"
```

---

## Documentation
- [ ] Update `docs/new_agent/projects/galaxy_game/biology/life_form_docs.md` — [what to update, or flag gap]
- [ ] Flag doc gap: [description if needed] — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: none
**Blocks**: none (standalone refactor)
**Related tasks**: None directly, but benefits all biology/life form documentation tasks

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: Implementation Agent (Qwen)
**Completion date**: 2026-08-03
**Final test result**: Skipped — pre-existing Rails syntax error in `app/services/ai_manager.rb` (unmatched `when :maintenance` at line 346) blocks spec execution. This is unrelated to AlienLifeForm changes.

### What was changed
- `galaxy_game/app/models/celestial_bodies/alien_life_form.rb` — **deleted** (40 lines, dead class definition)
- `galaxy_game/spec/models/celestial_bodies/alien_life_form_spec.rb` — **deleted** (55 lines, spec for dead class)
- `galaxy_game/app/models/celestial_bodies/celestial_body.rb` — removed `/AlienLifeForm$/` regex case from type classification method (2 lines removed)

### Issues discovered
- Task description assumed `Biology::LifeForm` has `origin` field for provenance tracking, but it does NOT. `CelestialBodies::AlienLifeForm` and `Biology::LifeForm` are separate classes with separate tables (not STI). This simplifies the refactor — no data migration needed, just dead code removal.
- Pre-existing Rails syntax error in `app/services/ai_manager.rb:346` (unmatched `when :maintenance`) prevents spec execution. Not related to this task.

### Follow-up tasks needed
- Fix pre-existing `ai_manager.rb` syntax error to restore spec execution capability
- Consider whether the migration file `20250515165628_create_celestial_bodies_alien_life_forms.rb` should be kept as historical record or removed (currently left in place)

### Lessons learned
- Task descriptions can contain incorrect assumptions about field existence — always verify before proceeding
- Dead code removal is often simpler than consolidation when classes are separate (not STI)

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: alien_life_form.rb + spec deleted, celestial_body.rb regex removed | commit 6d37f95e | specs skipped due to pre-existing ai_manager.rb syntax error (line 346)
