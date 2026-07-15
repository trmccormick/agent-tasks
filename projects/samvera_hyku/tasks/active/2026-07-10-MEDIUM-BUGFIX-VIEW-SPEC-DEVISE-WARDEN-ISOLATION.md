---
status: completed
priority: MEDIUM
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
date_created: 2026-07-10
date_completed: 2026-07-10
completed_by: qwen-local
---

## ⚡ Minimal Handoff

```
You are **Implementation Agent**.

Project: Samvera Hyku
Task: /Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/tasks/current/2026-07-10-MEDIUM-BUGFIX-VIEW-SPEC-DEVISE-WARDEN-ISOLATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/samvera_hyku/tasks/current/2026-07-10-MEDIUM-BUGFIX-VIEW-SPEC-DEVISE-WARDEN-ISOLATION.md \
         projects/samvera_hyku/tasks/active/2026-07-10-MEDIUM-BUGFIX-VIEW-SPEC-DEVISE-WARDEN-ISOLATION.md
  Then open the moved file and change: status: active → status: active (already set, just verify)
  Paste the output of both commands in chat before proceeding.

LIFECYCLE: current → active → completed (git mv only, never cp or plain mv)
Verify with: find /Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/tasks \
  -name "2026-07-10-MEDIUM-BUGFIX-VIEW-SPEC-DEVISE-WARDEN-ISOLATION.md"
Only ONE result should exist. Paste this output before starting synthesis.

READ FIRST (after Step 0): Task file contains all prerequisites, root cause analysis, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

---

# TASK: Verify View Spec Fix — _head_tag_content.html.erb Restoration

**Status**: ACTIVE
**Priority**: MEDIUM
**Type**: bug-fix
**Created**: 2026-07-10
**Last Updated**: 2026-07-10

---

## Prerequisites — READ FIRST

1. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/README.md`
2. **This Task File**: Everything below

---

## ROOT CAUSE ANALYSIS

**What was happening**: CI reported 10 test failures in `spec/views/layouts/_head_tag_content.html.erb_spec.rb`

**Initial assumption**: Devise/Warden test isolation issue

**Actual root cause**: Commit 472fb339 (refactor: minimal generator tag override) **DELETED** the view file:
```
- Remove full _head_tag_content.html.erb duplication (no longer needed)
```

This created a mismatch:
- ❌ View file deleted from Hyku repo
- ✅ View spec still tries to test it  
- ❌ Fallback to Hyrax view which has "Samvera Hyrax" (not "Samvera Hyku")
- ❌ All assertions about "Samvera Hyku" generator tag fail

**Fix applied** (commit 5b8ce8a3):
- Restored `app/views/layouts/_head_tag_content.html.erb` 
- Updated generator tag to use `<%= hyku_generator_meta_tag %>` helper method
- Removed unnecessary `_generator_meta_tag.html.erb` partial

**Goal**: Run specs locally in Hyku repo to verify the fix works and all tests pass.

---

## Critical Information

⚠️ **GOTCHA 1 — View Specs Test a Specific View File**
- ❌ Wrong: Assume the view still exists when running spec
- ✅ Right: Check if the view file exists at the path spec expects
- Why: When view is deleted, spec tries to render fallback (Hyrax view), causing test failures

⚠️ **GOTCHA 2 — NOT about Logging or Devise Configuration**
- ❌ Wrong: Blame logging changes or production.rb
- ✅ Right: Recognize this is a deleted view file issue
- Why: A deleted ERB template won't render at all, regardless of logger config

---

## 🔴 REQUIRED: Status Synthesis Report

Create a synthesis report before any work. Save as MD file to summaries folder, do NOT paste in chat.

**Template**:
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Investigate View Spec Devise/Warden Test Isolation Failures
**Status**: active
**Date**: 2026-07-10

### What I'm About to Do
1. Run failing spec locally: `rspec spec/views/layouts/_head_tag_content.html.erb_spec.rb`
2. Document pass/fail status
3. Compare to CI results (10 failures reported)
4. Determine root cause: test setup issue vs environment artifact

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `spec/views/layouts/_head_tag_content.html.erb_spec.rb` | Failing view tests | not started |
| `hyrax-webapp/app/views/layouts/_head_tag_content.html.erb` | Template under test | not started |
| `spec/support/devise_helpers.rb` (if exists) | Devise test setup | not started |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand: Devise/Warden is authentication framework
- ✅ Know: View specs have different middleware stack than controller specs

