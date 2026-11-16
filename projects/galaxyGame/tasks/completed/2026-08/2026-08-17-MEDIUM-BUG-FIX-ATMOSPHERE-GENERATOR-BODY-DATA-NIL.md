---
status: completed
priority: MEDIUM
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: false
created: 2026-08-17
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-17-MEDIUM-BUG-FIX-ATMOSPHERE-GENERATOR-BODY-DATA-NIL.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-17-MEDIUM-BUG-FIX-ATMOSPHERE-GENERATOR-BODY-DATA-NIL.md \
         projects/galaxy_game/tasks/active/2026-08-17-MEDIUM-BUG-FIX-ATMOSPHERE-GENERATOR-BODY-DATA-NIL.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-17-MEDIUM-BUG-FIX-ATMOSPHERE-GENERATOR-BODY-DATA-NIL.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

---

# TASK: AtmosphereGeneratorService — @body_data nil/wrong Outside Normal Generation Flow

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: bug-fix
**Created**: 2026-08-17

---

## Context

Discovered incidentally on 2026-08-17 while implementing the parent-magnetosphere-influence
task (`2026-08-14-MEDIUM-FEATURE-PARENT-MAGNETOSPHERE-INFLUENCE.md`, now completed). A spec
run surfaced a failure in `AtmosphereGeneratorService#generate_composition_for_body` — `@body_data`
was nil or otherwise wrong when the method was invoked outside the normal full-system
generation flow.

The implementing agent correctly identified this as a **pre-existing bug unrelated to
their task's own change**, and instead of fixing `AtmosphereGeneratorService` itself
(which would have been unscoped work), adjusted their *own* new test to avoid triggering
that code path. That was the right call for staying in scope — but it means this bug is
still real and unfixed, and was never independently filed until now.

**Not yet confirmed**: exact reproduction steps, exact nil/wrong value, or full impact —
this task file is a first-pass writeup from what was visible in the parent-influence
task's transcript, not a from-scratch investigation.

## Problem Statement

`AtmosphereGeneratorService#generate_composition_for_body` fails or misbehaves when
`@body_data` is nil or in an unexpected state — this happens when the method is called
outside the standard full-system-generation pass ordering (e.g. from a test invoking it
in isolation, or possibly from any other code path that doesn't go through the normal
`SystemBuilderService` sequence).

**Current behavior**: Unknown precisely — needs reproduction. At minimum, one spec
context reliably triggers the failure by calling generation logic outside standard flow.
**Expected behavior**: The method should either handle a nil/absent `@body_data` gracefully
(clear error, sane default, or explicit guard) or the real callers should always guarantee
`@body_data` is set before this method runs — needs investigation to determine which is
the correct fix.

## Gotchas

- This may only be a symptom of test setup not matching real invocation order, rather
  than a bug that manifests in the live game — confirm whether normal `SystemBuilderService`-driven
  generation always sets `@body_data` correctly before calling this method, and the bug
  is test-only, before assuming it's a live-game bug.
- If it IS a live-game bug (e.g. any other code path calls this method directly without
  going through the standard pass), that's a more serious finding — check for other
  callers of `generate_composition_for_body` beyond the standard flow.
- `AtmosphereGeneratorService` may be shared/broadly-used code — confirm scope before
  committing a fix; if callers are numerous, this needs the standard shared-code Synthesis
  Report escalation before commit.

## Files Involved

**[FILL IN — Qwen/implementation agent to confirm exact paths and line numbers via
terminal access. Claude has no filesystem access and cannot verify these directly.]**

Known starting point (unconfirmed line numbers):
- `app/services/star_sim/atmosphere_generator_service.rb` — `generate_composition_for_body` method
- `spec/services/star_sim/procedural_generator_magnetosphere_spec.rb` — the spec context that
  triggered this, worked around rather than fixed (see the parent-magnetosphere-influence
  task's commit for the exact workaround applied)

## Acceptance Criteria

- [ ] Root cause identified: is `@body_data` nil/wrong due to test setup mismatch, or a real
      live-code caller-ordering issue?
- [ ] Fix applied at the correct layer (defensive guard in the service, or ensuring callers
      set `@body_data` correctly — whichever the root cause indicates)
- [ ] No regressions in existing AtmosphereGeneratorService specs
- [ ] The workaround applied in the parent-magnetosphere-influence spec (avoiding this code
      path) can optionally be reverted once the real fix lands, if reverting makes the test
      more representative of real usage — not required, but worth checking

## Stop Conditions

- If `AtmosphereGeneratorService` turns out to be called from many places across the
  codebase, stop and escalate via Synthesis Report before committing any fix — shared-code
  rule applies.
- If root cause can't be determined within a reasonable investigation pass, report findings
  rather than guessing at a fix.

## Dependencies
**Blocked by**: none
**Blocks**: none identified
**Related**: 2026-08-14-MEDIUM-FEATURE-PARENT-MAGNETOSPHERE-INFLUENCE.md (completed/2026-08/ — where this was discovered)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: Implementation Agent (Qwen)
**Completion date**: 2026-08-24
**Final test result**: All specs pass — 51 procedural_generator + 22 magnetosphere + 12 data_driven_generation = 85 examples, 0 failures

### What was changed

1. **Fixed argument swap in `procedural_generator.rb` line 29**:
   - Before: `AtmosphereGeneratorService.new(material_lookup || Lookup::MaterialLookupService.new, {})`
   - After: `AtmosphereGeneratorService.new({}, material_lookup || Lookup::MaterialLookupService.new)`
   - The constructor expects `(celestial_body_data, material_lookup_service)` — arguments were reversed

2. **Removed workaround in `procedural_generator_magnetosphere_spec.rb`**:
   - Removed 3 lines that mocked `generate_composition_for_body` to return nil specifically to avoid triggering the @body_data bug
   - The workaround is no longer needed since the root cause is fixed

### Issues discovered

- **Root cause was a swapped argument order**, not just nil/missing data. When ProceduralGenerator created its own AtmosphereGeneratorService, `@body_data` became a MaterialLookupService instance (not a hash), and `@material_lookup` became an empty hash `{}`.
- This meant `@body_data[:albedo]` called `[]` on a service object → nil
- And `@material_lookup.find_material(...)` called `.find_material` on `{}` → NoMethodError
- The bug was latent because the test in `data_driven_generation_spec.rb` passed `nil, nil` (which also fails but doesn't crash — just produces wrong results silently)

### Lessons learned

- Always verify constructor argument order when calling services — swapped positional args are a silent failure mode
- Workarounds that "make tests pass" should be tracked as technical debt and removed once root cause is fixed
- Only 3 callers of `AtmosphereGeneratorService.new` across the codebase — not shared-code, so no Synthesis Report escalation needed
