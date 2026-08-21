---
status: backlog
priority: MEDIUM
type: bug-fix
system_domain: AI_MANAGER
mvp_alignment: ISRU_PRODUCTION
local_worker_safe: true
created: 2026-08-16
updated: 2026-08-21
estimated_effort: 2-3 hours
blocker_for: []
depends_on: []
---

# TASK: Luna Oxygen Production — Diagnose Broken Pathway

## 🔍 Diagnostic Task (Primary Deliverable: Identify Which Oxygen Source Is Failing)

**Status:** Ready for dispatch. Task requires diagnostic-first approach before implementation.

**Context:** Fixture-bundle task item #9 flagged an "oxygen issue" on Luna, but oxygen production has **7+ distinct pathways**. Cannot assume HarvesterCompletionJob is the culprit.

---

## Oxygen Production Pathways (Full Taxonomy)

Luna can produce oxygen through:

| Pathway | System | Method |
|---------|--------|--------|
| **Mining** | Water Ice Extraction | H2O from PSR craters → electrolysis → O2 |
| **Extraction** | Regolith Processing | Water in regolith → extraction → O2 |
| **PVE Processing** | Oxide Reduction | Regolith oxides → reduction → O2 |
| **Smelting** | Ore Smelting | Ore smelting byproducts → O2 |
| **CO2 Processing** | Carbon Reduction | CO2 → CO2 reduction cycle → O2 + CH4 |
| **Biological** | Greenhouse | Plant photosynthesis → O2 |
| **Biological** | Bioreactor | Biological CO2 reduction → O2 |

**Problem:** Fixture-bundle says "oxygen issue" but doesn't specify which pathway. Must diagnose.

---

## Critical Distinction: Harvester Scope

**HarvesterCompletionJob only handles MINING/EXTRACTION pathways:**
- Surface robot harvesters deployed by `AIManager::EscalationService`
- Completes orders for water ice mining, regolith extraction, etc.
- Does NOT handle: greenhouses, bioreactors, PVE, smelting, CO2 processing

**Greenhouses, bioreactors, and PVE use different systems entirely.**

---

## Diagnostic Steps (What Agent Must Do First)

### Step 1: Run fixture tests and identify which oxygen source is failing
- Execute rspec on fixture-bundle task #9 (oxygen-related spec)
- Observe: Is oxygen NOT being produced at all? Or is it produced but not stored/accessible?
- Log which oxygen pathway's test is failing

### Step 2: Trace the failing pathway
- If mining/extraction fails → trace HarvesterCompletionJob
- If greenhouse fails → trace greenhouse production system
- If bioreactor fails → trace bioreactor system
- If PVE/smelting/CO2 fails → trace those systems

### Step 3: Identify the real bug
- Could be: order dispatch issue
- Could be: inventory key mismatch (oxygen vs O2)
- Could be: fixture mocking issue
- Could be: system not wired to settlement inventory at all

### Step 4: Fix that specific bug
- Only after diagnosis, implement the fix

### Step 5: Write test coverage
- Add unit or integration tests to prevent regression

---

## Potential Gotchas

**Inventory Key Normalization:**
- Different oxygen sources might normalize keys differently
- `'oxygen'` vs `'O2'` vs other formats could cause cross-system failures
- Fixture might seed under wrong key

**Multiple Oxygen Sources:**
- Settlement might have multiple oxygen sources active simultaneously
- If only one fails, others might mask the issue
- Need to test each pathway in isolation

**Cross-Cutting Inventory Routing:**
- Settlement inventory might have wrong configuration
- Recipes/consumers might look for key that oxygen sources don't provide

---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent** (Diagnostic Phase).

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-16-MEDIUM-BUG-FIX-HARVESTER-COMPLETION-JOB-OXYGEN-FIXTURE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-16-MEDIUM-BUG-FIX-HARVESTER-COMPLETION-JOB-OXYGEN-FIXTURE.md \
         projects/galaxy_game/tasks/active/2026-08-16-MEDIUM-BUG-FIX-HARVESTER-COMPLETION-JOB-OXYGEN-FIXTURE.md
  
  Then open the moved file and change: status: backlog → status: active
  Commit: git add . && git commit -m "Move oxygen diagnostic task to active"
  
  Paste the output of find command before proceeding:
  find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks -name "2026-08-16-MEDIUM-BUG-FIX-HARVESTER-COMPLETION-JOB-OXYGEN-FIXTURE.md"
  (Should return ONLY ONE result. If multiple exist, cleanup before proceeding.)

READ FIRST (after Step 0): Task file contains all diagnostic steps and gotchas.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-08-21-DIAGNOSTIC-OXYGEN-PATHWAYS-LUNA.md
  Chat is for questions only — never paste synthesis into chat.

RETURN TO CHAT WITH:
  1. Synthesis report (findings from diagnostic steps)
  2. Which oxygen pathway is broken (mining, greenhouse, bioreactor, PVE, smelting, CO2 processing?)
  3. The actual bug (code location, root cause, not just "oxygen issue")
  4. Revised task scope for implementation phase
  Do NOT attempt implementation until strategist confirms bug diagnosis.
