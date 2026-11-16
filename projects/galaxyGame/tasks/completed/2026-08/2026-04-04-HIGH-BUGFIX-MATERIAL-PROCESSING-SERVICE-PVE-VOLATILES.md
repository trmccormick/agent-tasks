---
name: "PVE Operational Data Schema Fix + MaterialProcessingService Validation"
priority: HIGH
phase: phase6+
created: 2026-04-04
status: completed
type: bugfix/data
relocated_from: review/
relocated_reason: "Luna surface/ISRU work — belongs in Phase 6, not sim calibration"
last_reviewed: 2026-07-28

# TASK: PVE Operational Data Schema Fix + MaterialProcessingService Validation

**Priority**: HIGH  
**Phase**: phase6+ (Luna Surface Settlement)  
**Type**: bugfix/data  
**Created**: 2026-04-04  
**Last Updated**: 2026-07-28  

---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase06-lava-tube-base/2026-04-04-HIGH-BUGFIX-MATERIAL-PROCESSING-SERVICE-PVE-VOLATILES.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/phase06-lava-tube-base/2026-04-04-HIGH-BUGFIX-MATERIAL-PROCESSING-SERVICE-PVE-VOLATILES.md \
         projects/galaxy_game/tasks/active/2026-04-04-HIGH-BUGFIX-MATERIAL-PROCESSING-SERVICE-PVE-VOLATILES.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-04-04-HIGH-BUGFIX-MATERIAL-PROCESSING-SERVICE-PVE-VOLATILES.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-BUGFIX-PVE-SCHEMA-FIX.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

## Context

The Planetary Volatiles Extractor (PVE) is a Phase 2 Luna ISRU unit that extracts volatiles from regolith. The MaterialProcessingService `complete_job` method reads operational data and processes outputs based on output resource IDs. Mk1 PVE uses correct chemical formula IDs (`H2O`, `mixed_volatiles`, `depleted_regolith`) but Mk2 and Mk3 use old format IDs (`extracted_water`, `extracted_gases`) that won't match the code's case handlers.

---

## Problem Statement

### Issue 1: Mk2/Mk3 Operational Data Schema Mismatch

| Unit | Output ID for water | Output ID for gases | Matches code? |
|------|---------------------|---------------------|---------------|
| **Mk1** ✅ | `H2O` | `mixed_volatiles` | ✅ Yes |
| **Mk2** ❌ | `extracted_water` | `extracted_gases` | ❌ No — code expects `H2O`/`mixed_volatiles` |
| **Mk3** ❌ | `extracted_water` | `extracted_gases` | ❌ No — code expects `H2O`/`mixed_volatiles` |

The `complete_job` method has case handlers for `'H2O'` and `'mixed_volatiles'`. Mk2/Mk3 output IDs won't match any handler, so water/gas extraction silently produces nothing.

### Issue 2: ±5% Variation Not Implemented

The code calculates volatile extraction as deterministic (no variation). The task originally specified ±5% random variation for realism but it was never implemented.

### What's Already Working ✅

- Core `complete_job` logic is correct (Case A: non-zero scaling, Case B: geosphere-driven zero-amount)
- Tests already expect chemical formula outputs (`H2O`, `CO2`) — not stale
- Mk1 PVE operational data is correctly formatted
- Depleted regolith calculation exists in code

---

## Acceptance Criteria

- [ ] Mk2 output_resources: `extracted_water` → `H2O`, `extracted_gases` → `mixed_volatiles`
- [ ] Mk3 output_resources: `extracted_water` → `H2O`, `extracted_gases` → `mixed_volatiles`, `trace_minerals` → keep (new Mk3-specific)
- [ ] All three PVE units produce correct outputs when processed by MaterialProcessingService
- [ ] ±5% variation implemented in `complete_job` Case B handlers: `produced = base_amount * (1.0 + (rand * 0.10 - 0.05))`
- [ ] Spec tests pass for all three PVE variants
- [ ] No regressions in TEU or other material processing specs

---

## Files to Edit

