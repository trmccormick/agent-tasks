# Task: Fix Remaining 11 RSpec Failures

**Status**: READY FOR DELEGATION  
**Priority**: HIGH  
**Type**: Debugging  
**Created**: 2026-06-01  
**Assigned To**: Claude Haiku 4.5 (capable model required)  
**DO NOT USE**: Qwen 9B (insufficient for complex debugging)  
**Est. Time**: 2-3 hours  

---

## Current Agent Availability (June 1, 3pm+ timeline)

**Right Now**: 
- 30b: Occupied (reviewing stale task)
- 27b: Available after 30b finishes (M4 local inference option)
- Claude free: Available at 3pm

**Assignment Options** (in priority order):
1. ⏳ **After 30b finishes**: Try 27b on M4 (local inference, no token cost)
2. 📅 **At 3pm**: Claude free tier becomes available (fastest option, no wait)

**Decision**: Use whichever becomes available first
- If 27b on M4 ready before 3pm → Use it
- If still waiting at 3pm → Use Claude free

---

## ⚠️ AGENT CAPABILITY REQUIREMENTS

**This task requires a CAPABLE model.** Do not assign to Qwen 9B.

### Why Not Qwen 9B?
Yesterday's experience (May 31-June 1): 
- ❌ 9B implementation had 4+ logic errors requiring multiple corrections
- ❌ Each correction cycle burned tokens instead of saving them
- ❌ Simple tasks work fine, but complex debugging/implementation needs stronger model

### Required Capabilities for This Task
- ✅ Investigate RSpec errors and trace root causes
- ✅ Read test setup and implementation code together
- ✅ Identify test fixture/mock issues vs logic bugs
- ✅ Fix issues without constant back-and-forth
- ✅ Validate fixes independently

### Recommended Models
1. **Claude Haiku 4.5** (preferred) — strong reasoning, good at debugging
2. **Qwen 27b/30b** — if available, capable at complex logic
3. **Local 70B models** — if available

**NOT RECOMMENDED**: Qwen 9B, Mistral 7B (too small for this complexity)

---

## Overview

Full RSpec suite ran with 11 failures out of 3936 tests. Most are unrelated to shortage detection work. Need systematic investigation and fixes.

---

## Failures by Category

### Category 1: ISRUCapabilityManager Tests (4 failures)
**Status**: ✅ JUST FIXED — Test file rewritten to match corrected implementation

Tests now mock `settlement.base_units` for equipment instead of `operational_data` dig paths. Should pass on next run.

**Files**:
- `spec/services/logistics/isru_capability_manager_spec.rb` — Complete rewrite ✅

---

### Category 2: Market::Marketplace (3 failures)
**Status**: ❌ NEEDS INVESTIGATION

**Failure**: `RuntimeError: Insufficient funds`

**Details**:
- Tests: place_order (fully filled, partially filled)
- Error occurs in `Financial::Account.transfer_funds` 
- Called from `Market::TradeExecutionService.transfer_net_funds`
- Triggered during marketplace trade execution

**Investigation Needed**:
1. Check test setup for MarketPlace — are buyer/seller accounts funded?
2. Verify `TradeExecutionService.transfer_net_funds` logic
3. Check if test accounts are created with sufficient balance
4. May be a mock/fixture setup issue

**Files**:
- `spec/models/market/marketplace_spec.rb` (lines 196, 220, 262)
- `app/models/financial/account.rb` (line 31)
- `app/services/market/trade_execution_service.rb` (line 68)

---

### Category 3: GameDataGenerator (1 failure)
**Status**: ❌ NEEDS INVESTIGATION

**Failure**: `expected File to exist`

**Details**:
- Test expects output file to exist at `output_path`
- File is not being created during test
- Likely a file system / path issue

**Investigation Needed**:
1. What is `output_path` in the test?
2. Does the test properly mock file system or use temp directory?
3. Is the generator actually being called?
4. Check file permissions or path configuration

**Files**:
- `spec/services/generators/game_data_generator_spec.rb` (line 13)

---

### Category 4: ContractService (1 failure)
**Status**: ❌ NEEDS INVESTIGATION

**Failure**: `expected nil to respond to 'persisted?'`

**Details**:
- `create_internal_transfer` returns nil instead of persisted contract
- Expected a Contract object that responds to `persisted?`
- Service not creating/saving the contract

**Investigation Needed**:
1. Check if `Logistics::ContractService.create_internal_transfer` is actually creating records
2. Verify required parameters are being passed
3. Check contract validation/requirements
4. May need to add missing setup (settlement, account, etc.)

**Files**:
- `spec/services/logistics/contract_service_spec.rb` (line 20)
- `app/services/logistics/contract_service.rb`

---

### Category 5: MaterialLookupService (1 failure)
**Status**: ❌ NEEDS INVESTIGATION

**Failure**: `expected Rails.logger.error to be called with(/Invalid JSON in file:/)`

**Details**:
- Error handler not being triggered
- Expecting logger to receive error message about invalid JSON
- Corrupted JSON file not being handled properly

**Investigation Needed**:
1. Is the corrupted JSON file actually created in the test?
2. Is the lookup service actually encountering the corrupted file?
3. Is the error handler in the right place?
4. May need to verify error path is triggered

**Files**:
- `spec/services/lookup/material_lookup_service_spec.rb` (line 251)
- `app/services/lookup/material_lookup_service.rb`

---

### Category 6: ProceduralGenerator (1 failure)
**Status**: ❌ NEEDS INVESTIGATION

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

1. **Investigate each failure independently**
   - Read error message carefully
   - Check test setup
   - Check implementation code
   - Identify root cause

2. **Fix one category at a time**
   - ISRUCapabilityManager: Already fixed, just verify tests pass
   - Market::Marketplace: Fund test accounts or fix balance logic
   - GameDataGenerator: Fix file path / temp directory
   - ContractService: Ensure contract is created and persisted
   - MaterialLookupService: Ensure corrupted file is actually encountered
   - ProceduralGenerator: Debug template matching logic

3. **Validate fixes**
   - Run tests for fixed category
   - Confirm all tests in that category pass
   - Do not skip to next category until current is green

4. **Report results**
   ```
   ## Failure Fixes Report

   ### ISRUCapabilityManager (4 tests)
   Status: PASS / FAIL
   Remaining issues: [if any]

   ### Market::Marketplace (3 tests)
   Status: PASS / FAIL
   Root cause: [...]
   Fix applied: [...]

   ### GameDataGenerator (1 test)
   Status: PASS / FAIL
   Root cause: [...]
   Fix applied: [...]

   ### ContractService (1 test)
   Status: PASS / FAIL
   Root cause: [...]
   Fix applied: [...]

   ### MaterialLookupService (1 test)
   Status: PASS / FAIL
   Root cause: [...]
   Fix applied: [...]

   ### ProceduralGenerator (1 test)
   Status: PASS / FAIL
   Root cause: [...]
   Fix applied: [...]

   ### Final Result
   Failures remaining: X/11
   All tests passing: YES / NO
   ```

---

## Important Notes

- ISRUCapabilityManager test file was just rewritten (June 1 morning)
- Investigation findings should focus on ROOT CAUSE not symptom
- Don't guess — check actual code + test setup
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
