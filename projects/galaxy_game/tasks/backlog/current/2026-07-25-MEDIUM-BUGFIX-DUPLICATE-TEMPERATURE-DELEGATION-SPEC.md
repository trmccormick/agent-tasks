---
status: backlog
priority: MEDIUM
type: bug-fix
system_domain: OTHER
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-25-MEDIUM-BUGFIX-DUPLICATE-TEMPERATURE-DELEGATION-SPEC.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-25-MEDIUM-BUGFIX-DUPLICATE-TEMPERATURE-DELEGATION-SPEC.md \
         projects/galaxy_game/tasks/active/2026-07-25-MEDIUM-BUGFIX-DUPLICATE-TEMPERATURE-DELEGATION-SPEC.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-25-MEDIUM-BUGFIX-DUPLICATE-TEMPERATURE-DELEGATION-SPEC.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Remove duplicate `describe 'temperature delegation'` block from biosphere_spec.rb
**Status**: BACKLOG | ACTIVE | BLOCKED | COMPLETED
**Priority**: MEDIUM
**Type**: bug-fix
**Created**: 2026-07-25
**Last Updated**: 2026-07-25

## Problem

`galaxy_game/spec/models/celestial_bodies/spheres/biosphere_spec.rb` contains two `describe 'temperature delegation'` blocks:
- First block at line ~512 (the original)
- Second block at line ~541 (the duplicate — needs removal)

Both blocks have identical setup (`let(:atmosphere)`, temperature_data) but slightly different expectations in the "falls back" example. The second block is a stale duplicate that should be removed.

## Solution (ALREADY APPLIED)

The second `describe 'temperature delegation'` block has been removed from the spec file. **Verification steps:**

1. Confirm only one `describe 'temperature delegation'` exists:
   ```bash
   grep -n "describe 'temperature delegation'" galaxy_game/spec/models/celestial_bodies/spheres/biosphere_spec.rb
   ```
   Should return exactly ONE match (around line 512).

2. Run specs to verify no regressions:
   ```bash
   docker-compose -f docker-compose.dev.yml exec -T web bundle exec rspec spec/models/celestial_bodies/spheres/biosphere_spec.rb --format documentation
   ```

## Acceptance Criteria
- [ ] Only one `describe 'temperature delegation'` block exists in the spec file
- [ ] All biosphere specs pass (0 failures, 0 errors)
- [ ] No duplicate describe blocks anywhere in the file

## Notes
- The first block has a single expectation: `expect(biosphere.tropical_temperature).to eq(300.0)`
- The second (removed) block had two expectations: `tropical_temperature` AND `polar_temperature` both at 300.0/250.0
- If the polar_temperature expectation is needed, it should be merged into the first block — not left as a duplicate