| File | Purpose | Change |
|------|---------|--------|
| `data/json-data/operational_data/units/production/extractors/planetary_volatiles_extractor_mk2_data.json` | Mk2 operational data | Fix output_resources IDs |
| `data/json-data/operational_data/units/production/extractors/planetary_volatiles_extractor_mk3_data.json` | Mk3 operational data | Fix output_resources IDs |
| `galaxy_game/app/services/manufacturing/material_processing_service.rb` | Processing logic | Add ±5% variation to Case B |

### Reference Files (read but don't edit)
| File | Why You Need It |
|------|-----------------|
| `data/json-data/operational_data/units/production/extractors/planetary_volatiles_extractor_mk1_data.json` | Correct format reference |
| `galaxy_game/spec/services/manufacturing/material_processing_service_spec.rb` | Existing PVE tests |

---

## Implementation Steps

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/phase6+/2026-04-04-HIGH-BUGFIX-MATERIAL-PROCESSING-SERVICE-PVE-VOLATILES.md \
       projects/galaxy_game/tasks/active/2026-04-04-HIGH-BUGFIX-MATERIAL-PROCESSING-SERVICE-PVE-VOLATILES.md
```

Then open the moved file and change YAML status: `status: backlog` → `status: active`

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-04-04-HIGH-BUGFIX-MATERIAL-PROCESSING-SERVICE-PVE-VOLATILES.md"
```

### Step 1 — Fix Mk2 Operational Data

In `planetary_volatiles_extractor_mk2_data.json`, change output_resources:
```json
// BEFORE (broken)
"output_resources": [
  { "id": "extracted_gases", "amount": 0, "unit": "kilogram" },
  { "id": "extracted_water", "amount": 0, "unit": "kilogram" },
  { "id": "depleted_regolith", "amount": 0, "unit": "kilogram" }
]

// AFTER (correct)
"output_resources": [
  { "id": "mixed_volatiles", "amount": 0, "unit": "kilogram" },
  { "id": "H2O", "amount": 0, "unit": "kilogram" },
  { "id": "depleted_regolith", "amount": 0, "unit": "kilogram" }
]
```

### Step 2 — Fix Mk3 Operational Data

In `planetary_volatiles_extractor_mk3_data.json`, change output_resources:
```json
// BEFORE (broken)
"output_resources": [
  { "id": "extracted_gases", "amount": 0, "unit": "kilogram" },
  { "id": "extracted_water", "amount": 0, "unit": "kilogram" },
  { "id": "trace_minerals", "amount": 0, "unit": "kilogram" },
  { "id": "depleted_regolith", "amount": 0, "unit": "kilogram" }
]

// AFTER (correct)
"output_resources": [
  { "id": "mixed_volatiles", "amount": 0, "unit": "kilogram" },
  { "id": "H2O", "amount": 0, "unit": "kilogram" },
  { "id": "trace_minerals", "amount": 0, "unit": "kilogram" },
  { "id": "depleted_regolith", "amount": 0, "unit": "kilogram" }
]
```

Note: `trace_minerals` is kept — it's Mk3-specific and won't match any case handler (silently ignored, which is fine).

### Step 3 — Add ±5% Variation to MaterialProcessingService

In `complete_job` Case B handlers, add variation to each produced amount:
```ruby
# In H2O case:
variation = 1.0 + (rand * 0.10 - 0.05)
produced = input_amount * (h2o.to_f / 100.0) * geosphere_eff * variation

# In mixed_volatiles case:
variation = 1.0 + (rand * 0.10 - 0.05)
produced = input_amount * (percent.to_f / 100.0) * geosphere_eff * variation

# In depleted_regolith case:
total_extracted = crust_volatiles.values.map do |percent|
  variation = 1.0 + (rand * 0.10 - 0.05)
  input_amount * (percent.to_f / 100.0) * geosphere_eff * variation
end.sum
produced = input_amount - total_extracted
```

