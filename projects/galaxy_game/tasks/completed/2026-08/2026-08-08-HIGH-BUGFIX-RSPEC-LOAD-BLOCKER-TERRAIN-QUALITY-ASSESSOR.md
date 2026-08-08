# TASK: Fix RSpec Load Blocker — terrain_quality_assessor_spec.rb require path mismatch

**Task ID:** `2026-08-08-HIGH-BUGFIX-RSPEC-LOAD-BLOCKER-TERRAIN-QUALITY-ASSESSOR`
**Date Created:** 2026-08-08
**Priority:** HIGH
**Type:** bug-fix
**System Domain:** SPEC_HEALTH
**MVP Alignment:** AI_MANAGER_LUNA_SETTLEMENT (blocks full suite pass/fail signal)
**Local Worker Safe:** true

---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase05-luna-calibration/2026-08-08-HIGH-BUGFIX-RSPEC-LOAD-BLOCKER-TERRAIN-QUALITY-ASSESSOR.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/galaxy_game/tasks/backlog/phase05-luna-calibration/2026-08-08-HIGH-BUGFIX-RSPEC-LOAD-BLOCKER-TERRAIN-QUALITY-ASSESSOR.md \
         projects/galaxy_game/tasks/active/2026-08-08-HIGH-BUGFIX-RSPEC-LOAD-BLOCKER-TERRAIN-QUALITY-ASSESSOR.md

Then open the moved file and change: status: backlog → status: active
Paste the output of both commands in chat before proceeding.
Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-08-HIGH-BUGFIX-RSPEC-LOAD-BLOCKER-TERRAIN-QUALITY-ASSESSOR.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-BUGFIX-RSPEC-LOAD-BLOCKER-TERRAIN-QUALITY-ASSESSOR.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

## Context

Last night's full overnight RSpec run failed to load entirely — **0 examples run, 1 error outside examples**. This blocks trustworthy pass/fail signal on the whole suite, including the Luna Simulation Baseline task already staged in this folder.

The error is a `require_relative` path mismatch in one spec file. The source class exists but under a different filename than what the spec expects.

---

## Problem Statement

**Error output:**
```
An error occurred while loading ./spec/services/terrain/terrain_quality_assessor_spec.rb. - Did you mean?
                    rspec ./spec/services/tileset/terrain_tile_renderer_spec.rb

Failure/Error: require_relative '../../../../galaxy_game/app/services/terrain/terrain_quality_assessor'

LoadError:
  cannot load such file -- /home/galaxy_game/app/services/terrain/terrain_quality_assessor
```

**Root cause confirmed:** The spec at `spec/services/terrain/terrain_quality_assessor_spec.rb` line 2 has a `require_relative` that resolves to `/home/galaxy_game/app/services/terrain/terrain_quality_assessor`, but the actual source file is named **`quality_assessor.rb`**, not `terrain_quality_assessor.rb`.

**Current state:**
- Source file exists at: `galaxy_game/app/services/terrain/quality_assessor.rb` ✅
- Spec file exists at: `galaxy_game/spec/services/terrain/terrain_quality_assessor_spec.rb` ✅
- The spec's require path is wrong by one directory-level prefix (`terrain_` vs nothing)

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Section |
|---|---|---|
| `galaxy_game/spec/services/terrain/terrain_quality_assessor_spec.rb` | Spec file with wrong require path | Line 2: `require_relative '../../../../galaxy_game/app/services/terrain/terrain_quality_assessor'` |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/app/services/terrain/quality_assessor.rb` | The actual source class — confirm it exists and has the expected class name |
| `galaxy_game/app/services/terrain/multi_body_terrain_generator.rb` | Context: what other terrain services exist (for comparison) |

---

## Implementation Steps

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP)

Follow the Minimal Handoff block above. Paste outputs in chat before proceeding.

### Step 1 — Verify Source File Exists and Confirm Class Name

Inside Docker:
```bash
docker exec web bash -c 'ls /home/galaxy_game/app/services/terrain/quality_assessor.rb'
docker exec web bash -c 'head -5 /home/galaxy_game/app/services/terrain/quality_assessor.rb'
```

Confirm the class name (e.g., `class QualityAssessor` or `class TerrainQualityAssessor`). This determines what the spec's require should resolve to.

### Step 2 — Fix the require_relative Path

The spec file at line 2 currently has:
```ruby
require_relative '../../../../galaxy_game/app/services/terrain/terrain_quality_assessor'
```

This resolves from `spec/services/terrain/` going up 4 levels to the repo root, then into `app/services/terrain/`. The correct path should be:
```ruby
require_relative '../../../../galaxy_game/app/services/terrain/quality_assessor'
```

**Fix:** Change `terrain_quality_assessor` to `quality_assessor` on line 2 of the spec file.

### Step 3 — Verify No Other Load Errors

After fixing, run a targeted load check:
```bash
cd /Users/tam0013/Documents/git/galaxyGame && docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/terrain/ --format progress' 2>&1 | tail -5
```

Expected: all examples load and run (no LoadError).

### Step 4 — Check for Other Hidden Load Errors

Run the full suite to confirm no other previously-hidden load errors surface:
```bash
cd /Users/tam0013/Documents/git/galaxyGame && docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec 2>&1 | tail -10'
```

**Report:** Did fixing this reveal any other previously-hidden load errors? If yes, list them and STOP — do not fix them. Report them for Tracy's triage.

---

## Acceptance Criteria
- [ ] `require_relative` path corrected to the actual source file location (or spec retired if source was intentionally deleted)
- [ ] Full RSpec suite loads without error afterward (0 load errors, not just this file passing)
- [ ] Report before/after: did fixing this reveal any other previously-hidden load errors?
- [ ] No regressions in the terrain_quality_assessor spec examples

---

## Stop Conditions — escalate to user immediately if:
- The source class `quality_assessor.rb` appears to have been intentionally removed as part of TerrainForge cleanup work this session → flag for Tracy's confirmation before deciding delete-the-spec vs fix-the-path
- Fixing this reveals additional load errors in other spec files → report them and stop (do not fix)
- The full suite still fails to load after the fix → report the new error and stop

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add galaxy_game/spec/services/terrain/terrain_quality_assessor_spec.rb
git commit -m "fix: correct terrain_quality_assessor spec require path (quality_assessor.rb, not terrain_quality_assessor.rb)"
```

---

## Dependencies
**Blocked by**: none
**Blocks**: Luna Simulation Baseline task (blocks full suite pass/fail signal)
**Related tasks**: None

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
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
