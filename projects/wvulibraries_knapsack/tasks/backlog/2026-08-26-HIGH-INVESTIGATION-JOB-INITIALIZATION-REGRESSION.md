# Task: Investigate Job Initialization NameError Regression

**Status**: BACKLOG  
**Priority**: HIGH (Jobs failing, only on M4)  
**Created**: 2026-08-26  
**Assigned to**: Qwen  

---

## Problem Statement

**Symptom**: Background jobs failing with `NameError: uninitialized constant Wings::ModelRegistry` (NEW M4 regression)

**Test Evidence**: Sidekiq dashboard on M4 shows 72+ discarded jobs with Wings::ModelRegistry error

**Key Observation**: **Does NOT occur on VM (hykudev)**—this is a NEW regression introduced by our commits.

**M4 vs VM Comparison**:
- **M4 Stack Car**: Multiple job errors including Wings::ModelRegistry NameError (72+ jobs), Hyrax::ObjectNotFoundError, ActiveJob::DeserializationError
- **VM hykudev**: Single consistent error (ContentUpdateEventJob → ActiveJob::DeserializationError: ObjectNotFoundError) — pre-existing, NOT our regression

**Timeline**:
- VM at commit 0cc0230 (pre-existing job issues only, no Wings error)
- M4 at commit 6b26690+ (Wings error is NEW, introduced by our commits)

**Root Cause**: Unknown. Our recent changes to facet-limiting and Windows guard broke job initialization order. Need to identify which commit.

---

## Acceptance Criteria

Investigation MUST produce:

1. **Culprit Commit Identified**:
   - Which commit between 0cc0230 and HEAD introduced the regression?
   - Which file was changed?
   - Why does it break job initialization?

2. **Root Cause Explained**:
   - Jobs execute outside web request cycle
   - Wing::ModelRegistry error occurs before jobs can load
   - How does our change affect initialization order?

3. **Fix Recommended**:
   - Can existing valkyrie_wings_guard.rb protect jobs too?
   - Or do jobs need separate guard/initialization?
   - Proposed fix location and approach

4. **Synthesis Report**:
   - Commit hash that broke jobs
   - File changed and why it matters
   - Root cause analysis
   - Fix recommendation with priority

---

## Investigation Steps

### STEP 0: Prepare Environment
- Move task from backlog/ to active/ via git mv
- Create synthesis file path: projects/wvulibraries_knapsack/summaries/2026-08-26-INVESTIGATION-JOB-INITIALIZATION-REGRESSION.md

### STEP 1: Confirm Regression is Real
Verify M4 has failing jobs, VM does not:

On M4 (wvu_knapsack):
curl -s 'https://admin-wvu-knapsack.localhost.direct/jobs/jobs' -k -u samvera:hyku 2>&1 | grep -i "discarded\|NameError\|Wings::ModelRegistry" | head -20

On VM (hykudev):
ssh tam0013@hykudev.lib.wvu.edu
curl -s 'https://admin-hykudev.lib.wvu.edu/jobs/jobs' -k -u samvera:hyku 2>&1 | grep -i "discarded\|NameError\|Wings" | head -20

Document: Which environment shows errors, which doesn't.

### STEP 2: Bisect to Find Culprit Commit
Use git bisect to narrow down which commit broke jobs:

cd /Users/tam0013/Documents/git/wvu_knapsack

git log --oneline 0cc0230..HEAD | head -20

Candidates (commits between VM and M4):
- CatalogSearchBuilderWrapper (facet-limiting)
- CatalogControllerDecorator (facet config)
- valkyrie_wings_guard.rb (Wings initialization)
- 999_catalog_controller_decorator.rb (after_initialize hook)

Test each by:
1. Checking out commit
2. Rebuilding Docker: sh down.sc.local.sh && sh up.sc.local.sh
3. Checking if jobs still fail
4. Document: "Commit X works" or "Commit X breaks"

### STEP 3: Analyze Culprit Commit
Once you identify which commit breaks jobs:

git show COMMIT_HASH

Review:
- What file was changed?
- What was the change (diff)?
- How could it affect job initialization?

Check: Does it involve Rails initialization hooks (after_initialize, config_load_paths, etc.)?

### STEP 4: Trace Job Initialization
Compare two initialization flows:

Working flow (VM 0cc0230):
- Rails.application.initialize!
- Jobs load
- Background workers start
- No Wings::ModelRegistry error

Broken flow (M4 HEAD):
- Rails.application.initialize!
- Our changed code runs (maybe too early?)
- Jobs load and hit Wings::ModelRegistry error
- Jobs fail

Question: Is valkyrie_wings_guard.rb protecting jobs? Or is something else running before jobs can use it?

### STEP 5: Understand Job vs Web Cycle
Key difference:
- Web requests go through middleware → controllers → search builders → Wings guard works
- Jobs execute: rake tasks, ActiveJob workers, Sidekiq — do they load the guard?

Check: Is valkyrie_wings_guard.rb in a location that runs for jobs?

File locations:
- config/initializers/999_catalog_controller_decorator.rb (only for web controllers?)
- config/initializers/valkyrie_wings_guard.rb (runs globally? only for web?)

Test: Do jobs load and run valkyrie_wings_guard.rb?

### STEP 6: Propose Fix
Once root cause clear, recommend:
1. Revert problematic commit (if simple fix)?
2. Move Wings guard to earlier initialization (config/application.rb)?
3. Add job-specific guard in job class?
4. Adjust after_initialize hook to not run during job startup?

---

## Synthesis Requirements

Create file: projects/wvulibraries_knapsack/summaries/2026-08-26-INVESTIGATION-JOB-INITIALIZATION-REGRESSION.md

Format:
- **Culprit Commit**: Hash, message, file changed
- **Root Cause**: Why jobs fail with Wings::ModelRegistry
- **Evidence**: Test results showing which commits work/break
- **Fix Recommendation**: Specific approach with code location
- **Priority**: Is it a revert, quick fix, or deeper refactor?

---

## Context

**Environment**: M4 Mac, Stack Car (jobs via Sidekiq)
**Stack**: Hyku 7.1.0 + Hyrax 5.2.0, Rails 7.2.3, Ruby 3.3.10
**Branch**: fix/hide-type-facet-add-show-more-facets (M4 HEAD: 6b26690)
**Baseline**: VM at 0cc0230 (jobs work fine)
**Error**: NameError: uninitialized constant Wings::ModelRegistry in ValkyrieCharacterizationJob
**Discarded Jobs**: 72 visible in Sidekiq dashboard

**Key Insight**: This is a NEW regression (not pre-existing). VM works, M4 broken. Caused by commits between 0cc0230 and 6b26690.

---

## Notes

- Blocking: Can't trust M4 until jobs work
- This is a regression, not an existing issue
- Must identify culprit commit to avoid deploying broken code
- Fix likely straightforward once root cause identified (initialization order issue)
- May require coordination with facet-limiting changes (don't want to revert those, just fix initialization)