### Step 4 — Verify Tests Pass

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/manufacturing/material_processing_service_spec.rb --format progress 2>&1 | tail -20'
```

Expected: all existing tests pass, no regressions.

---

## Stop Conditions — escalate to user immediately if:
- Mk3 `trace_minerals` should be handled by a case handler (not silently ignored)
- The variation formula needs to be configurable per-unit rather than hardcoded ±5%
- Any architectural decision conflicts with locked decisions in `DECISIONS.md`

---

## Commit Instructions

```bash
git add data/json-data/operational_data/units/production/extractors/planetary_volatiles_extractor_mk2_data.json data/json-data/operational_data/units/production/extractors/planetary_volatiles_extractor_mk3_data.json galaxy_game/app/services/manufacturing/material_processing_service.rb
git commit -m "fix: PVE Mk2/Mk3 output_resources schema + add ±5% volatile extraction variation"
```

**Task file move on completion:**
```bash
git mv projects/galaxy_game/tasks/active/2026-04-04-HIGH-BUGFIX-MATERIAL-PROCESSING-SERVICE-PVE-VOLATILES.md \
       projects/galaxy_game/tasks/completed/2026-07/2026-04-04-HIGH-BUGFIX-MATERIAL-PROCESSING-SERVICE-PVE-VOLATILES.md

git commit -m "chore: move 2026-04-04-HIGH-BUGFIX-MATERIAL-PROCESSING-SERVICE-PVE-VOLATILES.md to completed/"
```

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]  
**Completion date**: YYYY-MM-DD  

### What was changed
- Mk2 output_resources: `extracted_water` → `H2O`, `extracted_gases` → `mixed_volatiles`
- Mk3 output_resources: `extracted_water` → `H2O`, `extracted_gases` → `mixed_volatiles` (kept `trace_minerals`)
- MaterialProcessingService: added ±5% variation to Case B volatile extraction

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
**Estimate**: 6 hours (diagnosis + spec fix + validation)
**Relocated**: From reorganization_attempt_3 on 2026-06-22 (confirmed phase5 Luna bootstrap blocker)

---

## 🎯 Objective

Verify and complete MaterialProcessingService PVE (Planetary Volatiles Extractor) output logic. The service was partially refactored to output volatiles by chemical formula (H2O, CO2, N2, etc.) instead of generic `extracted_water` and `mixed_volatiles`, but tests may not reflect current implementation.

**Luna Mission Impact**: PVE deployment is Phase 2 of Luna mission. If this service doesn't output correctly, regolith cannot be processed into oxygen/water/He3 needed for settlement survival.

---

## 📋 Problem Statement

| Aspect | Status | Details |
|--------|--------|---------|
| **Code State** | ⏳ Partial | Service updated with H2O/mixed_volatiles handling (lines 128-157) |
| **Test State** | ❓ Unknown | Spec may reference old output IDs (`extracted_water`) |
| **PVE Output** | ⏳ Needs verification | Outputs should use chemical formulas from geosphere data |
| **Variation** | ✅ Designed | ±5% variation modeled but not yet implemented |
| **Luna Blocking** | 🔴 YES | Phase 2 ISRU deployment depends on this working |

---

## 🔍 Technical Context

### Current Implementation (lines 94-157)

**Case A: Non-zero output amount** (lines 110-114)
- Scales by input amount and efficiency
- ✅ Working

**Case B: Zero output amount** (lines 115-157)
- Reads geosphere `stored_volatiles` 
- Converts to percentages
- Case handlers for H2O, mixed_volatiles, depleted_regolith
- ⏳ Needs verification

### Expected PVE Output Model

```
Input: regolith (kg)
Outputs (from geosphere crust_composition.volatiles):
  H2O    → regolith × (geosphere.volatiles['H2O'] %) × efficiency × variation(±5%)
  CO2    → regolith × (geosphere.volatiles['CO2'] %) × efficiency × variation(±5%)
  N2     → regolith × (geosphere.volatiles['N2'] %) × efficiency × variation(±5%)
  [each volatile in geosphere data]
  depleted_regolith → input - sum(extracted volatiles)
