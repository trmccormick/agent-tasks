---
status: backlog
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

- [x] Agent Dispatch Interface section below is complete and accurate
- [x] All Step 0-N instructions are clear and actionable
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific
- [x] Acceptance Criteria are measurable
- [x] Dependencies clear (blocks catalog facet limiting test)

**Task is READY FOR DISPATCH.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Qwen (Implementation Agent)** working on **WVU Libraries Knapsack**.

Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/backlog/2026-08-21-HIGH-BUGFIX-DEBUG-GOODJOB-WINGS-ERROR.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE:
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/wvulibraries_knapsack/tasks/backlog/2026-08-21-HIGH-BUGFIX-DEBUG-GOODJOB-WINGS-ERROR.md \
         projects/wvulibraries_knapsack/tasks/active/2026-08-21-HIGH-BUGFIX-DEBUG-GOODJOB-WINGS-ERROR.md
  git commit -m "task: move goodjob wings debug to active dispatch"

STEP 1 — READ PROJECT CONTEXT:
  cat /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/status.md

STEP 2 — UNDERSTAND THE PROBLEM:
  The GoodJob queue is failing with Wings::ModelRegistry deserialization error.
  This BLOCKS the catalog facet limiting test (next task).
  You must determine: Is this a pre-existing environment issue, or did our recent changes break it?

STEP 3 — FOLLOW INVESTIGATION STEPS:
  See "Investigation Steps" section below.
  After completion, create SYNTHESIS REPORT using template at end of this file.

STEP 4 — REPORT FINDINGS:
  Post synthesis report to chat. Do NOT proceed to facet testing until this is resolved.
```

---

## 📋 TASK: Debug GoodJob Wings::ModelRegistry Deserialization Error

**Objective**: Determine root cause of GoodJob job failures and fix if caused by recent code changes.

**Context**: 
- User changed decorator loading on branch `fix/hide-type-facet-add-show-more-facets`
- Recent commits: ce0ff2d (prepend fix), 2b148dd (initializer), 60c9463 (wrapper)
- After changes, GoodJob shows: `ActiveJob::DeserializationError: Error while trying to deserialize arguments: uninitialized constant Wings::ModelRegistry`
- 1263+ jobs stuck in failed state
- User suspects our changes broke the background job initialization

**This blocks**: The catalog facet limiting test cannot proceed until indexing completes.

---

## 🔧 Investigation Steps

### STEP 1: Identify Culprit Commit

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# See commits on this branch vs main
git log --oneline origin/main..HEAD

# Record the output — what changed?
```

**Questions to answer**:
- Which commits are only on this branch?
- What are the commit messages?
- Do any touch background job initialization, Rails configuration, or Wings/Valkyrie setup?

### STEP 2: Test on Main Branch (Pre-change State)

```bash
# Stash current changes
git stash

# Switch to main
git checkout origin/main

# Restart Stack Car
cd /Users/tam0013/Documents/git/wvu_knapsack
sh down.sc.local.sh && sleep 5 && sh up.sc.local.sh

# Wait for web container to start (2-3 minutes)
# Then check GoodJob queue
```

**Navigate to**: `https://admin-wvu-knapsack.localhost.direct/jobs/jobs?locale=en`

**Question**: Does the Wings::ModelRegistry error exist on main branch?
- **YES** = Error is pre-existing, not caused by our changes
- **NO** = One of our commits broke it

### STEP 3: Return to Feature Branch

```bash
git checkout fix/hide-type-facet-add-show-more-facets
git stash pop
```

### STEP 4: If Error was PRE-EXISTING

Document in synthesis report that this is an environment-level issue unrelated to our changes. Proceed to next task (facet testing).

### STEP 5: If Error was CAUSED BY OUR CHANGES

Investigate which commit:
- Review ce0ff2d (prepend in initializer)
- Review 2b148dd (initial initializer)
- Check if to_prepare block is preventing Wings initialization

**Likely culprits**:
- `config/initializers/load_catalog_controller_decorator.rb` — does require_relative break class loading?
- The to_prepare block timing — is it preventing Wings::ModelRegistry initialization?

**Debug approach**:
```bash
# Check initializer file
cat /Users/tam0013/Documents/git/wvu_knapsack/config/initializers/load_catalog_controller_decorator.rb

# Look for any patterns that could prevent Wings loading
# - Does require_relative cause circular dependency?
# - Is to_prepare block timing too early/late?

# Check Rails logs
sc logs web 2>&1 | grep -i "wings\|valkyrie\|error" | head -30
```

**Fix strategy** (if needed):
- Remove or modify the to_prepare block
- Change timing of decorator application
- Ensure Wings is fully initialized before decorator runs

### STEP 6: Create Synthesis Report

Use template at end of this file.

---

## 🎯 Acceptance Criteria

✅ Task complete when synthesis report includes:
1. **Root Cause Identified**: Pre-existing OR caused by commit [HASH]
2. **Evidence Provided**: Git log, test results, error messages
3. **If Pre-existing**: Clear statement that this is environment-level issue
4. **If Caused by Our Changes**: Solution proposed or fix implemented
5. **Action Item for Next Task**: Clear readiness statement for facet testing

---

## 📝 SYNTHESIS REPORT TEMPLATE (COPY & PASTE)

```
## GoodJob Wings::ModelRegistry Error Investigation

**Date Investigated**: [TODAY]
**Branch**: fix/hide-type-facet-add-show-more-facets

### FINDING: [Pre-existing OR Caused by Our Changes]

### ROOT CAUSE
[Describe what is causing the error]

### EVIDENCE
```bash
# Git log showing recent commits:
[PASTE OUTPUT]

# Test result on main branch:
[Did error exist? YES / NO]

# Error messages from logs:
[PASTE RELEVANT ERROR]
```

### COMMITS INVOLVED
- ce0ff2d: [Summary and whether it contributed]
- 2b148dd: [Summary and whether it contributed]
- 60c9463: [Summary and whether it contributed]

### SOLUTION STATUS
- [ ] Pre-existing — no action needed for our work
- [ ] Fixed — describe fix and test result
- [ ] Unresolved — describe blocker and next steps

### RECOMMENDATION FOR FACET TESTING TASK
[Can we proceed with next task? What state is the system in?]
```

---

## 🏗️ Architecture Notes

**Wings/Valkyrie**:
- Handles object serialization/deserialization
- ModelRegistry must be initialized before background jobs can deserialize job arguments
- If Rails initialization order is disrupted, Wings won't be ready when GoodJob tries to deserialize

**GoodJob**: 
- Processes background jobs from database queue
- Needs full Rails environment (models, initializers) loaded
- If initializer runs at wrong time, it can prevent other initializers from running

**to_prepare block**:
- Runs after all initializers, every time code reloads (development)
- Used for class decorators/monkey-patching
- If it prevents other code from running, it breaks initialization order

---

## ❌ Do NOT

- Do not run `sh up.sc.local.sh` multiple times in quick succession (destroys volumes)
- Do not modify decorator/initializer yet — just investigate
- Do not assume error is unrelated without testing on main branch

## ✅ DO

- Do test on main branch before concluding it's pre-existing
- Do check Rails logs for initialization order issues
- Do provide full synthesis report before stopping
