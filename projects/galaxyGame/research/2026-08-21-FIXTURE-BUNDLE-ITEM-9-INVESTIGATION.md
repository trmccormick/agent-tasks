# Research Task: Fixture-Bundle Item #9 (Oxygen) — Find Actual Root Cause

**Date Created:** 2026-08-21
**Assigned to:** Qwen (Research Agent)
**Priority:** HIGH
**Time Estimate:** 15-20 min

---

## Problem Statement

Oxygen task (`2026-08-16-MEDIUM-BUG-FIX-HARVESTER-COMPLETION-JOB-OXYGEN-FIXTURE.md`) was created assuming HarvesterCompletionJob is broken. But **this assumption was never verified**.

**Critical discovery:** The fixture-bundle synthesis report that flagged the "oxygen issue" **stops at item #8 and never documents item #9**.

---

## What You Need to Find

Your task is to discover what item #9 actually says by reading the original sources.

### Questions to Answer (In Order)

1. **Where is the fixture-bundle synthesis report?**
   - Location: `projects/galaxy_game/summaries/` in agent-tasks
   - Search for file mentioning "fixture-bundle"
   - Find reference to original task file

2. **What does item #9 say exactly?**
   - Read the original fixture-bundle task file
   - Find the "item #9" section
   - Extract exact text, not a summary

3. **Which test file contains item #9?**
   - Item #9 should reference a spec file path
   - Example: `spec/services/ai_manager/escalation_service_spec.rb` or similar
   - Get exact file path and line number if possible

4. **What is the failing assertion?**
   - Read the test code
   - Find the exact `expect(...)` or assertion that fails
   - Copy exact error message if available

5. **Is it really about HarvesterCompletionJob?**
   - Or is the issue in a different system?
   - (Remember: you can't harvest oxygen on Luna. Real pathways are PSR water mining + electrolysis, or regolith + PVE/TEU)

---

## What You'll Report Back

One paragraph diagnosis with:
1. Exact item #9 issue (from task file, not assumption)
2. Which test file and line number
3. What the test actually asserts
4. Which system is broken (harvester job, or something else?)

Example format:
> "Item #9 tests that `Settlement#inventory.current_storage_of('oxygen')` returns >0 after a harvester completes. Test fails because... [root cause]. This is a [system name] issue, not HarvesterCompletionJob."

---

## Why This Matters

- **Current state:** Oxygen task assumes HarvesterCompletionJob is broken
- **Reality:** We don't know what item #9 actually says
- **Risk:** Task could be wrong, wasting agent time on incorrect system
- **Goal:** Get real root cause before dispatching to implementation agent

---

## Files to Search

In agent-tasks repo:
- `projects/galaxy_game/summaries/` — look for fixture-bundle synthesis
- Reference the original fixture-bundle task file from there
- Read that task file's "item #9" section

In galaxyGame repo:
- Look for test file referenced in item #9
- Read the actual test code and assertion

---

## Report Format

Respond in chat with:
```
# Fixture-Bundle Item #9 — Research Complete

**Finding:** [One paragraph diagnosis]

**Test File:** [path and line number]
**Assertion:** [exact expect() statement]
**Root Cause:** [which system is broken]
**Correct Oxygen Pathway:** [PSR water mining + electrolysis, or regolith + PVE, or other]

**Next Step:** Re-scope oxygen task from "assume HarvesterCompletionJob" to "[correct system/issue]"
```

Then we'll re-scope the oxygen task and pass it to the implementation agent with correct information.

---

**Do not guess.** Read the actual files. If you can't find item #9 in the synthesis report, say so and we'll search differently.