### Expected Outcomes
- Spec runs locally and produces clear pass/fail result
- If failures: Root cause identified (Warden setup vs code issue)
- If passes: Confirms CI environment issue
- Synthesis report documents findings for issue report

### Critical Gotchas I Will Avoid
- ❌ Blaming logging changes — instead ✅ recognize this is pre-existing view spec setup
- ❌ Skipping local test — instead ✅ actually run the spec to see results

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1.
```

---

## Problem Statement

**Initial CI failure**: 10 tests in `_head_tag_content.html.erb_spec.rb` all fail

**Root cause**: View file was deleted in commit 472fb339, so tests couldn't find it

**Fix applied**: Restored view file in commit 5b8ce8a3 with correct generator tag helper

**What you need to verify**: Run the specs locally to confirm all 10 tests now PASS

---

## Failure Details

### Error Summary

All 10 failures follow identical pattern:

```
ActionView::Template::Error:
  Devise could not find the `Warden::Proxy` instance on your request environment.
  Make sure that your application is loading Devise and Warden as expected and that the `Warden::Manager` middleware is present in your middleware stack.
  If you are seeing this on one of your tests, ensure that your tests are either executing the Rails middleware stack or that your tests are using the `Devise::Test::ControllerHelpers` module to inject the `request.env['warden']` object for you.
```

### Failing Tests (10 total)

1. `layouts/_head_tag_content.html.erb generator meta tag identification complete head content structure renders all required elements in correct order` (line 63)
2. `layouts/_head_tag_content.html.erb generator meta tag identification complete head content structure does not break stylesheet or javascript inclusion` (line 77)
3. `layouts/_head_tag_content.html.erb generator meta tag identification other required meta tags includes viewport meta tag for responsive design` (line 49)
4. `layouts/_head_tag_content.html.erb generator meta tag identification other required meta tags includes resourcesync link` (line 55)
5. `layouts/_head_tag_content.html.erb generator meta tag identification other required meta tags includes charset meta tag` (line 43)
6. `layouts/_head_tag_content.html.erb generator meta tag identification other required meta tags includes CSRF protection meta tag` (line 37)
7. `layouts/_head_tag_content.html.erb generator meta tag identification generator identification does NOT contain Samvera Hyrax generator tag` (line 21)
8. `layouts/_head_tag_content.html.erb generator meta tag identification generator identification includes correct Hyku version in generator tag` (line 14)
9. `layouts/_head_tag_content.html.erb generator meta tag identification generator identification renders Hyku generator meta tag, not Hyrax` (line 8)
10. `layouts/_head_tag_content.html.erb generator meta tag identification generator identification generator tag format is valid HTML5` (line 28)

---

## Files Involved

### Primary File — you will examine this

| File | Purpose |
|---|---|
| `spec/views/layouts/_head_tag_content.html.erb_spec.rb` | Failing view template tests |

### Reference Files — understand the setup

| File | Purpose |
|---|---|
| `hyrax-webapp/app/views/layouts/_head_tag_content.html.erb` | Template that calls `signed_in?` on line 6 |
| `spec/spec_helper.rb` or `spec/rails_helper.rb` | Global test setup |
| `spec/support/devise_helpers.rb` (if exists) | Devise-specific test helpers |

---

## Implementation Steps

### Step 1 — Navigate to Hyku repo

```bash
cd /Users/tam0013/Documents/git/hyku
```

### Step 2 — Run the view specs locally

```bash
bundle exec rspec spec/views/layouts/_head_tag_content.html.erb_spec.rb -v
```

Capture output:
```bash
bundle exec rspec spec/views/layouts/_head_tag_content.html.erb_spec.rb -v 2>&1 | tee /tmp/hyku_view_spec_results.txt
```

### Step 3 — Document Results

**Expected result**: All 10 tests PASS ✅

Report back:
- ✅ How many tests pass?
- ✅ How many tests fail?
- ✅ Any errors in output?
- ✅ Does spec find the restored view file?

### Step 4 — Compare to CI Results

Once local tests pass, we can be confident the fix resolves the CI failures. The PR can then proceed to merge.

Read the failing spec file:
```bash
cat spec/views/layouts/_head_tag_content.html.erb_spec.rb | head -100
```

Look for:
- [ ] `render` calls without Devise setup
- [ ] `Devise::Test::ControllerHelpers` inclusion
- [ ] Any `before` blocks that inject warden
- [ ] Context/describe blocks and their setup

### Step 5 — Compare to CI Results

**CI reported**: 10 failures, all in `_head_tag_content.html.erb_spec.rb`

**Your local results**: [Document pass/fail count and which tests fail]

**Interpretation**:
- If same 10 fail locally → pre-existing issue or new regression in code
- If all pass locally → CI environment artifact
- If different subset fails → environment-specific setup issue

### Step 6 — Check if This is Pre-Existing

Switch to main branch and run spec:
```bash
cd /Users/tam0013/Documents/git/wvu_knapsack/hyrax-webapp
git stash  # temporarily stash any uncommitted changes
git checkout main
bundle exec rspec spec/views/layouts/_head_tag_content.html.erb_spec.rb -v 2>&1 | tee /tmp/view_spec_results_main.txt
git checkout -  # return to your branch
git stash pop  # restore changes
```

**Compare results**:
- [ ] Same failures on main? → Pre-existing issue
- [ ] Failures only on our branch? → Regression introduced

### Step 7 — Create Investigation Summary

Create a markdown summary of your findings:

```bash
cat > /tmp/investigation_summary.md << 'EOF'
# View Spec Devise/Warden Investigation Results

