---
title: "Terraforming manager cleanup — remove duplication, clarify orchestration"
priority: MEDIUM
status: backlog
owner: Implementation Agent (Qwen)
type: refactor
system_domain: TERRA_SIM / AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
created: 2026-07-24
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/drafts/2026-07-24/2026-07-24-MEDIUM-REFACTOR-TERRAFORMING-MANAGER-CLEANUP.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/drafts/2026-07-24/2026-07-24-MEDIUM-REFACTOR-TERRAFORMING-MANAGER-CLEANUP.md \
         projects/galaxy_game/tasks/active/2026-07-24-MEDIUM-REFACTOR-TERRAFORMING-MANAGER-CLEANUP.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-24-MEDIUM-REFACTOR-TERRAFORMING-MANAGER-CLEANUP.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-REFACTOR-TERRAFORMING-MANAGER-CLEANUP.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: TerraformingManager cleanup

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: refactor
**Created**: 2026-07-24
**Last Updated**: 2026-07-24

---

## Context

`TerraformingManager` is the canonical orchestration layer for biosphere and terraforming behavior. However, it contains duplicated methods and unclear boundaries between pattern-driven logic (template-based terraforming) and fallback logic (emergency/edge-case handling). This task cleans up the class by removing duplication, clarifying which methods are primary vs. fallback, and preserving its role as the central orchestration layer.

---

## Problem Statement

- `TerraformingManager` has duplicated methods (same logic in multiple places)
- Fallback logic is intermingled with pattern-driven logic — hard to distinguish primary flow from emergency handling
- No clear documentation of which methods are canonical vs. fallback
- The class is large and complex, making it a contributor friction point

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: Fallback methods may be intentionally redundant**
- ❌ Wrong: Assume all duplicated methods are bugs to be removed
- ✅ Right: Some duplication is intentional — fallback methods handle edge cases that pattern-driven methods don't cover
- Why: Removing "duplicates" without understanding their fallback purpose can break terraforming in edge cases

⚠️ **GOTCHA 2: TerraformingManager coordinates with multiple subsystems**
- ❌ Wrong: Only look at `TerraformingManager` itself
- ✅ Right: Also check how it's called from AI Manager services, biosphere model, and atmosphere model
- Why: The manager is an orchestrator — its callers may depend on methods that seem unused within the class

### Files to Audit (Read-Only)

| File/Directory | Purpose |
|---|---|
| `app/services/terraforming_manager.rb` (or similar path) | Primary file to refactor |
| `app/services/ai_manager/` | Services that call TerraformingManager |
| `app/models/celestial_bodies/spheres/biosphere.rb` | Biosphere model — may delegate to TerraformingManager |
| `app/models/celestial_bodies/spheres/atmosphere.rb` | Atmosphere model — may delegate to TerraformingManager |
| `spec/services/terraforming*` | Tests revealing expected behavior |

---

## Implementation Steps

### Step 1 — Audit TerraformingManager methods

```bash
grep -n "def " /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/services/[terraforming_manager_path].rb | sort
```

Categorize each method:
- **Pattern-driven**: Core terraforming logic (template-based, primary flow)
- **Fallback**: Emergency/edge-case handling (secondary flow)
- **Orchestration**: Coordination methods that call other services/models
- **Duplicated**: Same logic appears in multiple methods — identify which is canonical

### Step 2 — Identify and remove true duplication

For each pair of duplicated methods:
1. Determine which is the canonical version (usually the one with more complete logic)
2. Have the non-canonical version delegate to the canonical one
3. Add a comment explaining why the delegation exists

```ruby
# Before
def apply_temperature_pattern(target_temp)
  # 50 lines of temperature adjustment logic
end

def adjust_surface_temperature(target_temp)
  # Same 50 lines, slightly different variable names
end

# After
def apply_temperature_pattern(target_temp)
  # Canonical implementation — 50 lines
end

def adjust_surface_temperature(target_temp)
  # DELEGATE: Use apply_temperature_pattern for consistency
  apply_temperature_pattern(target_temp)
end
```

### Step 3 — Clarify fallback vs. pattern-driven logic

Add clear section headers and comments within the class:

```ruby
class TerraformingManager
  # ========================================================================
  # PATTERN-DRIVEN LOGIC (Primary Flow)
  # These methods implement template-based terraforming for standard cases.
  # ========================================================================
  
  # ========================================================================
  # FALLBACK LOGIC (Emergency/Edge Cases)
  # These methods handle edge cases that pattern-driven logic doesn't cover.
  # They are intentionally separate from primary flow for clarity.
  # ========================================================================
  
  # ========================================================================
  # ORCHESTRATION (Coordination with other subsystems)
  # These methods coordinate terraforming across biosphere, atmosphere, etc.
  # ========================================================================
```

### Step 4 — Add method-level documentation

For each public method, add:
- One-line purpose statement
- Whether it's pattern-driven or fallback
- Key parameters and return value

### Step 5 — Run affected specs

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/terraforming* spec/models/celestial_bodies/spheres/biosphere_spec.rb 2>&1 | tail -30'
```

Fix any failures. Repeat until all affected specs pass.

### Step 6 — Verify

- [ ] No true duplication remains (each unique logic exists in exactly one canonical method)
- [ ] Fallback methods delegate to or clearly reference canonical implementations
- [ ] Section headers clearly separate pattern-driven, fallback, and orchestration logic
- [ ] All public methods have purpose documentation
- [ ] All affected specs pass (isolation run: 0 failures)

---

## Acceptance Criteria
- [ ] No true duplication remains in TerraformingManager
- [ ] Fallback vs. pattern-driven logic clearly separated with section headers
- [ ] All public methods documented with purpose, parameters, return value
- [ ] All affected specs pass (isolation run: 0 failures)
- [ ] No behavioral changes — this is a pure refactor

---

## Stop Conditions — escalate to user immediately if:
- Cannot determine which of two "duplicated" methods is canonical
- Fallback methods depend on state that the canonical method doesn't expose
- Removal causes failures in specs outside terraforming/biosphere domain
- A migration is needed (unlikely for refactor-only task)

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add [specific files only — never git add .]
git commit -m "refactor: clean up TerraformingManager — remove duplication, clarify fallback vs pattern-driven logic"
```

---

## Documentation
- [ ] Update `docs/new_agent/projects/galaxy_game/terraforming/terraforming_manager_docs.md` — [what to update, or flag gap]
- [ ] Flag doc gap: [description if needed] — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: none
**Blocks**: none (standalone refactor)
**Related tasks**: None directly, but benefits all terraforming documentation tasks

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified]

### Lessons learned
[What worked, what didn't, what future refactors should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