```

**That's it.** Everything else is IN this task file.

---

## Diagnostic Steps (Your Actual Work)

### Phase 1: Identify Which Oxygen Source Is Failing

**Step 1A: Find and run the fixture-bundle test**
```bash
cd /Users/tam0013/Documents/git/galaxyGame
# Find tests tagged with fixture-bundle or oxygen-related tests
grep -r "fixture.*bundle\|oxygen.*fixture" spec/ --include="*.rb" | head -20
# Look for test #9 or related oxygen tests mentioned in fixture-bundle task
```
- Find the specific test file and run it
- Observe: Does oxygen production fail? Does inventory handling fail? Does settlement consumption fail?
- **Record:** Which test fails? What's the assertion error?

**Step 1B: Trace where oxygen is supposed to come from in the test**
- Read the test file
- What oxygen source is being tested? (water mining, greenhouse, bioreactor, PVE, etc.?)
- Is it using HarvesterCompletionJob, or a different system entirely?
- **Record:** Which system is being tested? Which job/service is responsible?

**Step 1C: Check if multiple oxygen sources are configured**
```bash
grep -r "oxygen" spec/fixtures --include="*.rb" -A 2 -B 2
# See how oxygen is seeded in different fixture files
```
- Are there multiple oxygen-producing systems active?
- Does the test work for one source but fail for another?
- **Record:** All oxygen pathways configured in fixture, which ones work/fail

### Phase 2: Diagnose the Root Cause

**Step 2A: If it's HarvesterCompletionJob**
- Run the job isolation test (if it exists)
- Check: Does material get added to inventory? Under what key?
- Verify key format: `settlement.inventory.items.keys` — what keys exist?
- **Record:** Actual inventory key (e.g., 'oxygen' vs 'O2' vs material ID)

**Step 2B: If it's Greenhouse/Bioreactor/PVE**
- Find the production system in code
- Trace: Does it call `settlement.inventory.add_item()`? With what key?
- Check: Is that system even wired to the settlement?
- **Record:** Which system is broken, and how it's supposed to produce oxygen

**Step 2C: Cross-Check Inventory Key Normalization**
```bash
grep -r "current_storage_of\|add_item" galaxy_game/app/models/inventory.rb -A 3
# How does inventory handle keys? Does it normalize them?
grep -r "'oxygen'\|'O2'\|'H2O'" spec/ --include="*.rb" | head -20
# What key format do tests expect?
```
- **Record:** What key format does inventory expect?

### Phase 3: Synthesize Findings

Write a synthesis report with:
1. **Broken Pathway:** Which oxygen source is failing? (harvester mining, greenhouse, bioreactor, PVE, CO2 reduction, etc.)
2. **Root Cause:** What's actually broken?
   - e.g., "HarvesterCompletionJob adds oxygen under 'oxygen' key but inventory lookups use 'O2'"
   - OR: "Greenhouse system never wired to settlement inventory"
   - OR: "Bioreactor fixture mocked but not configured in settlement"
3. **Bug Location:** Exact file and method
4. **Why Fixture Failed:** How this bug manifests in the test
5. **Impact on Other Sources:** Does this break just this pathway, or all oxygen production?

---

## Acceptance Criteria (Diagnostic Phase)

- [ ] **Identified failing pathway:** Which oxygen source (harvester mining, greenhouse, bioreactor, PVE, smelting, CO2, or combination) doesn't work
- [ ] **Found root cause:** Specific code bug, not "vague oxygen issue"
- [ ] **Verified scope:** Is this a single-system bug, or does it affect multiple oxygen pathways?
- [ ] **Synthesis report written:** Full findings documented in summaries folder
- [ ] **Ready for implementation:** Strategist reviews diagnosis and approves scope for fix phase

**Success Signal:** Strategist reads synthesis report and says "Yes, that's the real bug — proceed to implementation" or "That's not it, investigate X instead."

---

## Gotchas & Traps

1. **Trap — Wrong Assumption About HarvesterCompletionJob:**
   - Don't assume the bug is in this job just because fixture mentions it
   - The bug could be in greenhouse, bioreactor, PVE, or cross-cutting inventory handling
   - **Verify:** Actually run the test and see which assertion fails

2. **Trap — Multiple Oxygen Sources Mask Each Other:**
   - Settlement might have 3+ oxygen sources configured
   - If one breaks but others work, the test might pass
   - **Diagnose:** Test each pathway in isolation if possible

3. **Trap — Inventory Key Format Variations:**
   - Different sources might use different key formats (string symbols vs material IDs)
   - `'oxygen'`, `'O2'`, material ID 123, or something else?
   - **Verify:** Check actual inventory.items.keys after production completes

4. **Trap — Fixture Mocking Can Hide Real Issues:**
   - Test might mock a system that isn't actually integrated
   - Real game flow might not use that mocked system at all
   - **Check:** Does fixture test match real production flow?

---
