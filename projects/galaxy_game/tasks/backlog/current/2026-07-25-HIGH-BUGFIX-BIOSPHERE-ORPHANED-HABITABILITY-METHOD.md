---
status: backlog
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-25-HIGH-BUGFIX-BIOSPHERE-ORPHANED-HABITABILITY-METHOD.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-25-HIGH-BUGFIX-BIOSPHERE-ORPHANED-HABITABILITY-METHOD.md \
         projects/galaxy_game/tasks/active/2026-07-25-HIGH-BUGFIX-BIOSPHERE-ORPHANED-HABITABILITY-METHOD.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-25-HIGH-BUGFIX-BIOSPHERE-ORPHANED-HABITABILITY-METHOD.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Fix orphaned `def habitability` stub at top of biosphere.rb
**Status**: BACKLOG | ACTIVE | BLOCKED | COMPLETED
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-07-25
**Last Updated**: 2026-07-25

## Problem

Lines 1-13 of `galaxy_game/app/models/celestial_bodies/spheres/biosphere.rb` contain an orphaned `def habitability` stub floating at the top-level before the class definition. This is dead code:

```ruby
# NOTE: The following methods are stubs...
      # Returns the current habitability value for this biosphere
      public
      def habitability
        if self.habitable_ratio.present?
          self.habitable_ratio
        else
          calculate_habitability
        end
      end
```

**Issues:**
- Ruby parses this as a private method on `Object`, not on `Biosphere`
- The `public` keyword at top level affects visibility of subsequent methods
- Inconsistent indentation (8 spaces) vs class body (6 spaces)
- This is a no-op — calling `biosphere.habitability` will NOT hit this code

## Solution (ALREADY APPLIED)

The orphaned code has been moved inside the class body as a proper instance method. **Verification steps:**

1. Confirm lines 1-13 are now clean (no orphaned code):
   ```bash
   head -15 galaxy_game/app/models/celestial_bodies/spheres/biosphere.rb
   ```
   Should show: `# app/models/celestial_bodies/spheres/biosphere.rb` → `module CelestialBodies`

2. Confirm `def habitability` exists inside the class body (around line 35-40):
   ```bash
   grep -n "def habitability" galaxy_game/app/models/celestial_bodies/spheres/biosphere.rb
   ```
   Should return exactly one match, inside the class body.

3. Run specs to verify no regressions:
   ```bash
   docker-compose -f docker-compose.dev.yml exec -T web bundle exec rspec spec/models/celestial_bodies/spheres/biosphere_spec.rb
   ```

## Acceptance Criteria
- [ ] No orphaned code at file top (lines 1-5 are clean)
- [ ] `def habitability` is a proper instance method inside `class Biosphere`
- [ ] All biosphere specs pass
- [ ] `habitability` method is callable and returns expected values

## Notes
- The method body uses `habitable_ratio` (no `self.` prefix) — consistent with Ruby conventions
- If `habitable_ratio` is nil, falls back to `calculate_habitability`
- This is a cleanup task only — the habitability formula itself is addressed in a separate task
