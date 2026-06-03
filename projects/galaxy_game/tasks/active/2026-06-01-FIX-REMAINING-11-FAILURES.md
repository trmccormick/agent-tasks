# Task: Fix Remaining RSpec Failures

**Status**: READY FOR DELEGATION  
**Priority**: HIGH  
**Type**: Debugging  
**Created**: 2026-06-03  
**Assigned To**: GPT-5 Mini (0x) — JUST COMPLETED ✅  
**Est. Time**: 1-2 hours  

---

## Overview

Full RSpec suite ran with **8 failures out of 3936 tests**. These are unrelated to shortage detection work and need systematic investigation and fixes.

**Note**: This task file was updated on June 3, 2026. Three additional failures were handled separately by GPT-5 Mini this morning (see deleted backlog tasks for details). The remaining failures below represent what still needs attention after that work.

---

## Failures by Category

### Current State (as of June 3, 2026)

After GPT-5 Mini's work this morning:
- ✅ ISRUCapabilityManager: Fixed and passing
- ✅ Market::Marketplace: Fixed and passing  
- ✅ GameDataGenerator: Fixed and passing
- ✅ ContractService: Fixed and passing
- ✅ MaterialLookupService: Fixed and passing
- ❌ ProceduralGenerator: Still failing (1 test)

**Total remaining failures**: 1 out of original 11

---

### Category 6: ProceduralGenerator (1 failure) — STILL NEEDS INVESTIGATION

**Failure**: `expected 0 to be between 2 and 6`

**Details**:
- Test expects ~40% of planets to use templates
- Actually getting 0 template-based planets
- Template selection logic not working

**Investigation Needed**:
1. Check template loading/availability in test environment
2. Verify the probability logic (should be ~40%)
3. Check if templates are being matched to planets
4. May be a random seed issue or template availability

**Files**:
- `spec/services/star_sim/procedural_generator_spec.rb` (line 144)
- `app/services/star_sim/procedural_generator.rb`

---

## Process for Agent

1. **Investigate the remaining failure**
   - Read error message carefully
   - Check test setup
   - Check implementation code
   - Identify root cause

2. **Fix ProceduralGenerator**
   - Debug template matching logic
   - Verify templates are available in test environment
   - Check probability/random seed handling

3. **Validate fixes**
   - Run tests for fixed category
   - Confirm all tests pass

4. **Report results**
   ```
   ## Failure Fixes Report

   ### ProceduralGenerator (1 test)
   Status: PASS / FAIL
   Root cause: [...]
   Fix applied: [...]

   ### Final Result
   Failures remaining: X/8
   All tests passing: YES / NO
   ```

---

## Important Notes

- Investigation findings should focus on ROOT CAUSE not symptom
- Don't guess — check actual code + test setup
- This is the only remaining failure after GPT-5 Mini's work
- If fixture/factory issue, fix the setup, not the test
- Report blockers if test infrastructure is missing

---

## Context Files

- Full test output: check `/home/galaxy_game/log/rspec_full_*.log`
- Shortage detection status: `CURRENT_STATUS_SHORTAGE_SERVICES.md`
- Investigation findings: `INVESTIGATION-FINDINGS.md`

---

## Ready for Agent

✅ All failures categorized  
✅ Investigation direction clear  
✅ Files identified  
✅ Expected output format documented  

Delegate when ready.
