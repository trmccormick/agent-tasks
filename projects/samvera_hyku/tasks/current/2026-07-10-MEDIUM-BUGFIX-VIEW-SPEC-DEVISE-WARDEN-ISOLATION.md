---
status: active
priority: MEDIUM
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## ⚡ Minimal Handoff

```
You are **Investigation Agent**.

Project: Samvera Hyku
Task: /Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/tasks/current/2026-07-10-MEDIUM-BUGFIX-VIEW-SPEC-DEVISE-WARDEN-ISOLATION.md

GOAL: Determine if test failures are CI-specific or reproducible locally.

STEP 1: Run the failing spec locally
STEP 2: Document results (passes vs fails)
STEP 3: Compare to CI results
STEP 4: Create synthesis report with findings
```

---

# TASK: Investigate View Spec Devise/Warden Test Isolation Failures

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

## Context

CI pipeline reported 10 test failures in `spec/views/layouts/_head_tag_content.html.erb_spec.rb`. All failures show the same root cause: `Devise::MissingWarden` exception when view template calls `signed_in?` helper. The failures appear to be test setup/isolation issues unrelated to recent code changes.

**Goal**: Reproduce locally to determine if this is:
- ✅ Pre-existing issue in Hyku main (CI environment artifact)
- ✅ New regression introduced by recent changes
- ✅ Environment-specific setup issue

---

## Critical Information

### Architecture Gotchas

⚠️ **GOTCHA 1 — View Specs Need Devise Test Helpers**
- ❌ Wrong: `render` without setting up `request.env['warden']`
- ✅ Right: Use `Devise::Test::ControllerHelpers` or manually inject warden in before block
- Why: View tests don't automatically load Rails middleware stack where Devise registers Warden

⚠️ **GOTCHA 2 — This is NOT Related to Logging Changes**
- ❌ Wrong: Assume failures are caused by production.rb or docker-compose changes
- ✅ Right: Recognize this is a pre-existing view spec setup issue
- Why: No logging changes touch view rendering or Devise configuration

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

**Current behavior**: CI pipeline shows 10 test failures in `_head_tag_content.html.erb_spec.rb`.

**Error message**: `Devise could not find the Warden::Proxy instance on your request environment`

**When it happens**: When view template renders and calls `signed_in?` helper on line 6

**Why it matters**: 
- Unclear if this is pre-existing or new regression
- CI failures block deployment
- Need to distinguish between view spec setup issue vs actual code regression

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

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP)

**Do this before anything else:**

```bash
cd /Users/tam0013/Documents/git/agent-tasks

git mv projects/samvera_hyku/tasks/current/2026-07-10-MEDIUM-BUGFIX-VIEW-SPEC-DEVISE-WARDEN-ISOLATION.md \
       projects/samvera_hyku/tasks/active/2026-07-10-MEDIUM-BUGFIX-VIEW-SPEC-DEVISE-WARDEN-ISOLATION.md
```

Then open the moved file and change the YAML status:
```yaml
status: active  →  status: active
```

(It's already active, so this confirms the move)

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/tasks \
     -name "2026-07-10-MEDIUM-BUGFIX-VIEW-SPEC-DEVISE-WARDEN-ISOLATION.md"
```

**Expected**: Exactly one result at `tasks/active/2026-07-10-MEDIUM-BUGFIX-VIEW-SPEC-DEVISE-WARDEN-ISOLATION.md`

Paste the find output in chat before proceeding to Step 1.

### Step 1 — Create Synthesis Report

Before running any code, create a synthesis report file:

```bash
cat > /Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/summaries/2026-07-10-SYNTHESIS-VIEW-SPEC-DEVISE-INVESTIGATION.md << 'EOF'
[Use the template from "REQUIRED: Status Synthesis Report" section above]
EOF
```

Post link to this file in chat before proceeding to Step 2.

### Step 2 — Run Failing Spec Locally

Navigate to the Hyku submodule:
```bash
cd /Users/tam0013/Documents/git/wvu_knapsack/hyrax-webapp
```

Run the specific failing spec:
```bash
bundle exec rspec spec/views/layouts/_head_tag_content.html.erb_spec.rb -v
```

**Capture full output** (redirect to file for review):
```bash
bundle exec rspec spec/views/layouts/_head_tag_content.html.erb_spec.rb -v 2>&1 | tee /tmp/view_spec_results.txt
```

### Step 3 — Document Results

After spec completes, note:
- ✅ How many pass vs fail?
- ✅ Are the failures identical to CI failures?
- ✅ Do all 10 fail, or different subset?
- ✅ Any new failures or warnings?

### Step 4 — Examine Test Setup

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
