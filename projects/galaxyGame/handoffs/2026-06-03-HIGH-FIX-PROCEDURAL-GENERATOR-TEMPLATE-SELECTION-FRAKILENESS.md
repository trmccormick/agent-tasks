# Agent Handoff: Fix ProceduralGenerator Template Selection Failure

**Handoff Date**: June 3, 2026  
**Task File**: `tasks/backlog/2026-06-03-HIGH-FIX-PROCEDURAL-GENERATOR-TEMPLATE-SELECTION-FRAKILENESS.md`  
**Agent Type**: Implementation Agent  

---

## Task Summary

Fix the `ProceduralGenerator` test failure where only 1 planet uses templates instead of the expected ~40% (2-6 planets).

**Failure Message**: `expected 0 to be between 2 and 6`

---

## Agent Selection Criteria

### Required Capabilities:
- ✅ Debug RSpec test failures
- ✅ Read and understand Ruby service layer code
- ✅ Investigate template loading/selection logic
- ✅ Fix probability/random selection issues
- ✅ Validate fixes with targeted tests

### Recommended Agents (in priority order):
1. **GPT-5 mini** — Strong debugging, good at Ruby, handles complex logic well
2. **Claude Haiku 4.5** — Capable model for debugging tasks
3. **Qwen 27b/30b** — If available locally, capable at complex logic

### NOT Recommended:
- ❌ Qwen 9B — Previous experience showed it struggles with complex debugging (4+ logic errors requiring corrections)
- ❌ Mistral 7B — Too small for this complexity

---

## Critical First Step: Move Task File

**Before writing any code**, the agent MUST:

1. **Move task file from backlog to active**:
   ```bash
   git mv tasks/backlog/2026-06-03-HIGH-FIX-PROCEDURAL-GENERATOR-TEMPLATE-SELECTION-FRAKILENESS.md \
          tasks/active/2026-06-03-HIGH-FIX-PROCEDURAL-GENERATOR-TEMPLATE-SELECTION-FRAKILENESS.md
   ```

2. **Update YAML header**:
   - Change `status: backlog` to `status: active`

3. **Commit the change**:
   ```bash
   git add tasks/active/2026-06-03-HIGH-FIX-PROCEDURAL-GENERATOR-TEMPLATE-SELECTION-FRAKILENESS.md
   git commit -m "Start task: 2026-06-03-HIGH-FIX-PROCEDURAL-GENERATOR-TEMPLATE-SELECTION-FRAKILENESS"
   ```

4. **This signals to the strategist that work has begun** — Do not skip this step.

---

## Target Files

### Primary:
- `app/services/star_sim/procedural_generator.rb` — Template selection logic

### Test:
- `spec/services/star_sim/procedural_generator_spec.rb` (line 144) — Failing test

### Related:
- `app/models/star_system/template.rb` — If templates are a model
- Any template loading/configuration files

---

## Implementation Scope

The agent should:

1. **Read the failing test** — Understand expected behavior vs actual
2. **Check implementation** — Review template selection logic in `ProceduralGenerator`
3. **Verify templates exist** — Confirm templates are loaded and available in test environment
4. **Identify root cause** — Determine why only 1 planet uses template instead of ~6
5. **Fix the issue** — Apply appropriate fix (logic, data, or test setup)
6. **Validate** — Run targeted spec to confirm fix works
7. **Report** — Document findings and fix applied

---

## Requirements

- ✅ Preserve existing behavior unless task explicitly changes it
- ✅ Make only the changes needed for this task
- ✅ Do not create documentation
- ✅ Do not edit unrelated files
- ✅ Keep all command execution inside Docker
- ✅ For RSpec, do not stream full output; run targeted spec and report final summary line plus relevant failure snippets

---

## Testing Requirements

- Update or add focused specs for the changed behavior
- Run only the targeted spec file(s) needed to verify the change
- If a nil-related failure appears after model creation, stop and validate factory/model setup before changing service logic

---

## Output Required

1. Brief action plan if implementation is not yet started
2. Code changes once approved
3. Targeted test result summary
4. Any assumptions or blockers
5. Stop immediately if any requirement is unclear

---

## Repository Rules (from README.md)

- ✅ All command execution must be inside Docker
- ✅ Do not stream full RSpec output; report final summary line plus relevant failure snippets
- ✅ Focus on ROOT CAUSE, not symptoms
- ✅ Validate fixes before moving to next step

---

## Notes

- This is a remaining failure from the original 11 RSpec failures (May 31)
- The 3 failures from this morning were handled separately and moved to completed
- Focus on ROOT CAUSE, not symptoms