```

### Variation Formula

```ruby
# Not yet implemented
variation = 1.0 + (rand * 0.10 - 0.05)  # ±5% uniform random
produced = base_amount * variation
```

---

## ✅ Acceptance Criteria

- [ ] Spec tests for PVE complete_job pass (all 4+ test cases)
- [ ] PVE outputs chemical formulas (H2O, CO2, N2, CH4, etc.) — NOT `extracted_water`
- [ ] Depleted regolith correctly calculated as input minus extracted
- [ ] Variation (±5%) implemented or documented as future enhancement
- [ ] Luna test: `rake luna_mission:execute` Phase 2 deploy_pve_unit passes
- [ ] No regressions in other material processing specs

---

## 📂 Files to Check/Edit

### Primary (Likely to Edit)
- [galaxy_game/app/services/manufacturing/material_processing_service.rb](galaxy_game/app/services/manufacturing/material_processing_service.rb)
  - Lines 94-157: `complete_job` method, PVE output handling
  - Verify variation (±5%) is implemented
  - Check geosphere data reading is correct

- [galaxy_game/spec/services/manufacturing/material_processing_service_spec.rb](galaxy_game/spec/services/manufacturing/material_processing_service_spec.rb)
  - Verify test expectations match chemical formula outputs (H2O, not extracted_water)
  - Test for depleted_regolith output calculation
  - Test variation behavior (if implemented)

### Reference (Check But Don't Edit)
- [data/json-data/operational_data/units/production/extractors/planetary_volatiles_extractor_mk1_data.json](data/json-data/operational_data/units/production/extractors/planetary_volatiles_extractor_mk1_data.json)
- [data/json-data/operational_data/units/production/extractors/planetary_volatiles_extractor_mk2_data.json](data/json-data/operational_data/units/production/extractors/planetary_volatiles_extractor_mk2_data.json)
- [data/json-data/operational_data/units/production/extractors/planetary_volatiles_extractor_mk3_data.json](data/json-data/operational_data/units/production/extractors/planetary_volatiles_extractor_mk3_data.json)

---

## 🚧 Implementation Checklist

### Phase 1: Diagnosis
- [ ] Read full `complete_job` implementation (lines 94-157)
- [ ] Check geosphere model for `stored_volatiles` structure
- [ ] Read PVE operational data files (Mk1, Mk2, Mk3)
- [ ] Check current spec expectations (what outputs does it expect?)

### Phase 2: Verify/Fix Code
- [ ] Confirm H2O case handler reads from geosphere correctly
- [ ] Confirm mixed_volatiles case handler outputs all volatiles except H2O
- [ ] Confirm depleted_regolith calculation: `input - sum(extracted)`
- [ ] Add ±5% variation if not present: `1.0 + (rand * 0.10 - 0.05)`
- [ ] Verify output IDs match geosphere volatile names

### Phase 3: Update Specs
- [ ] Update test expectations to expect H2O, CO2, etc. (not extracted_water)
- [ ] Test depleted_regolith output value
- [ ] Test with various geosphere compositions
- [ ] Test for edge cases (geosphere with no volatiles, etc.)

### Phase 4: Integration Test
- [ ] Run: `rspec spec/services/manufacturing/material_processing_service_spec.rb`
- [ ] Verify all tests pass
- [ ] Run Luna mission: `rake luna_mission:execute`
- [ ] Check Phase 2 PVE deployment succeeds

### Phase 5: Commit
- [ ] Commit: `fix: MaterialProcessingService — PVE volatile outputs and variation`

---

## 🔗 Dependencies

- **Blocked By**: None
- **Blocks**: Luna Phase 2 (ISRU deployment) 🔴 CRITICAL
- **Related**: Thermal Extraction Unit (TEU) similar logic

---

## 📝 Completion Template

*Agent completes this section upon implementation*

**Status**: [NOT STARTED / IN PROGRESS / COMPLETED / BLOCKED]
**Completion Date**: [YYYY-MM-DD]
**Git Commit**: [commit hash]
**Test Results**: [spec/services/manufacturing/material_processing_service_spec.rb — X passed, 0 failed]
**Luna Test**: [Phase 2 deploy_pve_unit — PASSED / FAILED]
**Notes**: [Any issues found, variation implementation status]