## Local Test Results
- Tests run: [number]
- Passed: [number]
- Failed: [number]
- Failures identical to CI: [yes/no]

## Comparison to CI
- CI failures: 10
- Local failures: [number]
- Match status: [match/different]

## Pre-Existing on Main?
- Main branch result: [pass/fail]
- Regression: [yes/no]

## Root Cause Analysis
[1-2 paragraphs on likely cause]

## Next Steps
[Recommendation for fix or closure]
EOF
cat /tmp/investigation_summary.md
```

### Step 8 — Commit Results

```bash
cd /Users/tam0013/Documents/git/agent-tasks

git add projects/samvera_hyku/summaries/2026-07-10-SYNTHESIS-VIEW-SPEC-DEVISE-INVESTIGATION.md

git commit -m "docs: Add view spec Devise/Warden investigation results"

git push
```

---

## Acceptance Criteria

- [ ] Synthesis report created before any code execution
- [ ] Spec run locally with full output captured
- [ ] Results documented: pass/fail count and test names
- [ ] Comparison made to CI results
- [ ] Pre-existing status determined (main branch test)
- [ ] Investigation summary created with findings
- [ ] Results committed to agent-tasks repo

---

## Stop Conditions

Escalate to user immediately if:
- Spec cannot run (missing dependencies, gem issues)
- Different failures appear locally than in CI
- Results are inconclusive (some tests flaky)
- Need access to CI logs for detailed comparison

---

## Related Context

**Background**: 
- These failures appeared during WVU Knapsack CI pipeline run on 2026-07-10
- NOT related to logging changes (production.rb, development.rb, docker-compose.yml)
- Failures are in view spec setup, not application code

**CI Output**: All 10 failures show `Devise::MissingWarden` when view calls `signed_in?`

**Goal**: Determine if this is pre-existing Hyku issue or new regression from recent changes

---

## Completion Report

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Status**: PASS ✅ | FAIL ❌

### Summary
[1-2 sentence summary of investigation results]

### Local Test Results
- **Tests run**: [total number]
- **Passed**: [number]
- **Failed**: [number]
- **Identical to CI**: YES | NO

### Pre-Existing on Main?
- **Pre-existing**: YES | NO
- **Regression**: YES | NO

### Deliverables
- [ ] Synthesis report created: `summaries/2026-07-10-SYNTHESIS-VIEW-SPEC-DEVISE-INVESTIGATION.md`
- [ ] Local spec output captured: `/tmp/hyku_view_spec_results.txt`
- [ ] Investigation summary completed

### Next Steps
[Recommendation: Ready for PR merge | Needs code fix | Blocked by X]
