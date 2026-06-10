# Task: Fix ProceduralGenerator Template Selection Failure

**Status**: READY FOR DELEGATION  
**Priority**: HIGH  
**Type**: Debugging  
**Created**: 2026-06-03  
**Assigned To**: GPT-5 Mini (0x) — PENDING  
**Est. Time**: 1-2 hours  

---

## Overview

The `ProceduralGenerator` test is failing with:
```
expected 0 to be between 2 and 6
```

This failure was part of the original 11 failures from May 31. GPT-5 mini fixed 3 separate failures this morning but did NOT address this one.

---

## Test Details

**File**: `spec/services/star_sim/procedural_generator_spec.rb` (line 144)

**Test Name**: `should use templates for approximately 40% of planets`

**Expected Behavior**:
- ~40% of generated planets should use procedural templates
- Expected count: 2-6 template-based planets out of ~15 total
- Actual count: 0 template-based planets

---

## Implementation Details

**File**: `app/services/star_sim/procedural_generator.rb`

Need to review:
1. How templates are loaded/registered
2. The probability logic for template selection
3. Whether templates are available in test environment
4. Random seed handling (if any)
5. Template matching criteria

---

## Investigation Steps

1. **Read the failing test** — Understand what it expects vs what happens
2. **Review ProceduralGenerator implementation** — Check template loading and selection logic
3. **Check test setup** — Verify templates are available in test environment
4. **Identify root cause** — Is it a probability issue? Template availability? Matching logic?
5. **Fix the issue** — Adjust logic, add missing setup, or fix configuration
6. **Validate** — Run test to confirm fix

---

## Expected Fix Types

Possible causes:
- Templates not loaded in test environment
- Probability calculation incorrect (not ~40%)
- Template matching too strict (no planets match templates)
- Random seed causing unlucky run (unlikely but possible)
- Missing configuration for template usage

---

## Success Criteria

- ✅ Test passes consistently (not just once)
- ✅ ~40% of planets use templates as expected
- ✅ No hardcoded values or workarounds
- ✅ Root cause documented in code comments

---

## Related Files

- `spec/services/star_sim/procedural_generator_spec.rb`
- `app/services/star_sim/procedural_generator.rb`
- Possibly: `config/` files, database fixtures for templates

---

## Notes

- This is the ONLY remaining failure after GPT-5 mini's work this morning
- All other 10 failures have been fixed
- Focus on understanding WHY templates aren't being used
- Don't guess — check actual code and test setup